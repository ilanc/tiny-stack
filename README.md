# Tiny Stack

![thumbnail](https://github.com/Sh4yy/tiny-stack/assets/23535123/d33b50cd-ca17-4a39-9154-237442fc6c8a)

A simple and tiny stack for building web applications using [Astro](https://astro.build), [SQLite](https://www.sqlite.org/index.html), and [Litestream](https://litestream.io).

## Tutorial

[Here](https://logsnag.com/blog/the-tiny-stack) is a thorough tutorial on the Tiny Stack.

## Quick Start

1. Build the Docker image

```bash
docker build -t tiny-stack .
```

2. Set environment variables

Copy/paste the contents of `.env.example` into a new `.env` file and fill in the values, then run:

```bash
source .env
```

OR export individual variables:

```bash
# https://litestream.io/getting-started/
docker run -p 9000:9000 -p 9001:9001 minio/minio server /data --console-address ":9001"
xdg-open http://localhost:9001/ &
# Buckets > Create Bucket > Save: bucket name = mybkt
# export REPLICA_URL=https://<account_id>.r2.cloudflarestorage.com
# litestream replicate fruits.db s3://mybkt.localhost:9000/fruits.db
export REPLICA_URL=s3://mybkt.localhost:9000/fruits.db
export LITESTREAM_ACCESS_KEY_ID=minioadmin
export LITESTREAM_SECRET_ACCESS_KEY=minioadmin

# install litestream & sqllite in host
wget https://github.com/benbjohnson/litestream/releases/download/v0.3.13/litestream-v0.3.13-linux-amd64.deb
sudo dpkg -i litestream-v0.3.13-linux-amd64.deb
rm !$
sudo apt install sqlite3

# create fruits.db
sqlite3 fruits.db
CREATE TABLE fruits (name TEXT, color TEXT);
INSERT INTO fruits (name, color) VALUES ('apple', 'red');
INSERT INTO fruits (name, color) VALUES ('banana', 'yellow');

# replicate it to minio s3
litestream replicate fruits.db s3://mybkt.localhost:9000/fruits.db

docker run   -p 4321:4321   -e REPLICA_URL   -e LITESTREAM_ACCESS_KEY_ID   -e LITESTREAM_SECRET_ACCESS_KEY   -v $(pwd)/data:/data -it --entrypoint=/bin/sh   tiny-stack
```

3. Run the Docker image

```bash
docker run \
  -p 4321:4321 \
  -e REPLICA_URL \
  -e LITESTREAM_ACCESS_KEY_ID \
  -e LITESTREAM_SECRET_ACCESS_KEY \
  -v $(pwd)/data:/data \
  tiny-stack
```

The same command as a single line in case slashes are causing issues:

```bash
docker run -p 4321:4321 -e REPLICA_URL -e LITESTREAM_ACCESS_KEY_ID -e LITESTREAM_SECRET_ACCESS_KEY -v $(pwd)/data:/data tiny-stack
```

4. Head to http://localhost:4321
