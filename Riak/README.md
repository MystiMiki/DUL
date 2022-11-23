## [Riak](https://riak.com/index.html)
- prezentují se vysokou odolností a přeností dat
- open-source i komerční
- Basho Technologies
- inspirováno Amazon DynamoDB


Hlavní bonusy Riak:

- 100% dostupnost
- nekonečné škálování
- zotavení po závadě
- nízká latence

Dva produkty: 
| Riak KV | Riak TS |
| ----------- | ----------- |
| distribuované úložiště dat NoSQL | časové řady |


<img src="https://github.com/MystiMiki/DUL/blob/main/assets/OperationalSimplicity.jpg" alt="NoSQLDatabases" width="250" align="right"/>

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

Chris Mozolian v prezentaci, která proběhla na konferenci Devoxx 2013 v Británii, zmiňuje, že Riak není databáze malého rozsahu, přepokládá se, že už od začatku budete začínat aspoň se 4 uzly.




