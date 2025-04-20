Vagrant.configure("2") do |config|
  config.vm.boot_timeout = 300  # 結構時間がかかります
  config.vm.box = "ubuntu/jammy64"  # Ubuntu 22.04

  config.vm.network "private_network",
    ip: "192.168.56.2"

  config.vm.hostname = "primary"
  config.vm.provider "virtualbox" do |vb|
    vb.name = "parimary"
    vb.memory = 16 * 1024
    vb.cpus = 4
  end

  config.vm.provision "shell", inline: <<-SHELL
    echo 'vagrant:vagrant' | chpasswd
  SHELL
 
  config.vm.provision "shell",
    privileged: false,
    path: "bootstraps/00_default.sh"

end
