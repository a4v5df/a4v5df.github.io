---
title: Azure Solution Dev. day_1
date: 2025-03-15
categories: [cloud]
math: true
excerpt: "Azure CLIの基本操作、決定木を用いたサービス選定方法、App Serviceの料金プランおよびNode.jsアプリのAzureへのデプロイ手順を整理しました。"

---

# Azure CLI

## コマンド
- `--out table` : `--out`は出力を'table'形式で表示する
- `az account clear` : ローカルのキャッシュが壊れたときに使用

==参考== CLIでのコマンドは動詞であり、実際のプログラミングコードでの関数も動詞、変数は名詞 (コーディング規約を守ること)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Solution Dev. day_1/Pasted image 20250109104727.png)

ここで\[reference subservice]の\[ ]はあってもなくてもよい

- `az account clear` : (reference : account) / (clear : command)

==参考== [CLIコマンド関連の学習資料](https://jmespath.org/)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Solution Dev. day_1/Pasted image 20250109191727.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Solution Dev. day_1/Pasted image 20250109110037.png)
- 初めに可用性セットを作成しないと、後で追加することができないことを学びました

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Solution Dev. day_1/Pasted image 20250109110129.png)
- `--help`パラメータで説明書を見ながら作成

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Solution Dev. day_1/Pasted image 20250109110511.png)

- 必須であることを示す

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Solution Dev. day_1/Pasted image 20250109115419.png)
- ローカルの該当位置にあるファイルの名前がフレンドリーでないため
- `ren $home\.ssh\id_rsa vm-pym_key` (これはDOSコマンドです)でリネームすることを推奨します

## 決定木

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Solution Dev. day_1/Pasted image 20250109133332.png)

- Build new : クラウド環境に合わせて開発を進めるという意思決定
- マイクロサービスアーキテクチャ: 技術トレンドの一種 $\leftrightarrow$ モノリシックアーキテクチャ
- 上記のような決定木をうまくたどって、どの技術を使用するかを決めましょう。

アプリサービスでできること:
![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Solution Dev. day_1/Pasted image 20250109134238.png)

1. GitHubにコミットされると自動的にデプロイ可能? はい
2. ロードバランサーのように自動で可用性を保証するか? はい / 全世界のユーザーが接続しても対応可能か? はい
3. 他のSaaSプラットフォームから我々のものを取り込むことができる
4. 世界標準、セキュリティなどをすべて考慮して作成
5. WordPressを搭載してデプロイ可能 (WordPressをセットアップするだけで使用可能)
6. はい、本当にうまくいっているようです
7. バックエンド用途にも利用可能
8. 静的ウェブアプリとは: HTML、CSS、JSなどを作成し、動画などの静的データを持つウェブサービス $\leftrightarrow$ 動的ウェブは、ウェブページを作成したが、例: (NAVERカフェに記事を投稿すると、それをDBに保存し、その記事を探すときはDBから取得してレンダリングしてメモリに表示する: 実際にHTMLファイルが存在するわけではないので、ブックマークなどでアドレスを保存してもDB上に保存されているので、記事リストから探す感じ)

-----------
- nanoserver: 小さなサイズのオペレーティングシステムコンテナに搭載される?

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Solution Dev. day_1/Pasted image 20250109135635.png)

選択できるもの
カスタムイメージ: もしKotlinを使用したいが選択リストにない場合、作成してKotlinのものをコンテナに搭載すること

----

## Appサービス料金プラン概要

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Solution Dev. day_1/Pasted image 20250109140140.png)

- 携帯料金プランの比喩: 5Gプラン $\in$ (10GB, 80GB ...) と通話料金プラン $\in$ (無料通話, 100分 ...) という感じでしょうか? / プランは英語でplan
- 安いものは機能が少なく、高いものは多くのことを提供する

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Solution Dev. day_1/Pasted image 20250109140606.png)

- 大量購入して分けて使うことができる感じ

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Solution Dev. day_1/Pasted image 20250109141108.png)
- 無料プランはリソースを共有するプランです
- Windowsベースにはオペレーティングシステムのライセンス費用や性能向上のための様々な機能が含まれており、同じスペックでもオペレーティングシステムによって価格が異なる可能性があります

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Solution Dev. day_1/Pasted image 20250109141621.png)

- 実際に運用に使うプラン、テスト用として小さなプランなど色々あります
- Feature view : Auto scaling, Custom domainなどの機能を使えるかによって選択可能です

==後でプランを変更可能==
![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Solution Dev. day_1/Pasted image 20250109141930.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Solution Dev. day_1/Pasted image 20250109141949.png)

- Zoneをサポートしていない地域ではゾーン冗長オプションを選択できない場合があります
- 価格が低いプランの場合にもゾーン冗長オプションを選択できない場合があります

----

## 実習

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Solution Dev. day_1/Pasted image 20250109150226.png)
移動すると

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Solution Dev. day_1/Pasted image 20250109150307.png)
ここで様々なこと（PowerShellモード、拡張機能のインストールなど）が可能です

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Solution Dev. day_1/Pasted image 20250109155013.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Solution Dev. day_1/Pasted image 20250109155050.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Solution Dev. day_1/Pasted image 20250109155122.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Solution Dev. day_1/Pasted image 20250109155149.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Solution Dev. day_1/Pasted image 20250109155429.png)
regionを指定しない場合、Resource groupに従います(おそらく)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Solution Dev. day_1/Pasted image 20250109155454.png)
必要に応じて適切に選択してください

結果:
![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Solution Dev. day_1/Pasted image 20250109155646.png)

赤い枠: ブラウザで表示した際のデフォルト画面

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Solution Dev. day_1/Pasted image 20250109160257.png)

テンプレートエンジン

`Set-ExecutionPolicy -ExecutionPolicy RemoteSigned` : PowerShellでexpressコマンドが実行できない場合、管理者権限でターミナルを開いて実行 (cmdでは実行しなくてもよいのはなぜ?)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Solution Dev. day_1/Pasted image 20250109161051.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Solution Dev. day_1/Pasted image 20250109161123.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Solution Dev. day_1/Pasted image 20250109161159.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Solution Dev. day_1/Pasted image 20250109161437.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Solution Dev. day_1/Pasted image 20250109161511.png)

# デプロイ

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Solution Dev. day_1/Pasted image 20250109161651.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Solution Dev. day_1/Pasted image 20250109162203.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Solution Dev. day_1/Pasted image 20250110093257.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Solution Dev. day_1/Pasted image 20250109163423.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Solution Dev. day_1/Pasted image 20250109163438.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Solution/Azure Solution Dev. day_1/Pasted image 20250109163333.png)

## 課題
1. アプリサービスをNodeでデプロイし、Azure CLIでデプロイします。
2. ゲストブックをデプロイして動作確認（VSCode）