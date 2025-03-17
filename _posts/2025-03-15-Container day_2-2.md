---
title: Container day_2-2
date: 2025-03-15
categories: [container]
math: true
excerpt: Dockerを使用したプライベートコンテナレジストリの構築とAzure Container Registry（ACR）の設定方法を解説します。イメージのタグ付け、アップロード、管理ユーザー設定、Azure CLIを使った効率的なビルド・デプロイまで、実践的な手順を詳しく紹介しています。

---

# コンテナレジストリ

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2-2/Pasted image 20250114144124.png)

Registry（$\approx$ イメージライブラリ） $\in$ Repository -> イメージを取得して -> 実行するとコンテナになります。

## プライベートレジストリの実装方法

- オンプレミスでの実装、クラウド（Azure）での提供。
- 使用理由:
  1. 作成したものを他人に使わせたくない場合。
  2. DockerHubは世界中に広がっているため、少し遅くなる可能性があります。そのため、独自に使用すればより高速に取得できます。

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2-2/Pasted image 20250114144904.png)

オレンジの四角：バージョン1、バージョン2...
レジストリ $\in$ リポジトリ（上の画像のようにバージョンのスナップショットが集まったリポジトリ）

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2-2/Pasted image 20250114150138.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2-2/Pasted image 20250114145729.png)

アップロード可能なもの
- イメージ
- Helm（ヘルムと読む）：k8sのパッケージ管理ツール（npmのようなもの）
- Composeのyamlファイル

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2-2/Pasted image 20250114145948.png)

Using 0 of 1 private repositories : アカウントごとに1つのプライベートリポジトリを無料で作成できます。
ユーザー名がnamespaceの役割を果たすため、リポジトリ名が同じでも、namespace + リポジトリ名がユニークであればアップロード可能です。

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2-2/Pasted image 20250114150306.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2-2/Pasted image 20250114150156.png)

# Azure Container Registry (ACR)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2-2/Pasted image 20250114155516.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2-2/Pasted image 20250114160114.png)

- 中間でSKUを変更することはできません（新しく作成する必要があります）。

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2-2/Pasted image 20250114160402.png)

- リポジトリオプションにバージョン、タグがあります（pushすると自動で作成されます）。

# 実践

## 02_acr

### #1. Azureに接続（またはポータルログイン）

```
az login
```

### #2. レジストリ用リソースグループを作成（または既存のリソースグループを使用）

```
az group create --name rg-containerlab-pym --location koreacentral
```

### #3. ベーシックコンテナレジストリを作成（またはポータルで作成）

```
az acr create --resource-group rg-containerlab-pym --name crpym --sku Basic
```

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2-2/Pasted image 20250114161121.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2-2/Pasted image 20250114161508.png)
- 管理ユーザーを有効化
- ログインサーバー：namespaceの役割を果たします。

### #4. ACRインスタンスにログイン

```
az acr login --name crpym
```

### #5. コンテナレジストリインスタンスの完全なログインサーバー名を取得

```
az acr list --resource-group rg-containerlab-pym --query "[].{acrLoginServer:loginServer}" --output table
```

### #6. ACRログインサーバーの正規化された名前を使用してイメージにタグを付ける

```
docker tag guestbook-app crpym.azurecr.io/guestbook-app:v1
```
ここで`crpym.azurecr.io`を見て、ACRであることが理解できます。

### #7. DockerイメージをACRに登録

```
docker push crpym.azurecr.io/guestbook-app:v1
```

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2-2/Pasted image 20250114163151.png)

- 完了後の確認

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2-2/Pasted image 20250114163124.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2-2/Pasted image 20250114163228.png)

### 拡張機能の参考

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2-2/Pasted image 20250114163542.png)
- レジストリを接続できます。

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2-2/Pasted image 20250114163651.png)

- CLIを使用せずに削除可能

==ここでローカルのguestbookをすべて削除した状態==

### #8. レジストリのリポジトリからアップロードされたイメージを一覧表示（またはポータルを使用）

```
az acr repository list --name cr[SUFFIX] --output table
```

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2-2/Pasted image 20250114163450.png)

### #9. リポジトリのタグを一覧表示

```
az acr repository show-tags --name cr[SUFFIX] --repository guestbook-app --output table
```

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2-2/Pasted image 20250114163835.png)

### #10. コンテナレジストリからguestbook-app:v1コンテナイメージを取得して実行

```
docker run -p 8080:300 -d --name iuguestbook cr[SUFFIX].azurecr.io/guestbook-app:v1
```

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2-2/Pasted image 20250114164004.png)

### #11. 管理者ユーザーの設定

```
# ACIやAKSなどでACRのリポジトリにアクセスするために必要です。
az acr update --name <acrName> --admin-enabled true
```

### #12. 管理者ユーザーのパスワードをクエリ

```
az acr credential show --name <acrName> --query "passwords[0].value"
```

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2-2/Pasted image 20250114164101.png)

ACRのアクセスキーを見るとパスワードが2つあるため、配列形式で表示されます。

### #13. az CLIでビルドとデプロイを一度に行う

```
az acr build -t crpym.azurecr.io/newguestbook:v1 -r crpym .
```

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2-2/Pasted image 20250114165023.png)

- 完了すると、

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2-2/Pasted image 20250114164953.png)

上で3ステップに分けて行ったことを一度に実行できます。
`newguestbook`などで大文字を使用してはいけません（すべて小文字にしてください）。
このようにするとローカルに残りません。

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_2-2/Pasted image 20250114170730.png)

newguestbookがありません。