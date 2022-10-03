# AWSフルコース（22年9月度）第３回課題

## ■第4回課題
- AWS上に新しくVPCを作ってください
- EC2とRDSを構築してください
- EC2からRDSへ接続をし、正常であることを確認して報告してください

## ■手順の全体像

- VPCの立ち上げ
- EC2用のセキュリティグループの作成
- RDS用のセキュリティグループの作成
- DB用のサブネットの作成
- EC2でインスタンスの起動
- RDSでデータベース作成
- VSCodeでSSH接続
- RDSとEC2の接続

 ### 1.  VPCの立ち上げ


下記項目を埋めていく


1.  作成するリソース：VPCなど
→サブネットもインターネットゲートウェイもどちらも作成されるので便利


2. 名前タグの自動生成
→任意の名前を入力

※他項目はデフォルトの項目でOK

![第4回課題_VPCの作成1](https://user-images.githubusercontent.com/100008521/193501164-41eec3e5-acc4-4771-914d-53b4cd0d7210.png)


 ### 2.  EC2用のセキュリティグループを作成


1. VPC＞セキュリティグループ>セキュリティグループを作成

2. セキュリティグループと説明は任意の名前をつける

3. VPCは先ほど作成したVPCを選択

![第4回課題_EC2用のセキュリティグループ作成1](https://user-images.githubusercontent.com/100008521/193501249-12685909-afb6-4884-9b25-f0e46f4629e2.png)



4. インバウンドルールで、タイプ3種「HTTP」「SSH」「すべてのICPM -IPv4」を選択し、上の画像の通りに選択後、セキュリティグループを作成

![第4回課題_EC2用のセキュリティグループ作成2](https://user-images.githubusercontent.com/100008521/193501326-ad3341b4-52b8-4898-bec2-bbe75f45afe7.png)

### 3.  RDS用のセキュリティグループを作成


1. VPC＞セキュリティグループ>セキュリティグループを作成

2. セキュリティグループと説明は任意の名前をつける（RDS用とわかるとよい）

3. VPCは先ほど作成したVPCを選択

![第4回課題_RDS用のセキュリティグループ作成1](https://user-images.githubusercontent.com/100008521/193501367-430a954d-b63d-4ff9-b8e0-f9355eff7d3e.png)

4. インバウンドルールで「MySQL/Aurora」をタイプ選択し、画像のように変更

![第4回課題_RDS用のセキュリティグループ作成2](https://user-images.githubusercontent.com/100008521/193501386-290cab22-4891-4ebb-9a2e-41e16be35edd.png)


### 4.  DB用のサブネットグループの作成

1. RDS>サブネットグループ>DBサブネットグループを作成を選択
2. 名前と説明は任意のテキストを入力
3. VPCを先ほど作成したVPCで選択

![第4回課題_DBサブネットグループ作成1](https://user-images.githubusercontent.com/100008521/193501403-ec75a6b7-662b-4fb9-8f67-00f886c8648b.png)


4. サブネットを追加で、VPCが設置されているのアベイラビリティゾーンを選択

5. サブネット内のPrivate-Subnetをそれぞれ選択（RDSはPrivate-Subnetに設置するため）

![第4回課題_DBサブネットグループ作成2](https://user-images.githubusercontent.com/100008521/193501407-419ef8d3-5769-4646-83a2-47f40e44958d.png)


### 5. EC2でインスタンスの起動

1. EC2>インスタンス>インスタンスを起動
2. 名前を任意のテキスト入力

![第4回課題_EC2インスタンス作成1](https://user-images.githubusercontent.com/100008521/193501491-3d9d30ac-b91f-4313-a330-4c0cdc38115e.png)


3. アプリケーションおよび OS イメージ は、「Amazon Linux」で64ビットを選択
![第4回課題_EC2インスタンス作成2](https://user-images.githubusercontent.com/100008521/193501487-b5cb0772-3a5a-4f31-8b46-4e72c8af6056.png)

4. インスタンスタイプはt2.micro
5. キーペアは「新しいキーペア」を作成※接続の際必要なため、必ず設定


![第4回課題_EC2インスタンス作成3](https://user-images.githubusercontent.com/100008521/193501484-86ecd7c1-98a7-4086-b51d-522b6c1a5844.png)


6. ネットワーク設定はで、VPCは作成したVPC、サブネットは「パブリックサブネット」を選択※EC2はパブリックサブネットに置く
7. パブリック IP の自動割り当ては「有効化」
8. セキュリティグループは、先ほど作成した「EC2」用の既存のセキュリティグループを選択
9. 他はデフォルトで使用し、インスタンスを起動

![第4回課題_EC2インスタンス作成4](https://user-images.githubusercontent.com/100008521/193501481-39aa52d4-b025-4ebb-8214-339f6ba80568.png)

###  6. RDSでデータベース作成

1. RDS>データベース>データベースの作成
2. 標準作成を選択

![第4回課題_RDS作成1](https://user-images.githubusercontent.com/100008521/193501718-2d2c0bc5-6108-4e81-b852-e0cd257bc5bd.png)


3. エンジンタイプは、今回は「MySQL」
4. テンプレートは今回は、「開発/テスト」に設定
![第4回課題_RDS作成2](https://user-images.githubusercontent.com/100008521/193501711-6b7fa3e6-25db-411b-875b-b524e8f4b7be.png)

5. 設定では、識別を入力し、マスターパスワードを入力※接続時に使うので必ずメモをとる

![第4回課題_RDS作成3](https://user-images.githubusercontent.com/100008521/193501700-0a1ec304-80e1-4687-aef4-f1e7f8b0b3b0.png)

6. 接続の項目で、「VPC」は、作成したVPCを選択
7. DBサブネットグループは、作成したサブネットを選択
8. VPCセキュリティグループは「既存の選択」で、作成したグループを選択し、アベイラビリティゾーンも設定


![第4回課題_RDS作成4](https://user-images.githubusercontent.com/100008521/193501695-485a3674-d815-499d-a2d2-ef9bce6dc8e9.png)

9. 残りはデフォルトのままで、データベースの作成


###  7. VSCodeでSSH接続

今回は、VSCodeを使って、SSH接続をします。

やり方は、下記の記事を参考！とてもわかりやすいです！

https://blog.serverworks.co.jp/tech/2020/02/20/vscode-remote-ssh/

ちなみに私は、ここで今回エラー沼にハマりました。

原因は、Configファイルに入力する「IdentityFile」を「IdenttityFile」にして綴り間違いしていたためで、まだまだしょぼいです。

‘’’Host remotessh
  Hostname <サーバーのIP>
  User <ログインユーザー名>
  Port 22
  IdentityFile <秘密鍵のパス>
‘’’


###  8. RDSとEC2の接続

SSH接続できたら、EC2で該当のインスタンスを開き、接続を押します。

1. MySQLのインストール
‘’’
$ sudo yum install mysql
‘’’
2. RDSと接続
‘’’
$ mysql -u admin -p -h <RDSのエンドポイントを張り付け>
‘’’

DBにログインができました！
〜〜画像〜〜〜



## ■つまづいたところ
- VScodeでEC2にSSH接続

→単語の綴りなどで初歩的なミスが原因

- VPCのサブネット作成の際のIPv2CIDRの数字

→CIDRブロックの件だが、結論、VPC作成時「VPCなど」を選択するとサブネットを複数自動生成して、CIDRも振り分けてくれるので、基本「VPCなど」で作成すると良さそう


## ■追加で作業・調査すること

- セキュリティグループと各サービスの関連性
- Cloud9でEC2にSSH接続
- 構成図の作成


## 参考記事

[AWS]EC2からプライベートサブネットのRDSへ接続する
https://zenn.dev/shake_sanma/articles/5b3e12bf7f558d