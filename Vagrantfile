#  -*- mode: ruby -*-
# vi: set ft=ruby :
# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/bionic64"
  config.vm.hostname = "ubuntu"
    
  # Provider for VirtualBox
  config.vm.provider :virtualbox do |vb|
    vb.memory = "2048"
    vb.cpus = 2
  end

  config.vm.provider :docker do |docker, override|
    override.vm.box= nil
    docker.image = "rofrano/vagrant-provider:ubuntu"
    docker.has_ssh = true
    docker.remains_running = true
    docker.privileged = true
    docker.volumes = ["/sys/fs/cgroup:/sys/fs/cgroup:ro"]
  end

  # Provision Docker Engine and pull down PostgreSQL
  config.vm.provision :docker do |d|
    d.pull_images "postgres:alpine"
    d.run "postgres:alpine",
       args: "-d -p 5432:5432 -e POSTGRES_PASSWORD=postgres"
  end
    
  config.ssh.insert_key = false
  config.ssh.private_key_path = ['~/.vagrant.d/insecure_private_key', '~/.ssh/id_rsa']
  config.vm.provision "file", source: "~/.ssh/id_rsa.pub", destination: "~/.ssh/authorized_keys"
  config.vm.provision "file", source: "~/.ssh/id_rsa.pub", destination: "~/.ssh/id_rsa.pub"
  config.vm.provision "file", source: "~/.ssh/id_rsa", destination: "~/.ssh/id_rsa"

  config.vm.provision "shell", inline: <<-SHELL
    sudo sed -i -e "\\#PasswordAuthentication yes# s#PasswordAuthentication yes#PasswordAuthentication no#g" /etc/ssh/sshd_config
    sudo systemctl restart sshd.service
    echo "finished"
  SHELL

  config.vm.provision "shell", inline: <<-SHELL
    apt-get update
    apt-get -y upgrade
    DEBIAN_FRONTEND=noninteractive apt-get install -y mysql-server libapache2-mod-php php php-cli php-common php-mysql php-xml php-gd php-opcache php-mbstring php-tokenizer php-json php-bcmath php-zip unzip
    curl -sS https://getcomposer.org/installer | sudo php -- --install-dir=/usr/local/bin --filename=composer
    curl -fsSL https://deb.nodesource.com/setup_lts.x | sudo -E bash -
    DEBIAN_FRONTEND=noninteractive apt-get install -y nodejs
  SHELL

end

