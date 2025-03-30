---
title: Azure_Infra day_1
date: 2025-03-15
categories: [cloud]
math: true
excerpt: "Azureのインフラストラクチャ基礎としてクラウドコンピューティングの特徴、サービスモデル（IaaS、PaaS、SaaS）の違い、Azureリソース管理（リソースグループ、RBAC）および仮想ネットワーク設計の基本をまとめました。"
---

# クラウドコンピューティング
* ネットワーク
* サーバー
* ストレージ

Cloud computing $\leftrightarrow$ On-Premises Service

### トラブルシューティングのためには全体の問題に対する複合的な理解が必要

- 全体を構成する各問題に対する理解がないと、トラブルシューティングができず、クラウドサービスがブラックボックスとなり、サービスプロバイダー（AWS, Azure）に依存する問題があります。したがって、内部的な動作を理解する必要があります。

- フォールトトレランス（耐障害性）
  - 障害が発生することを前提に、障害が発生してもサービスを提供できるような方策が提供されています。
  - これはRaid Level 5の特性のようです。

- 弾力性
  - 例）ワークロードの変化に応じてサーバーのサイズを調整
  - 疾病管理庁のNaverサーバーの例

- 拡張性
  - 既存システムに追加できるかどうか

*弾力性* + *拡張性*  -> **通常一緒に伴います**

## クラウドコンピューティングの特徴

- 規模の経済
  - サーバーを大きくして価格を下げることができます。

- 資本支出（CapEx）と運営費用（OpEx）
  - CapEx = 0、借りて使うので（減価償却なし、埋没費用なし）
    - 車のリース、建物の賃貸の例

- 消費ベースのモデル
  - 使用した分だけ支払う方式

- *参考*：ゲーム会社のサーバーはWindowsを多く使う（そのためAzureが多く使われるそうです）

### パブリッククラウド
 - httpsを使用

### プライベートクラウド

- 企業が自社でクラウドサービスを構成して提供
- 組織が管理するため、コストが高くなる可能性がありますが、それでも使用する理由はセキュリティ問題です。

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_1/Pasted image 20250106114307.png)
#### マイクロサービス

- Divide and Conquerアルゴリズムのようなサービス方式
- 例）トスのホーム画面のフロントがすべて分割されて組み合わさるもの（microFEと呼ばれるそうです）

#### IaaS（Infrastructure as a Service）
- 共有キッチンの比喩（原材料（小麦粉、肉など）がある状況）
- ![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_1/Pasted image 20250107093701.png)

#### PaaS
- 材料はすべて準備されており、組み合わせるだけの感じ
- ![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_1/Pasted image 20250107093635.png)
- スタートアップが好む方式（インフラ管理が不要なのでサービス開発に集中できる）

#### SaaS
- 無人中華料理店の比喩
- ![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_1/Pasted image 20250107093529.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_1/Pasted image 20250106115430.png)

**この授業では ==IaaS, PaaS== を扱います**

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_1/Pasted image 20250106115859.png)

### 用語説明

#### 全文検索ウィンドウ
![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_1/Pasted image 20250106134243.png)

#### サービス（$\approx$ メニュー / スキーマ、設計図）

#### リソース（$\approx$ メニュー注文の結果 / インスタンス、実装物）
- 支払いはリソースに対して請求されます
  ![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_1/Pasted image 20250106134427.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_1/Pasted image 20250106134501.png)

- リージョン（韓国リージョン） -> リージョンペア（釜山、ソウル）など（ペアが1つしかなくても他国とペアを組む）**プライマリ、セカンダリ**構造（災害時の対応のため）
- リージョン -> ゾーン（データセンターが1つの場合はゾーンがない）
  - 例）ソウルの場合、ゾーンがあり、ゾーンがあるとゾーン内に1つ以上のデータセンターが存在する
    データの複製はゾーン、リージョン間の単位で行われる

データセンター $\in$ サーバー
- データセンターの条件：電力、サーバーの安定性
- ほとんどが自動化されている

### 可用性

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_1/Pasted image 20250106135831.png)
- 定義：安全かつ安定的に維持すること

