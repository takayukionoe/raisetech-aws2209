# ■第6回課題

■課題の概要

1. CloudTrailから3つ情報をピックアップ

2. CloudWatch アラームを使って、ALB のアラームを設定して、メール通知してみてください。
（アラームとアクションを設定した状態で、Rails アプリケーションが使える、使えない状態に して、動作を確認してみてください。どうなりますか？）

3. AWS 利用料の見積作成

4. マネジメントコンソールから、現在の利用料を確認して教えてください。 先月の請求情報から、EC2 の料金がいくらか確認


## 1. CloudTrailから3つ情報をピックアップ

![スクリーンショット 2022-11-22 19 59 54](https://user-images.githubusercontent.com/100008521/203297577-d6fd210e-ca72-4752-9156-9332929fb95f.png)

○イベント名：
ModifyTemporaryCredentialsOnEnvironmentEC2


○イベント時間
November 15, 2022, 21:58:20 (UTC+09:00)


○イベントソース
cloud9.amazonaws.com


○イベント時間
November 15, 2022, 21:58:20 (UTC+09:00)


発信元 IP アドレス
AWS Internal


## 2. CloudWatch アラームを使って、ALB のアラームを設定して、メール通知してみてください。 

▼手順

- Amazon SNSでトピックの作成
- 通知用のメールアドレスを設定
- CloudWatchアラームを作成（今回はOKとALARMの2種類を作成）

### ■Railsアプリ起動時のメール通知とCloudWatch画面

![【第6回課題】アプリ起動時のCloudwatch画面※修正](https://user-images.githubusercontent.com/100008521/203941681-686a32e2-93c9-4576-b280-0008c7d4511d.png)

![【第6回課題】アプリ起動時のアラームOK画面※修正](https://user-images.githubusercontent.com/100008521/203941719-0ec1ef52-68e9-402e-bbab-f5925d32f154.png)

### ■Railsアプリ停止時のメール通知とCloudWatch画面

![【第6回課題】アプリ停止時のCloudwatch画面※修正](https://user-images.githubusercontent.com/100008521/203941735-631ed4a4-fb9c-4afb-9801-d1f10e7f9185.png)

![【第6回課題】アプリ停止時のアラームALARM画面※修正](https://user-images.githubusercontent.com/100008521/203941751-0c20bf68-c3e5-436a-aa01-e633cb7c1b8b.png)

## 3. AWS 利用料の見積作成

■これまでの課題で使用してきたリソースは下記

 - VPC
 - EC2
 - RDS
 - ALB
 - S3


 - VPCの見積について

前提：
・Amazon Virtual Private Cloud (VPC) の作成と使用に追加料金は発生しない。

・オプションの VPC 機能の利用については。使用量ベースの料金が発生


▼見積金額
・0円

今回は他のVPCサービスを使用しないため

NAT Gatewayはよく出てくるので[こちらチェック](https://www.fenet.jp/aws/column/aws-beginner/404/)


 - EC2の見積について

○オペレーションシステム

・「Linux」を選択


○ インスタンスタイプ

・「名前でインスタンスを検索します」から該当のインスタンスタイプを選択

・今回は、課題でも使用した「t2.micro」を選択

○数量（必要なAmazon EC2のインスタンスの数を入力）

・課題では1つ使用したので「1」を記入

○使用率

・24時間使用する想定のたm「24時間／日」を記入


○価格戦略

・最も安価な「EC2 Instance Savings Plans」を採用


○Amazon Elastic Block Storage (EBS)

意味：
・EC2にアタッチする追加ストレージ
・高い可用性（99.999%）あり

○各 EC2 インスタンスのストレージ

下記選択肢が、あるが今回はテスト環境にも使用できる「汎用 SSD (gp2) ボリューム」を採用


[参考・引用：Amazon EBSの料金（AWS公式サイトより）](https://aws.amazon.com/jp/ebs/pricing/)


○開発環境やテスト環境など幅広い用途に利用

・汎用 SSD (gp2) ボリューム：
1 か月にプロビジョニングされたストレージ 1 GB あたり 0.10USD

汎用 SSD (gp3) - ストレージ：
0.08USD/GB 月

○高いパフォーマンスを発揮し、高頻度アクセスのアプリケーション向け

プロビジョンド IOPS SSD (io1) ボリューム：
1 か月にプロビジョニングされたストレージ 1 GB あたり 0.125USD、さらに 1 か月にプロビジョニングされた IOPS あたり 0.065USD

プロビジョンド IOPS SSD (io2) - ストレージ：
0.125USD/GB 月

○ログやデータストア向け

スループット最適化 HDD (st1) ボリューム：
1 か月にプロビジョニングされたストレージ 1 GB あたり 0.045USD

○その他

Cold HDD (sc1) ボリューム：
1 か月にプロビジョニングされたストレージ 1 GB あたり 0.015USD

マグネティック（前世代）：
使用しない

 - RDS

▼MySQL インスタンスの仕様

○数量
・これは1つ使用
・対応は、db.m1.smallで最小サイズを選択

○使用状況 (オンデマンドのみ)
・20時間／週で見積

○RDS Proxy
・アクセスが多い製品向け[（詳細はこちら）](https://www.sunnycloud.jp/column/20210502-01/)

・今回はNoを選択


 - ALB（ELB）

○Elastic Load Balancing

・ALBを選択

○サービスの設定
・1つ

○ロードバランサーキャパシティーユニット (LCU)


・ALB ごとの新しい接続の平均数を25にて算出

[公式の料金例を参考](https://aws.amazon.com/jp/elasticloadbalancing/pricing/)


 - S3

○S3 ストレージクラスとその他の機能の選択

・S3 Standardを選択

○S3 Standard ストレージ

・S3 Standard ストレージで「1GBあたり月」を選択


最終的なお見積りは[こちら](https://calculator.aws/#/estimate?id=a96874b46a40cd4de3df18f6a26be1d95647c911)


4. マネジメントコンソールから、現在の利用料を確認して教えてください。 先月の請求情報から、EC2 の料金がいくらか確認

・先月の請求金額は、9,852円で着地。RDSを無料枠を超える量使っていた、、、
・コストのアラート設定は必須だと感じた

![【第6回課題】請求情報](https://user-images.githubusercontent.com/100008521/203301106-bb29bb13-00ba-4a7f-99e5-1a137c5cd212.png)