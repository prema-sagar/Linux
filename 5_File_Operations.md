# File Operations Commands in Linux

## Overview
File operations are fundamental for managing files and directories in Linux systems.

## Essential Commands

### 1. ls - List Files and Directories
```bash
# List all files with details
ls -la

# Real-time Example: Monitor directory changes
watch -n 1 'ls -la /path/to/dir'

# List files by size
ls -lSh
```

### 2. cp - Copy Files/Directories
```bash
# Copy with progress
cp -av source destination

# Real-time Example: Backup with timestamp
cp -r directory directory_$(date +%Y%m%d)

# Preserve attributes
cp -p source destination
```

### 3. mv - Move/Rename Files
```bash
# Move files safely
mv -i source destination

# Real-time Example: Organize files by extension
for f in *.txt; do mv "$f" text_files/; done

# Rename with pattern
rename 's/\.txt$/\.doc/' *.txt
```

### 4. find - Search Files
```bash
# Find by name
find /path -name "pattern"

# Real-time Example: Find and process large files
find / -size +100M -exec ls -lh {} \;

# Find modified files
find . -mtime -7 # Files modified in last 7 days
```

### 5. rsync - Synchronize Files
```bash
# Sync directories
rsync -avz source/ destination/

# Real-time Example: Backup with progress
rsync -avz --progress /source/ /backup/

# Remote sync
rsync -avz -e ssh source/ user@remote:/destination/
```

## Real-World Scenarios

### 1. Automated Backup System
```bash
#!/bin/bash
# Daily backup script
SOURCE_DIR="/var/www"
BACKUP_DIR="/backup"
DATE=$(date +%Y%m%d)

# Create backup
rsync -avz --delete \
    --backup --backup-dir=$BACKUP_DIR/incremental/$DATE \
    $SOURCE_DIR/ $BACKUP_DIR/current/

# Compress old backups
find $BACKUP_DIR/incremental -mtime +7 -exec tar czf {}.tar.gz {} \; -exec rm -rf {} \;
```

### 2. File Organization Script
```bash
#!/bin/bash
# Organize files by extension
BASEDIR="$1"

# Create directories for each type
mkdir -p "$BASEDIR"/{images,documents,videos,archives}

# Move files to appropriate directories
find "$BASEDIR" -maxdepth 1 -type f | while read file; do
    case $(file -b --mime-type "$file") in
        image/*) mv "$file" "$BASEDIR/images/" ;;
        video/*) mv "$file" "$BASEDIR/videos/" ;;
        application/pdf|text/*) mv "$file" "$BASEDIR/documents/" ;;
        application/zip|application/x-rar) mv "$file" "$BASEDIR/archives/" ;;
    esac
done
```

### 3. File Monitoring System
```bash
#!/bin/bash
# Monitor file changes
WATCH_DIR="/important/directory"
inotifywait -m -r -e modify,create,delete $WATCH_DIR | while read path action file; do
    echo "$(date): $action $path$file"
    logger "File change detected: $action on $path$file"
done
```

## Common Issues and Solutions

### 1. Disk Space Issues
```bash
# Problem: Large files filling disk
# Solution:
find / -size +100M -exec ls -lh {} \;
du -sh /* | sort -hr
```

### 2. File Permission Issues
```bash
# Problem: Permission denied errors
# Solution:
find . -type f -exec chmod 644 {} \;
find . -type d -exec chmod 755 {} \;
```

### 3. Synchronization Issues
```bash
# Problem: Files not syncing
# Solution:
rsync -avz --delete --dry-run source/ destination/  # Test first
rsync -avz --delete source/ destination/            # Actually sync
```



## Best Practices

1. **Backup Before Operations**
```bash
# Create backup before mass operations
cp -r directory directory.bak
```

2. **Use Safe Options**
```bash
# Use interactive mode for dangerous operations
rm -i
mv -i
cp -i
```

3. **Check Commands First**
```bash
# Test commands with echo
for f in *.txt; do echo mv "$f" "new_$f"; done
```

4. **Proper Permissions**
```bash
# Set secure default permissions
umask 022
chmod -R u=rwX,g=rX,o= directory
```

5. **Error Handling**
```bash
#!/bin/bash
# Handle errors in file operations
set -e
trap 'echo "Error on line $LINENO"' ERR

cp file1 file2 || {
    echo "Copy failed"
    exit 1
}
```

## Monitoring and Maintenance

1. **Regular Cleanup**
```bash
# Clean old files
find /tmp -type f -mtime +30 -delete
```

2. **Space Monitoring**
```bash
#!/bin/bash
# Monitor directory sizes
while true; do
    du -sh /var/log/* > size_log.txt
    sleep 3600
done
```

3. **Access Monitoring**
```bash
# Monitor file access patterns
auditctl -w /path/to/file -p warx
ausearch -f /path/to/file
```
