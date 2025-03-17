---
title: Container day_2
date: 2025-03-15
categories: [container]
math: true
excerpt: Dockerコンテナの作成・管理方法とDocker Composeを用いた複数コンテナの一括管理方法を詳しく解説します。ポートマッピング、ネットワーク管理、コンテナ間の通信設定など、実践的なコマンド操作を幅広く紹介しています。

---

Docker image 実行 -> container 

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2/Pasted image 20250114091320.png)

Docker container のライフサイクル 
- 実行中
- 停止
- 廃棄
	- 廃棄してもデータを維持したい場合は外部ストレージに接続しておく必要があります
	- コンテナのオリジナルイメージが同じでも、それを実行したものは別のコンテナです（実行時にそれぞれのコンテナIDがユニークです）


![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2/Pasted image 20250114092241.png)

ローカルにないイメージも実行可能です。
Dockerコマンド -> Dockerデーモンに伝達 -> コンピュータを探索 -> なければDockerHubを探索 -> ローカルにpull -> 実行

==参考== `-it` 疑似ターミナルを接続するオプション：コンテナもOSの一部を持っているので、Linux OSに入る感覚で、入ってLinuxの一部を使い、exitで出ることができます
- `uname` $\approx$ rename, aliasのような感覚
- `--name` :


# 実習

## 04_docker
### #1. コンテナの生成と実行

## Dockerfile インストラクションの再定義

```
docker container run -it alpine

docker run -it alpine uname -a
```

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2/Pasted image 20250114092822.png)
STATUS : UP(実行中という意味)


![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2/Pasted image 20250114092841.png)

停止状態です


![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2/Pasted image 20250114092931.png)


![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2/Pasted image 20250114093034.png)

IDがユニークです


![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2/Pasted image 20250114093414.png)

### #2. コンテナに名前を付ける
```
docker container run -d --name helloworld mcr.microsoft.com/azuredocs/aci-helloworld

docker ps
```
- これを実行するとhelloworldとして表示されます   

### #3. コンテナのフォアグラウンド実行 (停止: ctrl + c)

フォアグラウンド実行(attached mode) : 制御権を取ります
`docker container run --name firstnode firstnode-app:1.0`


![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2/Pasted image 20250114093535.png)
- 制御権が移っているため、ターミナルで何も入力できず、ctrl+cで終了する必要があります
- ctrl+cで終了すると停止状態になります 

例えば、これを実行してWebサーバー（ローカル3000番ポート）を表示させると、表示されません 
なぜなら、`localhost:3000`は私のローカルポート3000であり、コンテナで動作しているWebサーバーは、コンテナというローカル内部の別のコンピュータで実行中のサーバーのような感覚です。

-> Dockerではポートマッピングという接続方法を提供しています

### #4. 指定ポートでコンテナをバックグラウンドで実行

バックグラウンド実行(detached mode) : 実行し、制御権はターミナルに戻ります
`docker container run --name guestbook-app -d -p 80:3000 guestbook-app`


![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2/Pasted image 20250114094740.png)
  
  - ポートマッピング(`-p 80:3000`) : このコンテナはローカルで実行しているため、Dockerで実行するWebサーバーにはローカルのポート3000では接続できません。ポート3000を接続してホストのポート80で接続するとコンテナのポート3000に接続してくれるという意味です
  - マッピングを指定しないと、何らかのポートを指定します（`docker ps`コマンドで確認可能）

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2/Pasted image 20250114094847.png)


==参考== もしローカルで複数のWebサーバーを同じポートでテストするのは（ホストポートを別にしない限り）不可能です（ライブラリ間での衝突のリスクもあります）が、コンテナでポートフォワーディングするローカルコンピュータのポートを異なるものにすれば可能です
### #5. 実行中のDockerコンテナのIDのみを表示

`docker container ls -q`


### 参考 Docker拡張機能で

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2/Pasted image 20250114095209.png)
stop : 停止
Restart : 再起動
Remove : 廃棄

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2/Pasted image 20250114095235.png)

- ポートフォワーディングに関する情報


![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2/Pasted image 20250114100936.png)

