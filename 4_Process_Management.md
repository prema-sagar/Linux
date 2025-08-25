# Process Management Commands in Linux

## Overview
Process management is essential for controlling applications and system resources.

## Essential Commands

### 1. ps - Process Status
```bash
# List all processes
ps aux

# Real-time Example: Monitor specific process
ps aux | grep nginx

# Show process tree
ps axjf
```

### 2. top/htop - Interactive Process Viewer
```bash
# Basic process monitoring
top

# Real-time Example: Monitor specific user's processes
top -u username

# Process tree view in htop
htop -t
```

### 3. kill - Terminate Processes
```bash
# Gracefully terminate process
kill -15 <PID>

# Real-time Example: Force kill unresponsive process
kill -9 <PID>

# Kill by process name
pkill process_name
```

### 4. nice/renice - Process Priority
```bash
# Start process with priority
nice -n 10 command

# Real-time Example: Change running process priority
renice +5 -p <PID>
```

### 5. systemctl - Service Control
```bash
# Check service status
systemctl status nginx

# Real-time Example: Monitor service
watch -n 1 'systemctl status nginx'
```

## Real-World Scenarios

### 1. Unresponsive Application
```bash
# Step 1: Find the process
ps aux | grep application_name

# Step 2: Check resource usage
top -p <PID>

# Step 3: Try graceful termination
kill -15 <PID>

# Step 4: Force kill if necessary
kill -9 <PID>
```

### 2. Service Management
```bash
# Restart service with monitoring
systemctl restart service_name
journalctl -fu service_name

# Check failed services
systemctl --failed
```

## Process Monitoring Scripts

### 1. Process Resource Monitor
```bash
#!/bin/bash
PROCESS=$1
while true; do
    echo "=== $(date) ==="
    ps -p $(pgrep $PROCESS) -o pid,ppid,%cpu,%mem,cmd
    sleep 5
done
```

### 2. Zombie Process Hunter
```bash
#!/bin/bash
# Find and report zombie processes
ps aux | awk '$8=="Z" {print $2}' | while read pid; do
    echo "Zombie process found: $pid"
    echo "Parent: $(ps -o ppid= -p $pid)"
done
```

### 3. Service Monitor
```bash
#!/bin/bash
SERVICES=("nginx" "mysql" "redis-server")
for service in "${SERVICES[@]}"; do
    if systemctl is-active --quiet $service; then
        echo "$service is running"
    else
        echo "$service is DOWN"
        systemctl restart $service
    fi
done
```

## Common Issues and Solutions

### 1. Process Won't Die
```bash
# Problem: Process won't terminate
# Solution:
kill -15 <PID>  # Try graceful shutdown
sleep 5
kill -9 <PID>   # Force kill if still running
```

### 2. High Memory Usage
```bash
# Problem: Process consuming too much memory
# Solution:
ps -o pid,user,%mem,command ax | sort -b -k3 -r  # Find memory hogs
systemctl restart memory-intensive-service
```

### 3. Too Many Processes
```bash
# Problem: Too many instances
# Solution:
pgrep process_name | wc -l  # Count instances
pkill -9 process_name      # Kill all instances
```

## Best Practices

1. **Process Monitoring**
   - Regular resource checks
   - Set up monitoring alerts
   - Keep track of normal patterns

2. **Service Management**
   ```bash
   # Proper service restart
   systemctl stop service
   sleep 2
   systemctl start service
   systemctl status service
   ```

3. **Resource Control**
   ```bash
   # Use cgroups for resource limits
   systemctl set-property service.service CPUQuota=50%
   systemctl set-property service.service MemoryLimit=1G
   ```

4. **Automation**
   ```bash
   # Automatic process recovery
   while true; do
       if ! pgrep process_name > /dev/null; then
           start_process
       fi
       sleep 60
   done
   ```

5. **Logging**
   ```bash
   # Process start/stop logging
   logger "Process $1 started with PID $(pgrep $1)"
   ```
