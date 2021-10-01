# Dokcer Compose for Elasticsearch Multi nodes and Kibana with TLS enabled.



## How to use

### Running the Elastic Stack on Docker with TLS enabled

If security features are enabled, you must configure Transport Layer Security (TLS) encryption for the Elasticsearch transport layer. While it is possible to use a trial license without setting up TLS, we advise securing your stack from the start.

To get an Elasticsearch cluster and Kibana up and running in Docker with security enabled, you can use Docker Compose:

Generate certificates for Elasticsearch by bringing up the create-certs container:

```sh
mkdir certs elastic-master-data01 elastic-master-data02 elastic-data-data01 elastic-data-data02
chmod 777 certs elastic-master-data01 elastic-master-data02 elastic-data-data01 elastic-data-data02

docker-compose -f create-certs.yml run --rm create_certs

```

Bring up the three-node Elasticsearch cluster:

```sh
docker-compose -f elastic-docker-tls.yml up -d

```

> **_NOTE:_**  At this point, Kibana cannot connect to the Elasticsearch cluster. You must generate a password for the built-in kibana_system user, update the ELASTICSEARCH_PASSWORD in the compose file, and restart to enable Kibana to communicate with the secured cluster.


Run the elasticsearch-setup-passwords tool to generate passwords for all built-in users, including the kibana_system user. If you don’t use PowerShell on Windows, remove the trailing `\`characters and join the lines before running this command.

```sh
docker exec es01 /bin/bash -c "bin/elasticsearch-setup-passwords auto --batch --url https://elastic-master-01:9200"

```

> **_NOTE:_** Make a note of the generated passwords. You must configure the kibana_system user password in the compose file to enable Kibana to connect to Elasticsearch, and you’ll need the password for the elastic superuser to log in to Kibana and submit requests to Elasticsearch.


Set ELASTICSEARCH_PASSWORD in the elastic-docker-tls.yml compose file to the password generated for the kibana_system user.

Use docker-compose to restart the cluster and Kibana:

```bash
docker-compose stop
docker-compose -f elastic-docker-tls.yml up -d
```

---

Open Kibana to load sample data and interact with the cluster: https://localhost:5601.

> **_NOTE:_** Because SSL is also enabled for communications between Kibana and client browsers, you must access Kibana via the HTTPS protocol.

---



#### _Useful link - https://www.elastic.co/guide/en/elastic-stack-get-started/current/get-started-docker.html#get-started-docker-tls_
