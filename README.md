# slurmtk

Lightweight Slurm tools for resource monitoring and remote development.

## Installation

Clone the repo and add the scripts to your `PATH`:

```bash
git clone https://github.com/Han-Cao/slurmtk.git
export PATH="$PWD/slurmtk:$PATH"
```

## Tools

| Tool | Description | Requirements |
| ---- | ----------- | ------------ |
| [savail](#savail) | Check available CPU/GPU and memory on mixed nodes | awk |
| [stunnel](#stunnel) | Submit a job that launches a VS Code remote tunnel on a node | [VS Code CLI](https://code.visualstudio.com/docs/configure/command-line) |

### savail

Check the number of idle nodes and available CPU/GPU/memory on mixed nodes. It queries `scontrol show node`, displays a per-partition idle/mixed node summary, and lists the top N mixed nodes sorted by available CPU/GPU.

**Usage:**

```bash
Usage: savail [-p partition[,partition...]] [-n topn]
  -p PARTITION   Filter by Slurm partition(s), comma-separated (default: all)
  -n TOPN        Number of top mixed nodes to display by available CPU (default: 10)
  -h             Display this help message
```

**Example:**

Check available nodes in the `cpu` and `gpu` partitions, showing the top 2 mixed nodes by available CPU/GPU:
```bash
savail -p cpu,gpu -n 2
```

Output:
```
Partition         Idle  Mixed
--------------- ------ ------
cpu                  3     26
gpu                  5      2

Top mixed CPU nodes by available CPU:
Partitions      NodeName   AvailCPU    FreeMem
--------------  ---------- -------- ----------
cpu             cpu41           180     528889
cpu             cpu38           168     632384

Top mixed GPU nodes by available GPU:
Partitions      NodeName   GPU Type    AvailGPU AvailCPU    FreeMem
--------------  ---------- ---------- --------- -------- ----------
gpu             gpu04      a100               3        8      70384
gpu             gpu10      a30                1       52     473409
```
---

### stunnel

Submit a job that launches a VS Code remote tunnel (`code tunnel`) on a node.

**Usage:**

```bash
Usage: stunnel [-A account] [-p partition] [-c cores] [-t time] [-l logdir] [-a provider]
  -A ACCOUNT     Charge job to specified account (default: '')
  -p PARTITION   Partition requested (default: '')
  -c CORES       Number of requested cpus (default: '8')
  -g GRES        Required generic resources (default: '')
  -t TIME        Time limit (default: '5-0')
  -l LOGDIR      Directory for log file (default: '$HOME')
  -a PROVIDER    VS code tunnel auth provider: microsoft or github (default: 'microsoft')
  -h             Display this help message
```

**Note**: On a new machine, you may need to first configure the tunnel service manually by running `code tunnel` and following the prompts.

**Example:**

Submit a tunnel job on the cpu partition with 4 cores

```bash
stunnel -A myaccount -p cpu -c 4 -t 1-0
```

Wait for VS Code to launch and print the tunnel authentication URL in the log file (`$HOME/tunnel.<timestamp>.log`). This should take < 2 minutes.

```
To sign in, use a web browser to open the page https://login.microsoft.com/device and enter the code XXXXXXX to authenticate.
```

**Configuration**:
The default configuration can be modified to fit your needs by editing the `stunnel` script directly.

```
# Default values         # Corresponding option
ACCOUNT=""               # sbatch -A
PARTITION=""             # sbatch -p
CORES=8                  # sbatch -c
GRES=""                  # sbatch --gres
TIME=5-0                 # sbatch -t

PROVIDER="microsoft"     # code tunnel --provider
LOGDIR="$HOME"           # srun -o
```

## Contributing

If you encounter any issues or have suggestions for improvement, please feel free to open an issue or submit a pull request.