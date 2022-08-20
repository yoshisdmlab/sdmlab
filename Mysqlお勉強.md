# mysql使って勉強する
## 1. dockerでやる
### 1.1. Docker Desktopをインストールする
下記のページを参考にしてDocker Desktopをインストールする。  
https://matsuand.github.io/docs.docker.jp.onthefly/desktop/mac/install/
### 1.2. mysqlのイメージをpullして起動する
```
# -- docker imageをpull
@ docker pull mysql

# -- image見てみる
@ docker image ls                                                        
REPOSITORY          TAG       IMAGE ID       CREATED       SIZE
docker101tutorial   latest    9b95d3e573d4   4 days ago    27.5MB
mysql               latest    d56063370fd1   2 weeks ago   490MB
alpine/git          latest    f404a400c0ad   4 weeks ago   39.5MB

# -- mysql起動
docker run -it --name mysqlsv -e MYSQL_ROOT_PASSWORD=mysql -d mysql:latest

# -- dockerの起動状態を確認する
@ docker ps -a                                                           
CONTAINER ID   IMAGE          COMMAND                  CREATED          STATUS          PORTS                 NAMES
2b3803e08b7d   mysql:latest   "docker-entrypoint.s…"   42 minutes ago   Up 42 minutes   3306/tcp, 33060/tcp   mysqlsv
```

## 2. 勉強用のデータを準備する
### 2.1. sampleデータゲットする
下記の公式URLにworld databaseというデータがあるのでゲットする。  
https://dev.mysql.com/doc/index-other.html
```
# -- curlコマンドでファイルをダウンロードして解凍
@ curl https://downloads.mysql.com/docs/world-db.zip -output /tmp/world-db.zip
@ unzip world-db.zip
```
### 2.2. Docker上にデータをコピーする
```
# -- ホストのファイルをコンテナにコピーする
@ docker cp /tmp/world-db/world.sql mysqlsv:/tmp/
```
## 3. mysqlにデータを流し込む
```
# -- コンテナに入る
@ docker exec -it mysqlsv bash -p

# -- mysqlに入る
# mysql -u root -h localhost -p
Enter password: 
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 12
Server version: 8.0.30 MySQL Community Server - GPL

Copyright (c) 2000, 2022, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

# -- sqlを読み込んでデータを積み込む
mysql> SOURCE /tmp/world.sql
Query OK, 0 rows affected (0.00 sec)
Query OK, 0 rows affected (0.00 sec)
Query OK, 0 rows affected (0.00 sec)
Query OK, 0 rows affected (0.00 sec)
・・・
Query OK, 0 rows affected (0.00 sec)

# -- 積み込まれているか確認する
mysql> SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
| world              |
+--------------------+
5 rows in set (0.00 sec)

mysql> USE world;
Database changed

mysql> SHOW TABLES;
+-----------------+
| Tables_in_world |
+-----------------+
| city            |
| country         |
| countrylanguage |
+-----------------+
3 rows in set (0.01 sec)

# -- テーブルの詳細を確認する（10件ほど）
mysql> SELECT * FROM city LIMIT 10;
+----+----------------+-------------+---------------+------------+
| ID | Name           | CountryCode | District      | Population |
+----+----------------+-------------+---------------+------------+
|  1 | Kabul          | AFG         | Kabol         |    1780000 |
|  2 | Qandahar       | AFG         | Qandahar      |     237500 |
|  3 | Herat          | AFG         | Herat         |     186800 |
|  4 | Mazar-e-Sharif | AFG         | Balkh         |     127800 |
|  5 | Amsterdam      | NLD         | Noord-Holland |     731200 |
|  6 | Rotterdam      | NLD         | Zuid-Holland  |     593321 |
|  7 | Haag           | NLD         | Zuid-Holland  |     440900 |
|  8 | Utrecht        | NLD         | Utrecht       |     234323 |
|  9 | Eindhoven      | NLD         | Noord-Brabant |     201843 |
| 10 | Tilburg        | NLD         | Noord-Brabant |     193238 |
+----+----------------+-------------+---------------+------------+
10 rows in set (0.00 sec)

mysql> DESC city;
+-------------+----------+------+-----+---------+----------------+
| Field       | Type     | Null | Key | Default | Extra          |
+-------------+----------+------+-----+---------+----------------+
| ID          | int      | NO   | PRI | NULL    | auto_increment |
| Name        | char(35) | NO   |     |         |                |
| CountryCode | char(3)  | NO   | MUL |         |                |
| District    | char(20) | NO   |     |         |                |
| Population  | int      | NO   |     | 0       |                |
+-------------+----------+------+-----+---------+----------------+
5 rows in set (0.00 sec)

mysql> SELECT count(*) FROM city;
+----------+
| count(*) |
+----------+
|     4079 |
+----------+
1 row in set (0.00 sec)
```
