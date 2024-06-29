ポートフォリオで使用した</br>
開発環境の構築手順
=========

使用したもの
---------
- AWS
  - cloud9
  - EC2
- Git
- Github
- Ruby
- Rails
</br></br></br>

前提
---------
- 本番環境が構築できている
- EC2インスタンスを作成する際に生成したキーペアを保持している
</br></br></br></br>

構築
=========
</br>

Cloud9の作成
---------
※前提：</br>
- AWSのアカウントを作成している
- リージョンを「東京」に変更している
</br></br>

1. AWSの検索窓から「Cloud9」を検索する
2. 「環境を作成」
3. 「名前」「説明」を入力　※「説明」の入力は任意
4. 環境タイプ「新しいEC2インスタンス」を選択
5. インスタンスタイプ「t2.micro(1GiB RAM + 1 vCPU)を選択（無料枠）
6. 「作成」
7. Cloud9の構築が始まるため、完了するまで待つ
8. 構築が完了したら「Cloud9 IDE]の「開く」を選択し、開発画面へ移動
9. 完了
</br></br></br>

GitHubに公開鍵を登録する
---------
※前提：
- GitHubのアカウントを作成している
</br>
※公開鍵を登録する理由：
- メールアドレスとパスワードを用いて認証するより、情報漏洩の可能性が低くなる
- 一度設定すれば自動で認証されるため、パスワードのように定期的に入力しなおす必要がなくなる
</br></br>

※公開鍵→サービス（GitHub）に登録する鍵</br>
※秘密鍵→自分（Cloud9）で保持する鍵
※公開鍵は秘密鍵でのみ復元できる
</br></br>

1. Cloud9でターミナルを開き、`ssh-keygen`コマンドで公開鍵と秘密鍵を作成する
2. 実行後に求められる入力は、すべて未入力の状態でEnterキー
3. 公開鍵と秘密鍵が生成されたか`ls ~/.ssh`コマンドで確認する</br>
   以下のように表示されていれば問題ない
   ```
   username:~/environment $ ls ~/.ssh
    authorized_keys  id_rsa  id_rsa.pub
   ```
4. コマンド`cat ~/.ssh/id_rsa.pub`を実行し、出力された公開鍵を"ssh-rsa"からコピーする
5. GitHubを開き、「Settings」を選択
6. 左側メニュー「SSH and GPG keys」を選択後、右側「New SSH key」をクリック
7. 「Title」を入力（どのPCの鍵か分かればOK）
8. 「Key type」は「Authentication Key」を選択、「Key」にコピーした公開鍵をペーストする
9. 「Add SHH Key」をクリックして完了
</br></br></br>

Cloud9とGitHubの接続を確認する
---------
1. cloud9のターミナルでコマンド`ssh -T git@github.com`を実行
2. 初回接続の場合、以下の表示が出力されるので「yes」を入力してEnterキー
   ```
   The authenticity of host 'github.com (×××.×××.×××.×××)' can't be established.
    RSA key fingerprint is SHA256:nThbg6kXUpJWGl7E1IGOCspRomTxdCARLviKw6E5SY8.
    Are you sure you want to continue connecting (yes/no/[fingerprint])?
   ```
3. 以下の表示が出力されたら接続成功
   ```
   Hi hogehoge! You've successfully authenticated, but GitHub does not provide shell access.
    Connection to github.com closed.
   ```
</br></br>

Rubyをインストールする
---------
1. Cloud9のターミナルで以下のコマンドを実行
   - 実行していること
     - RVM（Ruby Version Manager）を最新の安定版にアップデート
     - RVMを使用してRubyのバージョン3.1.2をインストール
     - インストールしたRuby 3.1.2をデフォルトに設定</br>
       ※設定しないとCloud9の再起動後にCloud9のデフォルト(Ruby2.6.3)になるため
   ```
   ec2-user:~/environment $ rvm get stable
   ec2-user:~/environment $ rvm install 3.1.2
   ec2-user:~/environment $ rvm --default use 3.1.2
   ```
2. 現在のRubyのバージョンとデフォルト設定をコマンド`rvm list`を実行して確認する
3. 以下のように出力されたら完了
   ```
    ec2-user:~/environment $ rvm list
    ruby-3.0.6 [ x86_64 ]
    =* ruby-3.1.2 [ x86_64 ]
    
    # => - current
    # =* - current && default
    #  * - default
   ```
   </br></br></br>

