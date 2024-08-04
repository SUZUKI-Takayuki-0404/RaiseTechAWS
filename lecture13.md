# AWSフルコース講座 第13回課題

## 実施内容


### 実施結果

### 所感

## 備忘録

<details>
<summary>作業工程</summary>
</details>

- Ansibleのインストール (Ubuntu 22.04 LTS)
  - インストール準備とインストール
    ```
    sudo apt-get update
    ```
    ![図](images_lec13/0-1_sudo_apt-get_update.PNG)  
    ```
    sudo apt-get install software-properties-common
    ```
    ![図](images_lec13/0-2_sudo_apt-get_install_software-properties-common.PNG)  
    ```
    sudo apt-add-repository --yes --update ppa:ansible/ansible
    ```
    ![図](images_lec13/0-3_sudo_apt-add-repository_--yes_--update_ppa_ansible_ansible.PNG)  
    ```
    sudo apt-get install ansible
    ```
    ![図](images_lec13/0-4_sudo_apt-get_install_ansible.PNG)  
    ```
    ansible --version
    ```
    ![図](images_lec13/0-5_ansible_--version.PNG)  
- Ansibleによる環境構築準備  
  - 階層構造  
    ![図](images_lec13/2-0-2_ansible_folder_tree.PNG)  
  
  - Playbookの作成 `site.yml`  
    ![図](images_lec13/1-1_ansible_site_yml.PNG)  
  
  - Role
    - yum update (`sudo yum update`に相当)  
      ![図](images_lec13/1-2_ansible_roles_yum-update_tasks_main_yml.PNG)  
    - Rolesの階層は以下コマンドで作成可(ansible ディレクトリで実行)
      ```
      ansible-galaxy init roles/<Rile名>
      ```
      ![図](images_lec13/2-0-1_ansible-galaxy_init_roles_mysqlPNG.PNG)  
  
  - Inventoryの作成
    - ターゲットノード(EC2)のIPアドレスとユーザー名を定義⇒ドライランでエラー発生  
      ![図](images_lec13/1-3_ansible_inventories_host-err1.PNG)  
    - ポート番号を追加で定義⇒ドライランでエラー発生  
      ![図](images_lec13/1-3_ansible_inventories_host-err2.PNG)  
    - SSH接続用のpemキーのパスを追加⇒OK  
      ![図](images_lec13/1-3_ansible_inventories_host-ok.PNG)  
  
  - Ansible.cfgの作成
    - Inventoryファイルのパスを追加し、`ansible-playbook`コマンド時の追記を省略  
      ![図](images_lec13/1-4_ansible_cfg_inventory_path.PNG)  
    - EC2初回SSH接続時のfingerprintダイアログを発生させないため、`host_key_checking=False`の設定を追加  
      ![図](images_lec13/1-3-1_ansible_inventories_host_key_check_false.PNG)  

