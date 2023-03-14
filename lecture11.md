# 第11回課題

## ゴール
* ServerSpecのテストを実行し、テストでの成功を確認する

## 手順
* 導入環境
    * Ruby 3.1.2
* ssh接続でテストを実行する為、ローカル環境にServerspecを導入
    ```
    % gem install serverspec
    ```
* ディレクトリ作成
    ```
    % mkdir serverspec
    % cd serverspec
    ```
* テスト対象の設定
    ```zsh
    % serverspec-init
    ```
    ```
    Select OS type:
      1) UN*X
      2) Windows
    Select number: 1    # OSのタイプを選択、Linuxの為、1を選択
    Select a backend type:
      1) SSH
      2) Exec (local)
    Select number: 2    # 今回はlocalで確認（SSHはサーバーが2つないと作動しない）
    ```
* テストする項目を記載
    ```
    % cd spec
    % cd localhost #デフォルトでディレクトリが作成されている
    % vi sample_spec.rb
    ```
    ```sample_spec.rb
    require 'spec_helper'
    listen_port = 80
    describe package('nginx') do
      it { should be_installed }
    end
    describe port(listen_port) do
      it { should be_listening }
    end
    describe command('curl http://127.0.0.1:#{listen_port}/_plugin/head/ -o /dev/null -w "%{http_code}\n" -s') do
      its(:stdout) { should match /^200$/ }
    end
    ```
    * 今回は、サンプルをもとにNginxの起動を確認
    
* テスト実行
    ```
    % rake spec
    ```
    * ![テスト成功画面](img/lecture11-1.png)
        

---

## 遭遇したエラー
* テスト実行後、テスト項目が全てエラーになる
    *　![エラー画面](img/lecture11_servespecテスト成功)
    *　上記でも記載したが「 serverspec-init」実行後、SSH接続を選んでしまったため、そもそも認証ができなかったと考えられる
    *  「2) Exec (local)」こちらを選ぶ

* Cfnで作成したキーペアの格納場所がわからない
    *  System Managerで確認可能

## 深掘りたいこと
* Nginxではなく、pumaでの実行の際、下記のテスト項目がエラーになった
  * 修正.
    ```sample_spec.rb
      describe command('curl http://127.0.0.1:#{listen_port}/_plugin/head/ -o /dev/null -w "%{http_code}\n" -s') do
      its(:stdout) { should match /^200$/ }
    end
    ```

* これはそもそもNginxが起動するためのテストで他のWebサーバーのテストで使えない可能性がありそう

---
## 感じた事

* 遭遇したエラーの解決策を探すのにかなり時間を使ってしまったが、そもそも構成図がどんなものかを確認することで、認識齟齬をなくせそう
* AWSコースの卒業生のserverspecのテスト項目が豊富なので、全て課題が終わったら、トライする [（参考）](https://github.com/TomooAkamatsu/raisetech-aws2207/blob/main/lecture12/spec/ec2/ec2_spec.rb)
