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
cp templates/setup Vagrant
```

仮想マシンを起動します。
この操作により、ホストオンリーネットワークと DHCP サーバーが作成されます。

```
vagrant up
```

仮想マシンのIPアドレスと dhcpserver の情報を確認します。

```
vagrant ssh -c "ip a"
VBoxManage list dhcpservers
```

確認した情報を参考に、Wsl2 と Windows 上から ping で疎通できることを確認します。

```
ping 192.168.56.*
```

* VirutualBox ではデフォルトで 192.168.56.* のネットワークが作成されます。


この仮想マシンは初期環境構築と疎通確認の目的を達成したので破棄します。

```
vagrant destroy
rm Vagrantfile
```


# 仮想化マシンの構成を生成する

https://portal.cloud.hashicorp.com/vagrant/discover?query=alpine


Vagrant ファイルを配置します。

```
cp templates/setup Vagrant
```

ホストオンリーネットワークが存在しなければ作成します。

```
VBoxManage list hostonlyifs  # ホストオンリーネットワークの確認
VBoxManage hostonlyif create
```

ホストオンリーネットワークを確認します。

```
VBoxManage list hostonlyifs
```

使用済みIPアドレスを確認します。
`$_.IPAddress` のネットワークはホストオンリーネットワークで確認したネットワークで検索します。

```
# デフォルトゲートウェイ的な役割のIPが存在しているはず
Get-NetIPAddress | Where-Object { $_.IPAddress -like "192.168.56.*" }
```

Vagrant ファイルに、未使用のホストオンリーネットワークのIPアドレスを指定します。
IP は 99 以下を指定してください。
VirtualBox により 100 は DHCP サーバー、 101 以上が DHCP 範囲となることがあります（IPを指定しない場合に建てられます）。

```
config.vm.network "private_network",
    ip: "192.168.56.2"
```


# 仮想マシンの起動

仮想マシンを起動します。

```
vagrant up
```

以下のコマンドで仮想マシンにログインできることを確認します。

```
vagrant ssh
```

疎通確認のため http サーバーを起動します。

```
vagrant ssh -c "python3 -m http.server --bind 192.168.56.2 8080"
```

WSL2 上、Windows 上、仮想マシン上から、CURL などで疎通確認をします。

```
curl http://192.168.1.100:8080
```
