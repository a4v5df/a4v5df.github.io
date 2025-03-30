---
title: Container day_3
date: 2025-03-15
categories: [container]
math: true
excerpt: DockerやAzureを利用したDevOpsの基本概念と、開発・運用を統合して自動化を促進するための組織構造やGitのブランチ管理方法を学びます。
---

# Azure Container Instance (ACI)

==参考== IAC (Infrastructure As Code): コードを使ってインフラを定義すること（コードをJSONで宣言するため再利用性が高まり、コードで記述することで人の介入が減り、エラーが発生する可能性が低くなる）。Azure CLIでは順次line by lineで進行しますが、IACは大きな枠で一度に作成します。

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_3/Pasted image 20250115095025.png)

IACを使用して、共同予約グループに1つのテンプレートで複数のコンテナを搭載することができます。

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_3/Pasted image 20250115095413.png)

- コンテナインスタンスは作成時にポートを指定しないと修正できません。
- コマンドの再定義: DockerfileのCMD行に定義されたものを再定義可能です。

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_3/Pasted image 20250115110449.png)

- まずDockerHubからの取得実習から始めます：その他のレジストリをクリック（下の警告表示は動作が遅くなる可能性を示しています）。

- 設定事項：
```
#1. Azureポータル全体で「Container Instances」を検索
#2. Container Instancesブレードで[+作成]
*言及していない項目はすべてデフォルト値を使用。
[基本事項]
-リソースグループ: rg-containerlab-[SUFFIX]
-コンテナ名: aci-gb-[SUFFIX]
-地域: <指定した地域>
-SKU: 標準
-イメージソース: Azure Container Registry
-レジストリ: cr[SUFFIX]
-イメージ: guestbook-app
-イメージタグ: v1
[ネットワーク]
-DNS名ラベル: studygb[SUFFIX]
-ポート: 3000, TCP
[モニタリング]
-インサイト: 使用しない
[高度な設定]
-再起動ポリシー: 常に
#3. テスト
#4. その他のレジストリでDocker Hubに先行してデプロイしたゲストブックイメージを使用してコンテナインスタンスを追加デプロイ
```

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_3/Pasted image 20250115112544.png)

- IPアドレス、DNSのどちらでも接続可能（ただしポート番号を指定する必要があります）

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_3/Pasted image 20250115112622.png)

- ポート番号3000を指定しないと接続できません

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_3/Pasted image 20250115112741.png)

- ここにPOSTすると

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_3/Pasted image 20250115112756.png)

- ログにPOSTした記録が残り、GETなども残ります

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_3/Pasted image 20250115112816.png)

- コンテナのbashにも接続可能です

==コンテナ項目のイベント、属性、ログ、接続から多くの情報（CPUコア、メモリ性能、ログ）を得て、制御することができます==

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_3/Pasted image 20250115113419.png)

- このようにWebソケットエラーが発生することがありますが、その際には概要から再起動を試してみてください

# Azure Web App for Container

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_3/Pasted image 20250115114215.png)

- アプリでサポートされている技術スタック（言語）にKotlinがない場合？ -> Container（Windows、Linuxコンテナ両方可能）で搭載可能

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_3/Pasted image 20250115114404.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_3/Pasted image 20250115114843.png)

- 以下の項目を参照
```
#1. Azureポータル全体で「App Services」を検索
#2. App Servicesブレードで[+作成]
言及していない項目はすべてデフォルト値を使用。
[基本事項]
-リソースグループ: rg-containerlab-[SUFFIX]
-名前: app-gb-[SUFFIX]
-発行: コンテナ
-オペレーティングシステム: Linux
-地域: <指定した地域>
-価格プラン: asp-container-[SUFFIX] / S1
[コンテナ]
-イメージソース: Azure Container Registry
-レジストリ: cr[SUFFIX]
-イメージ: guestbook-app
-イメージタグ: v1
[モニタリングとセキュリティ]
-Application Insights: いいえ
#3. テスト
#4. DAY1のdocker-compose.ymlを使用してWordPressをデプロイする
```

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_3/Pasted image 20250115115623.png)

- クリックして
![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_3/Pasted image 20250115115650.png)

- 有効にする
SCM（ソースコントロールマネージャー）

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_3/Pasted image 20250115115820.png)

- ログがすべて表示されます

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_3/Pasted image 20250115121219.png)

- ここでは3000番ポートを指定していません

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_3/Pasted image 20250115121551.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_3/Pasted image 20250115121836.png)

### Docker Composeでの変更

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_3/Pasted image 20250115133522.png)

- 構成に入力されたファイルは.yamlファイルです

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Container/Container day_3/Pasted image 20250115133606.png)

- このように変更されます