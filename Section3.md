# Section 3 Ansibleによる自動化とテスト

## 3-0 Ansibleのインストール

###[公式サイト](http://docs.ansible.com/intro_installation.html#latest-releases-via-apt-ubuntu)を参照

    sudo apt-get install software-properties-common

    sudo apt-add-repository ppa:ansible/ansible

    sudo apt-get update

    sudo apt-get install ansible

## 3-1 ansibleでWordpressを動かす(2)を行なう

### Vagrantのプロキシ設定のプラグインのインストール

    vagrant plugin install vagrant-proxyconf

    vagrant plugin install vagrant -vbguest

    うまく行かない場合は追加で

    vagrant plugin install vagrant-aws

    vagrant plugin install vagrant-omnibus

    vagrant plugin install vagrant-vbguest

### Vagrantfileに記述(プロキシの設定)

    config.vm.network "private_network", ip:"xxx.xxx.xxx.xxx"

    if Vagrant.has_plugin?("vagrant-proxyconf")                             
      config.proxy.http = "http://172.16.40.1:8888/"
      config.proxy.https = "http://172.16.40.1:8888/"
      config.proxy.no_proxy = "localhost,127.0.0.1,.it-college.local"
    end

playbookを書く

### ansibleを実行

    ansible-playbook -i hosts playbook.yml -u vagrant -k

### 3-1-2? VagrantfileからAnsibleを呼び出す

#### Vagrantfileに記述

    config.vm.provision "ansible" do |ansible|
      ansible.playbook = "playbook.yml"
    end

コマンドを実行

    vagrant provision
