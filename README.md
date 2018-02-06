# mongodb-backup

[![Backup](https://hub.docker.com/r/tsangste/mongodb-backup/)](https://hub.docker.com/r/tsangste/mongodb-backup/)

This image runs mongodump to backup data using cronjob to folder `/backup`

## Usage:

    docker run -d \
        --env MONGODB_HOST=mongodb.host \
        --env MONGODB_PORT=27017 \
        --env MONGODB_USER=admin \
        --env MONGODB_PASS=password \
        --volume host.folder:/backup
        tsangste/mongodb-backup

Moreover, if you link `tsangste/mongodb-backup` to a mongodb container(e.g. official mongodb mongo:latest) with an alias named mongodb, this image will try to auto load the `host`, `port`, `user`, `pass` if possible.

    docker run -d -p 27017:27017 -p 28017:28017 -e MONGODB_PASS="mypass" --name mongodb mongo:latest
    docker run -d --link mongodb:mongodb -v host.folder:/backup --name mongodb-backup tsangste/mongodb-backup

## Parameters

    MONGODB_HOST    the host/ip of your mongodb database
    MONGODB_PORT    the port number of your mongodb database
    MONGODB_USER    the username of your mongodb database. If MONGODB_USER is empty while MONGODB_PASS is not, the image will use admin as the default username
    MONGODB_PASS    the password of your mongodb database
    MONGODB_DB      the database name to dump. If not specified, it will dump all the databases
    EXTRA_OPTS      the extra options to pass to mongodump command
    CRON_TIME       the interval of cron job to run mongodump. `0 0 * * *` by default, which is every day at 00:00
    MAX_BACKUPS     the number of backups to keep. When reaching the limit, the old backup will be discarded. No limit, by default
    INIT_BACKUP     if set, create a backup when the container launched
    USE_GZIP        if set, sets dump and restore to use gzip
    USE_ARCHIVE     if set, dump and restore with a single file format and can be used with gzip

## Restore from a backup

See the list of backups, you can run:

    docker exec mongodb-backup ls /backup

To restore database from a certain backup, simply run:

    docker exec mongodb-backup /restore.sh 2015.08.06.171901
