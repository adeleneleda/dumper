# dumper
A handy DB dumper and restorer

## Overview
Securing backups of databases is always one of the primary concerns especially in our data-centric world today where data lost may also mean millions of money lost.

Doing regular backups is only a part of the solution because storing them safely is also as important. 

__Dumper__ is a tool written in Python that dumps your PostgreSQL database and uploads it to your S3 bucket. Restoring is as easy. It retrieves your dump from your S3 bucket and restores it to your specified database. With it you can also set how often do you want to make uplaoded backups via cron jobs and also how often do you want your database to be transferred to the AWS Glacier.


## Prerequisites
You'll be needing `boto` and `python-crontab` to run __Dumper__. To install, you may just run:
  
     sudo pip install -r requirements.txt

## Sample Usage

### 1) Backup database and Upload to S3
Produce a PostgreSQL dump and upload it to the specified S3 bucket. Backups written at /tmp/dumper-backups/upload.

     ./dumper -a BACKUP -d db_name  -b bucket_name
     
Required parameters:

* -a BACKUP
* -d (database-name)
* -b (bucket)
 


     
### 2) Restore database from S3 File
Downloads dump file from S3 bucket, creates the specified database and restores it.

     ./dumper -a RESTORE -d target_db_name -f s3_dump_filename  -b bucket_name
     
     
Required parameters:

* -a BACKUP
* -d (target-database-name)
* -f (s3-dump-filename)
* -b (bucket)

### 3) List all available files in S3
View all available files in S3 bucket.

     ./dumper -a FILES
     
Required parameters:

* -a FILES

### 4) Set Cron jobs for database backup
Set hourly cron job schedule to backup specified database and upload to given S3 bucket.

     ./dumper -a CRON -d db_name -b atp-backups -c 12
     
Required parameters:

* -a CRON
* -d (db-name-to-be-backed-up)
* -b (bucket-name)
* -c (cron-schedule-in-hours)

*Note: Setup the following AWS credentials as Environment Variables

* ATP_AWS_ACCESS_KEY
* ATP_AWS_SECRET_ACCESS_KEY



### 5) View Cron jobs for database backup
View scheduled cron jobs for 

     ./dumper -a CRON -d sparks2 -b atp-backups
     
Required parameters:

* -a CRON
* -d (db-name-to-be-backed-up)
* -b (bucket-name)


### 6) Set S3 bucket lifecycle
Sets lifecycle of specified S3 bucket. Given your specified number of days, files under the given bucket will be transferred to the Glacier.

  # Set Glacier transfer for files more than 30 days
  ./dumper -a LIFECYCLE -b 'atp-backups' -e 30

### 7) View S3 bucket lifecycle
View glacier lifecycle for specified bucket

  ./dumper -a LIFECYCLE -b 'atp-backups'
  
  
  
## AWS Credentials
By default, Dumper looks for your AWS credentials as the values of the following keys in your environment variables:

* ATP_AWS_ACCESS_KEY
* ATP_AWS_SECRET_ACCESS_KEY

If it is not set or you want to override the ones in your environment variable, specify it when using the dumper command.

     ./dumper -a RESTORE -d target_db_name -f s3_dump_filename  -b bucket_name --aws_key_id=key_id --aws_secret_key=secret_key
     


## Optional Parameters


* -u (PostgreSQL user; default: postgres)
* -p (PostgreSQL password; default: '')
* -t (Destination Path  for File)



## Notes

Storing in the AWS Glacier is cheaper but is not recommended for smaller sized files (< 256 KB) as it may become more expensive. Only store files in the Glacier if they are already for a long term safe keeping as it may take up to 4 hours to retrieve an item to your S3.
