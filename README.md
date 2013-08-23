# FatBack

FatBack is a simple backup system that utilizes basic system
tools including heavy reliance on rsync.
* Backup to disk over the network
* Creates gzipped tarballs of:
** Most recent images of backup target
** Incremental copies of changes from current
* Leverages rsync heavily for:
** Incremental transfer of data 
** SSH transport to encrypt data in transit
** Incremental backups of changes
** Exclusion of files/directories from backup
* Uses Wake On LAN to wake machines that may be sleeping
* Configurable retention period for old data (old deltas from current)

The data backed up consists of:
* Full backups named:
	host_-path-to-dir_FULL.tar.gz
* Incremental backups (retained for specified number of days) named:
	host_-path-to-dir_INC_YYYYMMDD.tar.gz

The backups are stored in a configurable location:
	${BACKUP_DIR}/jobname/host/

All backups are stored as simple gzipped tarballs making restoring files simple.  There is no automatic restore capability provided nor required.  I just kept it simple here.

# Usage

* master.conf sets defaults for all jobs and must be present.  I tried to heavily comment this file to explain all of the possible options.
* Create a backup job by:
** create jobname.conf to over ride any default settings form master.conf
** create jobname.sked for each host/dir you want to backup (see below for format)
** If you want to exclude files from a backup, create a jobname.hostname.exclude file
* run the job by calling:
	fatback <jobname>

NOTE: You must have ssh access with key based authentication setup between backup server and backup target.

# File Formats

## .conf

* Key/Value pairs declaring shell variables.

## .sked

Each line is a colon seperated list.

	host:directory to backup:number of days to keep incremental backups:exclude file:MAC address

## .exclude

Though my naming convention is jobname.hostname.exclude, you can name the files whatever you want since you can specify the file in the .sked file.  This is simply a rsync exclude file and uses the syntax for rsync

# BUGS

There are probably a number (and probably some feature I never implemented).

The biggest "issue" is the need to decompress the most recent full backup for each backup run.  This is a bit ineffeceint, but hasn't really been a problem in practice.