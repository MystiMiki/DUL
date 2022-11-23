## [Riak](https://riak.com/index.html)
- prezentují se vysokou odolností a přeností dat
- open-source i komerční
- Basho Technologies
- inspirováno Amazon DynamoDB
- Erlang (multiparadigmatický programovací jazyk, specializovaný pro tvorbu distribuovaných, vysoce dostupných aplikací, odolných proti selhání)


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
- Java, Ruby, Python, C#, Node.js, Erlang, PHP, 

**Riak Search** - integrace Solr (pro indexování a dotazování) a Riak (pro ukládání a distribuci)

**Riak KV Spark Connector** - přesun dat z Riak KV do Apache Spark pro vylepšenou analýzu v paměti

**Riak Mesos Framework** - usnadnění a zefektivnění nasazení a správy Riak clusterů pomocí Mesos
 

<br>

Základní operace - GET, PUT, DELETE

Key - sémantický klíč (př. hudební aplikace, klíč: *autor_album_rok*)

Value - JSON, XML, HTML, dokumenty, binární soubory, obrázky a další

Bucket - se používá k definování virtuálního prostoru klíčů pro ukládání objektů Riak

Pokročilé operace - sekundární indexové vyhledávání, textové vyhledávání (prostřednictvím Apache Solr) a MapReduce


Chris Mozolian v prezentaci, která proběhla na konferenci Devoxx 2013 v Británii, zmiňuje, že Riak není databáze malého rozsahu, přepokládá se, že už od začatku budete začínat aspoň se 4 uzly.

### Provnání s Cassanrou
Cassandra funguje nejlépe, když je vaše schéma předem definované. Nejlépe se hodí pro data, která lze považovat za soubor vlastností. Riak KV je nejlepší, když jsou data binární objekt, neprůhledný blob nebo JSON.

## Riak KV a Python

***Shell***
```
pip install riak
```

***Python***
```
import riak
```

Vytvoření nové instance klienta při použití jednoho místního uzlu Riak:
***Python***
```
myClient = riak.RiakClient(pb_port=8087, protocol='pbc')
```
<hr>
1. Vytváření objektů

Value je int:

```
myBucket = myClient.bucket('test')

val1 = 1
key1 = myBucket.new('one', data=val1)
key1.store()
```

Value je string:

```
val2 = "two"
key2 = myBucket.new('two', data=val2)
key2.store()
```

Value je JSON:

```
val3 = {"myValue": 3}
key3 = myBucket.new('three', data=val3)
key3.store()
```
<hr>
2. Čtení objektů

```
fetched1 = myBucket.get('one')
fetched2 = myBucket.get('two')
fetched3 = myBucket.get('three')

assert val1 == fetched1.data
assert val2 == fetched2.data
assert val3 == fetched3.data
```
<hr>
3. Upravování objektů

```
fetched3.data["myValue"] = 42
fetched3.store()
```
<hr>
4. Mazání objektů

```
fetched1.delete()
fetched2.delete()
fetched3.delete()
```
Lze oveřit:
```
assert myBucket.get('one').exists == False
assert myBucket.get('two').exists == False
assert myBucket.get('three').exists == False
```
<hr>
5. Práce s komplexními objekty

```
book = {
  'isbn': "1111979723",
  'title': "Moby Dick",
  'author': "Herman Melville",
  'body': "Call me Ishmael. Some years ago...",
  'copies_owned': 3
}

booksBucket = myClient.bucket('books')
newBook = booksBucket.new(book['isbn'], data=book)
newBook.store()
```
Python Riak kóduje/dekóduje objekty jako JSON, pokud je to možné. Lze zkontrolovat: 
```
fetchedBook = booksBucket.get(book['isbn'])

print(fetchedBook.encoded_data)
```
<hr>

### Dotazování
Nejrychlejší způsob, jak z relační databáze udělat NoSQL databázi je denormalizace. Můžeme pokračovat v načítání souvisejících dat, dokud nenarazíte na jednu z velkých denormalizačních stěn:
- Omezení velikosti (objekty větší než 1 MB)
- Sdílená/referenční data (data, která objekt „nevlastní“)
- Rozdíly v přístupových vzorech (objekty, které jsou přečteny/zapsány jednou vs. často)
V jednom z těchto bodů budeme muset model rozdělit.

Možné rozdělení dat:
#### Stejné klíče - jiné buckety
Budeme mít customer, order a order_summary. Objednávka obsahuje customer_id. Order_sumary obsahuje customer_id a summaries, kde je seznam order_id.

