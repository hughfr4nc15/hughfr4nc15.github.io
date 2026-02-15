---
title: AdGuard Home Backup
date: 2026-02-15 22:30:00
categories: [Homelab, AdGuard Home]
tags: [homelab, linux, adguard home, backup, tutorial]     # TAG names should always be lowercase
---

This is a simple shell script I use to automate the backup of AdGuard Home configuration.

```bash
#!/bin/bash

# Crontab entry:
# Run AdGuard Backup
# 0 3 * * * /mnt/backup/AdGuard-Backup.sh

# ---------------
# AdGuard Backup
# ---------------

# Configuration
SOURCE_DIR="/opt/AdGuardHome"
BACKUP_DIR="/mnt/backup/AdGuard-Backup"
LOG_FILE="$BACKUP_DIR/AdGuard-Backup.log"
DATE_FORMAT="+%Y%m%d"
RETENTION_DAYS=7
LOG_RETENTION_DAYS=30

# Create necessary directories if they don't exist
mkdir -p "$BACKUP_DIR"

# Create the backup file
BACKUP_FILE="$BACKUP_DIR/AdGuardHome-$(date "$DATE_FORMAT").tar.gz"
tar -czf "$BACKUP_FILE" -C "$SOURCE_DIR" . >> "$LOG_FILE" 2>&1

# Clean up old backups
find "$BACKUP_DIR" -maxdepth 1 -type f -name "AdGuardHome-*.tar.gz" -mtime +$RETENTION_DAYS -exec rm -f {} \; >> "$LOG_FILE" 2>&1

# Clean up old log entries (keep only the last 30 days)
awk -v d="$(date -d "$LOG_RETENTION_DAYS days ago" "$DATE_FORMAT")" '$1 >= d' "$LOG_FILE" > "$LOG_FILE.tmp" && mv "$LOG_FILE.tmp" "$LOG_FILE"

# Log the completion
echo "$(date '+%Y-%m-%d %H:%M:%S') - Backup Completed: $BACKUP_FILE" >> "$LOG_FILE"
```

---
## 📦 Installation

### Prerequisites

- `bash`
- `tar`
- `gzip`
- AdGuard Home installed and configured

### Steps

* Download the script:

```bash
wget https://raw.githubusercontent.com/hughfr4nc15/AdGuard-Home-Backup/main/AdGuard-Backup.sh
```

* Make the script executable:

```bash
chmod +x AdGuard-Backup.sh
```

## 💻 Usage

### Backup

To create a backup, run the script:

```bash
./AdGuard-Backup.sh
```

This will create a compressed archive of your AdGuard Home configuration directory in the specified backup directory.

### Restore

To restore from a backup, extract the backup file to AdGuard Home directory.

**Important:** Make sure AdGuard Home is stopped before restoring a backup to avoid data corruption.

## ⚙️ Configuration

The script can be configured by modifying the following variables within the script:

```bash
# Path to the AdGuard Home configuration directory
SOURCE_DIR="/opt/AdGuardHome"

# Directory to store backups
BACKUP_DIR="/mnt/backup/AdGuard-Backup"
```

You can edit these variables directly in the `AdGuard-Backup.sh` file.