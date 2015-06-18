# Section 6 AWS(Amazon Web Services)

このセクションではAWS(Amazon Web Services)を使用したサーバー構築を行ないます。

## 講義関連リンク

* [AWS公式サイト](http://aws.amazon.com/jp/)
* [Cloud Design Pattern](http://aws.clouddesignpattern.org/index.php/%E3%83%A1%E3%82%A4%E3%83%B3%E3%83%9A%E3%83%BC%E3%82%B8)

## 6-0 AWSコマンドラインインターフェイスのインストール

[公式サイト](http://aws.amazon.com/jp/cli/)参照。

### awsコマンドのインストール

    sudo apt-get install python-pip

    pip install awscli

## 6-1	AWS EC2 + Ansible

###インスタンスの作成とsshログイン

    AWS[http://ap-northeast-1.console.aws.amazon.com/]のコンピューティングのEC2からインスタンスの作成をする

    Access Key IDとSecret Access Keyを受け取る

    aws configure

    AWS Access Key ID [None]:アクセスキーを入力
    AWS Secret Access Key [None]:シークレットキーを入力
    Default region name [None]:ap-northeast-1
    Default output format [None]:json

    chmod 400 n14002.pem

    ssh -i n14002.pem ec2-user@IPアドレス

ログインできない場合は

    デフォルトゲートウェイの確認(172.16.40.10)
    インスタンスのIPアドレスの確認

### Ansibleを用いてWebサーバーを構築

    sudo ansible-playbook -i hosts -u ec2 playbook.yml --private-key n14002.pem

### セキュリティグループの編集(インスタンスを選択すると下に出てくる)

    インバウンドから編集、ルールの追加で「http」を追加する。

### AMI(Amazon Machine Image)を作る

自分のインスタンスを右クリック→　イメージ→　イメージの作成

AMIを作成後、同じマシンを2つ起動して、コピーができていることを確認してください。

## 6-2 AWS EC2(AMIMOTO)

別の人が作ったAMIを使用してサーバーを起動
こちら[http://ja.amimoto-ami.com/how-to-use/]を参照

AMIMOTOのWordpressを起動してWordpressが見れることを確認する。

## 6-3 Route53

### Hosted Zoneを作る

    Route53→　Create Hosted Zone→　Create Record Set
    zoneファイルを内容をコピペする。

## 6-4 S3

    バケットを作成(ブラウザから)

### ターミナルからの操作

    ファイルのアップロード
       aws s3 cp ./ファイル名 s3://バケット名

    ファイルのダウンロード
      aws s3 cp s3://バケット名/ファイル名 ./ダウンロードしたい場所

## 6-5 CloudFront

### EC2のインスタンスの連携(ここ[http://recipe.kc-cloud.jp/archives/4997]を参照)

    「Create Distribution」をクリック
    
    Origin Dmain Name:EC2のインスタンスのパブリックDNSを入力
    Origin Path: 書かない
    Origin ID: 書かない

    「Create Distribution」をクリック

作成できたら「Dmain Name」をブラウザに貼り付けてアクセス出きることを確認

### ベンチマークをとる

同時接続100 実行回数100
ab -n 100 -c 10

EC2側
    Connection Times (ms)   
    min  mean[+/-sd] median   max   
    Connect:       36   48   6.6     48      59   
    Processing:   847 11312 6767.6  11349   23658   
    Waiting:      775 11215 6838.9  11349   23658   
    Total:        884 11360 6772.0  11405   23710   


Cloud Front側
    Connection Times (ms)   
    min  mean[+/-sd] median   max   
    Connect:       29   42   7.0     43      54   
    Processing:    77   98   9.7     98     116   
    Waiting:       75   92   8.2     92     109   
    Total:        131  140   3.5    140     146   

## 6-6 RDS(ここから下はまだしてないです)

RDSは…MySQLっぽい奴です。

RDSを立ち上げて、6-1で作ったAMIのWordpressのDBをRDSに向けてみよう。

## 6-7 ELB

ELBはロードバランサーです。すごいよ。

6-1で作ったAMIを3台ぶんくらい立ち上げてELBに登録し、負荷が割り振られているか確認してみよう。

## 6-8 API叩いてみよう

AWSは自分で作ったプログラムからもいろいろ制御できます!
なんでもいいのでがんばってプログラム書いてみてね(おすすめはSES)。
