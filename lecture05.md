# 第5回課題

## 実施内容

### [サンプルアプリケーション](https://github.com/yuta-ushijima/raisetech-live8-sample-app)のデプロイ結果

- 組み込みサーバーのみ  
  ![図](images_lec5/fruit_db_enbded.PNG)  

  - RDSへのデータ登録できていることも確認  
    ![図](images_lec5/check_RDS_table.PNG)  


- サーバーアプリケーションを分離  
  ![図](images_lec5/start_status_nginx_unicorn.PNG)  

  - curlコマンドでunix domain socket経由アクセス接続確認  
    ![図](images_lec5/curl_socket_nginx.PNG)  


- ELB(ALB)追加  
  ![図](images_lec5/access_through_alb.PNG)  


- S3追加  
  ![図](images_lec5/s3_test3.PNG)  
  ![図](images_lec5/s3_test4.PNG)  

### 構成図

- [Draw.io](https://app.diagrams.net/)で作成  
  ![図](images_lec5/.PNG)  


## 所感

- ALBとS3については、マネジメントコンソール内で説明書きを読みながら割とあっさり設定完了できた  
- 今回の課題は各種ソフトウェアのインストール・Ruby on Rails・Unicorn・Nginxの導入をAWSよりも学ぶウエイトが大きかった。 事前に設定方法や手順を整理して臨んだが、これができていなかったらもっと時間がかかったはず。  
- 今回一番の手戻り  
    - NginxとUnicornを両方起動して通信させる際、403エラーの解決に3日要した。原因はNginxの設定ファイル内のtry_filesの記載漏れが原因だが、エラー番号から連想できない箇所で、NginxとUnicornのユーザー不整合やアクセス権限の問題を探してしまった。  
      ![図](images_lec5/forbidden_by_tryfiles.PNG)  


## 備忘録

<details>
<summary>作業工程</summary>

1. EC2にSSHで接続  
   ![図](images_lec5/ssh.PNG)  

2. yumの最新化  
   ![図](/images_lec5/sudo_yum_update.PNG)  

    ```
    sudo yum update
    ```

3. MariaDBのアンインストール
- MariaDBがインストールされていることを確認  
  ![図](images_lec5/yum_installed_mariadb.PNG)  

  ```
  yum list installed | grep mariadb
  ```

- MariaDBの削除\(-yオプションで確認省略\)  
  ![図](images_lec5/mariadb_list_remove.PNG)  

  ```
  sudo yum remove -y mariadb-*
  ```

4. インストール
- git  
  ![図](images_lec5/install_git.PNG)  

  ```
  sudo yum install git  
  ```

- ruby 3.1.2  
    - GPG取得\(GNU Privacy Guard：公開鍵\)・RVMインストール  
      ![図](images_lec5/RVM_GPGkey.PNG)  
      
      ```
      gpg2 --keyserver keyserver.ubuntu.com --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB
      curl -sSL https://get.rvm.io | bash -s stable
      ```
 
    - Rubyのインストール・設定反映・デフォルト設定  
      ![図](images_lec5/source_RVM.PNG)  
      ![図](images_lec5/use_version_RVM.PNG)  

      ```
      source /home/ec2-user/.rvm/scripts/rvm
      rvm use --default 3.1.2
      source ~/.bash_profile
      ruby -v
      ```

- Bundler 2.3.14  
  ![図](images_lec5/installBundler.PNG)  

  ```
  gem install bundler -v 2.3.14
  bundler -v
  ```

- Rails 7.0.4  
  ![図](images_lec5/installRails.PNG)  

  ```
  gem install rails -v 7.0.4
  rails -v
  ```

- Node v17.9.1  
  ![図](images_lec5/installNVM.PNG)  
  ![図](images_lec5/use_version_Node.PNG)  

  ```
  curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.5/install.sh | bash
  . ~/.nvm/nvm.sh
  nvm -v
  nvm install 17.9.1
  nvm use 17.9.1
  node -v
  ```

- yarn 1.22.19  
  ![図](images_lec5/install_use_version_Yarn.PNG)  

  ```
  npm install -g yarn@1.22.19
  yarn -v
  ```

- MySQL  
    - インストール\(参考：[リンク](https://github.com/MasatoshiMizumoto/raisetech_documents/blob/main/aws/docs/install_mysql_on_cloud9_amazon_linux_2.md)\)  
      ![図](images_lec5/installMySQL1.PNG)  
      ![図](images_lec5/installMySQL2.PNG)  

      ```
      curl -fsSL https://raw.githubusercontent.com/MasatoshiMizumoto/raisetech_documents/main/aws/scripts/mysql_amazon_linux_2.sh | sh
      yum list installed | grep mysql
      ```

    - MySQLの起動・停止・状態確認  
      ![図](images_lec5/statusMySQL.PNG)  

      ```
      sudo systemctl start mysqld
      ```

      ```
      sudo service mysqld stop
      ```

      ```
      systemctl status mysqld.service
      ```

5. サンプルアプリケーションのクローン  
   ![図](images_lec5/git_clone.PNG)  

    ```
    git clone https://github.com/yuta-ushijima/raisetech-live8-sample-app.git
    ```

6. サンプルアプリケーションの環境設定と起動

- database.ymlの作成\(サンプルファイルからコピー\)  
  ![図](images_lec5/copy_database_yml.PNG)  

  ```
  cp config/database.yml.sample config/database.yml
  ```

- database.ymlの編集  
  ![図](images_lec5/vi_command_edit.PNG)  

  ```
  default: &default
  adapter: mysql2
  encoding: utf8mb4
  pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>
  username: RDSのユーザー名
  password: RDSのパスワード
  host: RDSのエンドポイント
  ```

- 環境構築  
  ![図](images_lec5/setupDatabase.PNG)  

  ```
  bin/setup
  ```

- アプリケーションサーバーの起動  
  ![図](images_lec5/bin_dev.PNG)  

  ```
  bin/dev
  ```

- EC2のインバウンドルールで3000番ポート追加  
  ![図](images_lec5/EC2_SG_add_port3000.PNG)  

7. Web サーバー\(Nginx\)とAP サーバー\(Unicorn\)の設定  

- Unicornのインストール  

  :::note warn
  組み込みサーバーによる起動成功を確認後に着手
  :::

    - Gemfileに以下コードが記載されていることを確認  

      ```ruby:Gemfile
      gem 'unicorn'
      ```

    - インストールコマンド実行
      ![図](images_lec5/install_bundle_unicorn.PNG)  

      ```
      bundle install
      ```

- 設定用ファイルの作成・編集  
  [`unicorn.rb`](https://github.com/herokaijp/devcenter/wiki/Rails-unicorn#%E8%A8%AD%E5%AE%9A)を編集し、sockteファイル・pidファイルの保存先を見直し  
  ![図](images_lec5/update_pid_socket_folder.PNG)  

  ```
  vi config/unicorn.rb
  ```

- Unicornの起動・停止・状態確認  
  [!NOTE]
  起動時に\-pオプションでポート番号指定、\-Eオプションで環境指定\(`deveopment`は開発環境、`production`は本番環境\)、-Dオプションでデーモン\(常駐\)プロセス  
  

  ```
  bundle exec unicorn -c config/unicorn.rb -p 3000 -E development -D
  ```

  ```
  kill -QUIT `cat tmp/pids/unicorn.pid`
  ```

  [!WARNING]
  pidファイルの保管場所にパスを修正しないと、pidファイルが無い旨のエラーが出て停止できない  


  ```
  ps -ef | grep unicorn | grep -v grep
  ```

  ![図](images_lec5/start_status_unicorn.PNG)  

  エラー時は以下を確認  
  ![図](images_lec5/error_RDS_not_activate1.PNG)  
  ![図](images_lec5/error_RDS_not_activate2.PNG)  
  
  ```
  cat log/unicorn.log
  ```

  Unicorn起動し動作確認  
  ![図](images_lec5/fruit_db_unicorn.PNG)  

  ```
  curl --unix-socket /home/ec2-user/raisetech-live8-sample-app/tmp/sockets/unicorn.sock http://\(パブリックIPアドレス\)
  ```

- Nginxのインストール  

  [!WARNING]
  Unicorn使用し起動成功を確認後に着手
  

  - インストールコマンドの確認  
    ![図](images_lec5/AWS_extras.PNG)  
    ![図](images_lec5/AWS_extras_enable_nginx.PNG)  

    ```
    amazon-linux-extras | grep "nginx"
    sudo amazon-linux-extras enable nginx1
    ```

  - インストールコマンドの実行  
    ![図](images_lec5/install_nginx.PNG)  

    ````
    sudo yum clean metadata
    sudo yum install nginx
    nginx -v
    ```

- 設定用ファイルの作成・編集・内容チェック  
  ![図](images_lec5/.PNG)★  

  ```
  sudo cp -a /etc/nginx/nginx.conf /etc/nginx/nginx.conf.sample
  sudo vi /etc/nginx/nginx.conf
  sudo nginx -t
  ```

  `local@unicorn`の記述の中に`proxy_set_header`がないと以下エラー  
    ![図](images_lec5/error_proxy_set.PNG)  

  `server`の記述の中に`try_files`がないと以下エラー  
    ![図](images_lec5/error_try_files.PNG)  


  - Nginxのの起動・停止・状態確認  
    ![図](images_lec5/start_status_nginx_only.PNG)  
    ![図](images_lec5/test_status_start_nginx.PNG)  
    ![図](images_lec5/ps_aux_unicorn_nginx.PNG)  

    ```
    sudo systemctl start nginx
    ```

    ```
    sudo systemctl stop nginx
    ```

    ```
    systemctl status nginx
    ps aux | grep nginx
    ```

  - EC2インスタンス起動時とあわせた自動起動ON/OFF  

    ```
    sudo systemctl enable nginx
    sudo systemctl disable nginx
    ```

  - エラー時は以下を確認  

    ```
    sudo cat /var/log/nginx/error.log
    ```

- `config/environments/development.rb`の設定変更後、CSS有効化のため以下コマンド実行  
  ![図](images_lec5/css_enhance.PNG)  

  ```
  bin/rails assets:precompile  
  ```

- EC2のインバウンドルールに80番ポート追加  
  ![図](images_lec5/SGupdated.PNG)  

8. ALBの設定

- ALB用のセキュリティグループの設定  
  ![図](images_lec5/.PNG)★  

- ALBの設定  
  ![図](images_lec5/elb-1.PNG)  

- ターゲットグループの設定とヘルスチェック\(要アプリ起動\)  
  ![図](images_lec5/tg-1.PNG)  
  ![図](images_lec5/tg_healsth_check1.PNG)  
  ![図](images_lec5/alb-properties-1.PNG)  

- ALB経由でアクセスしたところブロック  
  ![図](images_lec5/erro_alb_access.PNG)  

- `sconfig/environments/development.rb`の設定変更で解消  
  ![図](images_lec5/update_config_environments_development.PNG)  

9. S3の設定

- S3の作成   
  ![図](images_lec5/S3_properties.PNG)  
  ![図](images_lec5/S3_encryption_default_setting.PNG)  

- IAMユーザーの作成\(S3アクセス用\)  
  - IAMロールを作成しS3FullAccess権限付与  
    ![図](images_lec5/IAM_attatch_Policy.PNG)  
    ![図](images_lec5/IAM-make-key.PNG)  
    ![図](images_lec5/IAM-make-Key-UseCase.PNG)  

  - access_keyとsecret_access_keyを取得  
    ![図](images_lec5/IAM-Key-DL.PNG)  
    ![図](images_lec5/IAM-properties.PNG)  

  - EC2にIAMロール付与  
    ![図](images_lec5/IAMRole_Set_EC2.PNG)  
  - ![図](images_lec5/SetIAMToEC2.PNG)  
    ![図](images_lec5/MakeIAMforEC2.PNG)  

- EC2にS3接続用のIAMロールを作成して付与  
  - S3FullAccess権限付与  
    ![図](images_lec5/.PNG)★  
    ![図](images_lec5/IAMRole_Set_EC2.PNG)  

- Unicornの設定ファイル編集  
  - `Gemfile`の設定変更\(要すれば\)  
    ![図](images_lec5/edit_gemfile.PNG)  

    ```
    gem 'aws-sdk-s3', require: false
    ```

  - `config/storage.yml`の設定変更  
    ![図](images_lec5/edit_storage_yml.PNG)  

    ```
    service: S3
    region: <バケットのリージョン>
    bucket: <バケットの名称>
    access_key_id: <%= Rails.application.credentials.dig(:aws, :access_key_id) %>
    secret_access_key: <%= Rails.application.credentials.dig(:aws, :secret_access_key) %>
    ```

  - S3アクセス用のIAMユーザーのaccess_keyとsecret_access_keyを登録  
    ![図](images_lec5/edit_master_key.PNG)  

    ```
    EDITOR=vi rails credentials:edit
    access_key: ＜IAMユーザーのaccess_key＞
    secret_access_key: ＜IAMユーザーのsecret_access_key＞
    ```

    - 設定時、次のエラーメッセージが出たので、古い`credentials.yml.enc`をリネームして退避し、`config/master.key`、`credentials.yml.enc`を新しく作成  

      ```
      create  config/master.key
      Couldn't decrypt config/credentials.yml.enc. Perhaps you passed the wrong key?
      ```

      ![図](images_lec5/editoor_vi_credentials.PNG)  

      ```
      mv config/credentials.yml.enc config/credentials_old.yml.enc
      touch config/master.key
      ```

  - `config/environments/development.rb`の設定変更  
    ![図](images_lec5/edit_config_environments_development_rb.PNG)  

    ```
    config.active_storage.service = :amazon
    ```

- EC2、Nginx、Unicorn、RDSを起動して動作確認  
  ![図](images_lec5/S3_test1.PNG)  
  ![図](images_lec5/s3_test2.PNG)  

  </details>
