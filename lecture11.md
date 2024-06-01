# AWSフルコース講座 第11回課題

## 実施内容

Serverspec のテスト[サンプルコード](https://github.com/MasatoshiMizumoto/raisetech_documents/tree/main/aws/samples/serverspec)をカスタマイズしたうえで、テストを成功させる


### テストコード

- [課題5](https://github.com/SUZUKI-Takayuki-0404/RaiseTechAWS/blob/main/lecture05.md)の環境構築時に、インストール後に手作業で実施したソフトウェアのインストールおよびバージョン確認  
  - ソフトウェアがインストール済みであること
  - ソフトウェアバージョンが意図したものであること
  - 通信が成功すること

```ruby:sample.rb
    require 'spec_helper'
    
    listen_port80 = 80
    listen_port3000 = 3000
    
    # MariaDBがインストールされていないこと(＝検索結果に含まれない)
    describe command('yum list installed | grep mariad') do
      its(:stdout) { should match '' }
    end
    
    # gitがインストールされていること
    describe package ('git') do
      it { should be_installed }
    end
    
    # Ruby のバージョンは3.1.2であること
    describe command('ruby -v') do
      its(:stdout) { should match 'ruby 3.1.2' }
    end
    
    # bundlerのバージョンは2.3.14であること
    describe package('bundler') do
      it { should be_installed.by('gem').with_version('2.3.14') }
    end
    
    #railsのバージョンは7.0.4であること
    describe command('bash /home/ec2-user/check_rails_version.sh') do
      its(:stdout) { should match 'Rails 7.0.4' }
    end
    
    # nodeのバージョンは17.9.1であること
    describe command('node -v') do
      its(:stdout) { should match 'v17.9.1' }
    end
    
    ## yarnのバージョンは1.22.19であること
    describe command('yarn -v') do
      its(:stdout) { should match '1.22.19' }
    end
    
    # unicornのバージョンは6.1.0であること
    describe command('bash /home/ec2-user/check_unicorn_version.sh') do
      its(:stdout) { should match 'unicorn v6.1.0' }
    end
     
    # Nginxがインストール済であること
    describe package('nginx') do
      it { should be_installed }
    end
    
    # nginxの自動起動設定がenableになっているか
    describe service('nginx') do
      it { should be_enabled }
    end
    
    # ポート80番がリッスンであること
    describe port(listen_port80) do
      it { should be_listening }
    end
    
    # ポート3000番がリッスンであること
    describe port(listen_port3000) do
      it { should be_listening }
    end
    
    # テスト接続して動作すること
    describe command('curl http://127.0.0.1:#{listen_port80}/_plugin/head/ -o /dev/null -w "%{http_code}\n" -s') do
      its(:stdout) { should match /^200$/ }
    end

```

エラー対処のためスクリプトファイルとして実行\(Rails, Unicorn のバージョン確認\)
```cat check_rails_version.sh
# !/bin/bash

source /home/ec2-user/.rvm/scripts/rvm
rvm use ruby-3.1.2
rails -v

```

```cat check_unicorn_version.sh
# !/bin/bash

source /home/ec2-user/.rvm/scripts/rvm
rvm use ruby-3.1.2
unicorn -v

```


### 実行結果

  テスト成功を確認  
  ![図](images_lec11/4-1_all_test_passed.PNG)  

## 所感

  - 構築したい環境について予めどのような確認事項（＝テスト項目）が要るか洗い出しておき、インストール直後に自動テストすることで、後から問題発覚で工数大幅ロスを防げる。  
  - 今回の手戻り事例：
    - テスト実行時のエラー解決に多大な時間（８作業日）を要した。
    - 途中からChatGPT使用し、狭い知識範囲で思いつきの解決策ではなく、より広範な知識に基づく多種多様かつ網羅的な解決策を検討できた。  
    - 事象：  
      - Bundlerのバージョン確認時、コマンドライン手入力では正しく出力されているが、Serverspecでは別バージョンが表示。
      - Serverspecの環境変数が関係すると考え、Bundlerについてwhichコマンドで確認すると、パスが不一致。  
        ![図](images_lec11/3-4_test_failed_which_bundle.PNG)  
      - Rails、Unicornのバージョン確認時、コマンドライン手入力では正しく出力されているが、Serverspecでは出力されない。
      - ![図](images_lec11/3-16-3_unicorn_test_trial_result2.PNG)  
    - 処置：  
      - whichコマンドの問題は、期待値（出力結果）のパスに使っていたチルダ（ホームディレクトリ）をフルパス表記に置換することで解決。（＝テストコード不備でありバージョンの問題とは無関係）  
        ![図](images_lec11/3-4-2_bundler_path.PNG)  
        ![図](images_lec11/3-4-3_bundler_path_pass.PNG)  
      - Bundlerについては、Serverspecはdefault versionを参照していることがわかり、gemのバージョンを変更することでbundlerのデフォルトバージョンを整合させて解決  
        ![図](images_lec11/3-6_gem_list_bundler.PNG)  
        ![図](images_lec11/3-14-1_gem_update_system.PNG)  
      - RailsおよびUnicornは、Serverspecにスクリプトファイルを読み込ませることで解決。環境変数の修正策を出来るだけ多く集めるためChatGPTを使用。検討し得る策を提示させ、その実行結果をインプットに追加して探索範囲を狭めながら検討。  
        ![図](images_lec11/4-2-1_chatgpt_res.PNG)  
        ![図](images_lec11/4-2-2_chatgpt_res.PNG)  

## 備忘録

<details>
<summary>作業工程</summary>

- Serverspecのインストール
  ![図](images_lec11/1-1_install_severspec_rake.PNG)  
  ![図](images_lec11/1-2_serverspec-init.PNG)  

</details>
