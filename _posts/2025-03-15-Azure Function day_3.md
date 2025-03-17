---
title: Azure Function day_3
date: 2025-03-15
categories: [cloud]
math: true
excerpt: "Azure Functionsをローカル環境（VS Code）で作成し、Azureへのデプロイ方法やCORSの設定、静的ウェブサイトと組み合わせたフルスタックサーバーレス構成の実践手順をまとめました。"

---

# Local環境でAzure Functionsを開発する

- AzureポータルでFunctionを作成することはあまりありません。（通常はVScodeやIntelliJなどのローカルIDEで作成します）

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Function day_3/Pasted image 20250113091820.png)
[Azure Functions Core Tools GitHub](https://github.com/Azure/azure-functions-core-tools)
- VScodeに連携されているため、Function Toolをインストールした後、VScodeを再起動する必要があるかもしれません。

## 04_setup-function-devenv

### 1. Azure Functions Core Toolsのインストール
- `npm i -g azure-functions-core-tools@4 --unsafe-perm true`

### 2. Azure Functions Core Toolsの確認
- `func`と入力

### 3. ローカルFunctions Appの作成

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Function day_3/Pasted image 20250113092759.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Function day_3/Pasted image 20250113093039.png)
- JavaScriptを選択した際のプロジェクト構造
1. Portやプロトコルを選択するファイル
2. データファイルのプレースホルダー（サンプルなので削除可能）
3. launch.json：選択したランタイム（node, java, pythonなど）に関する情報

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Function day_3/Pasted image 20250113093605.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Function day_3/Pasted image 20250113094352.png)

## 実習 11 | ローカルFunctions Appの開発とAzureへのデプロイ

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Function day_3/Pasted image 20250113094805.png)
- これをクリックし、以下の設定を行います。
- プロジェクトフォルダ: fapp-tripadvisor（Function Appプロジェクトフォルダ）
- 開発言語: JavaScript 
- 関数テンプレート: HTTP trigger
- 関数名: SetLocation
- 認証レベル: Anonymous
- Open in current Window

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Function day_3/Pasted image 20250113101714.png)

### 4. index.jsの修正

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Function day_3/Pasted image 20250113102041.png)

### 5. ローカル関数の実行
- デバッグ: F5 / \[RUN]-\[Start Debugging]
- デバッグなしで実行: Ctrl+F5 / \[RUN]-\[Run Without Debugging]

- もし実行できない場合は、`Set-ExecutionPolicy`コマンドで実行ポリシーを設定してください。

### 6. ブラウザで実行結果を確認

### 7. ローカルFunctions AppをAzureにデプロイする

Functionを作成する2つの方法
![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Function day_3/Pasted image 20250113104117.png)

詳細設定を行う場合はこちらを使用
![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Function day_3/Pasted image 20250113103353.png)

- \[VS Code-Azure Extension] Deploy to Function App

Function Appの設定
  - リソースを選択: fapp-\[SUFFIX]-01
  - ホスティングプラン: Consumption
  - ロケーション: Korea Central
  - Nodeバージョン: v20
  - OS: Linux
  - リソースグループの作成: rg-paaslab-\[Suffix]
  - ストレージアカウント: stfunc\[Suffix]
  - Application Insight: スキップ

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Function day_3/Pasted image 20250113104255.png)

マイクロサービスでこのような関数型コードを使用して多く実装されます。以前のApp Serviceよりも一段進化した形で実装可能です。

### 8. Azureのfapp-\[SUFFIX]-02にデプロイされた関数URLを確認

### 9. ブラウザでURL呼び出し結果を確認

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Function day_3/Pasted image 20250113105421.png)


## フルスタックサーバーレス（静的ウェブ + Azure Functions）
大規模サービスを迅速に作成する際に使用。静的データがキャッシュしやすい特性を利用して、アクセスが集中した際のサーバー負荷を軽減します。例えば、外国人ユーザーが韓国のサービスにアクセスする場合、外国人は自身の国のサーバーに接続するのがユーザーにとっても速く、韓国サーバーにとっても負荷が分散されるため、この方法を採用します。そして、ウェブで提供されるデータが静的であるため、外国サーバーにキャッシュするのに適しています。これをCDN（Contents Delivery Network）と呼びます。静的データの例には、HTML、CSS、クライアントサイドのJavaScript、音声、ビデオ、画像などがあります。

ユーザーがデータを入力するようなサービスは静的ウェブではないため、このような場合、データベースを分散する必要があります。この場合、クラウドが提供するサービスで分散可能です。（ここで複数のAPIが必要ですが、Azure Functionで解決できます）

コンテナストレージのBlob Storageに非構造化データを保存できます。このデータをアクセスできるエンドポイントを1つ生成し、それにウェブを接続すればウェブサービスが完成します。また、CDNを接続すれば分散複製が可能です。

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Function day_3/Pasted image 20250113112024.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Function day_3/Pasted image 20250113112219.png)

インデックス文書名: 最初に呼び出される文書
URL構成: ストレージアカウント名 + 地域番号 + "web.core" -> Azureが提供するDNS、外部DNSサービス、または独自に作成してDNSに変更可能

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Function day_3/Pasted image 20250113112428.png)

3. AzCopy: DOS copyと類似（Amazon S3もコピー可能、逆は不可能）
5. GitHubとパイプラインを構成して一度にアップロード可能
今日は6、7番、5番は後でGitHub Actionで試す予定

## 実習 12 | 希望旅行地登録静的ウェブサイトの実装

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Function day_3/Pasted image 20250113112625.png)