#### 学習資料
- **[Microsoft Learn 技術リンク(KO)](https://learn.microsoft.com/ko-kr/)**
- **[Microsoft Learn 技術リンク(EN)](https://learn.microsoft.com/en-us/)**
  特定製品に関する学習資料、クイズなどがあります。

- 参考：Azure ChartはOFFになっています

# 実習 Hello Azure

**重複しないように最後にイニシャルをつける**

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_1/Pasted image 20250106142548.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_1/Pasted image 20250106142559.png)
![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_1/Pasted image 20250106142655.png)
- rg- : Resource Groupの略（慣例的に使用）

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_1/Pasted image 20250106142858.png)
![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_1/Pasted image 20250106142932.png)
![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_1/Pasted image 20250106143035.png)
![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_1/Pasted image 20250106143053.png)
![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_1/Pasted image 20250106143130.png)

- pip- : Public IP Addressの略（慣例的に使用）

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_1/Pasted image 20250106143255.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_1/Pasted image 20250106143345.png)
Zone-redundant : すべてのゾーンで使用
3 : Zone 3
2 : Zone 2

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_1/Pasted image 20250106143502.png)
- **Default is best practice** : 分からない場合はデフォルトにしましょう

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_1/Pasted image 20250106143603.png)
![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_1/Pasted image 20250106143658.png)
![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_1/Pasted image 20250106143828.png)
![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_1/Pasted image 20250106143907.png)

### Azureポータル構造
![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_1/Pasted image 20250106143949.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_1/Pasted image 20250106150620.png)
赤い \* : 必須入力、後で変更が難しい

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_1/Pasted image 20250106150817.png)

サブスクリプション $\in$ リソースグループ（数の制限なし）$\in$ リソース（制限なし）
- 特定のリソースはサブスクリプション全体で、あるリソースはリソースグループ内でユニークでなければならない（やってみると分かる）
- 削除は可能だが、注意して削除すること（制約を設定すること）
  - 同じライフサイクルを持つ

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_1/Pasted image 20250106151222.png)
- RBAC（Role Based Access Control）：役割に応じて異なる権限を持つ
- RGの名前変更（不可能）-> 名前変更が必要な場合は新しいRGを作成してリソースを移動（移動は可能）
  - リソース作成時に識別のためのID（RG名など）があり、それが参照などの依存性を持つため名前変更が不可能

#### RG生成アプローチ
- プロジェクト中心のRG生成方法（Web + DB, VM, ストレージ）
  - プロジェクトチームの立場では便利
  - DBだけを見たいときはあちこち行かなければならない
- リソース単位方式（Web, DB） +（VM） +（ストレージ）
  - 1-2番ではDBが集まっているのでDBのRGだけを開けば良いので便利

**各方式のトレードオフがあります**

- ポータルではRG削除時にマルチセレクトで削除できないようにしている（ミス防止のため？）
- シェルでは可能

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_1/Pasted image 20250106152531.png)

- プロジェクト進行中は削除ロック、終了後は読み取りロックを使用
![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_1/Pasted image 20250106152751.png)
![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_1/Pasted image 20250106152808.png)
- 継承される
- $\exists$ 原子性（Atomicity）=> DBのTxのようなもの

## テナント / サブスクリプション

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_1/Pasted image 20250106153718.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_1/Pasted image 20250106153726.png)
- Entra IDは一般的に組織を代表する（主ドメイン）

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_1/Pasted image 20250106153810.png)
テナント $\in$ チームメンバー情報：チームメンバーの管理、登録はテナントで
- 例のシナリオ：テナント_1に属するチームメンバー_Aがリソースにアクセスしたい場合 -> テナント_2にチームメンバー_Aを招待（この時、ユーザータイプはゲストとなる）

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_1/Pasted image 20250106154532.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_1/Pasted image 20250106154610.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_1/Pasted image 20250106154702.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_1/Pasted image 20250106154743.png)

- これはRBACです

### リソースとは何か
- Azureにあるものはすべてリソースです（Pythonで全てがオブジェクトであるのと似ているようです）
- Azureでリソースを制御するためにはIAM（アクセス制御）が必要です
- サブスクリプションレベル、RGレベル、リソースレベルでIAMを付与できます（RBAC志向のようです）

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_1/Pasted image 20250106155218.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_1/Pasted image 20250106155329.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_1/Pasted image 20250106155343.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_1/Pasted image 20250106155634.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_1/Pasted image 20250106155738.png)
- 信頼できるIDプロバイダーの詳細（Gmailはデフォルトではないのでアカウント作成時にOutLookを使用するよう言われたそうです）

### Azureの動作構造
![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_1/Pasted image 20250106160400.png)

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_1/Pasted image 20250106160544.png)

