## [Riak](https://riak.com/index.html)
- prezentují se vysokou odolností a přeností dat
- open-source i komerční
- Basho Technologies
- inspirováno Amazon DynamoDB


Výzvy pro distribuované systémy:

- Dostupnost dat
- Přesnost dat
- Náklady na rozsah

Dva produkty: 
| Riak KV | Riak TS |
| ----------- | ----------- |
| distribuované úložiště dat NoSQL | časové řady |


<img src="https://github.com/MystiMiki/DUL/blob/main/assets/OperationalSimplicity.jpg" alt="NoSQLDatabases" width="250"/>

<br>

### [Riak KV](https://riak.com/products/riak-kv/index.html)
- distribuované a horizontálně škálovatelné NoSQL
- víceuzlová architektura bez master 
- replikuje data ve výchozím nastavení do tří uzlů v clusteru

**Riak Search** - integrace Solr (pro indexování a dotazování) a Riak (pro ukládání a distribuci)

**Riak KV Spark Connector** - přesun dat z Riak KV do Apache Spark pro vylepšenou analýzu v paměti

**Riak Mesos Framework** - usnadnění a zefektivnění nasazení a správy Riak clusterů pomocí Mesos
 

<br>

Základní operace - GET, PUT, DELETE

Volba klíče - sémantický klíč (př. hudební aplikace, klíč: *autor_album_rok*)

Lze ukládat jakýkoli typ obsahu: xml, json, yaml, binarní soubory

