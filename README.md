## Docker ELK + NGINX stack
Run (Elasticseach, Logstash, Kibana, Nginx) stack with Docker and Docker-compose.

Based on the official images:
 - elasticsearch
 - logstash 
 -  kibana
 - nginx

# Requirements

### Setup

1. Install Docker 
2. Install docker-compose version >=1.6 
3. Install apache2-utils

Increase max_map_count on your host (Linux)

### You need to increase max_map_count on your Docker host:

```$ sudo sysctl -w vm.max_map_count=262144```

# Usage

```$ docker-compose up -d```

By default, the stack exposes the following ports:

 - 5000: Logstash TCP input. 
 - 9200: Elasticsearch HTTP 
 - 9300: Elasticsearch TCP transport 
 - 5601: Kibana 
 - 80: Nginx

# Configuration

### Kibana configuration

The Kibana default configuration is stored in ```kibana/config/kibana.yml```.

### Logstash configuration

The logstash configuration is stored in ```logstash/config/logstash.conf```.

### Elasticsearch configuration

The Elasticsearch container is using the shipped configuration and it is not exposed by default.

If you want to override the default configuration, create a file elasticsearch/config/elasticsearch.yml and add your configuration in it.

### Logstash configuration

The Logstash default configuration is stored in ```logstash/config/logstash.conf```.

### Nginx configuration

The Nginx default configuration is stored in ```nginx/config/nginx.conf```.
The Nginx Password Authentication is stored in ```nginx/config/.htpasswd```. 

to create a login and password use this utility ```htpasswd```.

```htpasswd nginx/config/.htpasswd monitoring```  (sammy in this example)

# Logs backup

Before backup/restore, you need to install AWS Cloud plugin

### Installation
Or for ES version < 5.x
```
cd /usr/share/elasticsearch
bin/plugin install cloud-aws
```
Or for ES version > 5.x

```
cd /usr/share/elasticsearch
bin/elasticsearch-plugin install repository-s3
```

## Backup and Restore

### Register S3 repository
```
curl -XPUT 'http://localhost:9200/_snapshot/repo_name' -d '{
    "type": "s3",
    "settings": {
        "access_key": "******",
        "secret_key": "*******",
        "bucket": "S3_bucket",
        "region": "*****",
        "base_path": "elasticsearch",
        "max_retries": 3
    }
}'
```
The settings are supported:
 - access_key: The S3 access key to use for authentication.
 - secret_key: The S3 secret key to use for authentication.
 - bucket: The name of the bucket to be used for snapshots (required)
 - region: The region where bucket is located. Defaults to US Standard
 - base_path: Specifies the path within bucket to repository data.  --  -  - Default to root directory if not set.
 - max_retries: Number of retries in case of S3 errors.

### Snapshotting

```
curl -XPUT 'http://localhost:9200/_snapshot/repo_name/?wait_for_completion=true' -d '{
    "indices": "your_index1, your_index2",
    "ignore_unavailable": "true",
    "include_global_state": false
}'
```

### Restoring

```
curl -XPOST 'localhost:9200/_snapshot/repo_name/snapshot_name/_restore
```

### Some useful commands

 - List all snapshots
 ```curl -XGET "localhost:9200/_snapshot/repo_name/_all"``` 
 - Detail information of snapshot
 ```curl -XGET "localhost:9200/_snapshot/repo_name/snapshot_name"```
 - Delete a specify snapshot
 ```curl -XDELETE "localhost:9200/_snapshot/repo_name/snapshot_name"```
 - Delete all snapshots
 ```curl -XDELETE "localhost:9200/_snapshot/repo_name"```

### Auto backup to S3 repositoriy

Before backup, you need to install python-paramiko

```sudo apt-get install python-paramiko```

Create a log file

```
sudo mkdir /var/log/S3logs
sudo touch /var/log/S3logs/S3.logs
sudo chmod u+wr,g+wr,o+wr /var/log/S3logs/S3.logs
```
Put the key in the same directory with the script

Register S3 repository

Run script
```
python autobackup_logs_to_s3_repository.py -s3 repo_name -d 10
```
The settings are supported:
 - s3 repository name
 - d Number of days as the logs stored on the server