### 1. TripAdvisorWeb.zipを解凍

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Function day_3/Pasted image 20250113113326.png)
- FE（クライアントサイド）ペルソナと考えてください

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Function day_3/Pasted image 20250113113437.png)
- Fetch APIを使用するコード部分

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Function day_3/Pasted image 20250113113546.png)
- Azure Storageを扱う拡張機能

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Function day_3/Pasted image 20250113114004.png)
- ストレージ名: sttrip\[Suffix]

### 2. index.htmlの修正

index.htmlファイルのL: 行番号
L5: `<title>Trip Advisor</title>`
L15: `<h1 class="display-4 mb-4">希望旅行地入力</h1>`
L20: `<input type="text" id="name" class="form-control" placeholder="行きたい旅行地を入力してください...">`
L22: `<input type="submit" class="btn btn-secondary" onclick="submitMessage(event)" value="登録">`
L55: `${text}`

### 3. Live Serverのローカルウェブサーバでウェブサイトを確認

2. 正しくアップロードされたか確認: FE VScodeのsttrippymを右クリックし、deploying -> blob containerで確認

### 4. （ポータル）stimg\[SUFFIX]の静的ウェブサイトを有効化
\[データ管理]-\[静的ウェブサイト]
- インデックス文書名: index.html
- エラードキュメントパス: error.html

### 5. ストレージアカウントを作成
- リソースグループ: rg-paaslab
- 名前: sttrip\[SUFFIX]
- 地域: Korea Central
- 冗長化: LRS

### 6. (VS Code) sttrip\[SUFFIX]の静的ウェブサイトを有効化

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Function day_3/Pasted image 20250113115435.png)
Configure static websiteウィンドウで
- インデックス文書名(1/2): index.html
- エラードキュメントパス(2/2): error.html

\[Configure Static Website...]
contain -> access keyに切り替え -> index.htmlファイルを確認可能

### 7. (VS Code) sttrip\[SUFFIX]の静的ウェブサイトにデプロイ
\[Deploy to Static Website via Azure Storage]

## CORS（Cross-Origin Resource Sharing）問題

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Function day_3/Pasted image 20250113120615.png)

私が作成したAPIを呼び出すには、このように呼び出してくださいという意味です。

このようにしてFEとBEの両方を実行しても、入力欄に入力しても反応がありません。

#### CORS問題
![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Function day_3/Pasted image 20250113120942.png)

この問題をCORS（Cross-Origin Resource Sharing）と言います。FEとBEのURLが異なるためです。
（FEは11番街で、BEはGマーケットのような比喩：ドメインが異なるため、セキュリティ上ブロックされます）-> 解決するには、相手のドメインが信頼できるドメインとして登録されている必要があります。
- 現在はローカルで実行しているので、`CORS : "*"`ワイルドカードで設定

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Function day_3/Pasted image 20250113121726.png)
status : 例外処理のようなもの

## 実習 13 | ウェブサイトのフルスタックサーバーレス実装  

### 1. ローカルFunctions AppのCORS設定

\[fapp-tripadvisor]の\[local.settings.json]

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Function day_3/Pasted image 20250113121356.png)
- `
	"Host":{
	    "LocalHttpPort":7071,
	    "CORS":"*",
	    "CORSCredentials":false
	}`

### 2. \[fapp-tripadvisor]のSetLocation関数内のindex.js修正（上記で実施）
L6: "リクエストされた " + name + " を希望旅行地として登録しました。"
L7: status

### 3. Trip Advisor静的ウェブサイトのAPI呼び出し修正

### 4. ローカルFunctions App\[fapp-tripadvisor]をAzureのfapp-\[SUFFIX]-02にデプロイ（先に実施）

### 5. Trip Advisor静的ウェブサイトのAzure FunctionsのSetLocation API呼び出し修正

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Function day_3/Pasted image 20250113134103.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Function day_3/Pasted image 20250113134153.png)

L30: `fetch('http://localhost:7071/api/SetLocation', {...`
L30をコピーしてFunction URLに変更

### 6. Azureのfapp-\[SUFFIX]-02の\[API]-\[CORS]設定

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Function day_3/Pasted image 20250113134241.png)

登録をクリックしても表示されない：CORS問題（フロントはローカルでバックエンドはクラウドのFunctionなので、ドメインが異なり問題が発生）

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Function day_3/Pasted image 20250113141524.png)

- 許可されたオリジン: *

これを許可すれば良いのですが、ローカルでテストしているのでローカルホストを許可されたオリジンにする必要があります -> しかし、ローカルがどこかを知ってどうやって設定しますか？ -> そこでワイルドカード * を使用します（ただし、これはセキュリティ上推奨されていません。短時間のテストでのみ使用してください）

そして登録すれば完了です。
![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Function day_3/Pasted image 20250113134605.png)

ここまででバックエンドはFunctionでクラウド、フロントはローカルです。

### 7. ローカルTrip Advisor静的ウェブサイトをAzureストレージの静的ウェブサイトにデプロイ

フロントをクラウドにアップロード
![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Function day_3/Pasted image 20250113135018.png)

これでファイル（CSS、index.html、JSファイル）がアップロードされます。

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Function day_3/Pasted image 20250113135657.png)

- 上記のURLがブラウザに入力するURLです。

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Function day_3/Pasted image 20250113135827.png)
- こちらも同じサイトに接続します。

### 8. Azureのfapp-\[SUFFIX]-02の\[API]-\[CORS]設定変更

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Function day_3/Pasted image 20250113135520.png)

- そして、先ほどローカルでテストしたときは * ですべてを開放していましたが、今はクラウドにURLができたので、それを指定して変更します。
- 許可されたオリジン: `https://sttrip.z12.web.core.windows.net`