Cloud9の容量を変更する
---------
1. Cloud9のコンソール画面で、使用するCloud9を選択した状態で「詳細を表示」をクリック
2. 「EC2インスタンスの管理」をクリック
3. 使用するEC2インスタンスのインスタンスIDをクリック
4. 「ストレージ」タブをクリック
5. 「ボリュームID」をクリック
6. ボリューム名のチェックボックスにチェックをつけ、アクションメニューのプルダウンメニューから「ボリュームの変更」をクリック
7. 「サイズ」のフォームに「20」と入力して「変更」、表示されるポップアップも「変更」して完了
8. インスタンスの状態が「実行中」だった場合は、一旦「停止」してから再度開始して使用する
</br></br></br>

Railsをインストールする
---------
※前提：</br>
以下のコマンドでRubyのバージョンが3.1.2であることを確認する
```
username:~/environment $ ruby -v
ruby 3.1.2p20 (2022-04-12 revision 4491bb740a) [x86_64-linux] 
```
</br></br>
1. Cloud9で必要なソフトウェアをインストールする
   ```
   username:~/environment $ wget https://wals.s3.ap-northeast-1.amazonaws.com/curriculum/rails/environment.sh 
   ```
2. ファイル`environment.sh`がダウンロードされていることを確認する
3. 以下のコマンドを実行する
   ```
   username:~/environment $ sh environment.sh
   ```
4. インストールされたソフトウェアが正しいバージョンでインストールされたかを確認する
   ```
    username:~/environment $ rails -v
    (出力)Rails 6.1.4
   ```
5. 他の必要なソフトウェアのバージョンを確認する
   ```
    username:~/environment $ node -v
    (出力)v18.17.1
    username:~/environment $ yarn -v
    (出力)1.22.19
    username:~/environment $ convert -version
    (出力)Version: ImageMagick 7.1.1-5 Q16-HDRI x86_64 92a5afcfa:20230326 https://imagemagick.org
    (出力)Copyright: (C) 1999 ImageMagick Studio LLC
    (出力)License: https://imagemagick.org/script/license.php
    (出力)Features: Cipher DPC HDRI OpenMP(4.5) 
    (出力)Delegates (built-in): jng jpeg lzma png tiff webp xml zlib
    (出力)Compiler: gcc (7.3)
   ```
6. sqliteのバージョンの確認をする
   ```
    username:~/environment $ irb
    3.1.2 :001 > require 'sqlite3'(入力する)
    (出力) => true 
    3.1.2 :002 > SQLite3::SQLITE_VERSION(入力する)
    (出力) => "3.36.0" 
    3.1.2 :003 > exit(入力する)
   ```
</br></br>

EC2にログインする
---------
1. Cloud9にキーペアをドラッグ&ドロップで転送する
2. ターミナル上で"/home/ec2-user/environment"にいることを確認し、</br>
  コマンド`mv practice-aws.pem ~/.ssh/`を実行
3. コマンド`sudo chmod 600 ~/.ssh/practice-aws.pem`でキーペアファイルの権限を変更
4. EC2インスタンスのパブリックIPv4アドレスを確認、コピーしておく
5. 以下のコマンドでSSH接続する
   ```
   username:~/environment $ ssh -i ~/.ssh/practice-aws.pem ec2-user@パブリックIPアドレス
   ```
6. 初回接続の場合、以下の表示が出力されるので「yes」を入力してEnterキー
   ```
    The authenticity of host 'パブリックIPアドレス' can't be established.
    ECDSA key fingerprint is SHA256:+MxOlUCf7HpfqkE4syIBDBSr0xt6Mq8sl/sS27TT1Mk.
    Are you sure you want to continue connecting (yes/no)?
   ```
7. 鳥のようなアスキーアートが出力され、デプロイ先にアクセスできたら完了
</br></br></br>

リポジトリをクローンする
---------
1.  Cloud9で以下のコマンドを実行
  ```
  $ git clone リポジトリのURL
  $ cd アプリケーション名
  $ scp -i ~/.ssh/practice-aws.pem ec2-user@IPアドレス:GitHubのリポジトリ名/.env ./
  # 上記コマンドでデプロイ先にある .env をコピー
  $ scp -i ~/.ssh/practice-aws.pem ec2-user@IPアドレス:GitHubのリポジトリ名/config/master.key ./config/
  # 同様にmaster.keyをコピー
  $ bundle install
  $ rails db:migrate
  $ rails db:seed
  $ yarn install
  $ rails s
  ```
2. 完了
</br></br></br>

以上
