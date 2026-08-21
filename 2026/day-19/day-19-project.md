# Day 19 – Shell Scripting Project: Log Rotation, Backup & Crontab

## Task
Apply everything from Days 16–18 in real-world mini projects.

You will:
- Write a **log rotation** script
- Write a **server backup** script
- Schedule them with **crontab**

---

## Expected Output
- A markdown file: `day-19-project.md`
- All scripts you write during the tasks

---

## Challenge Tasks

### Task 1: Log Rotation Script
Create `log_rotate.sh` that:
1. Takes a log directory as an argument (e.g., `/var/log/myapp`)
2. Compresses `.log` files older than 7 days using `gzip`
3. Deletes `.gz` files older than 30 days
4. Prints how many files were compressed and deleted
5. Exits with an error if the directory doesn't exist

#!/bin/bash
# Tells the OS to run this script using bash

LOG_DIR=$1
# Takes the first command-line argument (the folder path) and stores it in LOG_DIR
# e.g. running ./log_rotate.sh /var/log/myapp sets LOG_DIR="/var/log/myapp"

if [ ! -d "$LOG_DIR" ]; then
    # -d checks if the path is a directory. The ! negates it, so this
    # block runs if LOG_DIR is NOT a valid directory
    echo "Error: Directory does not exist."
    exit 1
    # exit 1 stops the script immediately with a non-zero (failure) status
fi

# Count first, then act
compress_count=$(find "$LOG_DIR" -name "*.log" -mtime +7 | wc -l)
# find searches LOG_DIR for files ending in .log that are older than 7 days
# (-mtime +7 means "last modified more than 7 days ago")
# wc -l counts how many lines (i.e. how many files) find returned
# $(...) captures that count into the variable compress_count

delete_count=$(find "$LOG_DIR" -name "*.gz" -mtime +30 | wc -l)
# Same idea, but counts already-compressed .gz files older than 30 days
# these are the ones due for permanent deletion

# Do the actual work
find "$LOG_DIR" -name "*.log" -mtime +7 -exec gzip {} \;
# Finds the same .log files again, but this time -exec runs a command on each result
# gzip {} compresses each matched file; {} is replaced with the actual filename
# \; marks the end of the -exec command

find "$LOG_DIR" -name "*.gz" -mtime +30 -exec rm {} \;
# Finds .gz files older than 30 days and deletes each one with rm {}

echo "Compressed: $compress_count file(s)"
echo "Deleted: $delete_count file(s)"
# Prints the counts calculated earlier, giving a summary of what happened




---

### Task 2: Server Backup Script
Create `backup.sh` that:
1. Takes a source directory and backup destination as arguments
2. Creates a timestamped `.tar.gz` archive (e.g., `backup-2026-02-08.tar.gz`)
3. Verifies the archive was created successfully
4. Prints archive name and size
5. Deletes backups older than 14 days from the destination
6. Handles errors — exit if source doesn't exist


#!/bin/bash
# Tells the OS to run this script with bash

SOURCE=$1
DEST=$2
# $1 is the first argument (folder to back up), $2 is the second (where to save the backup)
# e.g. ./backup.sh /home/ubuntu/somefolder /home/ubuntu/backups
# sets SOURCE="/home/ubuntu/somefolder" and DEST="/home/ubuntu/backups"

if [ ! -d "$SOURCE" ]; then
    # -d checks the path is a directory, ! negates it
    # runs if SOURCE is NOT a valid directory
    echo "Error: Source does not exist."
    exit 1
    # stops the script, no point continuing without something to back up
fi

if [ ! -d "$DEST" ]; then
    # same check, this time on DEST
    # catches a missing destination folder before tar ever runs
    echo "Error: Destination does not exist."
    exit 1
    # stops the script here rather than letting tar fail with a messier error later
fi

DATE=$(date +%Y-%m-%d)
# formats today's date as e.g. 2026-08-21
# $(...) captures that output into the DATE variable

ARCHIVE="backup-$DATE.tar.gz"
# builds a filename using today's date, e.g. backup-2026-08-21.tar.gz
# running the script twice in one day overwrites the same file rather than duplicating

tar -czf "$DEST/$ARCHIVE" "$SOURCE"
# tar creates an archive
# -c create a new archive
# -z compress it with gzip
# -f filename, followed by the destination path and archive name
# "$SOURCE" is what gets archived (the whole folder, contents included)

echo "Archive created: $ARCHIVE"
ls -lh "$DEST/$ARCHIVE"
# confirms the archive exists and shows its size in human readable form (-h)

find "$DEST" -name "backup-*.tar.gz" -mtime +14 -exec rm {} \;
# finds backup files in DEST older than 14 days and deletes them
# -mtime +14 means "last modified more than 14 days ago"
# -exec rm {} \; runs rm on each matched file, {} replaced by the filename
# basic retention: keep two weeks of backups, clear anything older




---

### Task 3: Crontab
1. Read: `crontab -l` — what's currently scheduled?
2. Understand cron syntax:
   ```
   * * * * *  command
   │ │ │ │ │
   │ │ │ │ └── Day of week (0-7)
   │ │ │ └──── Month (1-12)
   │ │ └────── Day of month (1-31)
   │ └──────── Hour (0-23)
   └────────── Minute (0-59)
   ```
3. Write cron entries (in your markdown, don't apply if unsure) for:
   - Run `log_rotate.sh` every day at 2 AM
   - Run `backup.sh` every Sunday at 3 AM
   - Run a health check script every 5 minutes

---

### Task 4: Combine — Scheduled Maintenance Script
Create `maintenance.sh` that:
1. Calls your log rotation function
2. Calls your backup function
3. Logs all output to `/var/log/maintenance.log` with timestamps
4. Write the cron entry to run it daily at 1 AM

---

## Hints
- Compress old files: `find /path -name "*.log" -mtime +7 -exec gzip {} \;`
- Timestamp: `date +%Y-%m-%d`
- Tar: `tar -czf backup.tar.gz /source/dir`
- Cron edit: `crontab -e`
- Log with timestamp: `echo "$(date): message" >> logfile`

---

## Documentation

Create `day-19-project.md` with:
- Each script's code
- Sample outputs
- Cron entries you wrote
- What you learned (3 key points)

---

## Submission
1. Add your scripts and `day-19-project.md` to `2026/day-19/`
2. Commit and push to your fork

---
