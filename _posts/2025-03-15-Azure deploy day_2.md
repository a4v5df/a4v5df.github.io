---
title: AzureDeploy_day_2
date: 2025-03-15
categories: [cloud]
math: true
excerpt: "Azureのデプロイスロットを利用したCI/CD環境の構築方法と、サーバーレスアーキテクチャ（Azure Functions）の概要および実習手順を整理しました。"

---

# デプロイスロット概要

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110103347.png)

開発からGitHubへのプロセスをCI（Continuous Integration）と呼び、GitHubからStaging SlotへのプロセスをCD（Continuous Deployment/Delivery）と呼びます。

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110103451.png)

スワップを一度行い、再度スワップすることでロールバック機能のようなことも可能です。

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110103524.png)
- ABテスト: 一部のユーザーに新機能を持つサービスを体験させ、ベータテストのフィードバックを得る方法
- スロットのヘッダー値（name == staging, name == self）を見て識別可能

-----

## 実習

### 1. Azureポータルでdevtestスロットを追加

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110103834.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110103858.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110104106.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110104219.png)

### 2. VS Codeでstagingスロットを追加

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110110352.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110104227.png)

### 3. ゲストブックコード修正

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110104428.png)
![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110104458.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110104547.png)

- ゲストブックサイトを日本語に変更する作業中

### 4. devtestスロットにデプロイ

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110105053.png)

### 5. Azureポータルでスロットスワップ

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110105511.png)

### 6. VS Codeでスロットスワップ

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110110009.png)

### 7. トラフィックルーティング

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110105856.png)

- 実際にはすぐにProductionスロットにデプロイするのは良くない（使用中のサービスに影響が出るため）、まずStagingスロットにデプロイし、徐々にデプロイするのが望ましい。

---
## 実習 7 サンドイッチページ

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110112339.png)

- imgstorweb: バックエンド
- ManagePlan: 価格プラン
- imgstorapi: フロントエンド
- imgstor: ストレージ
- images: Blobストレージ

## 02_setup_lab10

### 1. ストレージアカウント作成
- リソースグループ: rg-paaslab
- 名前: stimg\[SUFFIX]
- 位置: East US
- パフォーマンス: 標準
- レプリケーション: LRS

### 2. 作成したストレージアカウントの接続文字列を記録

ストレージに接続するために、接続文字列を環境変数として指定します。
![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110113110.png)

### 3. コンテナ作成

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110113215.png)
- 名前: images
- 公共アクセスレベル: Blob（Blobのみの匿名読み取りアクセス）

### 4. サンプルBlobアップロード

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110113332.png)
- grilledcheese.jpg

### 5. API Webアプリ作成

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110113715.png)
\[基本]
- リソースグループ: rg-paaslab
- 名前: api\[SUFFIX]
- 公開: コード
- ランタイムスタック: .NET 6 (LTS)
- オペレーティングシステム: Windows
- 位置: East US
- Windowsプラン: 新規作成 -> asp-imgapp / スタンダードS1
独自の基本ホスト名（プレビュー）を使用する選択肢は不要（選択するとURLが複雑になる）

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110113722.png)
\[モニタリング]
- Application Insights使用: いいえ

### 6. FE Webアプリ作成

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110113935.png)
\[基本]
- リソースグループ: rg-paaslab
- 名前: web\[SUFFIX]
- 公開: コード
- ランタイムスタック: .NET 6 (LTS)
- オペレーティングシステム: Windows
- 位置: East US
- Windowsプラン: asp-imgapp 
\[モニタリング]
Application Insights使用: いいえ

## 03_config_web_api

### 1. APIアプリ構成

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110114218.png)
StorageConnectionStringで先ほど入力した文字列を環境変数から取得可能
- 名前はWebサービスを作成するコード内の変数名

- 名前: StorageConnectionString
- 値: ストレージアカウント接続文字列

### 2. Webアプリ構成

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110114631.png)
- 名前はWebサービスを作成するコード内の変数名

