# Android Backup

This utility uses **rsync** and **ssh** to synchronize an Android device to a directory on a backup machine.
This first version makes a number of restricting assumptions that might be removed in later versions:

* The Android device is rooted and a full backup is desired.
    Later versions may allow for syncing only the user partition and thus allow lifting the root requirement.
* The backup machine is the machine running this program.
    Since rsync is triggered from the Android device, there is potential the backup machine could in fact be a third machine despite rsync not supporting two remote hosts.
    This program does not currently support this, though.
* The Android device must allow root escalation to the user logging in via **ssh**.
   One way this can be achieved is using the [SuperSU][supersu] app on a rooted device to grant root automatically to another app, [SSHelper][sshelper], which can provide a suitable **ssh** and **rsync** for use with this program.
* The backup machine must accept connections on the specified port as root using key specified with `--mobile-key`.

## Dependencies

This program requires enhanced **getopt**, which is not provided with macOS.
This program will output a message if the correct version is not available.
Mac users can install **getopt** with [Homebrew](https://brew.sh/):

    brew install gnu-getopt 

## Arguments

The options can be placed in any order on the command line.

### Required

* `--mobile-host=HOST`  
    Hostname or IP address of the Android device.
    Hostnames are currently converted to IP addresses.
* `--mobile-key=FILE`  
    Path to SSH key on Android device.
    The corresponding public key must exist in the backup machine `authorized_hosts` file.
* `--backup-host=HOST`  
    Hostname or IP address of the backup machine.
    Hostnames will be converted to IP addresses for reliable connection from Android device.
* `--backup-dir=FOLDER`  
    Path to backup directory.
    Relative paths will be prefixed with the current working directory.

### Optional

* `--mobile-port=PORT`  
    Port to connect to on the Android device.
    This can be specified on a per-host basis in configuration file on the backup host.
    Default 22.
* `--backup-port=PORT`  
    Port to connect to on the backup host.
    This can be specified on a per-host basis in configuration file on the backup host.
    Default 22.
* `-n`, `--dry-run`  
    Cause rsync to show what would have been transferred.
* `-v`, `--verbose`  
    Cause android_backup to be verbose, outputting more informational messages.
* `-V`, `--version`  
    Print version number.
* `-h`, `--help`  
    Show this help.

## Example

    android_backup \
        --mobile-host='CHARLIE-PHONE' \
        --mobile-port 2222 \
        --mobile-key='/data/data/com.arachnoid.sshelper/home/.ssh/id_rsa' \
        --backup-host='CHARLIE-DESKTOP' \
        --backup-dir="/Volumes/Main/Backups/Phones/current" \
        |& tee -a ~/cron.log 2>&1

## Author

Charlie Gorichanaz ([@CNG](https://github.com/CNG)) wrote this program to get more shell scripting experience by expanding one of his cronjobs into a utility potentially useful to others.


[sshelper]: http://arachnoid.com/android/SSHelper/
[supersu]: http://www.supersu.com/download