### #6. 実行中のDockerコンテナをフィルタリングして表示

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2/Pasted image 20250114101208.png)


`docker container ls --filter "name=guestbook-app"`

  

### #7. 終了したコンテナの一覧

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2/Pasted image 20250114101343.png)


```
docker container ls -a

docker ps -a
```

  

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2/Pasted image 20250114101921.png)

### #8. コンテナの終了

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2/Pasted image 20250114101542.png)


![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2/Pasted image 20250114101638.png)


- 実行中のコンテナは削除できないため、停止して削除する必要があります。
- `-f`を使用すると強制削除
`docker container stop helloworld`

  

### #9. コンテナの再起動

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2/Pasted image 20250114101710.png)



`docker container restart firstnode`

  

### #10. コンテナの廃棄

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2/Pasted image 20250114101836.png)


```
docker container rm <CONTAINER_ID>

#docker container rm 57417e8e2b7a
```
  




![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2/Pasted image 20250114102148.png)


### #11. 実行中のコンテナにコマンドを実行


![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2/Pasted image 20250114102455.png)


```
docker container exec <CONTAINER_ID/CONTAINER_NAME> pwd

#docker container exec 49504cf6f22d pwd

#docker container exec firstnode pwd

docker container exec -it <CONTAINER_ID/CONTAINER_NAME> sh

 """shで :""" mv header.ejs _header.ejs
```


![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2/Pasted image 20250114102805.png)
- ここでbashでファイルの名前を変更したので、guestbookのウェブサイトは正常に動作しません

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2/Pasted image 20250114102841.png)
- コンテナ内部でテストする際にshで入って変更してテストするなどの作業を行います。



![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2/Pasted image 20250114103032.png)


### #12. コンテナにファイルをコピー

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2/Pasted image 20250114103220.png)
- 既存ファイルの変更

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2/Pasted image 20250114103454.png)
- コピー完了


![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2/Pasted image 20250114103537.png)

変更されました

```
docker container cp header.ejs <CONTAINER_ID/CONTAINER_NAME>:/app/views/header.ejs

#docker container cp .\guestbook\views\header.ejs guestbook-app:/app/views/header.ejs
```

### #13. コンテナのリソース使用量を確認


![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2/Pasted image 20250114103758.png)

- リアルタイムでリソース使用量を表示します


```
docker container stats <CONTAINER_ID/CONTAINER_NAME>

#docker container stats guestbook-app
```


# 実習 - Dockerコマンドでコンテナを操作する



![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2/Pasted image 20250114111850.png)

Dockerでdb, fe, beをコンテナで実装した場合、通信が必要なのでネットワークが必要です

基本的にbridge, hostを利用して作成されたデフォルトのスコープはlocalです 
- bridge driver : コンテナ間で通信する際に使用
- host driver : hostのローカルネットワークとコンテナが通信する際に使用
- overlay : host_1(container_1, container_2) , host_2(container_3, container_4)の場合、host_1, host_2が接続された有線ネットワークを介してcontainer_1,2 $\leftrightarrow$ container_3,4が通信する方法

==参考== Kubernetes : ローカル、外部のホストのコンテナが通信する際に調整するために存在します。

- ネットワークの削除は空のネットワークである場合に可能です

## 05_docker-network


### #1. Dockerネットワーク一覧を取得

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2/Pasted image 20250114113122.png)

`docker network ls`

- デフォルトのネットワークよりも手動でネットワークを作成することを推奨します

  

### #2. ネットワークドライバー

- Dockerネットワークは、bridge, host, overlayなど目的に応じて様々な種類のネットワークドライバーをサポートしています。

  

### #3. ネットワークを作成

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2/Pasted image 20250114113316.png)


`docker network create test-net`

- `-d`オプションでネットワークドライバーを指定
- 明示的にドライバーを指定しない場合のデフォルトはbridgeです

### #4. ネットワークの詳細情報を確認

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2/Pasted image 20250114113346.png)

`docker network inspect test-net`

  

### #5. ネットワークにコンテナを接続する

#### #5.1 --networkオプションを使用しない場合はデフォルトのbridgeネットワークに接続

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2/Pasted image 20250114113814.png)

