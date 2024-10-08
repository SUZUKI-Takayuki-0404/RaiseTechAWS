# AWSフルコース講座 第13回課題

## 実施内容

CircleCI の[サンプル](https://github.com/MasatoshiMizumoto/raisetech_documents/tree/main/aws/samples/circleci)に ServerSpec や Ansible の処理を追加し、一連の処理を成功させる。  
- 構築した環境
  - [課題5](lecture05.md)で実装した[サンプルアプリ](https://github.com/yuta-ushijima/raisetech-live8-sample-app.git)をAnsibleで実装自動化  
    ![図](images_lec13/13-1-1_overview_lect13.jpg)  
  - [課題10](lecture10.md)でCloudformationにより構築自動化した環境にVPC Flow logも追加  
  - [課題11](lecture11.md)で自動テストコードを作成したServerspecを追加  
  - [課題12](lecture12.md)でCfn-lintによりセキュリティ指摘されたRDSのパスワードをSystems Managerのパラメータストアに追加  

## 実施結果

[![CircleCI](https://dl.circleci.com/status-badge/img/gh/SUZUKI-Takayuki-0404/Lecture13-CI/tree/main.svg?style=svg)](https://dl.circleci.com/status-badge/redirect/gh/SUZUKI-Takayuki-0404/Lecture13-CI/tree/main)  

- CircleCIによる一連の自動化処理が成功したことを確認した  
  - 作成したソースコード等を[Lecture13-CI](https://github.com/SUZUKI-Takayuki-0404/Lecture13-CI)に保管  
  - Cfn-lint  
    ![図](images_lec13/11-3-21_cfn_lint_success.PNG)  
  - Cloudformation  
    ![図](images_lec13/11-3-23_integration-test_cfn.PNG)  
    ![図](images_lec13/11-3-24_integration-test_cfn-ok.PNG)  
  - Ansible  
    ![図](images_lec13/11-5-33_integration-test_ans-start.PNG)  
    ![図](images_lec13/11-5-28_ans_execution_ok.PNG)  
  - Serverspec  
    ![図](images_lec13/11-6-26_spec_ok.PNG)  
  - CricleCI workflow  
    ![図](images_lec13/12-1-1_integration-test_start.PNG)  
    ![図](images_lec13/12-1-2_integration-test_cfn-start.PNG)  
    注釈：直下に示すCloudformationからAnsibleに渡した変数は、セキュリティのため非表示に変更し、パスワード変更・CircleCiから履歴削除済み \(手戻り事例を参照\)  
    ![図](images_lec13/12-1-3_integration-test_vars.PNG)  
    ![図](images_lec13/11-4-14_aws_log_without_sensitive_info.PNG)  
    全工程が成功  
    ![図](images_lec13/12-1-4_all_complete.PNG)  
- 自動構築したAWS環境上でサンプルアプリが正常に動作することを確認した  
    ![図](images_lec13/11-5-32_app_ok.PNG)  

## 所感

- 過去の課題で手作業で構築してきた環境が全て自動化され、同じ環境を再構築するときの手間が著しく減ることが分かった。（GitHubにpush後、30分程度で自動構築が完了した）  
- 自動化のための一連のソースコード作成工数を考えると、自動化のメリットが充分あるか否かはよく検討する必要がある。
- AnsibleおよびServerspecをCircleCIに処理追加した際、手作業では問題なく動いた処理でエラーが発生し、ソースコードを修正する必要があるなど、ツールのクセを理解する必要がある。

## 主な手戻り事例
 
- Cloudformation
  - CircleCIによる実行のために権限設定が必要  
    ![図](images_lec13/11-3-22_IAM_permission_list.PNG)  
- AWS CLI \(Ansibleへの変数の引継ぎ\)  
  - 出力した値を変数に格納しようとした際に失敗 ⇒ 出力と変数格納を別コマンドに分割で解消  
    ![図](images_lec13/11-4-9_aws_rds_id_ok.PNG)  
  - CircleCIのログにRDSのID・PASSを表示させてしまい機密情報流出 ⇒ AWS Systems Managerに登録したPASSの変更だけでなく、CircleCIのプロジェクト削除で処置  
    - CircleCIのサポートセンターに依頼し、サポートセンター担当者の方からのメールによる意思確認の後、プロジェクト\(全ビルド履歴\)の削除が完了  
      ![図](images_lec13/11-4-15_deletion_request.PNG)  
      ![図](images_lec13/11-4-16_deletion_email_communication.PNG)  
    - 参考情報 \(プロジェクト削除\)  
      - 現在のCircleCIでは、特定のビルドだけ削除は不可能：[リンク](https://support.circleci.com/hc/en-us/articles/360036286553-Can-I-delete-a-specific-build)  
      - プロジェクト削除で全ビルド履歴を削除可能（無料プランでも90日で履歴削除）：[リンク](https://support.circleci.com/hc/en-us/articles/21040161057947-Can-I-delete-a-project)  
      - 削除依頼の手順：[リンク](https://circleci.com/docs/stop-building-a-project-on-circleci/#remove-a-project-from-circleci)  
- Ansible  
  - MySQLダウンロード時に、コマンドラインでは成功したURLでエラー発生 ⇒ curlコマンドでステータスが302であり、リダイレクト先のURLに変更で成功  
    ![図](images_lec13/2-1-1_mysql_download-url-err1.PNG)  
  - ansible.cfgの内容が読み込まれない ⇒ ルートディレクトリに置く事で解決  
    ![図](images_lec13/11-5-17_ansible_timeout_cfg-out-of_ansible-folder.PNG)  
  - 手作業では正常に動作した`timeout`の項目がCircleCIではエラー ⇒ `shell`コマンド内で`timeout`オプションを追加し解決  
    ![図](images_lec13/11-5-23_ans_install_timeout-update.PNG)  
  - CircleCIはアウトプットが無いと10分で処理を中断し、時間のかかるインストール工程が失敗 ⇒ CircleCIのタイムアウト時間を延長  
    ![図](images_lec13/11-5-33_integration-test_ans_fail_timeout.PNG)  
- Serverspec  
  - 手作業では正常に動作した指定ポート番号のListen確認がCircleCIではエラー ⇒ パスが異なっていたので、`spec_helper`に環境変数を追加  
    ![図](images_lec13/11-6-25_spec_set_path_ok.PNG)  

## 備忘録

<details>
<summary>Ansibleのインストール</summary>

- Ansibleのインストール (`Ubuntu 22.04 LTS`向け)
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
    - Inventoryファイルのパスを追加し、`ansible-playbook`コマンド時のオプション追記を省略  
      ![図](images_lec13/1-4_ansible_cfg_inventory_path.PNG)  
    - EC2初回SSH接続時のfingerprintダイアログを発生させないため、`host_key_checking=False`の設定を追加  
      ![図](images_lec13/1-3-1_ansible_inventories_host_key_check_false.PNG)  

</details>
<details>
<summary>Playbookの作成</summary>

- Playbookの作成(Role別)
  - MySQL
    - MySQLのRepositoryをEC2に追加する際、コマンドラインで使ったダウンロード元URLでエラー（コマンドライン入力時は成功）
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
    - 起動エラー処置  
      - 上記だけだとSystemd起動エラー発生  
        ![図](images_lec13/7-2-1_systemd_start-failed1.PNG)  
        ![図](images_lec13/7-2-2_systemd_start-failed2.PNG)  
        ![図](images_lec13/7-2-3_systemd-start-failed_journalctrl_-xe.PNG)  
      - ChatGPTによる検討もしたが結局手詰まり  
        ![図](images_lec13/7-3-1_systemd_start_chatGPT1.PNG)  
        ![図](images_lec13/7-3-2_systemd_start_chatGPT2.PNG)  
        ![図](images_lec13/7-3-3_systemd-start-failed_journalctrl_-xe_puma.PNG)  
      - `bin/dev`コマンドを一度実行するとエラー発生しなくなることが判明  
        ![図](images_lec13/7-4-1_systemd-start_after-bindev.PNG)  
        ![図](images_lec13/7-4-2_systemd_start_after-bindev2.PNG)  
      - `bin/dev`コマンド内で呼び出しているgemパッケージをインストールしても変化なし  
        ![図](images_lec13/7-4-3_systemd_inside-bindev.PNG)  
      - 試行錯誤の結果、`bin/dev`コマンドを一度実行するとエラーは解消するが、正常処理の範囲内では実行を止められないので、タイムアウトさせて終了させるが、サンプルアプリケーションが起動したままになってしまうので、インスタンスを再起動  
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
      ![図](images_lec13/10-1-1_overall_test_yum_error.PNG)  
      - chatGPTにソースコードとエラーコードを分析させて対策追加  
        ![図](images_lec13/10-1-2_overall_test_yum_error_countermeasure.PNG)  
        ![図](images_lec13/10-1-3_overall_test_yum_error_countermeasure2.PNG)  

</details>
<details>
<summary>CircleCIへのCloudformation実装</summary>

- CircleCIへのCloudformation実装
  - CircleCI
    - CircleCIの適用先となるGitHubリポジトリを新規作成  
      ![図](images_lec13/11-1-1_create_new_repo.PNG)  
    - ローカルにクローンし、`.gitignore`作成およびテンプレートファイル保管してmainブランチにpush  
      ![図](images_lec13/11-1-2_create_new_repo.PNG)  
      ![図](images_lec13/11-1-3_create_files.PNG)  
      ![図](images_lec13/11-1-4_merge_1st_branch.PNG)  
      ![図](images_lec13/11-1-5_create_files2.PNG)  
    - CircleCI上で新規ブランチに`say-hello-workflow`を作成し、リネーム後にローカルへ`git fetch`  
      ![図](images_lec13/11-2-1_select_new_project.PNG)  
      ![図](images_lec13/11-2-2_starter_pipeline_new_branch.PNG)  
      ![図](images_lec13/11-2-3_starter_pipeline_new_branch2.PNG)  
      ![図](images_lec13/11-2-4_rename_branch.PNG)  
      ![図](images_lec13/11-2-5_rename_branch2.PNG)  
  - Cloudformation  
    - CloudformationおよびAWS CLIのorbsを`config.yml`に追加  
      ![図](images_lec13/11-3-1_orbs_add_aws-cloudformation_aws-cli.PNG)  
    - 権限不足でエラーが出るので専用のIAMユーザーを作成し権限を追加＆CircleCIの環境変数にアクセスキーとリージョン情報を登録  
      ![図](images_lec13/11-3-2_cfn_vpc_failed.PNG)  
      ![図](images_lec13/11-3-3_cfn_IAM_CircleCI2.PNG)  
      ![図](images_lec13/11-3-4_cfn_IAM_CircleCI4.PNG)  
      ![図](images_lec13/11-3-5_cfn_vpc_failed3_cfn_access-denied-IAM.PNG)  
      ![図](images_lec13/11-3-6_cfn_IAM_REGION.PNG)  
      ![図](images_lec13/11-3-7_cfn_IAM_key.PNG)  
      ![図](images_lec13/11-3-8_cfn_IAM_key_PASS.PNG)  
      ![図](images_lec13/11-3-9_cfn_IAM_key_ID.PNG)  
      ![図](images_lec13/11-3-10_cfn_ENV-VALs.PNG)  
    - CircleCI未承認のorbsを使うには設定変更が必要  
      ![図](images_lec13/11-3-11_cfn_vpc_failed2_uncertified_orbs.PNG)  
      ![図](images_lec13/11-3-12_cfn_vpc_failed2_uncertified_orbs2.PNG)  
      ![図](images_lec13/11-3-13_cfn_vpc_failed2_uncertified_orbs3.PNG)  
    - CloudFormationのへのアクセス権限も必要  
      ![図](images_lec13/11-3-14_cfn_vpc_failed3_cfn_access-denied.PNG)  
      ![図](images_lec13/11-3-15_cfn_vpc_failed3_cfn_access-added.PNG)  
      ![図](images_lec13/11-3-16_cfn_vpc_ok_cfn_access-added.PNG)  
    - IAMの権限設定\(最終的な状態\)  
      ![図](images_lec13/11-3-22_IAM_permission_list.PNG)  
    - IAMの設定変更\(今回はロールの作成\)を行っているテンプレートファイルには追加の設定項目`CAPABILITY_NAMED_IAM`が必要  
      ![図](images_lec13/11-3-17_cfn_error_CAPABILITY_NAMED_IAM.PNG)  
      ![図](images_lec13/11-3-18_cfn_error_CAPABILITY_NAMED_IAM2.PNG)  
    - 参考：CircleCIのエラーメッセージの詳細説明機能もエラー確認に有益  
      ![図](images_lec13/11-3-19_cfn_error_intelligent_summarize_failure_on.PNG)  
    - Systems Managerにテンプレートファイルへのハードコーディングを避けたい情報を登録  
      - Amazon SNS向けメールアドレス
      - RDS向けパスワード(SecureString対応)
        - 補足：今回はパスワードローテーションを使用しない、使用料無料の理由から、Secrets ManagerではなくSystems Managerを使用  
          ![図](images_lec13/11-3-20_cfn_ssm_created.PNG)  
    - AWS SNSのサブスクリプションの登録確認メール受信 ⇒ Confirmすれば設定完了  
      ![図](images_lec13/11-3-23_integration-test_cfn_sns.PNG)  
    - [第12回課題](lecture12.md)で使用したcfn-lintによるセキュリティチェックもパスしていることを確認  
      ![図](images_lec13/11-3-21_cfn_lint_success.PNG)  
    - 一連のスタック作成が完了  
      ![図](images_lec13/11-3-24_integration-test_cfn_complete.PNG)  

</details>
<details>
<summary>AWS CLIによる各種変数の引継ぎ</summary>

- AWS CLIによるCloudFormationで構築したリソースからAnsibleへの各種変数の引継ぎ  
  - AWS CLI
    - ローカルからAWS各リソースにアクセスできるようアクセス権を設定  
      ```
      aws configure
      ```
      ![図](images_lec13/11-4-1_CLI_setup.PNG)
  - 変数取得
    - ローカルからCLIを使い、Cloudformationで構築したリソースから以下情報を取得するため、必要なコマンドを検討
      - EC2 Public IP Address
      - RDS Master User Name
      - RDS Master Usr Password
      - RDS Endpoint
      - ALB DNS Name
      - S3 Bucket Name
  - EC2
    - Public IPはスタック名から直接取得できないので、インスタンスIDを取得し、これを引数にして取得  
      ![図](images_lec13/11-4-2_aws_instanceid_publicip.PNG)  
      ![図](images_lec13/11-4-3_cli_ec2_describe-instances_filter_name_query_PublicIP.PNG)  
  - RDS
    - Endpoint・Userはスタック名から直接取得できないので、DBインスタンスIDを取得し、これを引数にして取得  
      ![図](images_lec13/11-4-4_cli_rds_describe-instance_query_user.PNG)  
      ![図](images_lec13/11-4-5_cli_rds_describe-instance_query_address.PNG)  
      ![図](images_lec13/11-4-6_aws_rds_resourceid_endpoint.PNG)  
    - CircleCI実行時はインスタンスIDが変数に格納されないエラーが発生。AWS CLIを手動実行時は発生しない  
      ![図](images_lec13/11-4-7_aws_rds_id_error2.PNG)  
    - 変数代入時にエラーが発生しており、出力と代入とで行を分けることで回避  
      注釈：直下に示すCloudformationからAnsibleに渡した変数は、セキュリティのため非表示に変更し、パスワード変更・CircleCiから履歴削除済み \(手戻り事例を参照\)  
      ![図](images_lec13/11-4-8_aws_rds_id_error2-output-check1.PNG)  
      ![図](images_lec13/11-4-9_aws_rds_id_ok.PNG)  
  - ALB
    - DNS Nameはスタック名から直接取得できないので、DNS ARNを取得し、これを引数にして取得を取得  
      ![図](images_lec13/11-4-10_cli_enbv2_describe-lbs_query_LBName.PNG)  
      ![図](images_lec13/11-4-11_aws_alb_arn_dns.PNG)  
  - Systems Manager
    - RDSのPassを取得  
      ![図](images_lec13/11-4-12_cli_ssm_get-parameter_pass.PNG)  
  - S3
    - BucketNameを取得  
      ![図](images_lec13/11-4-13_cli_cloudforomation_describe-stacks-resource_s3_query.PNG)  
  - 各変数をシェルスクリプトへ出力  
    ```
    echo expourt 変数名=$(変数取得コマンド) >> シェルスクリプトファイル名
    ```

</details>
<details>
<summary>CircleCIへのAnsible実装</summary>

- CircleCIへのAnsible実装
  - Ansible
    - orbs追加  
      ![図](images_lec13/11-5-1_ans_orbs.PNG)  
    - ローカルで作成し動作確認済みのansible各種ファイルをコピー  
      ![図](images_lec13/11-5-2_ans_cp_ansible_directory.PNG)  
  - 変数引継ぎ
    - ジョブ内の別ステップ間で、`persist_to_workspace`で保存した変数を`attach_workspace`で引継ぎ  
      ![図](images_lec13/11-5-3_ans_perrsist-to_attach_on_ci.PNG)  
    - `env_vars.sh`に保管した変数を`ansible/inventories/host.yml`および`ansible/site.yml`に置換コマンドで反映  
      ![図](images_lec13/11-5-4_sns_env-vars_input-ok.PNG)  
  - Fingerprintの登録  
    ![図](images_lec13/11-5-6_ans_ssh_key_created.PNG)  
    ![図](images_lec13/11-5-7_ans_ssh_key_fingerprin_created.PNG)  
    - フィンガープリントを直接貼らずに環境変数にしてみたが、CircleCIのansibleフィンガープリントを読み取らないため直接入力に変更  
      ![図](images_lec13/11-5-8_ans_fingerprint_env_failed.PNG)  
      ![図](images_lec13/11-5-9_ans_fingerprint_env_failed2.PNG)  
  - インベントリーファイルの読み込みエラー
    - インベントリーファイルの指示なし  
      ![図](images_lec13/11-5-10_ans_inventory_failed_without_playbook-options.PNG)  
    - オプション設定を`inventory-parameters`で指定したがインベントリーファイルを読み取れないエラー  
      ![図](images_lec13/11-5-11_ans_inventory_failed_with_inventory-parameters.PNG)  
    - `playbook-options`でIPアドレスを引数にしたところ、結果PASSだがインベントリーファイル読み取れず  
      ![図](images_lec13/11-5-11_ans_inventory_failed_with_playbook-options.PNG)  
  - インスタンス接続エラー  
    - `playbook-options`でインベントリーファイルを引数にしたところ接続エラー  
      ![図](images_lec13/11-5-12_failed5_connection-sg.PNG)  
    - SSH接続についてセキュリティグループのインバウンドグループ設定を見直し  
      ![図](images_lec13/11-5-13_inventory_input-failed6_MyIP-delete.PNG)  
  - インスタンス接続のタイムアウトエラー
    - 確認メッセージが出てしまいタイムアウト  
      ![図](images_lec13/11-5-14_inventory_input-failed7_timeout.PNG)  
    - `ansible.cfg`が読み込まれていない  
      ![図](images_lec13/11-5-15_inventory_input-failed7_timeout_add_cfg2.PNG)  
    - `ansible.cfg`をルートディレクトリに動かしたところ読み取られるようになった  
      ![図](images_lec13/11-5-16_ansible_timeout_cfg-in-ansible-folder.PNG)  
      ![図](images_lec13/11-5-17_ansible_timeout_cfg-out-of_ansible-folder.PNG)  
      ![図](images_lec13/11-5-18_inventory_ok.PNG)  
  - Ansible実行時エラー（ローカル実行時は発生しなかった）  
    - 意図しない整数値コマンドでエラー⇒`timeout`の設定が怪しい  
      ![図](images_lec13/11-5-19_ans_install_failed_anyenv_unexpected_int.PNG)  
      ![図](images_lec13/11-5-20_ans_install_failed_anyenv_unexpected_int.PNG)  
    - `timeout`設定をコロンで囲むと、'ansible.builtin.shell'モジュールとコンフリクトしていると指摘  
      ![図](images_lec13/11-5-21_ans_install_failed_anyenv_conflict.PNG)  
    - `timeout`設定をコマンド内に変更  
      ![図](images_lec13/11-5-23_ans_install_timeout-update.PNG)  
  - Ansible内の変数代入エラー
    - `ansible/site.yml`で定義した変数が代入できていない  
      ![図](images_lec13/11-5-22_ans_valiable_import_failed.PNG)  
      ![図](images_lec13/11-5-29_ans_valiables_import_failed2.PNG)  
    - `ansible/site.yml`にCloudformationから変数引継ぎ後を再確認⇒Ansible内の変数の一部を誤って置換していた  
      ![図](images_lec13/11-5-24_ans_valiable_import_failed_unexpected_replacement.PNG)  
    - Cloudformationからの引継ぐ変数の名称を変更  
      ![図](images_lec13/11-5-25_ans_valiable_replace_variables_update.PNG)  
      ![図](images_lec13/11-5-26_ans_valiable_import_update_vars.PNG)  
  - CircleCIのタイムアウトエラー
    - アウトプットが無いと10分で処理を中断し、時間のかかるRubyインストール失敗  
      ![図](images_lec13/11-5-33_integration-test_ans_fail_timeout.PNG)  
    - `Ansible-playbook`実行がコマンド記述になるが、タイムアウト時間を30分に延長  
      ![図](images_lec13/11-5-34_integration-test_ans_extend_timeout.PNG)  
  - Ansible実行完了  
    ![図](images_lec13/11-5-27_ansexecuting.PNG)  
    ![図](images_lec13/11-5-28_ans_execution_ok.PNG)  
  - サンプルアプリ動作確認エラー  
    - ALBのDNS名でアクセスしようとすると502エラー発生⇒サーバー確認するとNginxが作動していない  
      ![図](images_lec13/11-5-29_app_502_err.PNG)  
    - サーバー名の長さは64以下にするか、上限値を増やす必要あり  
      ![図](images_lec13/11-5-30_app_502_err2.PNG)  
    - `/etc/nginx/nginx.conf`に設定項目を追加  
      ![図](images_lec13/11-5-31_app_502_nginx_hash_size_128.PNG)  
    - サンプルアプリの正常動作を確認  
      ![図](images_lec13/11-5-32_app_ok.PNG)  

</details>
<details>
<summary>CircleCIへのSeverspec実装</summary>

- CircleCIへのSeverspec実装
  - Serverspecの準備
    - orbs追加  
      ![図](images_lec13/11-6-1_spec_ruby_install_circleci.PNG)  
    - ローカルで必要なファイルの作成のためRubyをインストール  
      ![図](images_lec13/11-6-2_spec_ruby_install_local.PNG)  
    - Serverspecのインストールと初期設定  
      ![図](images_lec13/11-6-3_spec_install_local.PNG)  
      ![図](images_lec13/11-6-5_spec_directories.PNG)  
      ![図](images_lec13/11-6-7_spec_bundler_install_local.PNG)  
    - [課題11](lecture11.md)で作成したテストコードをローカルにコピー  
      ![図](images_lec13/11-6-8_spec_scp_testcode_from_kadai11.PNG)  
  - Bundlerコマンド実行エラー  
    - 必要なGemを`Gemfile`に追加して`push`すると、`Bundle install`コマンドを要求される。CircleCI実行時のコマンド実行もエラーが出るので、ローカルで実行が必要  
      ![図](images_lec13/11-6-9_spec_add_gems.PNG)  
      ![図](images_lec13/11-6-10_spec_exec_failed_need-bundle-install.PNG)  
    - ローカルで実行しようとしてもエラー  
      ![図](images_lec13/11-6-11_spec_failed_sudo-bundle-install.PNG)  
    - Gemを個別にインストールしようとしてもエラーが出るが、`ruby-dev`または`rebu-devel`を要求される  
      ![図](images_lec13/11-6-12_spec_exec_failed_need-bundle-install_need_ruby-dev_or_ruby-devel.PNG)  
      ![図](images_lec13/11-6-13_spec_install_ruby-devl.PNG)  
    - 個別にGemをインストールすると今度は成功  
      ![図](images_lec13/11-6-14_spec_install_bcrypt_ed25519_pbkdf.PNG)  
    - `Bundle install`コマンドも成功  
      ![図](images_lec13/11-6-15_spec_sudo-bundle-install.PNG)  
    - `.gitignore`に`vendor`および``ディレクトリを追加  
      ![図](images_lec13/11-6-16_spec_add_ignore_vendor_bundle.PNG)  
    - 改めて`push`すると今度は`rake`を要求されるので追加  
      ![図](images_lec13/11-6-17_spec_failed_need-rake.PNG)  
      ![図](images_lec13/11-6-18_spec_bundler_add_rake.PNG)  
  - Hostへの接続エラー
    - `host`に接続できていない  
      ![図](images_lec13/11-6-19_spec_failed_socketerror.PNG)  
    - `spec_helper.rb`の`host`にhost情報が入力される  
      ![図](images_lec13/11-6-20_spec_TARGET_HOST_dir.PNG)  
    - `spec_helper.rb`の`host`にPublic IPを直接代入するもエラー  
      ![図](images_lec13/11-6-21_spec_replace_TARGET_HOST.PNG)  
      ![図](images_lec13/11-6-22_spec_failed_EADDRNOTAVAIL.PNG)  
    - `.ssh/config`ファイルを作成しhost情報を直接記入するとエラー内容が変化  
      ![図](images_lec13/11-6-23_spec_add_ssh_config.PNG)  
    - 80番及び22番ポートがListenしていないエラーが出ているが、実際は正常に動作している ⇒ `ss`コマンド実行時の環境変数が未設定  
      ![図](images_lec13/11-6-24_spec_check_path_in_serverspec.PNG)  
    - ssコマンドが機能していなかったので、spec_helper.rb`に環境変数の設定追加し解消  
      ![図](images_lec13/11-6-25_spec_set_path_ok.PNG)  
      ![図](images_lec13/11-6-26_spec_ok.PNG)  

</details>
