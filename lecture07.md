# AWSフルコース講座 第6回課題

# 実施内容

### 課題5で構築した環境のセキュリティリスク

|No.|脆弱性|対策|
|--|--|--|
|1|暗号化されていないため情報を盗み見される|ACMを追加しHTTP通信にする|
|2|S3をパブリックアクセスにしてしまう|S3のコンソール画面で公開になっていないことを確認する\(補足：デフォルトではブロックパブリックアクセスは有効\)|
|3|別のユーザーにAWSリソース運用を任せる場合、IAMの権限(FullAccess)は過大|別のユーザーに管理させる場合は必要最小限の権限のみとする|


# 所感

 - セキュリティは重要だが、過剰なセキュリティは過剰コストなので、取扱うデータの重要性にあわせて、必要なセキュリティ対策を必要な分だけ導入できるよう優先的に知識をつけたい。
 - AWSのセキュリティ対策以前の基本的事項として、ソーシャルエンジニアリングや日常のパスワード管理など基本的な情報管理に注意する。

# 備忘録

<details>
<summary> 作業工程 </summary>

- [責任共有モデル](https://aws.amazon.com/jp/compliance/shared-responsibility-model/)  
  AWSとAWS利用者との間の責任分担を規定するモデル  
    - AWSは、サービスを提供するインフラ\(ハードウェア、ソフトウェア、ネットワーキング、AWSクラウドのサービスを実行する施設で構成\)の保護について責任を負う。
    - AWSサービス利用者\(ユーザー\)は、AWS上に保管する個人情報、構成\(ファイアウォール、設置等\)、設定\(暗号化やアクセス制限等\)、監視\(利用実態把握\)などAWSサービスの使い方全般に責任を負う。


- AWSが提供している主要なセキュリティ対策サービス  
  - AWSリソースの構成・設定関連
    - [AWS Security Hub](https://docs.aws.amazon.com/ja_jp/securityhub/?icmpid=docs_homepage_security)  
      AWSのセキュリティ状態を設定情報から検索し継続的にチェックし、脆弱な部分を指摘。5段階のセキュリティ標準から1つ以上選択し適用。
      セキュリティイベントの集約管理\(Security Hubの結果をひとつのAWSアカウント・リージョンに集約可能\)
    - [IAM Access Analyzer](https://docs.aws.amazon.com/ja_jp/IAM/latest/UserGuide/what-is-access-analyzer.html)  
      [IAM](https://docs.aws.amazon.com/ja_jp/IAM/latest/UserGuide/introduction.html)の機能のひとつ。
      AWS CloudTrailのIAM操作履歴(90日まで)を基に必要最小限ポリシーの作成や、AWSベストプラクティスに対し設定ポリシーを検証し、過大な権限を与えていないかの確認が可能。
    - [Amazon Inspector](https://docs.aws.amazon.com/ja_jp/inspector/?icmpid=docs_homepage_security)  
      自動的にリソースを評価し、脆弱性やベストプラクティスからの逸脱がないかどうかを確認。重要度の順にセキュリティの所見を示した詳細なリストが作成される。
    - [Amazon GuardDuty](https://docs.aws.amazon.com/ja_jp/guardduty/?icmpid=docs_homepage_security)  
      CloudTrail、VPCフローログ\(EC2\)、DNSログなどを利用したモニタリングサービスで分析結果をHIGH\/MID\/LOWに分類。

  - アプリケーション・保管データ関連
    - [CodeGuru Reviewer](https://docs.aws.amazon.com/ja_jp/codeguru/latest/reviewer-ug/welcome.html)  
      Java/Pythonアプリケーションのパフォーマンス、効率、コード品質を向上するための推奨事項を提案する。
    - [Patch Manager](https://docs.aws.amazon.com/ja_jp/systems-manager/latest/userguide/patch-manager.html)  
      [AWS System Manager\(SSM\)](https://docs.aws.amazon.com/ja_jp/systems-manager/?icmpid=docs_homepage_mgmtgov)の機能のひとつ。
      セキュリティ関連およびその他の種類の更新について、OSやアプリケーションへのパッチ適用を自動化。
    - [Amazon Macie](https://docs.aws.amazon.com/ja_jp/macie/?icmpid=docs_homepage_security)  
      S3バケット内の機密データを検出、モニタリング、保護

  - ファイアウォール関連  
    - [AWS WAF\(Application Firewall\)](https://docs.aws.amazon.com/ja_jp/waf/?icmpid=docs_homepage_security)  
      AWSリソースに転送されるウェブリクエスト\(リクエスト発信元IPアドレス、リクエストコンポーネント etc.\)をモニタリングし管理する。  
    - [AWS Shield](https://docs.aws.amazon.com/ja_jp/waf/?icmpid=docs_homepage_security)  
      DDoS攻撃に対し保護を提供。Standardレベルは追加料金なしで自動組み込み。  
    - [Network Firwall](https://docs.aws.amazon.com/ja_jp/network-firewall/?icmpid=docs_homepage_security)  
      インターネットゲートウェイとVPCの境界に設置するファイアーウォール、および侵入検知および防止サービス。

  - 暗号化関連
    - [ACM\(Certificate Manager\)](https://docs.aws.amazon.com/ja_jp/acm/)  
      AWSリソースでのSSL/TLS証明書の準備、管理、デプロイを一元管理容易する。  
      他のAWSサービスと結合され、コンソール画面からSSL/TLS証明書の配置ができる。  
    - [AWS Key Management Service\(KMS\)](https://docs.aws.amazon.com/ja_jp/kms/?icmpid=docs_homepage_crypto) は、AWS の暗号鍵マネージドサービス  
      S3はじめAWSの他のサービスで使用される暗号化およびキー管理サービス。保管データの暗号化に加え、鍵自体の暗号化も行う。
    - [AWS Secrets Manager](https://docs.aws.amazon.com/ja_jp/secretsmanager/?icmpid=docs_homepage_security)  
      データベースやその他のサービスの認証情報を安全に暗号化、保存、取得。
      必要に応じSecrets Managerを呼び出し認証情報を取得することで、アプリケーションでの認証情報ハードコーディングを不要にする。
      RDSではDBのアクセスパスワード定期変更も可能。  

  - 監査・不正検知関連
    - [AWS Config](https://docs.aws.amazon.com/ja_jp/config/?icmpid=docs_homepage_mgmtgov)  
      構築したリソースの構成情報や変更履歴を記録、管理する。  
    - [AWS CloudTrail](https://docs.aws.amazon.com/ja_jp/cloudtrail/?icmpid=docs_homepage_mgmtgov)  
      アカウントのAWSサービスに対するAPI操作履歴を記録・保持する。  
    - [AWS CloudWatch](https://docs.aws.amazon.com/ja_jp/cloudwatch/?icmpid=docs_homepage_mgmtgov)  
      Amazon CloudWatch は、数分で使用を開始できる、信頼性、拡張性、および柔軟性あるモニタリングソリューションを提供。  
    - [AWS SNS\(Simple Notification Service\)](https://docs.aws.amazon.com/ja_jp/sns/?icmpid=docs_homepage_appintegration)  
      メッセージ配信を提供する\(CloudWatch Alarmや各種アプリケーションから、AWS運用担当者や別のアプリケーションへ\)
    - [Amazon Detective](https://docs.aws.amazon.com/ja_jp/detective/?icmpid=docs_homepage_security)  
      セキュリティ検出結果や疑わしいアクティビティを分析、調査し、その原因を特定する。

</details>
