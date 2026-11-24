---
layout: page
title: How to prepare test data
permalink: /docs/tips/test-data
---

## Using Trino's TPCDS or TPCH connectors

Enable `base/trino` in `kubernetes/kustomization.yaml` as described in the [Guide](/docs/guide#choose-components), then start the cluster with `./bin/up`. Keep the default HDFS, Hive, Hive Metastore, Tez, and YARN components enabled for the Hive examples below.

Open the client shell with `./bin/ssh`. Datasets of TPC-DS or TPC-H are available via Trino's `tpcds` or `tpch` catalogs.

```console
zookage@client-node-0:~$ trino
trino> SHOW SCHEMAS FROM tpcds;
       Schema
--------------------
 information_schema
 sf1
 sf10
 sf100
 sf1000
 sf10000
 sf100000
 sf300
 sf3000
 sf30000
 tiny
(11 rows)

trino> SHOW TABLES FROM tpcds.tiny;
         Table
------------------------
 call_center
 catalog_page
 catalog_returns
 catalog_sales
 customer
 customer_address
 customer_demographics
 date_dim
 dbgen_version
 household_demographics
 income_band
 inventory
 item
 promotion
 reason
 ship_mode
 store
 store_returns
 store_sales
 time_dim
 warehouse
 web_page
 web_returns
 web_sales
 web_site
(25 rows)
```

You can copy the datasets as another format. This is an example to copy `customer` as a Hive table.

```console
zookage@client-node-0:~$ trino
trino> CREATE TABLE hive.default.customer AS SELECT * FROM tpcds.tiny.customer;
CREATE TABLE: 1000 rows
```

You can query it from another query engine or system.

```console
zookage@client-node-0:~$ beeline
...
0: jdbc:hive2://hive-hiveserver2:10000/defaul> SELECT c_salutation, COUNT(*) FROM customer GROUP BY c_salutation ORDER BY c_salutation;
...
+---------------+------+
| c_salutation  | _c1  |
+---------------+------+
| NULL          | 30   |
| Dr.           | 283  |
| Miss          | 137  |
| Mr.           | 160  |
| Mrs.          | 98   |
| Ms.           | 125  |
| Sir           | 167  |
+---------------+------+
```
