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
- 以下のコマンドを実行
- 
