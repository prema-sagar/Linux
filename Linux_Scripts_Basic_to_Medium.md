# Linux Scripts: Basic to Medium Level

## Basic Scripts

### 1. System Information Script
```bash
#!/bin/bash
# Get basic system information
echo "=== System Information ==="
echo "Hostname: $(hostname)"
echo "Kernel: $(uname -r)"
echo "CPU: $(grep 'model name' /proc/cpuinfo | head -1 | cut -d':' -f2)"
echo "Memory: $(free -h | grep Mem | awk '{print $2}')"
echo "Disk Space: $(df -h / | tail -1 | awk '{print $4}') free"
```

### 2. File Cleanup Script
```bash
#!/bin/bash
# Delete files older than 7 days in a directory
DIR="/path/to/directory"
DAYS=7

echo "Cleaning files older than $DAYS days in $DIR"
find "$DIR" -type f -mtime +$DAYS -exec rm {} \;
echo "Cleanup complete"
```

### 3. Service Status Checker
```bash
#!/bin/bash
# Check if important services are running
SERVICES=("ssh" "nginx" "mysql")

for service in "${SERVICES[@]}"; do
    if systemctl is-active --quiet $service; then
        echo "✓ $service is running"
    else
        echo "✗ $service is stopped"
    fi
done
```

## Intermediate Scripts

### 1. Log Monitor with Email Alert
```bash
#!/bin/bash
# Monitor log file for errors and send email alerts
LOG_FILE="/var/log/syslog"
EMAIL="admin@example.com"
PATTERN="error|failed|critical"

tail -fn0 "$LOG_FILE" | while read line; do
    if echo "$line" | grep -iE "$PATTERN" > /dev/null; then
        echo "$line" | mail -s "Log Alert" "$EMAIL"
    fi
done
```

### 2. Disk Space Monitor
```bash
#!/bin/bash
# Monitor disk space and send alerts
THRESHOLD=90
EMAIL="admin@example.com"

df -h | grep '^/dev/' | while read line; do
    usage=$(echo $line | awk '{print $5}' | sed 's/%//')
    mount=$(echo $line | awk '{print $6}')
    
    if [ $usage -gt $THRESHOLD ]; then
        echo "Warning: $mount is $usage% full" | \
        mail -s "Disk Space Alert" "$EMAIL"
    fi
done
```

### 3. Backup Script with Rotation
```bash
#!/bin/bash
# Backup important directories with rotation
SOURCE_DIR="/home/user/important"
BACKUP_DIR="/backup"
MAX_BACKUPS=5

# Create backup
DATE=$(date +%Y%m%d_%H%M%S)
tar czf "$BACKUP_DIR/backup_$DATE.tar.gz" "$SOURCE_DIR"

# Rotate old backups
cd "$BACKUP_DIR"
ls -t backup_*.tar.gz | tail -n +$((MAX_BACKUPS + 1)) | xargs -r rm
```

## Medium Scripts

### 1. Website Availability Monitor
```bash
#!/bin/bash
# Monitor website availability and response time
URLS=("https://example.com" "https://api.example.com")
LOG_FILE="/var/log/website_monitor.log"

check_url() {
    url=$1
    start_time=$(date +%s.%N)
    http_code=$(curl -o /dev/null -s -w "%{http_code}" "$url")
    end_time=$(date +%s.%N)
    response_time=$(echo "$end_time - $start_time" | bc)
    
    echo "$(date): $url - HTTP $http_code - ${response_time}s" >> "$LOG_FILE"
    
    if [ "$http_code" != "200" ]; then
        notify_admin "$url is down (HTTP $http_code)"
    fi
}

for url in "${URLS[@]}"; do
    check_url "$url"
done
```

### 2. System Resource Monitor
```bash
#!/bin/bash
# Monitor system resources and create reports
LOG_DIR="/var/log/sysmon"
mkdir -p "$LOG_DIR"

while true; do
    # CPU usage
    top -bn1 | grep "Cpu(s)" > "$LOG_DIR/cpu.log"
    
    # Memory usage
    free -m > "$LOG_DIR/memory.log"
    
    # Disk I/O
    iostat > "$LOG_DIR/io.log"
    
    # Network connections
    netstat -an | grep ESTABLISHED > "$LOG_DIR/network.log"
    
    # Generate summary
    echo "=== System Status Report ===" > "$LOG_DIR/summary.txt"
    echo "Time: $(date)" >> "$LOG_DIR/summary.txt"
    echo "Load Average: $(uptime | awk -F'load average:' '{print $2}')" >> "$LOG_DIR/summary.txt"
    echo "Memory Free: $(free -h | grep Mem | awk '{print $4}')" >> "$LOG_DIR/summary.txt"
    
    sleep 300  # Run every 5 minutes
done
```

### 3. Security Audit Script
```bash
#!/bin/bash
# Basic security audit script
REPORT_FILE="/var/log/security_audit.log"

echo "=== Security Audit Report ===" > "$REPORT_FILE"
date >> "$REPORT_FILE"

# Check failed login attempts
echo -e "\nFailed Login Attempts:" >> "$REPORT_FILE"
grep "Failed password" /var/log/auth.log | tail -5 >> "$REPORT_FILE"

# Check listening ports
echo -e "\nOpen Ports:" >> "$REPORT_FILE"
netstat -tuln >> "$REPORT_FILE"

# Check disk usage
echo -e "\nDisk Usage:" >> "$REPORT_FILE"
df -h >> "$REPORT_FILE"

# Check large files
echo -e "\nLarge Files (>100MB):" >> "$REPORT_FILE"
find / -type f -size +100M -exec ls -lh {} \; 2>/dev/null >> "$REPORT_FILE"

# Check system updates
echo -e "\nAvailable Updates:" >> "$REPORT_FILE"
apt list --upgradable 2>/dev/null >> "$REPORT_FILE"
```

These scripts are arranged in order of complexity, from basic system tasks to more sophisticated monitoring and maintenance operations. Each script includes comments explaining its purpose and can be customized based on specific needs.

Remember to:
1. Change email addresses, paths, and thresholds as needed
2. Make scripts executable (`chmod +x script.sh`)
3. Test in a safe environment first
4. Add error handling for production use
5. Consider logging and alerting requirements
