# RaiseTech AWS フルコース

## 受講内容
- Amazon Web Servicesの基本的な使い方を学習し、最終課題として近年の開発手法の実態であるCI/CDを実装し、現場2～3年程度までのITエンジニアの技術・思考を習得  
- 各サービス、ツールの使い方と合わせ、現場で必要とされるIT知識、セキュリティ等の運用面の注意事項、その他ノウハウについて実演含む講義を受講  

## 各回講義での学習内容まとめ

|No.| 学習テーマ|提出課題|課題で得た学び|  
|---|---|---|--  
|1|クラウドインフラが変えたもの・インフラエンジニアとは・開発環境の構築|AWSアカウント作成・IAM設定・Cloud9作成|―|  
|2|バージョン管理・Markdown記法|[第2回課題](./lecture02.md)|<li>GitHub-flow（main・developの2つの基幹ブランチ）・Git-flow（基幹ブランチはmainのみ）</li><li>ブランチの作業目的を明確にし別作業を混同しない</li>|  
|3|Webアプリケーションのデプロイ<li>システム開発プロセス</li><li>アプリケーションサーバー</li>|[第3回課題](./lecture03.md)|<li>Cloud9使用方法</li><li>エラー原因の特定と処置（仮説・検証）</li>|  
|4|クラウドインフラ環境構築（手動）<br>（VPC、EC2、RDS） |[第4回課題](./lecture04.md)|AWSの基本リソースの使い方・操作感（クセ）の習熟|  
|5|クラウドインフラ環境構築（手動）<li>アプリケーションのデプロイ</li><li>ELB（冗長化・負荷分散）</li><li>S3（データ保管）</li><li>インフラ構成図</li>|[第5回課題](./lecture05.md)|<li>Amazon Linuxへのインストール操作とエラー対応</li><li>Rails・Unicorn・Systemd・Nginxの導入</li><li>構成図の書き方</li>|  
|6|システムの安定稼働（監視、コスト管理）<li>AWSでの証跡、ロギング、監視、アラーム通知</li><li>AWSでのコスト管理</li> | [第6回課題](./lecture06.md)|<li>CloudTrailによる証跡確認</li><li>アラート通知とアラート閾値設定</li><li>使用リソースのコスト見積書作成</li>|  
|7|システムにおけるセキュリティの基礎<li>責任共有モデル</li><li>AWSのセキュリティ対策</li> | [第7回課題](./lecture07.md)|セキュリティリスクの洗い出しと対策・セキュリティ対策とコストの費用対効果検討|  
|8<br>9|ライブコーディング<li>VPC、EC2、RDS 構築</li><li>アプリケーションのデプロイ</li><li>ELB の構築、アプリと ELB の接続</li><li>サーバー構成変更（組込み ⇒ Web＋AP）</li> |―|ショートカット等の作業効率向上ノウハウ（講師実演）|  
|10|Infrastructure as Code<li>インフラ自動化</li><li>Cloudformationテンプレート解説</li> |[第10回課題](./lecture10.md)|<li>変数のネーミングルール統一と事前整理</li><li>クロススタック使用時の依存関係の煩雑化回避</li>|  
|11|インフラのテスト<li>テスト駆動開発</li><li>Serverspec</li>|[第11回課題](./lecture11.md)|テスト駆動開発・必要なテスト項目の事前洗い出し|  
|12|DevOps・CI/CD<li>CircleCI</li><li>Terraformの解説</li>|[第12回課題](./lecture12.md)|<li>CircleCIによる構築自動化</li><li>Cloudformationテンプレートへのセキュリティ対策</li>|  
|13|構成管理(プロビジョニング)ツール<li>Ansible</li><li>CircleCIとの併用</li> |<li>[第13回課題](./lecture13.md)</li><li>[成果物](https://github.com/SUZUKI-Takayuki-0404/RaiseTechAWS/blob/matome/lecture13.md)</li>|<li>環境構築～テスト実行まで一連の自動化</li><li>Ansible Playbookにおける冪等性の確保</li><li>CircleCI使用時のセキュリティ対策</li>|  
|14<br>15|ライブコーディング（Ansible～CircleCI）|―|―|  
|16|現場へ出ていくにあたって<li>必要な技術と知識</li><li>現場での立ち振る舞い</li><li>就職・転職で優位に立つために</li>|―|―|  
