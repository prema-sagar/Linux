# System Monitoring Commands in Linux

## Overview
System monitoring is crucial for DevOps engineers to maintain server health and performance.

## Essential Commands

### 1. top - System Activity Monitor
```bash
# Basic usage
top

# Real-time Example: Monitor specific process
top -p $(pgrep nginx)

# Real-time Example: Monitor specific user's processes
top -u username

# Sort processes by memory usage
top -o %MEM
```

### 2. htop - Interactive Process Viewer
```bash
# Basic usage
htop

# Real-time Example: Filter processes by name
htop --filter="nginx"

# Show processes as tree
htop -t
```

### 3. vmstat - Virtual Memory Statistics
```bash
# Show memory stats every 2 seconds
vmstat 2

# Real-time Example: Monitor system during high load
vmstat 1 100  # Monitor for 100 seconds with 1-second intervals
```

### 4. free - Memory Usage
```bash
# Show memory in human-readable format
free -h

# Real-time Example: Watch memory changes
watch -n 1 'free -h'

# Show memory in megabytes
free -m
```

### 5. uptime - System Load Average
```bash
# Check system uptime and load
uptime

# Real-time Example: Monitor load continuously
watch -n 5 uptime
```

## Real-World Scenarios

### 1. High CPU Usage Investigation
```bash
# Step 1: Check overall CPU usage
top

# Step 2: Find specific process causing high CPU
ps aux | sort -nrk 3,3 | head -n 5

# Step 3: Get thread-level details
top -H -p <PID>
```

### 2. Memory Leak Investigation
```bash
# Step 1: Check memory usage
free -h

# Step 2: Find processes using most memory
ps aux | sort -nrk 4,4 | head -n 5

# Step 3: Monitor specific process memory
watch -n 1 'pmap -x <PID> | tail -n 1'
```

### 3. Disk I/O Monitoring
```bash
# Check disk I/O statistics
iostat -xz 1

# Monitor specific disk
iostat -xz 1 /dev/sda
```

### 4. System Load Analysis
```bash
# Check current load
uptime

# Detailed CPU info
mpstat 1 5

# Process tree with resources
ps auxf
```

## Common Issues and Solutions

### 1. High CPU Usage
```bash
# Problem: CPU at 100%
# Solution:
top -b -n 1 | head -n 20  # Identify top CPU consumers
kill -15 <PID>            # Gracefully terminate process
renice +10 <PID>          # Lower process priority
```

### 2. Memory Issues
```bash
# Problem: Low memory
# Solution:
free -h                   # Check memory status
killall -9 chrome        # Kill memory-hungry processes
echo 1 > /proc/sys/vm/drop_caches  # Clear cache (as root)
```

### 3. Zombie Processes
```bash
# Problem: Zombie processes
# Solution:
ps aux | grep 'Z'        # Find zombie processes
kill -9 <PPID>           # Kill parent of zombie
```



