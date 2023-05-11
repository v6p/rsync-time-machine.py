# Rsync Time Machine 🕰️💾

Introducing `rsync-time-machine.py` - a Python port of the [`rsync-time-backup`](https://github.com/laurent22/rsync-time-backup) script, offering Time Machine-style backups using rsync. It creates incremental backups of files and directories to the destination of your choice. The backups are structured in a way that makes it easy to recover any file at any point in time. 🚀

It works on Linux, macOS, and Windows (via WSL or Cygwin). The main advantage over Time Machine is flexibility, as it can backup from/to any filesystem and works on any platform. You can also backup to a Truecrypt drive without any problem. 😃

`rsync-time-machine.py` is fully tested, has no external dependencies, is fully compatible with [`rsync-time-backup`](https://github.com/laurent22/rsync-time-backup), offers pretty terminal output, and is fully typed! 🎉

<details><summary><b><u>[ToC]</u></b> 📚</summary>

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->

- [:star2: Features](#star2-features)
- [:books: Usage](#books-usage)
- [:hammer_and_wrench: Installation](#hammer_and_wrench-installation)
- [:bulb: Examples](#bulb-examples)
- [:calendar: Backup Expiration Logic](#calendar-backup-expiration-logic)
- [:page_facing_up: Exclusion File](#page_facing_up-exclusion-file)
- [:lock: Built-in Lock](#lock-built-in-lock)
- [:gear: Rsync Options](#gear-rsync-options)
- [:no_entry_sign: No Automatic Backup Expiration](#no_entry_sign-no-automatic-backup-expiration)
- [:arrows_counterclockwise: How to Restore](#arrows_counterclockwise-how-to-restore)
- [:heart: Support and Contributions](#heart-support-and-contributions)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

</details>

## :star2: Features

* 📁 Each backup is in its own folder named after the current timestamp.
* 🔒 Backup to/from remote destinations over SSH.
* 🔗 Files that haven't changed from one backup to the next are hard-linked to the previous backup, saving space.
* ⚠️ Safety check - the backup will only happen if the destination has explicitly been marked as a backup destination.
* 🔄 Resume feature - if a backup has failed or was interrupted, the tool will resume from there on the next backup.
* 🚫 Exclude file - support for pattern-based exclusion via the `--exclude-from` rsync parameter.
* 🧹 Automatically purge old backups based on a configurable expiration strategy.
* 🔗 "latest" symlink that points to the latest successful backup.

## :books: Usage

To use `rsync-time-machine.py`, you'll need to provide source and destination paths, along with any desired options:

```bash
rsync-time-machine --help
```
Shows the help message:

<!-- CODE:BASH:START -->
<!-- echo '```bash' -->
<!-- rsync-time-machine --help -->
<!-- echo '```' -->
<!-- CODE:END -->

<!-- OUTPUT:START -->
<!-- ⚠️ This content is auto-generated by `markdown-code-runner`. -->
```bash
usage: rsync-time-machine [-h] [-p PORT] [-i ID_RSA] [--rsync-get-flags]
                          [--rsync-set-flags RSYNC_SET_FLAGS]
                          [--rsync-append-flags RSYNC_APPEND_FLAGS]
                          [--log-dir LOG_DIR] [--strategy STRATEGY]
                          [--no-auto-expire] [-v]
                          src_folder dest_folder [exclusion_file]

A script for creating and managing time-stamped backups using rsync.

positional arguments:
  src_folder            Source folder for backup. Format: [USER@HOST:]SOURCE
  dest_folder           Destination folder for backup. Format:
                        [USER@HOST:]DESTINATION
  exclusion_file        Path to the file containing exclude patterns.

options:
  -h, --help            show this help message and exit
  -p PORT, --port PORT  SSH port.
  -i ID_RSA, --id_rsa ID_RSA
                        Specify the private ssh key to use.
  --rsync-get-flags     Display the default rsync flags that are used for
                        backup. If using remote drive over SSH, --compress
                        will be added.
  --rsync-set-flags RSYNC_SET_FLAGS
                        Set the rsync flags that are going to be used for
                        backup.
  --rsync-append-flags RSYNC_APPEND_FLAGS
                        Append the rsync flags that are going to be used for
                        backup.
  --log-dir LOG_DIR     Set the log file directory. If this flag is set,
                        generated files will not be managed by the script - in
                        particular they will not be automatically deleted.
                        Default: $HOME/.rsync-time-backup
  --strategy STRATEGY   Set the expiration strategy. Default: "1:1 30:7
                        365:30" means after one day, keep one backup per day.
                        After 30 days, keep one backup every 7 days. After 365
                        days keep one backup every 30 days.
  --no-auto-expire      Disable automatically deleting backups when out of
                        space. Instead, an error is logged, and the backup is
                        aborted.
  -v, --verbose         Enable verbose output.
```

<!-- OUTPUT:END -->

Please refer to the original [`rsync-time-backup`](https://github.com/laurent22/rsync-time-backup) README for a list of options, as they have been preserved in the Python port.

## :hammer_and_wrench: Installation

To install `rsync-time-machine.py`, simply clone the repository:

```
git clone https://github.com/basnijholt/rsync-time-machine.py
```

## :bulb: Examples

* Backup the home folder to backup_drive:

```
./rsync_time_machine.py /home /mnt/backup_drive
```

* Backup with exclusion list:

```
./rsync_time_machine.py /home /mnt/backup_drive excluded_patterns.txt
```

For more examples and detailed usage instructions, please refer to the original [`rsync-time-backup`](https://github.com/laurent22/rsync-time-backup) README.

## :calendar: Backup Expiration Logic

Backup sets are automatically deleted following a simple expiration strategy defined with the `--strategy` flag. The default strategy is `1:1 30:7 365:30`. Please see the original README for a detailed explanation.

## :page_facing_up: Exclusion File

An optional exclude file can be provided as a third parameter, compatible with the `--exclude-from` parameter of rsync.

## :lock: Built-in Lock

The script is designed so that only one backup operation can be active for a given directory, avoiding conflicts.

## :gear: Rsync Options

To display, add, or remove rsync options, use the `--rsync-get-flags`, `--rsync-append-flags`, or `--rsync-set-flags` options.

## :no_entry_sign: No Automatic Backup Expiration

Use the `--no-auto-expire` flag to disable the default behavior of purging old backups when out of space.

## :arrows_counterclockwise: How to Restore

Restoring files from the backup is simple, as the script creates a backup in a regular directory. You can easily copy the files back to the original directory using a command like:

```
rsync -aP /path/to/last/backup/ /path/to/restore/to/
```

Consider using the `--dry-run` option to check what exactly is going to be copied. If you want to delete files that exist in the destination but not in the backup, use the `--delete` option. Be extra cautious when using this option to avoid data loss.

You can also restore files using any file explorer, including Finder on macOS or the command line.

## :heart: Support and Contributions

We appreciate your feedback and contributions! If you encounter any issues or have suggestions for improvements, please file an issue on the GitHub repository. We also welcome pull requests for bug fixes or new features.

Happy backing up! 💾🕰️🎉
