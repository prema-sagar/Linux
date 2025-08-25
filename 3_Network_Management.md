# Network Management Commands in Linux

## Overview
Network management is crucial for maintaining connectivity and troubleshooting network issues.

## Essential Commands

### 1. netstat/ss - Network Statistics
```bash
# List all listening ports
netstat -tulpn
ss -tulpn

# Real-time Example: Monitor connections
watch -n 1 'netstat -tulpn'

# Check established connections
netstat -nat | grep ESTABLISHED
```

### 2. ping - Test Connectivity
```bash
# Basic connectivity test
ping google.com

# Real-time Example: Continuous monitoring with timestamp
ping -D google.com

# Specific number of pings
ping -c 4 192.168.1.1
```

### 3. traceroute - Trace Packet Route
```bash
# Trace route to destination
traceroute google.com

# Real-time Example: TCP traceroute
traceroute -T -p 443 github.com

# Show IP addresses only
traceroute -n google.com
```

### 4. dig/nslookup - DNS Query
```bash
# DNS lookup
dig google.com

# Real-time Example: Check DNS propagation
watch -n 30 'dig +short google.com'

# Reverse DNS lookup
dig -x 8.8.8.8
```

### 5. curl/wget - Transfer Data
```bash
# Test web server
curl -I https://example.com

# Real-time Example: Monitor web service
while true; do curl -s -w "%{http_code}\n" -o /dev/null http://localhost; sleep 1; done

# Download with progress
wget -P https://example.com/file.zip
```

## Real-World Scenarios

### 1. Web Service Troubleshooting
```bash
# Step 1: Check if service is listening
netstat -tulpn | grep 80

# Step 2: Test local connection
curl -v localhost:80

# Step 3: Check DNS resolution
dig +short myservice.com

# Step 4: Test SSL certificate
openssl s_client -connect myservice.com:443
```

### 2. Network Performance Analysis
```bash
# Step 1: Monitor bandwidth
iftop -i eth0

# Step 2: Check network errors
ifconfig eth0 | grep -i error

# Step 3: Monitor specific port traffic
tcpdump -i eth0 port 80
```

### 3. Load Balancer Health Check
```bash
#!/bin/bash
SERVERS=("10.0.0.1" "10.0.0.2" "10.0.0.3")
for server in "${SERVERS[@]}"; do
    curl -s -w "%{http_code}\n" -o /dev/null http://$server
done
```



## Common Issues and Solutions

### 1. DNS Issues
```bash
# Problem: DNS resolution failure
# Solution:
cat /etc/resolv.conf  # Check DNS configuration
systemctl restart systemd-resolved  # Restart DNS resolver
dig @8.8.8.8 google.com  # Test with Google DNS
```

### 2. Network Interface Problems
```bash
# Problem: Network interface down
# Solution:
ifconfig eth0 down
ifconfig eth0 up
ethtool eth0  # Check interface status
```

### 3. Connection Tracking
```bash
# Problem: Too many connections
# Solution:
sysctl net.netfilter.nf_conntrack_count  # Check current connections
sysctl -w net.netfilter.nf_conntrack_max=131072  # Increase limit
```

## Best Practices

1. **Network Security**
   - Regular security audits
   - Monitor unusual traffic
   - Keep firewall rules updated

2. **Performance Monitoring**
   - Monitor bandwidth usage
   - Track latency
   - Watch for network errors

3. **Documentation**
   - Network topology
   - IP address allocation
   - Service dependencies

4. **Backup Connectivity**
   ```bash
   # Example: Backup network check
   if ! ping -c 1 primary-isp; then
       ip route add default via backup-isp
   fi
   ```

5. **Regular Testing**
   ```bash
   # Automated network test
   #!/bin/bash
   HOSTS=(
       "database:3306"
       "web-server:80"
       "cache:6379"
   )
   for host in "${HOSTS[@]}"; do
       IFS=: read -r server port <<< "$host"
       nc -zv $server $port
   done
   ```
