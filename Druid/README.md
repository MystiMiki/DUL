## [Druid](https://druid.apache.org/)
- je navržený pro rychlou analýzu slice-and-dice
- sloupcová databáze
- několik profilů spouštěcí konfigurace pro různé velikosti strojů, příklady:
  - nano (1 CPU, 4GiB RAM)
  - x-large (64 CPU, 512GiB RAM)  

  Operační systémy:
  * Linux, Mac OS X nebo jiný operační systém Unixu
  * Java 8u92+ nebo Java 11
  * **Windows není podporován**



### Instalace Druid pomocí Docker
Druid můžeme spustit pomocí [Docker](https://docs.imply.io/druid/docs/tutorials/docker).

#### 1. Stáhneme si potřebné soubory

`docker-compose.yml`
 
 ```
version: "2.2"

volumes:
  metadata_data: {}
  middle_var: {}
  historical_var: {}
  broker_var: {}
  coordinator_var: {}
  router_var: {}
  druid_shared: {}


services:
  postgres:
    container_name: postgres
    image: postgres:latest
    volumes:
      - metadata_data:/var/lib/postgresql/data
    environment:
      - POSTGRES_PASSWORD=FoolishPassword
      - POSTGRES_USER=druid
      - POSTGRES_DB=druid

  # Need 3.5 or later for container nodes
  zookeeper:
    container_name: zookeeper
    image: zookeeper:3.5
    ports:
      - "2181:2181"
    environment:
      - ZOO_MY_ID=1

  coordinator:
    image: apache/druid:24.0.1
    container_name: coordinator
    volumes:
      - druid_shared:/opt/shared
      - coordinator_var:/opt/druid/var
    depends_on: 
      - zookeeper
      - postgres
    ports:
      - "8081:8081"
    command:
      - coordinator
    env_file:
      - environment

  broker:
    image: apache/druid:24.0.1
    container_name: broker
    volumes:
      - broker_var:/opt/druid/var
    depends_on: 
      - zookeeper
      - postgres
      - coordinator
    ports:
      - "8082:8082"
    command:
      - broker
    env_file:
      - environment

  historical:
    image: apache/druid:24.0.1
    container_name: historical
    volumes:
      - druid_shared:/opt/shared
      - historical_var:/opt/druid/var
    depends_on: 
      - zookeeper
      - postgres
      - coordinator
    ports:
      - "8083:8083"
    command:
      - historical
    env_file:
      - environment

  middlemanager:
    image: apache/druid:24.0.1
    container_name: middlemanager
    volumes:
      - druid_shared:/opt/shared
      - middle_var:/opt/druid/var
    depends_on: 
      - zookeeper
      - postgres
      - coordinator
    ports:
      - "8091:8091"
      - "8100-8105:8100-8105"
    command:
      - middleManager
    env_file:
      - environment

  router:
    image: apache/druid:24.0.1
    container_name: router
    volumes:
      - router_var:/opt/druid/var
    depends_on:
      - zookeeper
      - postgres
      - coordinator
    ports:
      - "8888:8888"
    command:
      - router
    env_file:
      - environment 
```
<br>    

`environment`

```
# Java tuning
#DRUID_XMX=1g
#DRUID_XMS=1g
#DRUID_MAXNEWSIZE=250m
#DRUID_NEWSIZE=250m
#DRUID_MAXDIRECTMEMORYSIZE=6172m
DRUID_SINGLE_NODE_CONF=micro-quickstart

druid_emitter_logging_logLevel=debug

druid_extensions_loadList=["druid-histogram", "druid-datasketches", "druid-lookups-cached-global", "postgresql-metadata-storage", "druid-multi-stage-query"]

druid_zk_service_host=zookeeper

druid_metadata_storage_host=
druid_metadata_storage_type=postgresql
druid_metadata_storage_connector_connectURI=jdbc:postgresql://postgres:5432/druid
druid_metadata_storage_connector_user=druid
druid_metadata_storage_connector_password=FoolishPassword

druid_coordinator_balancer_strategy=cachingCost

druid_indexer_runner_javaOptsArray=["-server", "-Xmx1g", "-Xms1g", "-XX:MaxDirectMemorySize=3g", "-Duser.timezone=UTC", "-Dfile.encoding=UTF-8", "-Djava.util.logging.manager=org.apache.logging.log4j.jul.LogManager"]
druid_indexer_fork_property_druid_processing_buffer_sizeBytes=256MiB

druid_storage_type=local
druid_storage_storageDirectory=/opt/shared/segments
druid_indexer_logs_type=file
druid_indexer_logs_directory=/opt/shared/indexing-logs

druid_processing_numThreads=2
druid_processing_numMergeBuffers=2

DRUID_LOG4J=<?xml version="1.0" encoding="UTF-8" ?><Configuration status="WARN"><Appenders><Console name="Console" target="SYSTEM_OUT"><PatternLayout pattern="%d{ISO8601} %p [%t] %c - %m%n"/></Console></Appenders><Loggers><Root level="info"><AppenderRef ref="Console"/></Root><Logger name="org.apache.druid.jetty.RequestLog" additivity="false" level="DEBUG"><AppenderRef ref="Console"/></Logger></Loggers></Configuration>
```
#### 2. Vytvoření workspace a zprovoznení 
Vytvoříme si složku libovného názvu. Do této složky nahrajeme stažené soubory `dcoker-compose.yml` a `environment`.

Nyní spustíme docker compose pomocí příkazu v terminálu: `docker compose up --build -d`. Druid by měl běžet na localhostovi na portu 8888.

<img src="https://github.com/MystiMiki/DUL/blob/main/assets/tutorial-quickstart-01.png" alt="NoSQLDatabases"/>

Pokud vše funguje můžeme začít práci s Druid.

### Vytvoření kostky

  
