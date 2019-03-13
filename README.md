# Dockers-ELK + Elastic Search Curator/Cronjob


Run the latest version of the ELK (Elasticsearch, Logstash, Kibana) stack with Docker and Docker Compose.

It will give you the ability to analyze any data set by using the searching/aggregation capabilities of Elasticsearch
and the visualization power of Kibana.

Based on the official Docker images:

* [elasticsearch](https://github.com/elastic/elasticsearch-docker)
* [logstash](https://github.com/elastic/logstash-docker)
* [kibana](https://github.com/elastic/kibana-docker)
* [elasticsearch-curator](https://www.elastic.co/guide/en/elasticsearch/client/curator/current/index.html)


# How to setup 

1. Clone this repository and create an empty dir data

```console
$ mkdir data
```

2. Start the ELK stack using `docker-compose`:

```console
$ docker-compose up
```
# OR

You can also choose to run it in background (detached mode):

```console
$ docker-compose up -d


Give Kibana a few seconds to initialize, then access the Kibana web UI by hitting
[http://localhost:5601](http://localhost:5601) with a web browser.

By default, the stack exposes the following ports:
* 5000: Logstash TCP input.
* 9200: Elasticsearch HTTP
* 9300: Elasticsearch TCP transport
* 5601: Kibana
```

# Default Configurations

## Curator Actions

There are two default actions defined in the 'curator/action_file.yml'.

#### Action 1: Deleteing old indices after X number of days
First action defined is deleting the old indices X number of days ago.
* To set that X number of days, change the 'unit_count' under 'filters'.

```console
filtertype: age
source: name
direction: older
timestring: '%Y.%m.%d'
unit: days
unit_count: 5
```


The default index name follows this pattern "filebeat-{date}". "filebeat" being the prefix and date being in the format of "YYYY-MM-dd"
* To set either of these fields, modify the "value" of the filter type pattern and "timestring" respectively

```console
- filtertype: pattern
  kind: prefix
  value: filebeat-
- filtertype: age
  source: name
  direction: older
  timestring: '%Y.%m.%d'
```

#### Action 2: Deleting old indices after Y GB are used
Second action defined is deleting the old indices after an Y GB of space is used
* To set that Y GB, change the 'disk_space' under 'filters'.

```console
filtertype: space
disk_space: 0.001
```

The default index name follows this pattern "filebeat-{date}". "filebeat" being the prefix and date being in the format of "YYYY-MM-dd"
* To set either of these fields, modify the "value" of the filter type pattern and "timestring" respectively

```console
- filtertype: pattern
  kind: prefix
  value: filebeat-
- filtertype: space
  disk_space: 0.001
  use_age: True
  source: name
  timestring: '%Y.%m.%d'
```

## Cronjob Frequency

To set the cronjob frequency, go to "curator/Dockerfile" and then set the schedule value to the cronjob configuration you want

```console
ENV SCHEDULE "*/5 * * * * *"
```

## Install Plugins for any of the ELK-Stack

To install any plugin for any of the three components of the ELK Stack (Elastic Search, Kibana or Logstash), go to the intended directory and modify the Dockerfile.

For example, let us say we want to install a plugin on kibana, go to kibana dir and edit the Dockerfile as follows:

```console
FROM docker.elastic.co/kibana/kibana-oss:6.6.0

# Add your kibana plugins setup here
RUN kibana-plugin install <name|url>
```
