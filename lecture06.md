# AWSフルコース講座 第6回課題

# 実施内容

### CloudTrailで証跡の確認

AWS使用記録をCloudTrailのイベントから自身のIAM ユーザー名があるリストアップ  

  - イベント名：[GetEventSelectors](https://docs.aws.amazon.com/ja_jp/awscloudtrail/latest/userguide/logging-management-events-with-cloudtrail.html)  
      CloudTrailがこれまでに記録しているイベントを呼び出すイベント  
      ![図](images_lec6/Cloudtrail_properties.PNG)  

  - 主な内容：AWS上で、誰が、いつ、何をしたかを特定できる内容が含まれている
    - イベント時間
    - ユーザーID
    - エラーコード
    
    ![図](images_lec6/Cloudtrail_GetEventSelectors1.PNG)  


### CloudWatchアラームによるメール通知

ALB のアラームを設定しメール通知
  - Amazon SNSの設定
    - 送付先メールで確認メールに回答し受信準備完了  
      ![図](images_lec6/SNS_subscription_UnHealthy_Confirmation.PNG)  

  - CloudWatchでアクション設定
    - Alertアクションの設定  
      ![図](images_lec6/CloudWatch-Unhealthy-status.PNG)  

    - Railsアプリケーションを使えない状態にして動作確認  
      ![図](images_lec6/CloudWatch_Status-OKtoALERT.PNG)  

    - Railsアプリケーションを使える状態にして動作確認  
      ![図](images_lec6/CloudWatch_Status-ALERTtoOK.PNG)  


### AWS利用料の見積書作成

課題5までに作成したリソース内容の見積[URL](https://calculator.aws/#/estimate?id=82dfc620e4444961a2ac08790249d0c4fb957a1d)  
  ![図](images_lec6/AWS_Architecture.PNG)  


### 料金確認

マネジメントコンソールから、EC2の利用料を確認
  - 請求期間: 3月1日 - 2024年3月31日
  - EC2は無料利用枠の中で使用中
  - VPCでパブリックIPアドレスの利用料が発生

  ![図](images_lec6/CostSummary_240310.PNG)  
  ![図](images_lec6/CostSummary_240310_detail_lined.PNG)  


# 所感

  - アラーム設定により監視の負担を大きく軽減できるが、アラートを上げる状態とはどのようなものか、明確に定義しておく必要がある点に注意する。
    - どのメトリクスを使うか、
    - どのような条件・閾値を置くか
    - 複数の条件を組み合わせる場合  etc.

# 備忘録

<details>
<summary> 作業工程 </summary>

- CloudTrail
  - イベントのリスト  
    ![図](images_lec6/Cloudtrail-list.PNG)  

  - JSON形式のイベントレコード  
    ![図](images_lec6/Cloudtrail_EventRecord-json.PNG)  


- Amazon SNS
  - トピック設定  
    ![図](images_lec6/SNS_topic1.PNG)  

  - メール通知の受け取り設定  
    ![図](images_lec6/SNS_subscription_UnHealthy.PNG)  

  - ALERT状態に遷移した時、OK状態に遷移した時、それぞれのトピックを用意  
    ![図](images_lec6/SNS-topics.PNG)  


- CloudWatch
  - Alertアクションの設定  
    ![図](images_lec6/CloudWatch_Select_Metrics.PNG)  
    ![図](images_lec6/CloudWatch_Select_Metrics_Action.PNG)  
    ![図](images_lec6/CloudWatch_Select_Metrics_Explanation.PNG)  
    ![図](images_lec6/CloudWatch_Select_Metrics_Threshold.PNG)  
    ![図](images_lec6/CloudWatch_Healthy-state.PNG)  

  - OKアクションの設定  
    ![図](images_lec6/CloudWatch_Select_Metrics_Action_Healthy.PNG)  
    ![図](images_lec6/CloudWatch-after-bootingApps.PNG)  

  - アラームの有効かと無効化  
    ![図](images_lec6/CloudWatch_Alarm-disable.PNG)  
    ![図](images_lec6/CloudWatch_Alarm-enable.PNG)  

  - EC2を起動していない場合の表示  
    ![図](images_lec6/TG-unused.PNG)  
    ![図](images_lec6/TG-unused-status.PNG)  


- AWS Pricing Calculator
  - 見積もるリソースの選択  
    ![図](images_lec6/AWS_Pricing_Calculator1.PNG)  

  - EC2見積り  
    ![図](images_lec6/AWS_Pricing_Calculator2.PNG)  
    ![図](images_lec6/AWS_Pricing_Calculator3.PNG)  
    ![図](images_lec6/AWS_Pricing_Calculator4.PNG)  

    - EC2仕様情報  
      ![図](images_lec6/EC2_properties1.PNG)  
      ![図](images_lec6/EC2_properties2.PNG)  
      ![図](images_lec6/EC2_Strage_Properties.PNG)  

  - RDS見積り  
    ![図](images_lec6/AWS_Pricing_Calculator5.PNG)  
    ![図](images_lec6/AWS_Pricing_Calculator6.PNG)  
    ![図](images_lec6/AWS_Pricing_Calculator7.PNG)  

    - RDS仕様情報  
      ![図](images_lec6/RDS_properties1.PNG)  
      ![図](images_lec6/RDS_properties2.PNG)  
      ![図](images_lec6/RDS_Security_Proxy.PNG)  

  - VPC仕様情報  
    ![図](images_lec6/VPC_properties1.PNG)

  - VPC見積り  
    ![図](images_lec6/AWS_Pricing_Calculator8.PNG)  
    ![図](images_lec6/AWS_Pricing_Calculator9.PNG)  

  - S3見積り  
    ![図](images_lec6/AWS_Pricing_Calculator10.PNG)  
    ![図](images_lec6/AWS_Pricing_Calculator11.PNG)  

  - ALB見積り  
    ![図](images_lec6/AWS_Pricing_Calculator12.PNG)  
    ![図](images_lec6/AWS_Pricing_Calculator13.PNG)  

  - 課題5までに作成したリソース内容見積[URL](https://calculator.aws/#/estimate?id=ce1d019ab7dec12e96c39fe4b2a422c8d8e570c2)  

</details>