```
# Creating Data

customer = {
    'customer_id': 1,
    'name': "John Smith",
    'address': "123 Main Street",
    'city': "Columbus",
    'state': "Ohio",
    'zip': "43210",
    'phone': "+1-614-555-5555",
    'created_date': "2013-10-01 14:30:26"
}

orders = [
    {
        'order_id': 1,
        'customer_id': 1,
        'salesperson_id': 9000,
        'items': [
            {
                'item_id': "TCV37GIT4NJ",
                'title': "USB 3.0 Coffee Warmer",
                'price': 15.99
            },
            {
                'item_id': "PEG10BBF2PP",
                'title': "eTablet Pro, 24GB, Grey",
                'price': 399.99
            }
        ],
        'total': 415.98,
        'order_date': "2013-10-01 14:42:26"
    },
    {
        'order_id': 2,
        'customer_id': 1,
        'salesperson_id': 9001,
        'items': [
            {
                'item_id': "OAX19XWN0QP",
                'title': "GoSlo Digital Camera",
                'price': 359.99
            }
        ],
        'total': 359.99,
        'order_date': "2013-10-15 16:43:16"
    },
    {
        'order_id': 3,
        'customer_id': 1,
        'salesperson_id': 9000,
        'items': [
            {
                'item_id': "WYK12EPU5EZ",
                'title': "Call of Battle: Goats - Gamesphere 4",
                'price': 69.99
            },
            {
                'item_id': "TJB84HAA8OA",
                'title': "Bricko Building Blocks",
                'price': 4.99
            }
        ],
        'total': 74.98,
        'order_date': "2013-11-03 17:45:28"
    }]

order_summary = {
    'customer_id': 1,
    'summaries': [
        {
            'order_id': 1,
            'total': 415.98,
            'order_date': "2013-10-01 14:42:26"
        },
        {
            'order_id': 2,
            'total': 359.99,
            'order_date': "2013-10-15 16:43:16"
        },
        {
            'order_id': 3,
            'total': 74.98,
            'order_date': "2013-11-03 17:45:28"
        }
    ]
}


# Starting Client
client = riak.RiakClient(pb_port=10017, protocol='pbc')'

# Creating Buckets
customer_bucket = client.bucket('Customers')
order_bucket = client.bucket('Orders')
order_summary_bucket = client.bucket('OrderSummaries')


# Storing Data
cr = customer_bucket.new(str(customer['customer_id']),
                         data=customer)
cr.store()

for order in orders:
    order_riak = order_bucket.new(str(order['order_id']),
                                  data=order)
    order_riak.store()

os = order_summary_bucket.new(str(order_summary['customer_id']),
                              data=order_summary)
os.store()
```
Zatímco Customer a Order se příliš nemění (nebo by se měnit neměly), Order Summaries se bude pravděpodobně měnit často. Bude plnit dvojí povinnost tím, že bude fungovat jako index pro všechny objednávky zákazníků a bude také uchovávat některé relevantní údaje, jako je celková částka objednávky atd.
```
customer = customer_bucket.get('1').data
customer['order_summary'] = order_summary_bucket.get('1').data
customer
```
Což nám vrátí:
```
{
  u'city': u'Columbus', u'name': u'John Smith', u'zip': u'43210',
  u'created_date': u'2013-10-01 14:30:26',
  'order_summary': {
    u'customer_id': 1, u'summaries': [
      {u'order_id': 1, u'order_date': u'2013-10-01 14:42:26', u'total': 415.98},
      {u'order_id': 2, u'order_date': u'2013-10-15 16:43:16', u'total': 359.99},
      {u'order_id': 3, u'order_date': u'2013-11-03 17:45:28', u'total': 74.98}
    ]},
  u'phone': u'+1-614-555-5555', u'state': u'Ohio', u'address': u'123 Main Street',
  u'customer_id': 1
}
```
#### Sekundární indexy
Sekundární indexy (2i) jsou hodně podobné indexům SQL.
```
for i in range(1, 4):
    order = order_bucket.get(str(i))
    # Initialize our secondary indices
    order.add_index('salesperson_id_int', order.data['salesperson_id'])
    order.add_index('order_date_bin', order.data['order_date'])
    order.store()
```
Přidali jsme položku do indexů. Nyní můžeme najít všechny objednávky, které zpracovala Janes, která má *saleperson_id_int = 9000*.
```
janes_orders = order_bucket.get_index("salesperson_id_int", 9000)
janes_orders.results
```
A to nám vrátí:
```
['1', '3']
```
Můžeme použít i funkci rozsahu k vyhledání rozsahu hodnot:
```
october_orders = order_bucket.get_index("order_date_bin",
                                        "2013-10-01", "2013-10-31")
october_orders.results
```
A to nám vrátí:
```
['1', '2']
```







