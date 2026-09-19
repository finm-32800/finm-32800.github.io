# Unit Tests


Unit testing is an essential part of developing reliable, maintainable software. By following these steps and best practices, you can create effective unit tests for your Python functions, ensuring they behave as intended under various conditions.

For additional worked examples, see the [in-class examples for unit tests](https://github.com/finm-32800/inclass_examples/tree/main/unit_tests).

## Standard: Unit Tests in a Test File

Creating unit tests in separate test files is a fundamental practice in software development, ensuring your code works as expected through automated tests. This brief tutorial will explain how to write unit tests for a Python function called `weighted_average` using a test function named `test_weighted_average` located in a file called `test_misc_tools.py`.


### What Makes a Good Unit Test?

A good unit test should be:

- **Specific**: It tests a single aspect of the function's behavior.
- **Isolated**: It does not rely on external systems or complex setup.
- **Repeatable**: It can be run multiple times and will produce the same results.
- **Readable**: It clearly conveys what is being tested and what the expected outcome is.
- **Comprehensive**: It covers both typical use cases and edge cases.

Furthermore, some best practices would be to 

- **Test One Thing at a Time**: Each test function should test a single behavior of the function.
- **Use Descriptive Test Names**: The name of the test function should describe what aspect of the function it's testing.
- **Arrange-Act-Assert Pattern**: Organize your test into three sections: setting up data (`Arrange`), calling the function (`Act`), and asserting the outcome (`Assert`).
- **Handle Exceptions**: If your function can raise exceptions, write tests to ensure these exceptions are raised under the correct conditions.


### Example

Here's a concise example based on the provided `weighted_average` function and its test.


```python
import numpy as np
import pandas as pd
def weighted_average(data_col=None, weight_col=None, data=None):
    """Simple calculation of weighted average.

    Example:
    >>> df_nccb = pd.DataFrame({
    ...     'rate': [2, 3, 2],
    ...     'start_leg_amount': [100, 200, 100]},
    ... )
    >>> weighted_average(data_col='rate', weight_col='start_leg_amount', data=df_nccb)
    2.5
    """
    weights_function = lambda row: data.loc[row.index, weight_col]
    wm = lambda row: np.average(row, weights=weights_function(row))
    result = wm(data[data_col])
    return result

```
And the test

```python
from misc_tools import weighted_average
import pandas as pd

def test_weighted_average():
    # Set up test data
    df_nccb = pd.DataFrame({
        "rate": [2, 3, 2], 
        "start_leg_amount": [100, 200, 100]
    })
    # Call the function
    result = weighted_average(data_col="rate", weight_col="start_leg_amount", data=df_nccb)
    # Expected result
    expected = 2.5
    # Assert function output is as expected
    assert result == expected, "The weighted_average function should return 2.5"
```

### Running the Tests

To run your tests, use the `pytest` command in the terminal:

```sh
pytest test_misc_tools.py
```
or just

```sh
pytest
```

`pytest` will automatically find and run all test functions defined in the specified file, reporting any failures so you can address them.


## "Doctests" or Unit Tests in Docstrings
Writing unit tests within docstrings, a practice known as doctest, is an efficient way to both document and test your code simultaneously. Here's a quick walkthrough of writing effective unit tests as docstrings, using the provided Python functions `create_lagged_columns` and `with_lagged_columns` within the `misc_tools.py` module in https://github.com/jmbejara/blank_project as examples.

### Writing Doctests

Doctests are written in the docstring of a Python function, module, or class. They are written in the form of an interactive Python session.

**Syntax**

- **Prompt lines** (`>>>`): This is where you call your function or write expressions.
- **Output lines**: This is the expected output from the prompt line directly above it.
- **Blank lines**: A single blank line indicates the end of a test. Multiple blank lines can be used for readability.

### Example

Code to be tested:
```python


def create_lagged_columns(data=None, columns_to_lag=None, id_columns=None, lags=1, 
                         date_col='date', prefix='L'):

    all_dates = data[date_col].drop_duplicates().sort_values().reset_index(drop=True)
    date_to_lag_date = pd.concat([all_dates, all_dates.shift(-lags)], axis=1)
    date_to_lag_date.columns = [date_col, '_lagged_date']

    sub_df = data[[date_col, *id_columns, *columns_to_lag]]
    lag_sub_df = sub_df.merge(date_to_lag_date, on=[date_col])

    for col in columns_to_lag:
        lag_sub_df = lag_sub_df.rename(columns={col: f'{prefix}{lags}_' + col})

    lag_sub_df = lag_sub_df.drop(columns=[date_col])
    lag_sub_df = lag_sub_df.rename(columns={'_lagged_date':date_col})
    
    return lag_sub_df

def with_lagged_columns(data=None, columns_to_lag=None, id_columns=None, lags=1, 
                         date_col='date', prefix='L'):
    """
    >>> a=[[1,'1990/1/1',1],
    ... [1,'1990/2/1',2],
    ... [1,'1990/3/1',3],
    ... [2,'1989/12/1',3],
    ... [2,'1990/1/1',3],
    ... [2,'1990/2/1',4],
    ... [2,'1990/3/1',5.5],
    ... [2,'1990/4/1',5],
    ... [2,'1990/6/1',6]]

    >>> data=pd.DataFrame(a,columns=['id','date','value'])
    >>> data['date']=pd.to_datetime(data['date'])

    >>> data
       id       date  value
    0   1 1990-01-01   1.00
    1   1 1990-02-01   2.00
    2   1 1990-03-01   3.00
    3   2 1989-12-01   3.00
    4   2 1990-01-01   3.00
    5   2 1990-02-01   4.00
    6   2 1990-03-01   5.50
    7   2 1990-04-01   5.00
    8   2 1990-06-01   6.00

    >>> data_lag = with_lagged_columns(data=data, columns_to_lag=['value'], id_columns=['id'], lags=1)
    >>> data_lag
       id       date  value  L1_value
    0   1 1990-01-01   1.00       NaN
    1   1 1990-02-01   2.00      1.00
    2   1 1990-03-01   3.00      2.00
    3   2 1989-12-01   3.00       NaN
    4   2 1990-01-01   3.00      3.00
    5   2 1990-02-01   4.00      3.00
    6   2 1990-03-01   5.50      4.00
    7   2 1990-04-01   5.00      5.50
    8   2 1990-06-01   6.00      5.00
    
    """
    data_lag = create_lagged_columns(data=data, columns_to_lag=columns_to_lag, 
                                     id_columns=id_columns, lags=lags, 
                                     date_col=date_col, prefix=prefix)
    w_data_lag = data.merge(data_lag, on=[date_col, *id_columns], how='left')
    return w_data_lag

```

Consider the `with_lagged_columns` function. The doctest within its docstring demonstrates how to use the function and what output to expect:

```python
"""
...
>>> data_lag = with_lagged_columns(data=data, columns_to_lag=['value'], id_columns=['id'], lags=1)
>>> data_lag
   id       date  value  L1_value
0   1 1990-01-01   1.00       NaN
1   1 1990-02-01   2.00      1.00
...
"""
```

### Running Doctests

To run doctests, you can use the following command:

```sh
pytest --doctest-modules
```

This command will search for docstrings in all Python modules and run any found doctests.