- Playbookの作成(Role別)
  - MySQL
    - MySQLのRepositoryをEC2に追加する際、コマンドライン手入力で使っていたURLではエラー出力（コマンドライン入力時は成功）
    - EC2に直接追加せず、EC2の一時ファイル保管ディレクトリに一度ダウンロードを試行しても、403エラー出力（ブラウザにURL直接入力時は成功）
    - curlコマンドでURLを確認するとリダイレクトされている事が判明（ステータスコード302）  
      ![図](images_lec13/2-1-1_mysql_download-url-err1.PNG)
      ![図](images_lec13/2-1-1_mysql_download-url-err2.PNG)  
    - リダイレクト先のURLに変更で成功  
      ![図](images_lec13/2-1-1_mysql_download-url-ok.PNG)  
    - Mysql-community-serverパッケージは、実際にMySQLのRepositoryをダウンロードしないと実行できず、ドライランではエラーとなるため、`ignore_errors`設定を追加(動作確認後に削除)  
      ![図](images_lec13/2-2_mysql-community-server_ignore_errors.PNG)  
  
  - git  
    ![図](images_lec13/3-3_yum_git.PNG)  
  
  - anyenv  
    - 個別のroleフォルダの内容を一括作成  
      ![図](images_lec13/3-3-0_anyenv_ansible-galaxy_init_roles_anyenv.PNG)  
    - `shell`モジュールや`command`モジュールは以下設定を追加
      - 実行するとchangedが返されるので、`changed_when: no`を設定
      - 冪等性の理由から再実行されないよう条件を追加  
        ![図](images_lec13/3-3-1_anyenv_when_changed_no.PNG)  
      - ansibleの初回実行時は`anyenv -v`コマンドは失敗するが、以降は成功するので、失敗時のみ各処理を実行させる  
        ![図](images_lec13/3-3-1_anyenv_ansible-playbook_failed1.PNG)  
    - `anyenv install -init`コマンド実行時に`y/N`回答ダイアログが出るので、`yes`コマンドで対応  
      ![図](images_lec13/3-3-2_anyenv_install_init_yes_answer.PNG)  
      ![図](images_lec13/3-3-2_anyenv_install_init_yes.PNG)  
    - `anyenv install rbenv`コマンドはフルパス指定にしないとコマンドが認識されない  
      ![図](images_lec13/3-3-1_anyenv_fullpath.PNG)  
    - `rbenv install 3.2.3`コマンドは`install`コマンドが認識されない(PATH変数追加でもNG)ため、環境設定をロードするコマンドと合わせて実行
      - フルパス記述していない場合は`command not found`のメッセージ  
        ![図](images_lec13/3-3-2_anyenv_rb_nod_install_failled1.PNG)  
      - フルパス記述しても、`no such command 'install'`のメッセージ  
        ![図](images_lec13/3-3-2_anyenv_rb_nod_install_fullpath.PNG)  
        ![図](images_lec13/3-3-2_anyenv_rb_nod_install_failled2.PNG)  
      - `rbenv install`コマンドの前に環境設定を読み込ませるコマンドを追加することで成功  
        ![図](images_lec13/3-3-3_anyenv_rb_nod_install_ok.PNG)  

  - rails & bundler & yarn
    - Bundlerはデフォルト状態のバージョンが異なるので`gem update --system`コマンドで指定バージョンに変更  
      ![図](images_lec13/4-1-1_bundler_-v_default-version.PNG)  
    - Railsはインストール時にdocumentが無い旨のエラーが出るので、`--no-document`オプションを追加  
      ![図](images_lec13/4-2-1_rails_install_fail1.PNG)  
      ![図](images_lec13/4-2-1_rails_install_ok_with_--no-document.PNG)  
    - Yarnはインストール後、`nodenv rehash`コマンド実行により`yarn`コマンドが使えるようになるので、これをPATH変数に追加  
      ![図](images_lec13/4-3-1_yarn_command_fail.PNG)  
      ![図](images_lec13/4-3-1_yarn_ls_yarn_exists.PNG)  
      ![図](images_lec13/4-3-1_yarn_add_PATH.PNG)  

  - ImageMagick
    - epelのインストール時に'y/N'ダイアログが出るので、'yes'コマンドで対応  
      ![図](images_lec13/5-1-1_ImageMagick_install-epel_yn-diarog.PNG)  
    - 'epel-release'等のパッケージインストールはrootユーザー必須  
      ![図](images_lec13/5-1-1_ImageMagick_install-epel-release-and-others_failed_not-root.PNG)  
    - 'remi-release-7'および'ImageMagick7'のインストール時は`sudo`コマンド必須
      ![図](images_lec13/5-1-1_ImageMagick_install-remi_failed_sudo-not-found.PNG)  

  - raisetech-live8-sample-app
    - 課題5で作成したサンプルアプリを作動させたEC2インスタンスのファイルをテンプレート（拡張子j2）に使用  
      - EC2インスタンスからローカルにコピー
        ```
        scp -i (使用するpemキー、パス含む) ec2-user@XXX.XXX.XXX.XXX:/home/ec2-user/(コピー元ファイル)  /home/AAAA/(転送先ディレクトリ)
        ```
        ![図](images_lec13/6-1-1_scp_template.PNG)  
        ![図](images_lec13/6-1-2_scp_config_template2.PNG)
        ![図](images_lec13/6-1-3_extension_j2.PNG)
          
    - `/config/database.yml`は変更箇所/内容が多いのでテンプレートとして使用  
      - templateモジュール使用時に`remote_src`オプションを追加するよう書かれたエラーが表示されたので追加  
      ![図](images_lec13/6-2-1_template_failed-remote_src.PNG)  
      ![図](images_lec13/6-2-2_template_ok.PNG)  
    - `bin/setup`コマンドの実行が途中で止まってしまう
      - `timeout`設定をしておかないとansibleの実行が自動で止まらない  
        ![図](images_lec13/6-3-1_sampleapp_binsetup_failed-timeout.PNG)  
      - 途中で進まなくなったインスタンスを手動でコマンド実行すると、特定のgemパッケージのインストールが終わっていない
        ![図](images_lec13/6-3-2_sampleapp_binsetup_error-unfinished_gems1.PNG)  
        ![図](images_lec13/6-3-3_sampleapp_binsetup_error-unfinished_gems2.PNG)  
        ![図](images_lec13/6-3-4_sampleapp_binsetup_error-unfinished_gems3.PNG)  
      - コマンド実行時に追加インストールされているgemパッケージ  
        ![図](images_lec13/6-3-5_sampleapp_unfinished_gems.PNG)  
      - 途中でインストールが止まってしまうgemパッケージを個別インストールしたうえで`bin/setup`コマンドを実行することで解決  
        ![図](images_lec13/6-3-6_sampleapp_binsetup_ok.PNG)  
    - `yarn`のインストールコマンドも実行したが、試しに手作業で`bin/dev`実行するとエラー  
      ![図](images_lec13/6-4-1_sampleapp_bindev_failed-yarn.PNG)
      - `raisetech-live8-sample-app`ディレクトリ上で`yarn`のインストールコマンド実行  
        ![図](images_lec13/6-4-2_sampleapp_bindev_yarn_ok.PNG)
    - 以上までのインストールが完了したアプリで`bin/dev`コマンドを実行すると、正常に作動
      ![図](images_lec13/6-5-1_bindev_ok.PNG)

  - systemd
    - `puma.service.sample`を`/etc/systemd/system/puma.service`としてコピー  
    - systemdがインスタンス起動時に自動起動されるよう設定(今時点では起動不要なのでstoppedに設定)  
      ![図](images_lec13/7-1-1_systemd_setup.PNG)  
    - 上記だけだとSystemd起動エラー発生  
      ![図](images_lec13/7-2-1_systemd_start-failed1.PNG)  
      ![図](images_lec13/7-2-2_systemd_start-failed2.PNG)  
      ![図](images_lec13/X-9_systemd-start-failed_journalctrl_-xe.PNG)
      - ChatGPTによる検討もしたが結局手詰まり  
        ![図](images_lec13/7-3-1_systemd_start_chatGPT1.PNG)  
        ![図](images_lec13/7-3-2_systemd_start_chatGPT2.PNG)  
        ![図](images_lec13/X-9_systemd-start-failed_journalctrl_-xe_puma.PNG)
      - `bin/dev`コマンドを一度実行するとエラー発生しなくなることが判明  
        ![図](images_lec13/7-4-1_systemd-start_after-bindev.PNG)  
        ![図](images_lec13/7-4-2_systemd_start_after-bindev2.PNG)
      - `bin/dev`コマンド内で呼び出しているgemパッケージをインストールしても変化なし  
        ![図](images_lec13/7-4-3_systemd_inside-bindev.PNG)
      - 試行錯誤の結果、`bin/dev`コマンドを一度実行するとエラーは解消するが、正常処理の範囲内では実行を止められないので、タイムアウトさせて終了させるるが、サンプルアプリケーションが起動したままになってしまうので、インスタンスを再起動  
        ![図](images_lec13/7-4-4_systemd_bindev-with-timeout.PNG)  
        ![図](images_lec13/7-4-5_systemd_reboot-instance.PNG)

  - Nginx
    - `/etc/nginx/conf.d/app.conf`はファイル自体無いので課題5で作成したものをテンプレートして使用  
      ![図](images_lec13/8-1-1_scp_nginx_app-conf_template.PNG)  
    - テンプレートファイルから`app.conf`作成にはroot権限を要求されるので追加  
      ![図](images_lec13/8-2-1_nginx_appconf_failed-not-writable.PNG)  
      ![図](images_lec13/8-2-2_nginx_appconf_ok.PNG)  
    - `nginx.conf`の実行ユーザーを`replace`モジュールで変更  
    - Nginxがインスタンス起動時に自動起動されるよう設定(今時点では起動不要なのでstoppedに設定)  
      ![図](images_lec13/8-3-1_nginx_setup.PNG)  

  - S3 storage  
    - `/config/environments/development.rb`の一部内容を書き換え  
      ![図](images_lec13/9-1-1_scp_development-rb_template.PNG)  
      - 以下はロードバランサ経由でアクセスした際のエラー防止策であり画像保存先をS3に変更する目的ではないが、併せて設定しておく  
        ![図](images_lec13/9-1-2_storage-replace-devlopment.PNG)  
    - `config/storage/yml`の一部内容を書き換え  
      ![図](images_lec13/9-2-1_scp_config_strage_template.PNG)
      - ansible.builtin.replaceモジュールで文字列置換に予期せぬエラー発生  
        ![図](images_lec13/9-2-2_storage_replace_failed.PNG)  
      - 変換対象の文字列から`['を除外すると成功  
        ![図](images_lec13/9-2-3_storage_replace_failed2.PNG)  
        ![図](images_lec13/9-2-4_storage_replace_ok.PNG)  
  
  - 全体テスト  
    - `yum`の最新化で当初は出なかったエラーが発生    
      ![図](images_lec13/X10-1-1_overall_test_yum_error.PNG)  
      - chatGPTにソースコードとエラーコードを分析させて対策追加    
        ![図](images_lec13/10-1-2_overall_test_yum_error_countermeasure.PNG)  
        ![図](images_lec13/10-1-3_overall_test_yum_error_countermeasure2.PNG)  

- CircleCIへのAnsible実装


- Severspec

      ![図]()  
      ![図]()  
      ![図]()  
      ![図]()  
      ![図]()  


> [!NOTE]  
> サンプル

> [!TIP]  
> サンプル

> [!IMPORTANT]  
> サンプル

> [!WARNING]  
> サンプル

> [!CAUTION]  
> サンプル

![図]() 
