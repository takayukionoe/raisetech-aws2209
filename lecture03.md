# AWSフルコース（22年9月度）第３回課題

## 第3回課題
1. APサーバーについて調べてみましょう！

・APサーバーの名前とバージョンを確認してみましょう
　→Puma(5.6.5)
　
・AP サーバーを終了させた場合、引き続きアクセスできますか？
　→アクセスできない(「No application seems to be running here」とでる）
　
2. DB サーバーについて調べてみましょう。

・サンプルアプリケーションで使った DB サーバー(DB エンジン)の名前と
、今 Cloud9 で動作しているバージョンはいくつか確認してみましょう。
　→Ver 8.0.30 for Linux on x86_64 (MySQL Community Server - GPL)
　
　※「$ mysql --version」のコマンドで調べられる
　
・DB サーバーを終了させた場合、引き続きアクセスできますか?
　→アクセスできない
　
・Rails の構成管理ツールの名前は何でしたか？
　→Bundler。Gemはパッケージングされたアプリケーション、モジュール。
　
## 第3回課題で学んだこと

## 大まかな手順
1. データの反映
2. Rubyのバージョン確認
3. MySQLのインストール
4. MySQLのパスワードの修正
5. database.ymlのファイルの修正
6. データベース（DB）の作成
7. Railsアプリを起動


### 1. データの反映
・git cloneでアプリ格納場所のリポジトリをローカルに落とす

```
$ git clone <HTTPSのURL>
```

### 2. Rubyのバージョン確認
・Railsのバージョン確認
```
$ ruby -v
```

### 3. MySQLのインストール

```
sudo yum -y update

sudo yum remove -y mysql-server
sudo yum remove -y mariadb*


MYSQL_PACKAGE_URL="https://dev.mysql.com/get/mysql80-community-release-el7-3.noarch.rpm"
sudo yum localinstall -y $MYSQL_PACKAGE_URL
sudo yum install -y mysql-community-devel
sudo yum install -y mysql-community-server
```

・ MySQLサーバーの起動＆確認
```
sudo service mysqld start && sudo service mysqld status
```

・ 初期パスワードの確認
```
sudo cat /var/log/mysqld.log | grep "temporary password" | awk '{print $13}'


# MySQLにログイン（パスワードが表示されるので、必ずコピーしておく）
mysql -u root -p
```

### 4.Mysqlのパスワードの修正

・パスワードの修正は下記の記事を参考

https://qiita.com/ttyoku/items/f7b5c763cbe5ff80eed4


### 5. database.ymlのファイルの修正（usernameやパスワードの追加）

**開発環境のコードを修正する必要があるので、修正
→これをやらないと、エラー沼にハマるので注意**

```
# database.ymlのファイル内を修正

adapter: mysql2
encoding: utf8
database: ~~~~~~（デフォルト名）
pool: 5
username: root（今回は開発環境なのでroot）
password: MySQLインストール後に変更したパスワード（これないとデプロイできない）
host: localhost
```

上書き保存をする

**注意**：Railsには、「開発」「テスト」「本番」の3つの環境があり、今回のデプロイは「開発環境」でのデプロイを想定


### 6. データベース（DB）の作成

MySQLのインストールと、database.ymlの編集が終わったらいよいよデータベースの作成に入る


下記コマンドでconfig/database.ymlの設定内容にをもとにデータベースが作成される

```
$ rails db:create
```

余談だが、本番環境のデータベースを作成したい場合は
```
$ rake db:create RAILS_ENV=production
```


migrationファイルの内容をDBに反映する
→メリットは、SQL構文を書かずともDBにアクセスしてデータをいじれることらしい

→まだよくわかってないが、下記記事を参照

https://qiita.com/ichihara-development/items/9e9a8454178947eb519d

```
$ rails db:migrate
```


### 7. Rails アプリを起動

アプリをデプロイします。
```
・rails s
```

プレビューでアプリを見てましょう！

・preview→preview Running Applicatonで起動


・起動が遅い、できない場合は下記のコマンドで不要なコンテナを削除
```
$ docker system prune -a
```

・無事デプロイ成功！！

## 遭遇したエラー
1. 「rails s」で起動時「Can't connect to local MySQL server through socket '/tmp/mysql.sock' (13)」が発生

→「database.yml」のコードをいじる必要ある

○Before
```
# database.ymlのファイル内を修正

adapter: mysql2
database: ~~~~~~（デフォルト名）
```

○After
```
# database.ymlのファイル内を修正

adapter: mysql2
encoding: utf8
database: ~~~~~~（デフォルト名）
pool: 5
username: root（今回は開発環境なのでroot）
password: MySQLインストール後に変更したパスワード（これないとデプロイできない）
host: localhost
```

2. 容量不足でアプリの起動が遅い
→`$ docker system prune -a`で解決。できれば最初にやっておいた方がいいみたい


## 今回の課題で使用したコマンドたち
```
git clone
cd raisetech-live8-sample-app/
sudo yum -y update
sudo yum remove -y mysql-server
sudo yum remove -y mariadb*
MYSQL_PACKAGE_URL="https://dev.mysql.com/get/mysql80-community-release-el7-3.noarch.rpm"
sudo yum localinstall -y $MYSQL_PACKAGE_URL
sudo yum install -y mysql-community-devel
sudo yum install -y mysql-community-server
sudo service mysqld start && sudo service mysqld status
bundle install
ruby -v
mysql -u root -p
rails db:create
rails db:migrate
rails s
docker system prune -a
```

## Qiitaにも手順をまとめている

[GitHubとCloud9の連携方法とpushの実行までの全手順](https://qiita.com/saunadaisuki/private/ecd1a15e7db8eae4ff70)