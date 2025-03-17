---
title: Azure_Infra day_2
date: 2025-03-15
categories: [cloud]
math: true
excerpt: "Azureインフラストラクチャの基礎として、仮想ネットワーク、仮想マシンのデプロイ方法、ストレージの種類やデータ構造の違い、そしてクラウド環境での設計原則について実習を交えて整理しました。"

---

# 仮想ネットワーク

## IPアドレスの割り当て
- Azure内ではすべてPrivate IP
- Azure外部からAzure内に作成されたサービスにアクセスする場合、そのサービスはユニークなポイントである必要があり、Public IPである必要があります
- VPNやオンプレミスの場合はPrivate IP
- ![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_2/Pasted image 20250107102211.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_2/Pasted image 20250107102337.png)

- VMが外部インターネットと接続する場合はPublic IPを使用
- Load Balancer（負荷分散装置）
- Public IP以外はすべてPrivate IPを設定できます。

## 仮想ネットワーク実習

[Naming Convention](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/azure-best-practices/resource-naming)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_2/Pasted image 20250107104130.png)

[Azureリソースの略語の推奨](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/azure-best-practices/resource-abbreviations)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_2/Pasted image 20250107105027.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_2/Pasted image 20250107105220.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_2/Pasted image 20250107105002.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_2/Pasted image 20250107104855.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_2/Pasted image 20250107105535.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_2/Pasted image 20250107105836.png)

# 仮想マシン

- VM名は変更不可
- VM名 == ホスト名
- 名前指定時、オペレーティングシステムを考慮して長さを指定

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_2/Pasted image 20250107113049.png)

- オペレーティングシステム：Windows、Linuxを提供
- ネットワーキング要素：Public IP Address、Secure Group、Interface(Card)、V-net
- VMサイズ：スペックに応じて異なる
- ディスク：HDD、SSDなど
  - VMのハードディスクはファイルです (エミュレーション)
Datacenter $\in$ (ServerFarm $\in$ Switch, StorageFarm)
  ServerFarmで使用していたサーバーが故障した場合？ $\rightarrow$ 同じServerFarmの別の層に移動（ライブマイグレーション）
- 通常は管理ディスクを使用
- 非管理ディスクを使用するケース：ローカルでVMwareで作成したVMをクラウドに移行したい場合（このような場合は少ない）

Linux：SSH公開鍵でVM接続

## 仮想マシンデプロイ実習
## Windows Server VM

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_2/Pasted image 20250107120759.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_2/Pasted image 20250107120951.png)

==参考== GiB $\neq$ GB TiB $\neq$ TB KiB $\neq$ KB 1000KB = 1MB 1024KB = 1MiB (.)iB = 2^10 で計算

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_2/Pasted image 20250107121737.png)

- ここまではディスクをコンピュータに物理的に接続したのと類似
- フォーマッティング、パーティショニングが必要
![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_2/Pasted image 20250107122002.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_2/Pasted image 20250107122216.png)

- 実務では通常ブート診断を使用するそうです
![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_2/Pasted image 20250107133637.png)

- RDPファイルをインストールして接続することも可能ですが、

- ![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_2/Pasted image 20250107134909.png)

.sh拡張子：
.ps拡張子：Microsoft PowerShellスクリプト

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_2/Pasted image 20250107140605.png)

==参考== LTS：Long Term Support（オペレーティングシステムのバージョンを長期的にサポートすることを保証）

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_2/Pasted image 20250107140827.png)

- キーペア名の命名規則：[VM名] + "key"
![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_2/Pasted image 20250107140841.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_2/Pasted image 20250107141010.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_2/Pasted image 20250107141039.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_2/Pasted image 20250107141109.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_2/Pasted image 20250107151455.png)

## ストレージ
- ストレージの利用理由：データ保存

- データの種類
  - 非構造化データ：ハングルファイル、ワードなど / 演算不可
  - 半構造化データ：スキーマはあるがスキーマが可変的なもの（例：json, XML / NoSQL ← これら）、演算不可
  - 構造化データ：例）リレーショナルデータ（リレーショナルDBを使用するもの）

VMのVHD：ストレージ
ファイルサーバー←これを使用する場合
SMB（ファイル共有プログラム）Naasで使用し、ファイルサーバーが必要な場合→ストレージ

**AWSではバケットと呼ばれるようです。**

Blob（Binary Large Object）：非構造化データ
Append Block：ログデータ
など、データの特徴に合わせて適切なフレームに保存することでパフォーマンスが向上します

アクセス層でホット、クール、コールドに分けた理由：各層に応じてTx処理速度が異なり、コストも異なるため、もし保存だけが目的であれば、Tx処理コストが低く、安価なアーカイブに移すのが賢明な選択です

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_2/Pasted image 20250107173011.png)
- 解除する：クリックするとRecoveryサービスが生成される
- Recoveryサービスを削除する際の順序が重要（生成時にサービスを順にたどりRecoveryサービスをたどって削除する必要がある）：これはバックアップが重要だから
- 今回の実習はバックアップに関する実習ではないので解除しました

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_2/Pasted image 20250107180014.png)