- 名前: ApiUrl
- 値: APIアプリのURL

### 3. APIアプリコードデプロイ

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110115159.png)
- Line 18を実行して `provisioningState : "Succeeded"` となれば成功

実行コード: `az webapp deployment source config-zip --resource-group rg-paaslab-pym --src api.zip --name apipym`

### 4. APIアプリ動作テスト

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110115234.png)
- 先ほどアップロードしたgrilledcheeseイメージ

### 5. Webアプリコードデプロイ

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110115603.png)

実行コード: `az webapp deployment source config-zip --resource-group rg-paaslab-pym --src web.zip --name webpym`

### 6. Webアプリ動作テスト

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110115706.png)

### Webから画像をアップロードすると

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110122237.png)

### API Webに画像が追加されます

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110122326.png)

## Azure Function

AWSのLambdaに似ているとのことです。

## サーバーレス

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110133457.png)

- サーバーレスの特徴
  - 実際にはサーバーが存在しますが、ユーザーがサーバーについて気にする必要がなく、完全に抽象化されていることを意味します。
  - イベントドリブン
  - 使用型モデル（料金プラン）とも呼ばれ、コードが呼び出されて実行された分だけのコストが決まります。

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110133643.png)

- サーバーレス
  - 自動スケール設定が自動です。
  - スピンアップ時間が短く、迅速に呼び出され迅速に実行されます。
  - 呼び出された時だけ実行され、その時間だけ料金が請求されます（イベントドリブンの料金構造）

- PaaS
  - 自動スケーリングオプションを設定する必要があります。
  - 最初の接続時に時間がかかります。

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110134007.png)

- Azureが提供するFunctionとしてのサービス

- 統合プログラミングモデル
  - トリガーモデル: イベントを選択すると、そのイベントで選択されたコードを書く方法を提供します。
  - バインディングモデル: データ処理時、データをどこから取得してどこに送るかをコードで制御せず、.jsonで行うことを意味します。

- 強化された開発環境: 複数の拡張機能があり、ローカルでの開発環境を提供（初期開発コスト削減）
- 柔軟なホスティングオプション: 目的に応じた複数の料金プランを提供

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110134452.png)

Durable Functions: 基本的にfunctionはステートレスですが、functionが互いを呼び出す際、functionは短時間の実行が一般的ですが、30〜40分間実行される長時間の関数のタイムアウトを解決するために提供されます。

統合: 複数のソリューションをつなげる役割を果たすことができます（例: Gmailに来るイベントに応じてfunctionが実行される場合、GmailがSaaSサービスであることを考慮し、これらを連携して実行します）。

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110134951.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110135919.png)

[反応するイベントは定義されています](https://learn.microsoft.com/ja-jp/azure/azure-functions/functions-triggers-bindings?tabs=isolated-process%2Cnode-v4%2Cpython-v2&pivots=programming-language-csharp)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110140042.png)

- もし消費料金プランの最大値10分を超える場合、消費料金プランでは不可能で、他の料金プランに変更する必要があります。

- 一般的に関数型コードは実行ロジックのみを記述するため、コードが長くなく、大きなリソースを必要としません（少ないメモリ、低いCPUパワーで可能）。

- 継続的に呼び出すと多額の料金が発生する可能性があるため、プレミアムプラン、またはAzure FunctionsでなくApp Serviceを使用する方が良い場合があります。
==料金を継続的に監視し、効率的な方法を選択しましょう==

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110140510.png)

バインディングモデル: コードではなく、実際に使用するストレージサービスの環境変数（接続文字列）に入れると自動的に処理されます（データを処理しない関数はこの機能が不要ですが、データを処理する場合は必要です）、（単一の入力、出力でなく、複数も可能）（json形式で）

コンパイル言語（C#, Javaなど）は、それぞれの言語に合った方法で構成

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110140907.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110141042.png)
適切なものを選びましょう

## Azure Functionデプロイ

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110143645.png)

