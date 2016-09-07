# Packer Template for CentOS 6.8

VirtualBox 内で開発環境を立てられるようにした Packer Template

## Requirements

Vagrantと、Packerをローカルマシンに入れておく。

- [Vagrant](http://www.vagrantup.com/)
- [Packer](http://packer.io/)

※ Vagrant 1.8.5 には、バグがある。
http://ytooyama.hatenadiary.jp/entry/2016/07/23/213132

## Installation

1) packerを打って、しばし待つ（10分くらい）
```sh
$ packer build -only=virtualbox-iso template.json
```

2) centosボックスを作る。
```sh
$ vagrant box add CentOS-6.8-x86_64-minimal.box ./CentOS-6.8-x86_64-minimal.box
```

※ centos68ボックスを作り直したいときは。
```sh
$ vagrant destroy
$ vagrant box remove CentOS-6.8-x86_64-minimal.box
$ rm Vagrantfile
-> 2) からやり直す。
```


## Usage

1) init

```sh
$ vagrant init CentOS-6.8-x86_64-minimal.box
```

2) Hostからのssh接続のみ行う場合は、public_networkではなく、private_networkを設定してもよい。
```sh
$ vim Vagrantfile
  config.vm.network :private_network, ip: "192.168.33.10"
  # この場合、192.168.33.10でHostからつなぐことが出来る
```

3) Vagrantを立ち上げる
```sh
$ vagrant up
```

4) VM 内に入る。
```sh
$ vagrant ssh
```

5) IPアドレスを確認しましょう。
```sh
$ ifconfig
eth1      Link encap:Ethernet  HWaddr 08:00:27:FE:3F:E5
          inet addr:192.168.1.149  Bcast:192.168.1.255  Mask:255.255.255.0

->
192.168.1.149です。
```

※  eth1が立ち上がっていない時は？

```
$ sudo vim /etc/sysconfig/network-scripts/ifcfg-eth1
NM_CONTROLLED=no
BOOTPROTO=none
ONBOOT=yes
IPADDR=192.168.33.10
NETMASK=255.255.255.0
DEVICE=eth1
PEERDNS=no

# sudo service network restart
```


※  SSH Tips

`~/.ssh/config` に書くための設定を出力する。
```sh
$ vagrant ssh-config
```

設定の例。
```sh
$ vim ~/.ssh/config
Host vagrant
  HostName 192.168.1.149
  User vagrant
  Port 22
  IdentityFile /Users/<username>/.vagrant.d/insecure_private_key
```

ログインしてみる。
```sh
$ ssh vagrant
```
