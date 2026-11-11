# Omni Debug (`debug`)

# About

**`debug`** is a lightweight operating system debugger and system inspection tool for the [Omni Shell Framework](../omni/README.md). Written in pure Bash on top of Linux `/proc` and `/var/log`, it provides fast process querying, filtering, sorting, and termination by memory or CPU consumption, alongside kernel/system log tailing and hardware resource inspection.

## Key Features

- **Zero-Dependency `/proc` Process Inspector**: Reads `/proc/<pid>/stat`, `/proc/<pid>/status`, `/proc/<pid>/comm`, and `/proc/uptime` directly to calculate memory (`VmRSS`) and CPU usage percentages.
- **Threshold Filtering & Sorting**: Filter processes by memory (`K`, `M`, `G` suffixes) or CPU percentage (`%`), automatically sort results by memory or CPU, and cap results with a row limit.
- **Script-Friendly PID Extraction & Batch Kill**: Retrieve raw PIDs with `debug process get` or terminate processes by PID, command name, or resource filter with `debug process kill`.
- **Log & System Inspection**: Tail system logs (`/var/log/syslog`, `/var/log/messages`, or `/var/log/kern.log`) and inspect uptime, load averages, `/proc/meminfo`, and `/proc/cpuinfo`.

---

# Install

## Via the Official `omni` Provider

```bash
omni providers install omni
omni tools use debug --provider omni
```

## From a Local Directory

```bash
omni tools use -p /path/to/omni/debug
```

## Directly from GitHub

```bash
omni tools use -u https://github.com/omnishell/debug.git
```

---

# Usage

```bash
debug <process|log|system> [subcommand] [options]
```

## Process Management (`debug process`)

### List Processes (`debug process ls`)

Display a formatted table (`PID`, `COMMAND`, `MEM`, `CPU`) of running processes:

```bash
debug process ls [-m|--mem [<threshold>]] [-c|--cpu [<threshold>]] [-l|--limit|--l <N>]
```

- `-m`, `--mem [<threshold>]`: Sort by resident memory descending. If `<threshold>` is provided (supports `K`, `M`, `G` suffixes; defaults to KB), only processes using at least `<threshold>` memory are shown.
- `-c`, `--cpu [<threshold>]`: Sort by CPU usage descending. If `<threshold>` is provided (e.g., `5` or `5.0%`), only processes using at least `<threshold>%` CPU are shown.
- `-l`, `--limit`, `--l <N>`: Limit output to the top `<N>` matching processes.

**Examples:**

```bash
# List all running processes
debug process ls

# List the top 10 highest memory-consuming processes
debug process ls --mem --limit 10

# List processes consuming at least 200 MB of RAM
debug process ls --mem 200M

# List top 5 processes using more than 10% CPU
debug process ls --cpu 10% -l 5
```

### Get Matching PIDs (`debug process get`)

Output only the matching process IDs (one per line), ideal for piping or scripting:

```bash
debug process get [-m|--mem [<threshold>]] [-c|--cpu [<threshold>]] [-l|--limit|--l <N>]
```

**Example:**

```bash
# Get PIDs of processes using more than 1 GB of memory
debug process get -m 1G
```

### Kill Processes (`debug process kill`)

Forcefully terminate (`kill -9`) processes by PID, exact command name (`/proc/<pid>/comm`), or resource filters:

```bash
# Kill a specific PID
debug process kill 12345

# Kill all processes matching a command name
debug process kill firefox

# Kill processes matching memory/CPU thresholds
debug process kill --mem 2G
debug process kill --cpu 80% --limit 1
```

---

## System Logs (`debug log`)

Display the last `<N>` lines (default: `50`) from `/var/log/syslog`, `/var/log/messages`, or `/var/log/kern.log` (automatically selecting the first readable log file):

```bash
# Show the last 50 log lines
debug log
debug log ls

# Show the last 200 log lines
debug log ls --limit 200
debug log -l 200
```

---

## System & Hardware Metrics (`debug system`)

Inspect system uptime, load average, memory statistics, or processor details:

```bash
# Show system uptime (seconds) and load average (/proc/loadavg)
debug system
debug system info

# Show detailed memory statistics (/proc/meminfo)
debug system mem

# Show detailed processor specifications (/proc/cpuinfo)
debug system cpu
```
