# FilePuse Example
[Streaming data into Kafka S01/E02 - Loading XML file](https://dev.to/fhussonnois/streaming-data-into-kafka-s01-e02-loading-xml-file-529i)

[Ingesting XML data into Kafka](https://rmoff.net/2020/10/01/ingesting-xml-data-into-kafka-option-3-kafka-connect-filepulse-connector/)

## 1. Start the Docker Container
```
cd ~/src/kafka-connect-file-pulse
docker-compose up --build
```

## 2. Create Connector
```
curl -i -X PUT -H "Accept:application/json" \
    -H  "Content-Type:application/json" http://localhost:8083/connectors/source-filepulse-xml-02/config \
    -d '{
            "connector.class": "io.streamthoughts.kafka.connect.filepulse.source.FilePulseSourceConnector",
            "fs.cleanup.policy.class": "io.streamthoughts.kafka.connect.filepulse.fs.clean.DeleteCleanupPolicy",
            "fs.listing.class": "io.streamthoughts.kafka.connect.filepulse.fs.LocalFSDirectoryListing",
            "fs.listing.directory.path":"/tmp/kafka-connect/examples/",
            "fs.listing.filters": "io.streamthoughts.kafka.connect.filepulse.fs.filter.RegexFileListFilter",
            "fs.listing.interval.ms": "5000",
            "file.filter.regex.pattern":".*\\.(xml|XML)",
            "topic": "connect-file-pulse-quickstart-xml",
            "tasks.reader.class": "io.streamthoughts.kafka.connect.filepulse.fs.reader.LocalXMLFileInputReader",
            "reader.xml.parser.validating.enabled": true,
            "reader.xml.parser.namespace.aware.enabled": true,
            "reader.xml.exclude.empty.elements": true,
            "tasks.file.status.storage.class": "io.streamthoughts.kafka.connect.filepulse.state.KafkaFileObjectStateBackingStore",
            "tasks.file.status.storage.bootstrap.servers": "broker:29092",
            "tasks.file.status.storage.topic": "connct-file-pulse-status",
            "tasks.file.status.storage.topic.partitions": 10,
            "tasks.file.status.storage.topic.replication.factor": 1,
            "tasks.max": 1
        }'
```
## 3. Copy CSV file to docker-container
```
docker exec -it connect mkdir -p /tmp/kafka-connect/examples

docker cp humanResources.xml connect://tmp/kafka-connect/examples/humanResources-00.xml

docker exec -it connect ls /tmp/kafka-connect/examples/
```
##  4. Conector Status
```
curl -s localhost:8083/connectors/source-filepulse-xml-02/status | jq 	
```	
	
### List Connectors
```
curl -s -XGET "http://localhost:8083/connectors/"		
```
### Conector Status 
```
curl -s localhost:8083/connectors/source-filepulse-xml-02/status | jq
```
### Delete Connector 
```
curl -s -XDELETE "http://localhost:8083/connectors/source-filepulse-xml-02"
```
### Restart Connector 
```
curl -s -XPOST "http://localhost:8083/connectors/source-filepulse-xml-02/restart"
```
### List Topics  
```
curl -s -XGET "http://localhost:8083/connectors/source-filepulse-xml-02/topics" | jq
```
### List Tasks 
```
curl -s -XGET "http://localhost:8083/connectors/source-filepulse-xml-02/tasks" | jq
```
### Task Status 
```
curl -s -XGET "http://localhost:8083/connectors/source-filepulse-xml-02/tasks/0/status" | jq
```
### Restart Task 
```
curl -s -XPOST "http://localhost:8083/connectors/source-filepulse-xml-02/tasks/0/restart"
```