- Infra Azure Codeを探す -> Terraform
- ### **Terraformとは？**
  - **Terraform**は**HashiCorp**が開発した**Infrastructure as Code（IaC）**ツールで、クラウドインフラ（サーバー、ネットワーク、データベースなど）をコードで定義し管理することを可能にします。Terraformを使用すると、**Azure**のようなクラウドプラットフォームでインフラリソースを宣言的に定義し、自動で生成、変更、削除することができます。

**つまり、インフラをコーディングすることのようです。**

# ユーザーアカウントとグループ
- リソースを使う立場、管理する立場
  - ユーザーアカウント
    - ユーザーに応じて権限の付与と回収は面倒
    - -> グループによって権限管理
    - ![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_1/Pasted image 20250106165209.png)
    - セキュリティプリンシパル：認証と権限付与の対象（ユニークである必要があり、固有の識別子が必要）
    - アカウントとIDの違い：アカウント（ユーザーに関する情報を持つ対象）、ID（アカウントを代表する値）
    - ![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_1/Pasted image 20250106165543.png)
    - 赤いボックスがID（招待されたアカウントは名字が異なる）：これはAzureで権限付与と認証の対象となるものである
    - グループに権限を付与する際、オブジェクトIDに権限が付与される

  - デバイス：自分の使用するコンピュータにIDを付与して権限付与、認証を行い、アカウントのように扱える（ユーザーに関係なく）
  - アプリ登録：自分が作成したカスタムアプリを登録できる。該当アプリにIDが付与される
  - 仮想マシンにもID付与可能
  - **IDを付与すれば人のように扱える（セキュリティプリンシパルになれる）、ユーザーに限定されない。**
  - 管理者アカウント

## AzureにはRBACが2種類あります。

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_1/Pasted image 20250106171000.png)

1. Entra IDによるテナント

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_1/Pasted image 20250106170235.png)
-> Entra ID RBAC

2. 上で行ったものはAzure RBAC

- Azureポータルで作成するのはほとんどがセキュリティグループ
  - セキュリティグループで作成することで権限付与可能

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_1/Pasted image 20250106170816.png)
- 無料バージョンなので動的ではない
- ![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_1/Pasted image 20250106170920.png)
- 明示的に指定するのが「割り当て済み」オプション
- 動的：動的

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_1/Pasted image 20250106171053.png)

権限付与時に継承されるため、最上位には最も弱い権限を与え、下に行くほど具体的に権限を付与するのが推奨される方法です

**教科書 CH. 1〜4を参考に**

---

# 仮想ネットワーク

- SDN（Software Define Network）
- SDS（Software Define S）
- SDD（Software Define Datacenter）
- => Software Define Anything
-> これらには標準が提供されます

物理的にネットワークを実装することは、実際にケーブルを接続することであるため、必要なときに迅速に接続することはできません
- クラウドではストレージ、ネットワークはすべて仮想です
  - 仮想ネットワークは物理的ではないため、必要なときに迅速にインストール可能です
Azure内のVM同士の通信、Internet outbound通信などの複数の機能を使用するためにVirtual Networkは必須です
  - 仮想マシン自体はInternet通信可能、Internet outbound通信にはVirtual Networkが必須

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_1/Pasted image 20250106175253.png)
Azure内ではプライベートIPを使用
- 動的割り当てを使用すると、仮想マシンを再起動するとアドレスが変わることがあります。
- サブスクリプション内ではアドレス空間が同じでなければなりません。しかし、接続時にアドレス空間が重複してはなりません。
- もしサブスクリプション_A、サブスクリプション_Bを接続する際に同じ範囲を使用してはなりません。
- 管理の観点からは小さなアドレス範囲のVnetを複数持つよりも、大きなアドレス範囲のVnetを1つ持つことが推奨されます。

![image]({{ site.baseurl }}/assets/images/Cloud, Container, DevOps/Cloud/Azure_Infra/Azure_Infra day_1/Pasted image 20250106175623.png)
- 1. VM_AからVM_Bを探す際にBroadcastを行うが、小さくサブネット化するとBroadcast時にすぐ見つかる。
- 2. 道を分けて渋滞を減少させることの比喩
- 3. 特定のネットだけをプライベートに分けておけば、一方が侵害されても問題なし
- 4. 

### 設計原則
仮想ネットのアドレス空間 == サブネットのアドレス空間 -> 無意味な話です