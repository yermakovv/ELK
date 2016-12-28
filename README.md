Docker ELK + NGINX stack

Based on the official images:

elasticsearch
logstash
kibana
nginx

Setup

Install Docker
Install docker-compose version >=1.6

Increase max_map_count on your host (Linux)

You need to increase max_map_count on your Docker host:

$ sudo sysctl -w vm.max_map_count=262144

Usage
