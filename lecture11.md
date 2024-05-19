# AWSフルコース講座 第10回課題

## 実施内容

ServerSpec のテスト[サンプルコード](https://github.com/MasatoshiMizumoto/raisetech_documents/tree/main/aws/samples/serverspec)をカスタマイズしたうえで、テストを成功させる

### 図

### Templateファイル

![図](images_lec11/)  

### ファイル

課題5にて手作業で実施したソフトウェアのバージョン確認

```yml

        listen_port = 8080

    #Nginxがインストール済であること
        describe package('nginx') do
          it { should be_installed }
        end

    #指定のポートがリッスン（通信待ち受け状態）であること
        describe port(listen_port) do
          it { should be_listening }
        end

    #テスト接続して動作すること
        describe command('curl http://127.0.0.1:#{listen_port}/_plugin/head/ -o /dev/null -w "%{http_code}\n" -s') do
          its(:stdout) { should match /^200$/ }
        end

    # bundler のバージョン一致確認。
        describe package('bundler') do
          it { should be_installed.by('gem').with_version('2.3.14') }
        end
    
    # rails のバージョン一致確認。
        describe package('rails') do
          it { should be_installed.by('gem').with_version('7.0.4') }
        end

    # unicorn がインストールされているかの確認。
        describe package('unicorn') do
          it { should be_installed.by('gem') }
        end

    # ruby のバージョン一致確認。    
        describe command('ruby -v') do
          its(:stdout) { should match /ruby 3\.1\.2/ }
        end
    
    # node のバージョン一致確認。
        describe command('node -v') do
          its(:stdout) { should match /v17.9.1/ }
        end
    
    # yarn のバージョン一致確認。
        describe command('yarn -v') do
          its(:stdout) { should match /1.22.19/ }
        end
    
    # 3306番ポートでRDSに接続できるかの確認。
        describe command("mysql -u admin -p$RDS_PASSWORD -h $RDS_HOST -P 3306 -e quit") do
          its(:exit_status) { should eq(0) }
        end

    #追加テスト１　rubyが指定のバージョンでインストールされているか確認
       describe command('ruby -v') do
          its(:stdout) { should match /ruby 3\.2\.3/ }
       end

    #追加テスト２　gitがインストールされているか
       describe package ('git') do
          it { should be_installed }
       end

    #nginxの自動起動設定がenableになっているか
    #nginxは起動しているか
    #3000ポートがlistenしているか
    #Rubyは指定したバージョンになっているか
    #yarnは指定したバージョンになっているか
    #mysql-community-clientはインストールされているか
    #unicorn.sockは存在するか

```

## 所感
- とわかった。  
- とわかった。  


## その他
