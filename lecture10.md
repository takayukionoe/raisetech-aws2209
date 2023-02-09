# ■第10回課題

## ■課題内容
* CloudFormation を利用して、現在までに作った環境をコード化
* 以下の構成図を参考ににスタック作成した

![aws_8th drawio (1)](https://user-images.githubusercontent.com/100008521/217277642-49a84b6a-6115-46eb-a60a-63de3779c924.png)


## ■目的
* CloudFormationを使って、環境のコード化


## ■作成物

* VPC（VPC、IGW,Subnet、Route Table）
* EC2（EC2、Security Group、Key Pair）
* RDS（RDS、Subnet Group、Security Group）
* ELB（ALB、SEcurity Group、Target Group、Listener）


を使った、環境のコード化


## ■テンプレートの形式について

* AWS CloudFormation概要[公式](https://docs.aws.amazon.com/ja_jp/AWSCloudFormation/latest/UserGuide/template-formats.html)


* [VPC](https://docs.aws.amazon.com/ja_jp/AWSCloudFormation/latest/UserGuide/aws-resource-ec2-vpc.html)


* [Subnet](https://docs.aws.amazon.com/ja_jp/AWSCloudFormation/latest/UserGuide/aws-resource-ec2-subnet.html)

（補足）
AvailabilityZoneをap-northeast-1aで指定すると「[cfn-lint] W3010: Don't hardcode ap-northeast-1a for AvailabilityZones」と表示されるが、これは固有値ではなくて組み込み関数を指定することを推奨している（っぽい）


* IGW（[InternetGateway](https://docs.aws.amazon.com/ja_jp/AWSCloudFormation/latest/UserGuide/aws-resource-ec2-internetgateway.html)、[VPCGatewayAttachment](https://docs.aws.amazon.com/ja_jp/AWSCloudFormation/latest/UserGuide/aws-resource-ec2-vpc-gateway-attachment.html)）


* Routetable
  * [RouteTable](https://docs.aws.amazon.com/ja_jp/AWSCloudFormation/latest/UserGuide/aws-resource-ec2-routetable.html)
  * [Route](https://docs.aws.amazon.com/ja_jp/AWSCloudFormation/latest/UserGuide/aws-resource-ec2-route.html)
  * [[SubnetRouteTableAssociation](https://docs.aws.amazon.com/ja_jp/AWSCloudFormation/latest/UserGuide/aws-resource-ec2-subnetroutetableassociation.html)


* EC2-Instance
  * [Instance](https://docs.aws.amazon.com/ja_jp/AWSCloudFormation/latest/UserGuide/aws-properties-ec2-instance.html#cfn-ec2-instance-disableapitermination)

* [KeyPair](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-ec2-keypair.html)

* [SecurityGroup](https://docs.aws.amazon.com/ja_jp/AWSCloudFormation/latest/UserGuide/aws-properties-ec2-security-group.html)

* RDS
  * [DBSubnetGroup](https://docs.aws.amazon.com/ja_jp/AWSCloudFormation/latest/UserGuide/aws-resource-rds-dbsubnetgroup.html)
  * [DBInstance](https://docs.aws.amazon.com/ja_jp/AWSCloudFormation/latest/UserGuide/aws-resource-rds-dbinstance.html)

* S3
  * [Bucket](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-s3-bucket.html)

* ALB
  * [ElasticLoadBalancingV2::LoadBalancer](https://docs.aws.amazon.com/ja_jp/AWSCloudFormation/latest/UserGuide/aws-resource-elasticloadbalancingv2-loadbalancer.html)
  * [ElasticLoadBalancingV2::TargetGroup](https://docs.aws.amazon.com/ja_jp/AWSCloudFormation/latest/UserGuide/aws-resource-elasticloadbalancingv2-targetgroup.html)
  * [ElasticLoadBalancingV2::Listener](https://docs.aws.amazon.com/ja_jp/AWSCloudFormation/latest/UserGuide/aws-resource-elasticloadbalancingv2-listener.html)


■組み込み関数(今回使用した関数)


* Fn::ImportValue(!ImportValue)
  * 別のスタックによってエクスポートされた出力の値を返す
  * スタックを跨いで、値を挿入した時に使用


* Ref:(!Ref)Ref
  *  論理ID でIDまたはパラメータの値を返す
  * VPCIDなどの値の挿入に使用


* Fn::Sub(!Sub)

* テンプレートで、スタックを作成または更新するまで使用できない値を含むコマンドまたは出力を作成するために、この関数を使用できます。

* 今回はスタック名を挿入するために使用した


## ■わからない事・やる事リスト
* アベイラビリティーゾーンの指定は組み込み関数で指定した方がいいのか？
* Parameterの値は分かりやすくする必要がありそうだが、命名規則などは現場で事前に固めるなどあるのだろうか？


## ■遭遇エラー
* EC2インスタンス作成用のAMIのIDの管理はどのように行なっているか？
  →AMIのIDはリージョンによって変わるらしい

* スタックの修正が必要な場合は、「変更セット」からの変更がおすすめ
  →一度スタックを削除すると時間がかかる（同じ名前のIDは使えないっぽい）

* !RefがVScode上でエラーになる

（手順）
* Homebrewのインストール
↓
* settings.jsonにカスタムタグを追加


○参考記事
* VSCodeでAWS CloudFormation をYAMLで書くための個人的ベスト設定
https://qiita.com/yoskeoka/items/6528571a45cd69f93deb


## ■感想
* 構成図がないと書くResouresとの整合性が取れなくなるで、構成図を固めた上で作業に移ると良い
* 当初添付レポートを1つのスタックで作成していたが、それぞれで分けて作成したが、テンプレートの更新可動性を考えるとこちらの運用の方が良さそう
* 今回テンプレートは一種作成したが、他にバリエーションがありそうなため、時間を見つけて他のパターンも作成し理解を深めたい。



## ■参考記事

・CloudFormationで環境構築を自動化する (CI/CDまでの道⑩)
https://zenn.dev/jinwatanabe/articles/fddad29af441491dad07

・AWS CloudFormationでEC2を構築
https://qiita.com/tyoshitake/items/c5176c0ef4de8d7cf5d8

・【やってみた】AWS CloudFormation実行～テンプレートの更新
https://blog.serverworks.co.jp/2022/02/09/073000

・AWS CloudFormationでEC2を構築
https://qiita.com/tyoshitake/items/c5176c0ef4de8d7cf5d8

・EC2とCloudformationでキーペアの設定
https://qiita.com/tsukamoto/items/1e0f3c8ecf4cba5cf485

・【CloudFormation入門】5分と6行で始めるAWS CloudFormationテンプレートによるインフラ構築
https://dev.classmethod.jp/articles/cloudformation-beginner01/

