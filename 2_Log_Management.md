# Log Management Commands in Linux

## Overview
Log management is essential for troubleshooting and maintaining system health.

## Essential Commands

### 1. tail - Monitor Log Files
```bash
# Monitor log file in real-time
tail -f /var/log/syslog

# Real-time Example: Watch multiple logs
tail -f /var/log/nginx/access.log /var/log/nginx/error.log

# Show last 100 lines
tail -n 100 /var/log/syslog
```

### 2. grep - Search Through Logs
```bash
# Search for errors
grep -i error /var/log/syslog

# Real-time Example: Monitor errors in real-time
tail -f /var/log/syslog | grep -i error

# Search multiple patterns
grep -E 'error|warning|critical' /var/log/syslog
```

### 3. journalctl - SystemD Journal
```bash
# View all logs
journalctl

# Real-time Example: Follow specific service
journalctl -u nginx -f

# View logs since last boot
journalctl -b
```

### 4. zcat/zgrep - Work with Compressed Logs
```bash
# Read compressed logs
zcat /var/log/syslog.2.gz

# Search in compressed logs
zgrep "error" /var/log/syslog*.gz
```

## Real-World Scenarios

### 1. Application Error Investigation
```bash
# Step 1: Check recent errors
tail -f /var/log/application.log | grep -i error

# Step 2: Search specific timeframe
journalctl --since "2 hours ago" -u application

# Step 3: Save findings to file
journalctl -u application --since today > app_errors.log
```

### 2. Security Audit
```bash
# Check authentication attempts
grep "Failed password" /var/log/auth.log

# Monitor SSH attempts in real-time
tail -f /var/log/auth.log | grep "sshd"

# Check sudo usage
grep "sudo" /var/log/auth.log
```

### 3. Web Server Analysis
```bash
# Monitor access patterns
tail -f /var/log/nginx/access.log

# Count HTTP status codes
cat /var/log/nginx/access.log | cut -d '"' -f3 | cut -d ' ' -f2 | sort | uniq -c
```



## Common Issues and Solutions

### 1. Log Rotation
```bash
# Problem: Logs filling disk
# Solution: Configure logrotate
cat > /etc/logrotate.d/application << EOF
/var/log/application/*.log {
    daily
    rotate 7
    compress
    delaycompress
    missingok
    notifempty
}
EOF
```

### 2. Large Log Files
```bash
# Problem: Need to analyze huge logs
# Solution: Use efficient tools
split -l 1000000 huge.log segment_
parallel "grep 'error' {} > {}.errors" ::: segment_*
```

## Best Practices

1. **Log Organization**
   - Use proper log levels
   - Implement log rotation
   - Maintain consistent formats

2. **Monitoring Strategy**
   - Set up automated monitoring
   - Configure alerts for critical errors
   - Regular log analysis

3. **Retention Policy**
   - Define retention periods
   - Implement compression
   - Archive important logs

4. **Security**
   - Protect log files
   - Monitor access attempts
   - Regular security audits

5. **Tools Integration**
   ```bash
   # ELK Stack basic setup
   # Filebeat configuration
   filebeat.inputs:
   - type: log
     paths:
       - /var/log/*.log
     fields:
       log_type: syslog
   
   output.elasticsearch:
     hosts: ["localhost:9200"]
   ```
