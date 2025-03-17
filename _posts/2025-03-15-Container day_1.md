---
title: Container day_1
date: 2025-03-15
categories: [container]
math: true
excerpt: Docker環境の構築方法から基本的なDockerコマンドの使用法（イメージの検索、ダウンロード、タグ付け）、Dockerfileの記述方法とイメージのビルドプロセスなど、コンテナの基礎知識を詳しくまとめました。

---

# Container開発環境

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_1/Pasted image 20250113145558.png)

## 01_Docker Desktopのセットアップ

### #1. WSL 2を有効化
```
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux -NoRestart
```

### #2. ‘Virtual Machine Platform’を有効化
```
Enable-WindowsOptionalFeature -Online -FeatureName VirtualMachinePlatform
```

### #3. Linuxカーネル更新パッケージのダウンロードとインストール
[ダウンロードリンク](https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi)

### #4. WSL 2をデフォルトに設定
```
wsl --set-default-version 2
```

### #5. Azure CLIのインストール
[Windows用インストールガイド](https://docs.microsoft.com/ko-kr/cli/azure/install-azure-cli-windows?tabs=azure-cli)

### #6. Docker Desktopのダウンロード
[ダウンロードリンク](https://hub.docker.com/editions/community/docker-ce-desktop-windows)

### #7. Docker Desktopのインストール後、再起動

# コンテナ技術の概要

コンテナとは、アプリケーションとその実行環境を仮想化して分離することで、異なるオペレーティングシステムや実行環境同士が互いに影響を与えないようにする技術です。初期のUnixでは、プログラムの問題がオペレーティングシステムのクラッシュを引き起こし、コンピューターを再起動しなければならない問題がありました。コンテナは、このような問題に対処するために開発されました。

1982年にchrootとして登場しましたが、カーネルレベルでのコーディングが必要だったため、一般的には普及しませんでした。2008年にLinux Container (LXC) が登場し、より一般的に利用されるようになりましたが、ネットワークやストレージ状態による問題、セキュリティの課題が残っていました。

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_1/Pasted image 20250113150654.png)

### 仮想マシンとコンテナの違い

**仮想マシン**は、ハードウェア全体を仮想化し、オペレーティングシステム、CPU、メモリなどを完全に模倣します。そのため、ハードウェアの移動時にはネットワークトラフィックが増加します。

**コンテナ**は、アプリケーションを実行するための環境のみを仮想化します。オペレーティングシステムの一部（カーネルを含まず、ユーザーランドの一部のみ）を含む構造で、数MBのサイズであるため、ネットワーク上での通信と処理が速やかに行えます。

マイクロサービスアーキテクチャは、コンテナを組み合わせてプロジェクトを構築するのに適しています。

コンテナはオペレーティングシステムの一部のみを使用するため、仮想マシンよりもコストが低く、データサイズが小さいためオーケストレーションが容易で、多くの開発者に利用されています。

## Dockerサービスの概要

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_1/Pasted image 20250113151327.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_1/Pasted image 20250113151841.png)

Dockerの主なコンポーネントには以下があります。

- **dockerd (Docker Daemon)**: Docker Engine APIから実行命令を受け取ります。
- **Docker Client (CLI)**: Dockerのコマンドラインインターフェースです。
- **docker image**: Dockerコンテナを作成するためのテンプレートです。
- **docker registry**: Dockerイメージを保存するリポジトリです。
- **docker compose**: 複数のコンテナを管理し、依存関係を記述するためのツールです。
- **docker swarm**: Dockerのオーケストレーションツールで、Kubernetesのような機能を提供します。

オーケストレーションサービスは、複数のDockerコンテナを管理し、ロードバランサのような役割を果たします。

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_1/Pasted image 20250113160346.png)

Kubernetesをローカルで有効化することも可能ですが、リソースが不足しているとパフォーマンスが低下します。

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_1/Pasted image 20250113175055.png)

- **Dockerfile**
  - 紫色: 命令
  - 青色: 命令のパラメータ
  - 各行の命令はフォトショップのレイヤーのように考えることができます。

コンテナは実行中の状態で、イメージは実行前のファイル状態です。

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_1/Pasted image 20250113161356.png)

Docker HubはGitHubに似た役割を果たします。Docker hostはローカルコンピュータに相当し、Docker serverとDockerはREST APIを介して通信します。

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_1/Pasted image 20250113162110.png)

リポジトリ内のリソースは重複できないため、namespace（区別するための識別子）を付けますが、検索時には省略できます。

## 02_docker.azcil

### #0. sudoを使わずにDockerを利用
```
sudo usermod -aG docker tony

sudo -su tony

sudo chmod 666 /var/run/docker.sock
```

### #1. イメージの検索
```
docker search --limit 5 mysql
```

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_1/Pasted image 20250113162706.png)

Dockerにあるイメージの中には悪意のあるものもあるため、STARSが多く、公式のイメージを選択することが重要です。

### #2. イメージのダウンロード

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_1/Pasted image 20250113163008.png)

- 青色: バージョン (latest: 最新)
- プルのプロセスが長い理由は、前述のレイヤリングによりネットワーク負荷が少ないためです。

Docker HubレジストリからASP.NET Sampleアプリイメージを検索します。
```
docker pull mcr.microsoft.com/dotnet/samples:aspnetapp

docker pull mcr.microsoft.com/azuredocs/aci-helloworld
```

### #3. イメージのリスト確認
```
docker image ls

docker images
```

### #4. イメージにタグ（新しい名前）を追加

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_1/Pasted image 20250113164353.png)

image IDを確認すると、同じイメージであることがわかります。タグ機能はプライベートリポジトリにイメージをアップロードする際に多く使用されます。
```
docker image tag mcr.microsoft.com/azuredocs/aci-helloworld hello.docker/aci-helloworld:latest

docker tag mcr.microsoft.com/dotnet/samples:aspnetapp hello.docker/samples
```

`mcr.microsoft.com/azuredocs/aci-helloworld` -> `hello.docker/aci-helloworld:latest` で別名を付けたことになります。image IDを見ると同一であることが確認できます。

## Dockerfileとイメージビルド

### Dockerfileの構造

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_1/Pasted image 20250113170141.png)

- **FROM**
  - `node`: イメージ名 / `8.9.3-alpine`: バージョン
- **RUN**: ビルド時に実行されます。
  - `-p`: フォルダ作成時に親フォルダも作成
- **COPY**:
- **WORKDIR**: 作業ディレクトリを指定
- **RUN**
  - `npm install`: ノードモジュールの生成
- **CMD**: コンテナ化されたときに実行
  - `node /usr/src/app....`: ノードコマンドで実行
  - 空白がある場合は文法1の"命令"を使用

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_1/Pasted image 20250113170155.png)

- **ADD**
  - `. /app`: `.`（元の場所）から`/app`を追加
  - ADDは元の場所がリモートの位置にある場合に使用（例: 元がGitHubにある状況）

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_1/Pasted image 20250113171915.png)

Dockerfileは慣例的にファイル名を`Dockerfile`としますが、別の名前にする場合は`-f<別のファイル名>`を指定します。

`docker build . -t guestbook`の`.`は現在の位置を示します。

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_1/Pasted image 20250113173030.png)

- 赤いコマンドを実行後、緑のコマンドを実行すると`guestbook-app, firstnode-app`がimagesに現れます。
  - 参考: 作成環境に応じてライブラリが異なるため、サイズが異なる可能性があります（Windows 10とWindows 11でのライブラリバージョンの違いなど）。ファイルシステムごとの割り当てサイズが異なるため、見た目のサイズも異なることがあります。