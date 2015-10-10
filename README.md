# Docker ELK stack

This docker receipe is mainly inspired by [deviantony](https://github.com/deviantony/docker-elk) but fixes issue on Mac OSX about access denied when persistent storage is enabled for elasticseach (see bug [docker-library/elasticsearch/issues/51](https://github.com/docker-library/elasticsearch/issues/51)) by using a workaround and not dropping root privileges :

```log
[2015-09-03 17:37:12,223][INFO ][node                     ] [Vidar] version[1.7.1], pid[1], build[b88f43f/2015-07-29T09:54:16Z]
[2015-09-03 17:37:12,223][INFO ][node                     ] [Vidar] initializing ...
[2015-09-03 17:37:12,285][INFO ][plugins                  ] [Vidar] loaded [], sites []
{1.7.1}: Initialization Failed ...
- ElasticsearchIllegalStateException[Failed to created node environment]
    AccessDeniedException[/usr/share/elasticsearch/data/elasticsearch]
```



Run the ELK (Elasticseach, Logstash, Kibana) stack with Docker and Docker-compose.

It will give you the ability to quickly test your logstash filters and check how the data can be processed in Kibana.

Based on the official images:

* [elasticsearch](https://registry.hub.docker.com/_/elasticsearch/)
* [logstash](https://registry.hub.docker.com/_/logstash/)
* [kibana](https://registry.hub.docker.com/_/kibana/)

# Requirements

## Setup

1. Install Docker 
	* For Linux users install [Docker](http://docker.io) and
	* For Mac/Windows users install [Docker Toolbox](https://www.docker.com/toolbox)
 
2. Clone this repository


# Usage

Start the ELK stack using *docker-compose*:

```bash
$ docker-compose up
```

You can also choose to run it in background (detached mode):

```bash
$ docker-compose up -d
```

Now that the stack is running, you'll want to inject logs in it. The shipped logstash configuration allows you to send content via tcp:

```bash
$ nc localhost 5000 < /path/to/logfile.log
```

And then access Kibana UI by hitting [http://localhost:5601](http://localhost:5601) with a web browser.

By default, the stack exposes the following ports:
* 5000: Logstash TCP input.
* 9200: Elasticsearch HTTP (with Marvel plugin accessible via [http://localhost:9200/_plugin/marvel](http://localhost:9200/_plugin/marvel))
* 5601: Kibana 4 web interface

*WARNING*: If you're using *Docker Toolbox*, you must access it via the *docker-machine* IP address instead of *localhost*.

# Configuration

*NOTE*: Configuration is not dynamically reloaded, you will need to restart the stack after any change in the configuration of a component.

## How can I tune Kibana configuration?

The Kibana default configuration is stored in `kibana/config/kibana.yml`.

## How can I tune Logstash configuration?

The logstash configuration is stored in `logstash/config/logstash.conf`.

## How can I tune Elasticsearch configuration?

The Elasticsearch container is using the shipped configuration and it is not exposed by default.

If you want to override the default configuration, create a file `elasticsearch/config/elasticsearch.yml` and add your configuration in it.

Then, you'll need to map your configuration file inside the container in the `docker-compose.yml`. Update the elasticsearch container declaration to:

```yml
elasticsearch:
     build: elasticsearch/
     volumes:
        - ./elasticsearch/config/elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml
     command: elasticsearch 
     ports:
        - "9200:9200"
        - "9300:9300"
```

# Storage

## How can I store Elasticsearch data?

In order to persist Elasticsearch data, you'll have to mount a volume on your Docker host. Update the elasticsearch container declaration to:

```yml
elasticsearch:
     build: elasticsearch/
     volumes:
        - /path/to/storage:/usr/share/elasticsearch/data
     command: elasticsearch 
     ports:
        - "9200:9200"
        - "9300:9300"
```

This will store elasticsearch data inside `/path/to/storage`. By default in the compose file `/path/to/storage` is mapped to `./elasticsearch/data`