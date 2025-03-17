---
title: Azure_Infra day_3
date: 2025-03-15
categories: [cloud]
math: true
excerpt: "Azureのコンテナストレージ（Blob Storage）および仮想ネットワークの設定、IPアドレス割り当て、仮想マシンの可用性を確保するための設定、負荷分散、VM拡張セット（VMSS）など、インフラ構築のポイントを詳しく整理しました。"

---

# コンテナストレージ

Blob Storage $\approx$ オブジェクトストレージ  
オーディオ、ビデオ、災害復旧ファイル、ログデータなどはすべてBlob Storageに保存できます。オンプレミスで発生するすべてのファイルもAzure Blob Storageにアップロードして管理可能です。

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_3/Pasted image 20250108092208.png)

コンテナ $\in$ Blob

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_3/Pasted image 20250108094129.png)  
- Korea/battlefield_kor_~.txt のように見えますが、実際にはフラットなデータであり、Koreaフォルダは区切りとして使用されます（コンテナ内でフォルダアイコンとして表示されるものは区切りであり、実際のフォルダではないようです）。

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_3/Pasted image 20250108094144.png)
![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_3/Pasted image 20250108094335.png)

- URL 構造 : `https://stjarvispym.blob.core.windows.net/battledata/Korea/battlefield_kor_20230830.txt`

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_3/Pasted image 20250108094542.png)  
![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_3/Pasted image 20250108094705.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_3/Pasted image 20250108094651.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_3/Pasted image 20250108094725.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_3/Pasted image 20250108094801.png)  
- 一種のバージョン管理

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_3/Pasted image 20250108094835.png)  
- 削除した後、

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_3/Pasted image 20250108094907.png)  
- 昇格させると、

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_3/Pasted image 20250108094928.png)  
- 復旧します。

- 一種のWeb型ファイルエクスプローラー  
![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_3/Pasted image 20250108095258.png)

---

## 仮想マシンの可用性

- フロントエンド（FE）またはバックエンド（BE）のどちらかがダウンしても、サービスが可用な状態を維持すること。

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_3/Pasted image 20250108104621.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_3/Pasted image 20250108111542.png)  
- 2台のコンピュータラック、5つの論理的区分を可用性セットとして設定するという意味です。

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_3/Pasted image 20250108111815.png)  
- WebとDB、2つのセット。ここでは再起動時に一つのコンピュータラックを先に再起動し、他のラックを再起動します。

以前の演習ではLinuxサーバーでoutboundが可能だったため、$apt nginxでnginxのインストールが可能でしたが、Load balancerを接続すると該当Load balancerがブロックします。

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_3/Pasted image 20250108143141.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_3/Pasted image 20250108143658.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_3/Pasted image 20250108143824.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_3/Pasted image 20250108144126.png)

None属性: ステートレス（1番サーバーに接続した後、次に負荷分散で2番サーバーに接続しても問題ありません）  
RDPサーバーの場合: 特定のRDPサーバーに接続して作業した後、再度接続するときに別のRDPサーバーに接続すると既存のセッションではないため問題が発生します。  
セッション持続性: 上記の2つのケースに応じて、Noneはセッション持続性が不要な場合、その他は必要な場合です。

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_3/Pasted image 20250108144844.png)

- ここでのポートはLoad balancerのポート番号です。

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_3/Pasted image 20250108155833.png)  
- なにかを調べる

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_3/Pasted image 20250108161902.png)

- Load balancer接続後、RDPファイルで接続するためのNAT規則を設定します。  
![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_3/Pasted image 20250108162040.png)  
- 弾力性提供演習前に削除したファイルの集合

# VMSS 仮想マシン拡張セット

VMテンプレートを作成し、必要に応じてVMを生成する技術（生成するため、コスト管理に注意が必要）  
- 可用性セット、ゾーンが自動的に調整されるため、高可用性が可能です。

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_3/Pasted image 20250108163816.png)

- 均一性モード: 同一のスペックが必要な状況（複数のWebサーバーを同一に作成する状況）。  
- オーケストレーションモード: 複数のスペックが必要な状況（映像の画質に応じて異なる接続を行う場合）。

テンプレートを変更すると、該当テンプレートで生成されたVMも変更したテンプレートで同期を選択することができます。