- bridgeに接続された2つのコンテナ(firstnode, guestbook)
```
docker run -itd --name firstnode firstnode-app:1.0

docker network inspect bridge
```

  

#### #5.2 test-netに接続

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2/Pasted image 20250114113931.png)

firstnode, secondnodeは同じネットワークに接続されています

```
docker network connect test-net firstnode

docker run -itd --name secondnode --network test-net firstnode-app:1.0

docker network inspect test-net
```

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2/Pasted image 20250114114041.png)

firstnodeはbridge, test-netの2つに接続されています

  

### #6. ネットワークのコンテナ接続を解除

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2/Pasted image 20250114114136.png)
- disconnectしてguestbookのみが残ります

`docker network disconnect bridge firstnode`
  

### #7. コンテナ間の通信

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2/Pasted image 20250114114211.png)

- 接続状態を継続的に表示します 
`docker exec firstnode ping secondnode`





![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2/Pasted image 20250114114355.png)

`docker exec secondnode ping <IP_ADDRESS>`

  

### #8. ネットワークを削除

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2/Pasted image 20250114114509.png)

`docker stop firstnode secondnode`


![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2/Pasted image 20250114114647.png)


`docker network rm test-net`

`#実行中のコンテナがある場合は失敗します`

  

### #9. ネットワークの掃除

`#どのコンテナも接続されていないネットワークを掃除します`

`docker network prune`

==デフォルトネットワークに影響を与える可能性があるため、実習では実行しません== 



# Dockerコマンドラインツールの使い方

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2/Pasted image 20250114134449.png)

==無理にコマンドを覚えなくても、ヘルプを利用すれば使いこなせます==



# Docker Composeの取り扱い 


![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2/Pasted image 20250114135327.png)

- これまでの実習は1つのコンテナを扱っていましたが、一般的には複数のコンテナが相互に通信しながら依存関係を形成するのが一般的です。 
- composeというものがなくても、Docker内でファイルをしっかり作成して（相互の依存性などを考慮して）作ることができますが、 
- 個別に扱わずにまとめて一度に（終了時に同時に終了し、開始時に同時に開始されるなど）実行されるような方法があると便利です -> docker compose yml文書

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2/Pasted image 20250114140123.png)
- 上のイメージではwordpressイメージ1つ、dbイメージ1つ、リンクがdb1つ 
- docker composeはインデントを考慮する必要があります


![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2/Pasted image 20250114140209.png)

- containerでは`docker run`、composeでは`docker up`


# 実習 - DockerコマンドでDockerネットワークを操作する

## Docker Composeの取り扱い


## 06_docker-compose

```
version: '3.3'

  

services:

   db:

     image: mysql:5.7

     volumes:

       - db_data:/var/lib/mysql

     restart: always

     environment:

       MYSQL_ROOT_PASSWORD: somewordpress

       MYSQL_DATABASE: wordpress

       MYSQL_USER: wordpress

       MYSQL_PASSWORD: wordpress

  

   wordpress:

     depends_on:

       - db

     image: wordpress:latest

     ports:

       - "8000:80"

     restart: always

     environment:

       WORDPRESS_DB_HOST: db:3306

       WORDPRESS_DB_USER: wordpress

       WORDPRESS_DB_PASSWORD: wordpress

volumes:

    db_data:
```
- 上記をよく見ると、複数値が来る場合は`- test: val1/val2`のように-を付けて書き、単一値はそのまま書きます
- インデントを考慮する必要があります
- 後でymlファイル構造について簡単に学習する予定です
### #1. detachedモードでymlを実行

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2/Pasted image 20250114141353.png)

`docker-compose up -d` 
同じディレクトリにあるのでパス指定はしていません

  

### #2. 実行中のサービスを表示

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2/Pasted image 20250114141807.png)
`docker-compose ps`


![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2/Pasted image 20250114141852.png)

  ![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2/Pasted image 20250114141928.png)
  

### #3. 実行中のサービスを停止


![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2/Pasted image 20250114143745.png)

`docker-compose stop`


  

### #4. サービスの削除


![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2/Pasted image 20250114143804.png)

`docker-compose down`

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2/Pasted image 20250114143827.png)