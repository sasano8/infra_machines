Vagrant.configure("2") do |config|
  # config.ssh.forward_agent = true  # ホスト側でssh-agentが起動されている場合

  config.vm.boot_timeout = 300  # 結構時間がかかります
  config.vm.box = "ubuntu/jammy64"  # Ubuntu 22.04

  config.vm.network "private_network",
    type: "dhcp"

  config.vm.provider "virtualbox" do |vb|
    vb.name = "setup"
    vb.memory = 1024
    vb.cpus = 2
  end

end
