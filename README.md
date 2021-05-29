Docker image for my personal [nextcloud](https://nextcloud.com/) server.

## Features
- automatic backup to s3 bucket
- automatic [postgres backups](https://github.com/eeshugerman/postgres-backup-s3)
- nginx reverse proxy
- s3 restore commands

## Install

+ clone this repo
+ create an [env file](#env_file)
+ build docker image
```bash
sudo docker-compose build
```

## Run

```bash
sudo docker-compose up db
```

goto [localhost:9080](http://localhost:9080) and complete the setup.
Pick a strong username and password, because the backups will be locked to it.
You'll be required to enter the same password during restoration.


## Backup

Make sure the container is [ruuning](#run)

Get the id of the container using

```bash
sudo docker ps
```

There you'll find an image similar named to `aniketprajapatime_pg_backup_s3`.
Copy its `container id`, eg 7bd976320ff8

```bash
sudo docker exec <container id> sh backup.sh 
```

The backup is encrypted using `GPG` with `POSTGRES_PASSWORD` as key.

## Restore

> This will remove all of your data, so make sure you [backup](#backup) before doing this.
> 
> Make sure you use the same env file i.e the same usernames and passwords, from the time you took your last backup.

Make sure you are running a new instance, and all the volumes are new.
If not, remove them using

```bash
sudo docker-compose down --remove-orphans --volumes
```

[Run](#run) the container. Use the same username and password you had at the time of taking the backup.

Run the restore script.

```bash
sudo docker exec <container id> sh restore.sh 
```

## Env File

Create a`.env` file at the project root, assigning values accordingly

- `AWS_ACCESS_KEY_ID` S3 IIM access key
- `AWS_SECRET_ACCESS_KEY` S3 IIM secret
- `S3_REGION` S3 bucket region
- `S3_BUCKET` name of S3 bucket
- `S3_PREFIX` path to store postgres backup at in S3 bucket.
- `POSTGRES_HOST` same as `PGHOST`. Read more [here](https://www.postgresql.org/docs/9.1/libpq-envars.html) 
- `POSTGRES_DB` same as `PGDATABASE`. Read more [here](https://www.postgresql.org/docs/9.1/libpq-envars.html) 
- `POSTGRES_USER` same as `PGUSER`. Read more [here](https://www.postgresql.org/docs/9.1/libpq-envars.html) 
- `POSTGRES_PASSWORD` same `PGPASSWORD`. Read more [here](https://www.postgresql.org/docs/9.1/libpq-envars.html) 
- `PG_BACKUP_SCHEDULE` schedule for postgres backup. Read more [here](https://pkg.go.dev/github.com/robfig/cron#hdr-Predefined_schedules). eg: @daily


*.env*
```env
AWS_ACCESS_KEY_ID=
AWS_SECRET_ACCESS_KEY=

S3_REGION=
S3_BUCKET=

POSTGRES_HOST=
POSTGRES_DB=
POSTGRES_USER=
POSTGRES_PASSWORD=

PG_BACKUP_SCHEDULE=
```