- サービスプラン
  - CSP
  - ASP
  - プレミアムプラン
ストレージアカウント: コード、ログ、バインディング関連ファイルが保存されるため、指定する必要があります。
  - Function環境変数に表示されています

------

## 実習 8

## 01_deploy-function_portal

### 2. 関数アプリ作成

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110144505.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110144634.png)

\[基本]
- リソースグループ: rg-paaslab
- 関数アプリ名: fapp-\[SUFFIX]-01
- 公開: コード
- ランタイムスタック: Node.js
- バージョン: 18 LTS
- 地域: Korea Central
- OS: Linux
- プランタイプ: 使用量

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110144704.png)

\[ホスティング]
- ストレージアカウント: stfunc\[SUFFIX]

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110144727.png)

\[モニタリング]
- Application Insight: いいえ

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110150248.png)

### 3. 関数作成

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110150459.png)

- 開発環境: ポータルで開発
- テンプレート選択: HTTPトリガー

\[テンプレート詳細]
- 新規関数: SayHello
- 認可レベル: Anonymous

### 4. 関数テスト: {コード + テスト}

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110150534.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110150643.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110150839.png)
\[入力]- クエリ
- 名前: name
- 値: tony

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110150855.png)

\[出力] 確認

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110150922.png)

\[入力]
- 名前 : NULL
- 値 : NULL

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110150932.png)
\[出力]

### 5. 関数テスト: 関数URL

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110151019.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110151114.png)
- name= tony

## 02_deploy-functions_vscode

### 1. Function App作成

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110151848.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110151823.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110151906.png)

\[Create Function App in Azure...(Advanced)]
- 関数アプリ名: fapp-\[SUFFIX]-02
- ランタイムスタック: Node.js 18 LTS
- OS: Linux
- リソースグループ: rg-paaslab
- ホスティングプラン: Consumption
- ストレージアカウント: stfunc\[SUFFIX]
- Application Insight: Skip for now

### 2. (VS Code) Function拡張からデプロイした場合、読み取り専用であることを確認。

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110152317.png)

### 3. ポータルでの関数セクション機能比較

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110152346.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110152430.png)

### 4. ポータルで関数作成後VS Codeで確認

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110152519.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110152559.png)

### 5. URLで確認

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110152708.png)

## 03_deploy-img-manager

### 1. Pass

### 2. Azure Function Appデプロイ

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110154921.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110154748.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110155121.png)

\[基本]
- リソースグループ: rg-paaslab
- 関数アプリ名: fapp-\[SUFFIX]-03
- 公開: コード
- ランタイムスタック: .NET
- バージョン: 6
- 地域: 韓国中部
- オペレーティングシステム: Windows
- プランタイプ: 使用量

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110155202.png)

\[ホスティング]
- ストレージアカウント: stimg\[SUFFIX]

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110155218.png)

\[モニタリング]
Application Insights使用: いいえ

### 3. Azure Function App構成

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110163532.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110163800.png)

\[関数追加]
- テンプレート: Azure Blob Storageトリガー
- 名前: ImageManager
- パス: images/{name}
- ストレージアカウント接続: AzureWebJobsStorage

### 4. function.projファイルをImageManager関数にアップロード

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110170436.png)
- ここでfuncction.projファイルをポータルで開くと空なので、VS Codeでコピーしてポータルに貼り付け
- 元々空だそうで、Azureの初期からそうだったとのこと。

### 5. function.jsonファイル修正

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110170636.png)
- VS Codeで開いてコピーしてポータルに貼り付け

### 6. run.csxファイル修正

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110171040.png)

- VS Codeで開いてコピーしてポータルに貼り付け
- コード内容: アップロードした画像をロード（Load）→ サムネイル形式に変更（Resize, GrayScale）→ 画像を保存（SaveAsPng）

### 7. 画像をアップロードしてリフレッシュで完了！

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure deploy day_2/Pasted image 20250110171212.png)