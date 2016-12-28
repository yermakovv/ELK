
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

