# Elasticsearch Installation Guide

## Ubuntu/Debian
```bash
# Install Java
sudo apt install openjdk-11-jdk

# Add Elasticsearch repository
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
echo "deb https://artifacts.elastic.co/packages/7.x/apt stable main" | sudo tee /etc/apt/sources.list.d/elastic-7.x.list

# Install
sudo apt update
sudo apt install elasticsearch

# Configure
sudo nano /etc/elasticsearch/elasticsearch.yml
```

## Configuration
```yaml
cluster.name: my-cluster
node.name: node-1
path.data: /var/lib/elasticsearch
path.logs: /var/log/elasticsearch
network.host: localhost
http.port: 9200
discovery.type: single-node
```

## Start Service
```bash
sudo systemctl start elasticsearch
sudo systemctl enable elasticsearch

# Test
curl -X GET "localhost:9200/"
```

## Docker Installation
```bash
docker run -d \
  --name elasticsearch \
  -p 9200:9200 \
  -p 9300:9300 \
  -e "discovery.type=single-node" \
  -e "ES_JAVA_OPTS=-Xms512m -Xmx512m" \
  elasticsearch:7.17.0
```

## Memory Settings
```bash
# Edit JVM options
sudo nano /etc/elasticsearch/jvm.options

# Set heap size (50% of RAM, max 32GB)
-Xms2g
-Xmx2g
```

## Security
```bash
# Enable security
xpack.security.enabled: true

# Set passwords
sudo /usr/share/elasticsearch/bin/elasticsearch-setup-passwords auto
```
