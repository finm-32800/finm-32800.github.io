# Exercise: Jupyter Notebook on Midway via SSH

In this exercise, you will:

1. Connect to Midway3 via SSH
2. Request an interactive compute node with SLURM
3. Launch a Jupyter notebook server on the compute node
4. Set up SSH port forwarding so you can access the notebook from your laptop's browser

By the end, you'll have a Jupyter notebook running on a powerful remote machine,
accessible from your laptop as if it were running locally.

There are two ways to get a notebook-capable environment running on Midway:

1. **The manual option (Steps 1--8 below).** You request a compute node,
   start Jupyter yourself, and build the SSH tunnel by hand. Do this at least
   once---every piece of the pattern (SLURM, ports, tunnels) is something you
   should understand.
2. **The `scode` option.** RCC provides a tool called `scode` that automates
   the same pattern, but serves VS Code in your browser (with Jupyter notebook
   support) instead of the classic Jupyter interface. See
   [Option 2: VS Code in the Browser with `scode`](#option-2-scode) below.


## Prerequisites

- A UChicago CNetID with RCC access
- A terminal application (Terminal on macOS, Windows Terminal or Git Bash on Windows)
- Duo Mobile configured for multi-factor authentication


## Step 1: SSH into Midway3

Open a terminal on your laptop and connect:

```bash
ssh <your-cnetid>@midway3.rcc.uchicago.edu
```

Enter your password when prompted, then complete Duo MFA. You should see a
welcome message and a prompt like:

```text
[<cnetid>@midway3-login1 ~]$
```

You are now on a **login node**.

```{admonition} Reminder
:class: warning

Do not run computations on the login node. It is shared by all users and
intended only for lightweight tasks like file editing, job submission, and
data transfer.
```


## Step 2: Request an Interactive Compute Node

Use `sinteractive` to request a dedicated compute node:

```bash
sinteractive --account=finm32900 --partition=caslake \
    --time=02:00:00 --cpus-per-task=4 --mem=16G
```

This requests:
- **Account**: `finm32900` (our course allocation)
- **Partition**: `caslake` (the default Midway3 partition)
- **Time**: 2 hours
- **CPUs**: 4 cores
- **Memory**: 16 GB

You may wait briefly in the queue. Once a node is allocated, your prompt
changes to show the compute node name:

```text
[<cnetid>@midway3-0042 ~]$
```

You are now on a **compute node** with dedicated resources.


## Step 3: Set Up the Python Environment

Load the Python module and activate the course virtual environment:

```bash
module load python/3.11.9
source /project/finm32900/tdp-venv-p3119/bin/activate
```

Verify that Jupyter is available:

```bash
which jupyter-notebook
```

```{tip}
If you want to use your own conda environment instead, create one in
your home directory (e.g., `$HOME/envs/`) to avoid hitting the file count
limit on the shared project directory.
```


## Step 4: Get the Host IP and Start Jupyter

The compute node has an internal IP address that we'll need for port forwarding.
Get it and start Jupyter:

```bash
# Get the compute node's IP address
HOST_IP=$(/sbin/ip route get 8.8.8.8 | awk '{print $7;exit}')

# Pick a random port (to avoid collisions with other users)
PORT_NUM=$(shuf -i15001-30000 -n1)

# Print the connection info (you'll need this in Step 5)
echo "=========================================="
echo "  Host IP:  $HOST_IP"
echo "  Port:     $PORT_NUM"
echo "=========================================="

# Start Jupyter
jupyter-notebook --no-browser --ip=$HOST_IP --port=$PORT_NUM
```

Jupyter will print output like:

```text
[I 14:23:07.891 NotebookApp] Serving notebooks from local directory: /home/<cnetid>
[I 14:23:07.891 NotebookApp] Jupyter Notebook is running at:
[I 14:23:07.891 NotebookApp]   http://10.50.221.42:18273/?token=abc123def456...
```

```{admonition} Important: Note the connection details
:class: warning

Write down or copy:
- The **Host IP** (e.g., `10.50.221.42`)
- The **Port** (e.g., `18273`)
- The **token** from the URL

You will need all three in the next step. **Leave this terminal open**---Jupyter
is running in the foreground.
```


## Step 5: Set Up SSH Port Forwarding

Open a **new terminal window** on your laptop (do not close the Jupyter terminal).
Run:

```bash
ssh -N -f -L <PORT_NUM>:<HOST_IP>:<PORT_NUM> \
    <your-cnetid>@midway3.rcc.uchicago.edu
```

Replace `<PORT_NUM>` and `<HOST_IP>` with the values from Step 4. For example:

```bash
ssh -N -f -L 18273:10.50.221.42:18273 jdoe@midway3.rcc.uchicago.edu
```

Complete Duo MFA when prompted. The command runs in the background (no visible
output means success).

**What this does:**

```text
Your laptop (localhost:18273)
     │
     │  SSH tunnel (encrypted)
     ▼
Midway3 login node
     │
     │  Internal network
     ▼
Compute node (10.50.221.42:18273)  ← Jupyter is running here
```

Traffic to `localhost:18273` on your laptop is forwarded through the SSH tunnel
to the Jupyter server on the compute node.


## Step 6: Open Jupyter in Your Browser

On your laptop, open a web browser and go to:

```text
http://127.0.0.1:<PORT_NUM>/?token=<your-token>
```

For example:

```text
http://127.0.0.1:18273/?token=abc123def456...
```

You should see the Jupyter notebook interface. You are now running code on a
Midway3 compute node with 4 CPU cores and 16 GB of RAM, but interacting with
it through your laptop's browser.


## Step 7: Test It

Create a new Python 3 notebook and run:

```python
import os
import platform

print(f"Hostname: {platform.node()}")
print(f"CPUs available: {os.cpu_count()}")
print(f"User: {os.environ.get('USER', 'unknown')}")
```

You should see the compute node's hostname (e.g., `midway3-0042`), confirming
that your code is running on the cluster, not your laptop.

Try something more substantial:

```python
import numpy as np

# Generate a large random matrix (uses cluster RAM, not your laptop's)
X = np.random.randn(10_000, 10_000)
print(f"Matrix shape: {X.shape}")
print(f"Memory used: {X.nbytes / 1e9:.1f} GB")

# Matrix multiplication (uses cluster CPUs)
result = X @ X.T
print(f"Computation complete. Result shape: {result.shape}")
```


## Step 8: Clean Up

When you're done:

1. **Save your notebooks** (File → Save).
2. **Stop Jupyter**: Go to the terminal where Jupyter is running and press
   `Ctrl+C`, then confirm with `y`.
3. **Exit the compute node**: Type `exit` to release the node back to the
   cluster.
4. **Kill the SSH tunnel** (optional): Find and kill the background SSH process
   on your laptop:
   ```bash
   # Find the tunnel process
   ps aux | grep "ssh -N -f -L"

   # Kill it by PID
   kill <pid>
   ```

```{admonition} Always clean up
:class: warning

When you're done working, release the compute node by exiting the
`sinteractive` session. Idle nodes still consume your account's allocation
(SUs). Don't leave sessions running overnight.
```


## Troubleshooting

### "Port already in use"

If Jupyter says the port is already in use, pick a different random port:

```bash
PORT_NUM=$(shuf -i15001-30000 -n1)
echo "New port: $PORT_NUM"
jupyter-notebook --no-browser --ip=$HOST_IP --port=$PORT_NUM
```

### SSH tunnel fails or times out

- Make sure you completed Duo MFA for the tunnel connection.
- Check that the `HOST_IP` and `PORT_NUM` match exactly between Jupyter and
  the tunnel command.
- Try killing any existing tunnel processes and creating a new one.

### "Connection refused" in browser

- Verify Jupyter is still running in the first terminal.
- Verify the SSH tunnel is active: `ps aux | grep "ssh -N -f -L"`.
- Make sure the port numbers match in all three places: Jupyter startup, SSH
  tunnel command, and browser URL.

### Jupyter exits immediately

- Check that you loaded the Python module (`module load python/3.11.9`) and
  activated the virtual environment before starting Jupyter.
- Run `which jupyter-notebook` to confirm it's available.

### Queue wait is too long

- Try the `debug` QoS for a quick 15-minute session:
  ```bash
  sinteractive --account=finm32900 --qos=debug --time=00:15:00
  ```
- Check the queue: `squeue --partition=caslake` to see how busy the partition is.


(option-2-scode)=
## Option 2: VS Code in the Browser with `scode`

RCC provides a tool called `scode` that automates the entire pattern you just
did by hand: it submits a SLURM batch job, starts a VS Code server (web
version) on a compute node, and prints the exact SSH tunnel command to run.
Inside browser-based VS Code you can open `.ipynb` files and work with Jupyter
notebooks directly, along with a full editor and terminal.

The authoritative instructions are on the RCC website---treat that page as the
source of truth, since RCC updates the tool from time to time:

> [SCode: VS Code on Midway (RCC documentation)](https://docs.rcc.uchicago.edu/software/apps-and-envs/scode/main/)

In short, after SSHing into a login node, you launch a server with something
like:

```bash
scode serve-web -- --account finm32900 --time 01:00:00 --mem 16G
```

and `scode` prints an `ssh -L ...` tunnel command to run on your laptop and a
`http://localhost:8000/?tkn=...` URL to open in your browser. Note that this
is exactly the workflow from Steps 4--6 above---`scode` just scripts it for
you. Each launch generates a fresh port and token, so always copy the tunnel
command and URL from the most recent output.

### Quick fix: "channel N: open failed: connect failed: Connection refused"

At the time of writing, `scode` sometimes reports a broken address for the
compute node, and the tunnel fails even though the server is running fine.
The symptom: your SSH tunnel logs in successfully, but when you open the URL
in the browser, the SSH terminal fills with:

```text
channel 3: open failed: connect failed: Connection refused
```

**What's going on.** Look at the "Primary node" address that `scode` printed.
If it looks like `169.254.x.x`, that's the bug: addresses in the
`169.254.0.0/16` range are *link-local*---they are only meaningful on the
machine that owns them and are never routable between machines. `scode`
detected the wrong network interface on the compute node. Your tunnel reaches
the login node fine, but the login node cannot forward the connection to a
link-local address, so every browser request is refused. (A healthy launch
reports a cluster-internal address like `10.x.x.x`.)

**The fix.** Tunnel to the compute node's *hostname* instead of the reported
IP. Find the node name with `squeue -u $USER` (the `NODELIST` column, e.g.,
`midway3-0080`), then build the tunnel yourself, keeping the port from the
`scode` output:

```bash
ssh -L 8000:<node-name>:<port> <your-cnetid>@midway3.rcc.uchicago.edu
```

This works because SSH resolves the forwarding destination on the *login
node*, which knows the cluster-internal hostnames, and the VS Code server
listens on all of the compute node's interfaces. You can verify the fix from
the login node before even opening your browser:

```bash
nc -zv <node-name> <port>   # "succeeded" means the tunnel will work
```

```{admonition} Watch out for stale tunnels
:class: warning

If the tunnel command prints `bind [127.0.0.1]:8000: Address already in use`,
an earlier SSH session is still holding local port 8000 and **no forwarding
was set up**---the login succeeds anyway, which makes this easy to miss.
Pressing `Ctrl+C` inside an SSH session does not close it. Close your old SSH
windows (type `exit` in each), or simply pick a different local port, e.g.,
`ssh -L 9000:<node-name>:<port> ...` and browse to `http://localhost:9000/...`.
```

Also note: your campus VPN plays no role in this error. The VPN only affects
the laptop-to-login-node leg, which is already working by the time you see
"Connection refused"---the failure is on the login-node-to-compute-node leg,
entirely inside the cluster.


## Summary

You've now completed the full workflow for running Jupyter on an HPC cluster:

1. **SSH** into the cluster login node
2. **SLURM** (`sinteractive`) to get a compute node
3. **Jupyter** started on the compute node
4. **SSH port forwarding** to tunnel the connection to your laptop
5. **Browser** to interact with the notebook

This same pattern works for any web-based tool running on a remote machine, not
just Jupyter. Any time you need to access a service running on a port of a
remote machine, SSH port forwarding is the solution. RCC's `scode` tool
(Option 2 above) automates this exact pattern for VS Code---and now that you've
done it by hand, you can debug it when the automation misbehaves.
