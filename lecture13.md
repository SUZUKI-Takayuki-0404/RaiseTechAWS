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
- Ansibleによる環境構築  
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
