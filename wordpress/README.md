# WordPressの環境をローカルで構築してお試す
## 環境構築手順
1. M2 airにDocker Desktopをインストールする  
(Docker Desktopインストールする時に見るサイト)[https://www.docker.com/products/docker-desktop/]
上記のサイトからインストールする。

1. docker-compose.ymlを作成する  
ymlは任意のディレクトリを作成して格納する。
1. phpMyAdminの設定を追加する  
docker-compose.ymlを配置したディレクトリ配下に作成する。
```
mkdir phpmyadmin/phpmyadmin-misc.ini
```

## 起動と停止手順
起動する際には下記のコマンドを実行する。
```
docker-compose up -d
```

停止する際には下記。
```
docker-compose down
```
