# AWSフルコース講座 課題第3回

## 実施内容

### AWS上にVPC・セキュリティグループ・EC2・RDSの作成と接続確認

- VPCの作成
  - 設定内容
    ![図1](images_lec4/1VPC-Properties.PNG)


- セキュリティグループの作成
  - 設定内容
    ![図2](images_lec4/2SG-EC2-Inbound.PNG)
    ![図3](images_lec4/3SG-EC2-Outbound.PNG)
    ![図4](images_lec4/4SG-RDS-Inbound.PNG)
    ![図5](images_lec4/5SG-RDS-Outbound.PNG)


- EC2の作成
  - 設定内容
    ![図6](images_lec4/6EC2-properties1.PNG)
    ![図7](images_lec4/7EC2-properties2.PNG)


- RDSの作成とEC2からの接続確認
  - 設定内容
    ![図8](images_lec4/8RDS-Properties1.PNG)


  - 接続確認
    ![図9](images_lec4/9RDS-MySQL-Access.PNG)


## 所感

- 全体像を先に考える
- 手戻り事例
    ![図E1](images_lec4/E1_TerminateEC2.PNG)
    ![図E3](images_lec4/E2_SG-RuleChangeError.PNG)


## 備忘録

<details>
<summary>作業工程スクリーンショット</summary>

- EC2作成
  ![図A1](images_lec4/A1_EC2Name-AMI.PNG)
  ![図A2](images_lec4/A2_EC2-Keypair1.PNG)
  ![図A3](images_lec4/A3_EC2-Key-NetworkSettings.PNG)


- Ubuntu
  - unzip
    ![図A4](images_lec4/A4_install unzip.PNG)
    ![図A5](images_lec4/A5_install unzip2.PNG)
    
    
  - AWS CLI
    ![図A6](images_lec4/A6_AWSCLI2-1.PNG)
    ![図A7](images_lec4/A7_AWSCLI2-2.PNG)
    
    
- EC2接続
  ![図A8](images_lec4/A8_EC2-chmod400.PNG)
  ![図A9](images_lec4/A9_EC2-SSH-Access.PNG)


- MySQLインストール
  ![図A10](images_lec4/A10_InstallMySQL.PNG)


 - RDS作成
  ![図A11](images_lec4/A11_RDS-Instance-AutoScalingDisable.PNG)
  ![図A12](images_lec4/A12_RDS-Connection1.PNG)
  ![図A13](images_lec4/A13_RDS-raisetech-AWS-Kadai.PNG)
  ![図A14](images_lec4/A14_RDS-Connection2.PNG)


</details>
