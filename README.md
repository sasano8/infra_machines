# 前提条件

このリポジトリは Windows上のファイルシステム（DrvFs）に配置します。
Windows 上に置かれた Vagrant は DrvFs でしか動きません。

WSL2 から Windows 側のホームディレクトリを得るには以下を参考にしてください。

```
# Windows のホームディレクトリを取得し、wslpath に変換する
cd $(wslpath $(cmd.exe /C "echo %USERPROFILE%" | tr -d '\r'))

mkdir -p projects
git clone <this_repository>
```


## 仮想化ツールのインストール

Windows 上に VirtualBox をインストールします。

## Vagrant のインストール

Windows 上に Vagrant をインストールします。
インストール後、WSL2 上に Vagrant のエイリアスを設定します。

```
cat << EOF >> ~/.bashrc
alias vagrant='""/mnt/c/Program Files/Vagrant/bin/vagrant.exe"'
EOF
```

# 初期環境を構成する

Vagrant ファイルを配置します。

```
cp templates/primary Vagrantfile
```

仮想マシンを起動します。

```
vagrant up
```

疎通確認のため http サーバーを起動します。

```
vagrant ssh -c "python3 -m http.server --bind 192.168.56.2 8080"
```

WSL2 上、Windows 上、仮想マシン上から、CURL などで疎通確認をします。

```
curl http://192.168.56.2:8080
```

仮想マシンの電源をオフにするには下記のコマンドを実行します。

```
vagrant halt
```


仮想マシンを破棄する場合は下記のコマンドを実行します。

```
vagrant destroy
```

仮想マシンの構成（CPUやメモリなど。全て更新されるとは限りません）をするには下記のコマンドを実行します。

```
vagrant reload
```


# 既知の問題

Vagrant で dhcp 構成のマシンを作成すると、次のようなDHCPサーバーが構築されます。

```
VBoxManage dhcpserver modify --netname "HostInterfaceNetworking-VirtualBox Host-Only Ethernet Adapter" --ip 192.168.56.2 --netmask  255.255.255.0 --lowerip 192.168.56.3 --upperip 192.168.56.254 --enable
```

この DHCPサーバーの構成は、VBoxManage で変更できるものの、Vagrant では `--ip 192.168.56.2 --netmask  255.255.255.0 --lowerip 192.168.56.3 --upperip 192.168.56.254` のみ、利用可能な DHCP サーバーとして認識します（つまり、DHCPサーバーの構成は変更できません）。
