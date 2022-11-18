# AWSフルコース（22年9月度）第5回課題

## ■課題
- EC2 上にサンプルアプリケーションをデプロイして、動作させてください

- まずは組み込みサーバーだけで、動作したらサーバーアプリケーションを分けて動くかチャレ ンジしてみましょう

- 動作したら、ELB(ALB)を追加してみましょう

- ELB を加えて動作が確認できたら、さらに S3 を追加してみましょう。S3 をどのように使うかはお 任せします。

- 構築環境を構成図に書き起こしてください


## ■目次
1. AWS上での環境構築（VPC・EC2・RDSの設定）
2. ターミナル（VSCode）での環境構築（組み込みサーバー編）
3. ターミナル（VSCode）での環境構築（WebサーバーとAppサーバー編）
4. ELB（ALB）の追加 ・AWSマネジメントコンソールからEC2→ロードバランサーを選ぶ
5. S3の追加
6. 構成図
7. よく使ったコマンド集
8. 宿題


## ■作業内容

## ①AWS上での環境構築（VPC・EC2・RDSの設定）

1-1. VPCの作成

・VPCなどを選択（エクスペリエンスの紹介）
・Nameタグの自動生成
・IPv4 CiDR ブロックを設定（大きい数で作った方がいい）

※NATゲートウェイは（セキュリティ高めたい時に使用する）
※自動でサフィックス（末尾に自動生成）

1-2. RDSのサブネットグループ作成を作成

1-3. RDSのデータベースの作成
・データベースはアプリに合わせて選択
・パブリックアクセスをオンにする場合は、踏み台サーバーの設定を推奨

・最初のデータベース名はアプリサンプル名
・DBパラメータは、文字コードの設定（絵文字など）
・バックアップウィンドウは、利用者の少ない時間帯（深夜など）に設定
・ログのエキスポートは全てログタイプは設定（監査・エラー・全般・スロークエリ）


1-4. EC2インスタンスの起動

・Amazon LInuxで選択
・インスタンス（t2microは1GBでやや容量が重い、本番環境だとt3micro推奨）



## ②ターミナル（VSCode）での環境構築（組み込みサーバー編）
```
・yumアップデート
sudo yum update -y

・Node.jsのインストール
curl -fsSL https://rpm.nodesource.com/setup_lts.x | sudo bash -

・Nodeのバージョン確認
node -v
```
・Yarnのインストール
```
curl -sL https://dl.yarnpkg.com/rpm/yarn.repo | sudo tee /etc/yum.repos.d/yarn.repo

sudo yum -y install git gcc clang openssl-devel zlib-devel mysql-devel nodejs yarn
```

・Rubyのバージョンを切り変えるパッケージのインストール
```
curl -fsSL https://github.com/rbenv/rbenv-installer/raw/HEAD/bin/rbenv-installer | bash

・パスを通す（コマンドの短縮化、チルダを登録させる）

echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bash_profile

~/.rbenv/bin/rbenv init

echo 'eval "$(rbenv init -)"' >> ~/.bash_profile

source .bash_profile
```

・Rubyのバージョン確認
```
rbenv --version

rbenv install 2.6.3
※Gemfile.lockで確認可能

rbenv global 2.6.3

rbenv rehash

ruby -v
```
・MySQLのインストール
```
sudo rpm -Uvh https://dev.mysql.com/get/mysql80-community-release-el7-3.noarch.rpm
```


・RDS使用して、 MySQLの起動

```
mysql -u admin -p -h raisetech-rail-app-version2.csvluhpooxvl.ap-northeast-1.rds.amazonaws.com

※-pでパスワード認証しないといけない


show databases;
→データベース確認できる
```

・git clone

```
git clone https://github.com/yuta-ushijima/raisetech-live8-sample-app.git

cd raisetech-live8-sample-app

gem install bundler:2.3.14

bundle install

EDITOR="vi" bin/rails master.key:edit
```

・master.keyの作成
※本番環境の場合

```
EDITOR=vim bundle exec rails credentials:edit
```

・Javascriptをビルドするためのツールのインストール
```
bundle exec rails webpacker:install
```

・アセットファイルの圧縮
```
bundle exec rails assets:precompile RAILS_ENV=production
```

