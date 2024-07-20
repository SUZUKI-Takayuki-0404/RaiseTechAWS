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
    ![図](images_lec13/2-1_ansible_folder_tree.PNG)  
  - Playbookの作成 `site.yml`  
    ![図](images_lec13/1-1_ansible_site_yml.PNG)  
  - Role
    - yum update (`sudo yum update`に相当)  
      ![図](images_lec13/1-2_ansible_roles_yum-update_tasks_main_yml.PNG)  
    - Rolesの階層は以下コマンドで作成可(ansible ディレクトリで実行)
      ```
      ansible-galaxy init roles/<Rile名>
      ```
      ![図](images_lec13/2-0_ansible-galaxy_init_roles_mysqlPNG.PNG)  
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
      ![図](images_lec13/3-1-1_mysql_download-url-err1.PNG)
      ![図](images_lec13/3-1-1_mysql_download-url-err2.PNG)  
    - リダイレクト先のURLに変更で成功
      ![図](images_lec13/3-1-1_mysql_download-url-ok.PNG)  
    - Mysql-community-serverパッケージは、実際にMySQLのRepositoryをダウンロードしないと実行できず、ドライランではエラーとなるため、`ignore_errors`設定を追加
      ![図](images_lec13/3-2_mysql-community-server_ignore_errors.PNG)  
  - git  
    ![図](images_lec13/3-3_yum_git.PNG)  
  - anyenv
    - 個別のroleフォルダの内容を一括作成
      ![図](images_lec13/3-3-0_anyenv_ansible-galaxy_init_roles_anyenv.PNG)  
    - `shell`モジュールや`command`モジュールは以下設定を追加
      - 実行するとchangedが返されるので、`when_changed: no`に設定変更
      - 冪等性の理由から再実行されないよう条件を追加
        ![図](images_lec13/3-3-1_anyenv_when_changed_no.PNG)  
      - ansibleの初回実行時は`anyenv -v`コマンドは失敗するが、以降は成功するので、失敗時のみ各処理を実行するようにしている
        ![図](images_lec13/3-3-1_anyenv_ansible-playbook_failed1.PNG)  
    - `anyenv install -init`コマンド実行時に`y/N`回答を求められるので、`yes`コマンドで対応
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
  - gem(rails & bundler)
      - ![図]()  
        ![図]()  
        ![図]()  
  - yarn
    - aaa
      ![図]()  
      ![図]()  
      ![図]()  
  - サンプルアプリ
    - aaa
      ![図]()  
      ![図]()  
      ![図]()  
  - ImageMagick
    - aaa
      ![図]()  
      ![図]()  
      ![図]()  
  - Systemd
    - aaa
      ![図]()  
      ![図]()  
      ![図]()  
  - Nginx
    - aaa
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
