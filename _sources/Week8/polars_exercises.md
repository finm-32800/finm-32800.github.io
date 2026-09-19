# Polars Exercises: Code Snippets

The in-class exercises in [`polars/`](https://github.com/finm-32800/inclass_examples/tree/main/polars)
(in the [in-class examples repository](https://github.com/finm-32800/inclass_examples)) walk
through Polars progressively---from basic syntax to production-scale data handling. Below are
selected snippets from each module.

```{tip}
Run each script yourself to see full output and timing results:
`python pandas_vs_polars.py`, `python lazy_and_pushdown.py`, etc.
```


## 1. Syntax Comparison

*From `01_syntax_comparison/pandas_vs_polars.py`*

### Filtering + adding columns (no SettingWithCopyWarning)

```python
result = (
    plf
    .filter(pl.col("sector") == "Technology")
    .with_columns(pl.col("return_pct").log1p().alias("log_return"))
)
```

### Conditional logic

```python
plf.with_columns(
    pl.when(pl.col("return_pct") > 0.01)
    .then(pl.lit("strong_up"))
    .when(pl.col("return_pct") > 0)
    .then(pl.lit("weak_up"))
    .when(pl.col("return_pct") > -0.01)
    .then(pl.lit("weak_down"))
    .otherwise(pl.lit("strong_down"))
    .alias("signal")
)
```

### GroupBy with expressions

```python
plf.group_by("sector").agg(
    pl.col("return_pct").mean().alias("avg_return"),
    pl.col("volume").sum().alias("total_volume"),
    pl.col("ticker").count().alias("num_obs"),
)
```

### Window functions with `.over()`

```python
plf.with_columns(
    pl.col("return_pct").mean().over("sector").alias("sector_avg"),
    pl.col("volume").rank(descending=True).over("date").alias("volume_rank"),
)
```

### Full analytical pipeline in one chain

```python
result = (
    plf
    .filter(pl.col("return_pct").abs() < 0.05)
    .with_columns(pl.col("return_pct").log1p().alias("log_return"))
    .group_by("sector")
    .agg(
        pl.col("log_return").mean().alias("avg_log_return"),
        pl.col("volume").sum().alias("total_volume"),
        pl.col("ticker").count().alias("count"),
    )
    .sort("avg_log_return", descending=True)
)
```


## 2. LazyFrames and Query Optimization

*From `02_lazyframes_and_optimization/lazy_and_pushdown.py`*

### Lazy scan + filter + select + collect

```python
result = (
    pl.scan_parquet("stock_data.parquet")
    .filter(pl.col("sector") == "Technology")
    .select("date", "ticker", "return_pct", "volume")
    .group_by("ticker")
    .agg(
        pl.col("return_pct").mean().alias("avg_return"),
        pl.col("volume").sum().alias("total_volume"),
    )
    .collect()
)
```

### Inspecting query plans with `.explain()`

```python
query = (
    pl.scan_parquet("stock_data.parquet")
    .filter(pl.col("sector") == "Technology")
    .select("date", "ticker", "return_pct")
)

print(query.explain())
# The optimized plan shows:
#   - Filter pushed INTO the Parquet scan (predicate pushdown)
#   - Only 3 columns read from disk (projection pushdown)
```

### Combined predicate + projection pushdown

```python
# Eager: reads ALL rows and ALL columns, then filters and selects
result = (
    pl.read_parquet("stock_data.parquet")
    .filter(pl.col("sector") == "Technology")
    .select("date", "ticker", "return_pct")
)

# Lazy: only reads the rows and columns you need (~10x speedup)
result = (
    pl.scan_parquet("stock_data.parquet")
    .filter(pl.col("sector") == "Technology")
    .select("date", "ticker", "return_pct")
    .collect()
)
```


## 3. Streaming and Hive Partitioning

*From `03_large_data/01_streaming.py` and `03_large_data/02_hive_partitioning.py`*

### Streaming execution

```python
query = (
    pl.scan_parquet("trades.parquet")
    .group_by("sector", "exchange")
    .agg(
        pl.col("price").mean().alias("avg_price"),
        pl.col("quantity").sum().alias("total_qty"),
        pl.len().alias("num_trades"),
    )
)

# Standard: materializes full intermediate results
result = query.collect()

# Streaming: processes in batches, lower peak memory
result = query.collect(engine="streaming")
```

### Writing results directly to disk with `sink_parquet()`

```python
(
    pl.scan_parquet("trades.parquet")
    .group_by("sector")
    .agg(
        pl.col("price").mean().alias("avg_price"),
        pl.col("quantity").sum().alias("total_qty"),
        pl.len().alias("num_trades"),
    )
    .sort("sector")
    .sink_parquet("sector_stats.parquet")
)
# Results written to disk without ever being held in memory
```

### Writing Hive-partitioned data

```python
df.write_parquet(
    "trades_hive/",
    use_pyarrow=True,
    pyarrow_options={"partition_cols": ["sector"]},
)
# Creates: trades_hive/sector=Technology/..., trades_hive/sector=Finance/..., etc.
```

### Reading with partition pruning

```python
result = (
    pl.scan_parquet("trades_hive/**/*.parquet", hive_partitioning=True)
    .filter(pl.col("sector") == "Finance")     # only reads Finance/ directory
    .group_by("ticker")
    .agg(
        pl.col("price").mean().alias("avg_price"),
        pl.col("quantity").sum().alias("total_qty"),
        pl.len().alias("num_trades"),
    )
    .sort("total_qty", descending=True)
    .collect()
)
```


## 4. Performance Benchmarks

*From `04_performance_showdown/benchmark.py`*

### Rolling window with `.over()`

```python
plf.with_columns(
    pl.col("return_pct")
    .rolling_mean(window_size=20)
    .over("ticker")
    .alias("rolling_mean")
)
```

### Complex analytical pipeline

```python
result = (
    plf.lazy()
    .filter(pl.col("date") >= date(2020, 1, 1))
    .with_columns(
        pl.col("return_pct").log1p().alias("log_return"),
    )
    .sort("ticker", "date")
    .with_columns(
        pl.col("log_return")
        .rolling_std(window_size=20)
        .over("ticker")
        .alias("rolling_vol"),
        pl.col("date").dt.strftime("%Y-%m").alias("year_month"),
    )
    .group_by("sector", "year_month")
    .agg(
        pl.col("log_return").mean().alias("avg_return"),
        pl.col("rolling_vol").mean().alias("avg_vol"),
        pl.col("volume").sum().alias("total_volume"),
        pl.len().alias("count"),
    )
    .collect()
)
```

### Benchmark results (1M rows, M1 MacBook Pro)

| Task | pandas (s) | polars (s) | Speedup |
|------|-----------|-----------|---------|
| Filter + aggregate | 0.037 | 0.003 | 13.7x |
| Rolling window | 0.265 | 0.011 | 24.9x |
| Multi-key join | 0.085 | 0.006 | 14.0x |
| Complex pipeline | 0.121 | 0.034 | 3.6x |
| Memory (MB) | 230.8 | 53.2 | 4.3x |