・railsの本番環境における組み込みサーバーの起動コマンド
```
RAILS_ENV=production bundle exec rails server
```
・接続完了！
![第5回課題_組み込みサーバーでの接続確認](https://user-images.githubusercontent.com/100008521/201287271-960a76b9-2931-4c15-bbbb-cd447f8bbbe6.png)


## ③ターミナル（VSCode）での環境構築（WebサーバーとAppサーバー編）

▼講義メモ
・ウェブ3層構造は理科した方がいい
・AppaceよりもNginxのようが質としては優れている
・Unicornを使う（Pumaでもできる）

```
EDITOR="vi" bin/rails masterkey:edit
```
○nginxのインストール

・Nginxのインストール
```
sudo amazon-linux-extras info nginx1
```
※amazon-linux-extras用のコマンド

・Nginxのインストール
```
sudo amazon-linux-extras install -y nginx1
```

・Nginxのバージョン確認
```
nginx -v
```

・Nginxの格納先の確認
```
ls /etc
```
・Nginx配下のファイルの確認
```
ls /etc/nginx
```
Nginx.confとconf.dどちらでもOK


・nginx.confを開く
```
vim /etc/nginx/nginx.conf
```
・コマンド検索
```
sudo systemctl -h
```
・ステータス確認
```
sudo systemctl status
```
・nginxのステータス確認
```
sudo systemctl status nginx
```
・nginxの起動
```
sudo systemctl start nginx
```

○unicornの起動

```
bundle exec unicorn -c config/unicorn.rb -E production -D
```
※ -cはファイルの指定に使う
	
error_page

-Eは、環境変数
-Dは、バックで起動（裏側で）※おまじない


・プロセス確認
```
ps aux | grep unicorn
```

★これでは起動できない

・ログの確認
```
tail -f  raisetech-live8-sample-app/log/production.log
```
・nginxとunicornの接続確認
```
sudo vim /etc/nginx/nginx.conf
※set numberで行数追加できる 
```
```
mkdir -p nginx
```
・nginxのエラーログの確認
```
ls nginx/
```
```
tail -f nginx/error.log
```

・nginx.confの修正

```sudo vim /etc/nginx/nginx.conf
```

↑上記でファイルを開いたあと、下記を貼り付けると大体解消する

```
upstream @unicorn{
  # config/unicorn.rb内のunicorn.sockを指定する
  server /home/ec2-user/raisetech-live8-sample-app/unicorn.sock;
}

server {
  listen 80;
  # 接続を受け付けるリクエストURL ここに書いていないURLではアクセスできない
  server_name 〇〇.〇〇.〇〇.〇〇＜EC2インスタンスのパブリックIPアドレス＞;

  client_max_body_size 2g;

  # 接続が来た際のrootディレクトリ
  root /home/ec2-user/raisetech-live8-sample-app//public;

  # assetsファイル(CSSやJavaScriptのファイルなど)にアクセスが来た際に適用される設定
  location ^~ /assets/ {
    gzip_static on;
    expires max;
    add_header Cache-Control public;
  }

  try_files $uri/index.html $uri @unicorn;

  location @unicorn {
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header Host $http_host;
    proxy_redirect off;
    proxy_pass http://unicorn_server;
  }

  error_page 500 502 503 504 /500.html;
}
```


○nginxとunicornを必ず再起動させてから確認


・unicorn起動

```
bundle exec unicorn -c config/unicorn.rb -E production -D
```

・プロセス確認
```
ps aux | grep unicorn
```

・nginxの停止
```
sudo systemctl stop nginx
```

・nginxの起動
```
sudo systemctl start nginx
```

・アプリの動作確認完了！
![【第5回課題」Unicorn + Nginxで動いた図](https://user-images.githubusercontent.com/100008521/201287564-358ef4a8-4794-4896-90ea-c397de167583.png)


## ④ELB（ALB）の追加
・AWSマネジメントコンソールからEC2→ロードバランサーを選ぶ

・Application Load Balancerを選択
・セキュリティグループの作成
・ターゲットグループの作成
・インバウンドルールは、HTTPで 0.0.0.0/0
・ドメイン名（raisetech-rail-app~~）をコピーして接続確認

・ALBで接続完了
![【第5回課題】ELBで挙動確認](https://user-images.githubusercontent.com/100008521/201287695-7b75a204-232f-4843-b2f3-70c38ab74514.png)


## ⑤S3の追加

・vpcflowlogのデータログをエビデンスとして提出します
※S3に画像データを追加してのエビデンス提出予定でしたが、根が深そうなエラーが発生したので、こちらで提出いたします

![【第5回課題】S3接続](https://user-images.githubusercontent.com/100008521/201287896-80972658-d3e3-4a3d-bf18-9311c46dde7e.png)



## ⑥構成図
 ![aws_8th drawing](https://user-images.githubusercontent.com/100008521/201287974-5b28b993-fbc4-4dd2-9a79-f9f2d0695f2a.png)


## ⑦よく使ったコマンド集

・errorlogの確認コマンド（何回確認したか。。。）
```
tail -f nginx/error.log
```

・ファイルの編集コマンド（何度使ったことか。。。）

```
sudo vim /etc/nginx/nginx.conf
```

## ⑧宿題


・開発環境でのWebサーバー・アプリサーバーを使ってのデプロイ（組み込みサーバーだけはできた）

・S3の別の使い方（ALBのログをためる）

・ApacheとNginxの違いを調査（牛島さんからのアドバイス）

・「bundle exec unicorn -c config/unicorn.rb -E production -D」の「-D」は、バックグラウンドでの起動といい、なぜそのように動くのか調査