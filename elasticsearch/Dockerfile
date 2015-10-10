FROM elasticsearch:latest

# Install Marvel plugin : http://elasticsearch:9200/_plugin/marvel
RUN plugin -i elasticsearch/marvel/latest

# Patch docker-entrypoint in order to not drop root priviledges when starting ElasticSearch
# Ortherwise the process will not be able to create/update files and directory on the host
COPY docker-entrypoint.sh /
RUN chmod +x /docker-entrypoint.sh
