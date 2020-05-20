# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.

Vagrant.configure("2") do |config|
  config.ssh.insert_key = false
  (1..3).each do |num|
    config.vm.define :"worker-node#{num}" do |node|
      node.vm.box = "centos/7"
      node.vm.provider "virtualbox" do |vb|
        vb.name = "worker-node#{num}"
        vb.memory = 2048
        vb.cpus = 2
      end
      node.vm.hostname = "worker-node#{num}.myredhat.com"
      node.vm.network :private_network, ip:"192.168.33.1#{num}", virtualbox__intnet:"kubernetes"
      node.vm.provision :shell, run: "always", inline: <<-EOF
        echo "root:vagrant" | chpasswd
        # SSH の設定を変更
        sed -i -e "s/#PubkeyAuthentication/PubkeyAuthentication/g" /etc/ssh/sshd_config
        sed -i -e 's/PasswordAuthentication no/PasswordAuthentication yes/' /etc/ssh/sshd_config
        systemctl restart sshd
      EOF
    end
  end

  config.vm.define :"master-node" do |node|
    node.vm.box = "centos/7"
    node.vm.provider "virtualbox" do |vb|
      vb.name = "master-node"
      vb.memory = 2048
      vb.cpus = 2
    end
    node.vm.hostname = "master-node.myredhat.com"
    node.vm.network :private_network, ip:"192.168.33.10", virtualbox__intnet:"kubernetes"
    node.vm.provision :shell, run: "always", inline: <<-EOF
      echo "root:vagrant" | chpasswd
      # SSH の設定を変更
      sed -i -e "s/#PubkeyAuthentication/PubkeyAuthentication/g" /etc/ssh/sshd_config
      sed -i -e 's/PasswordAuthentication no/PasswordAuthentication yes/' /etc/ssh/sshd_config
      systemctl restart sshd
    EOF
  end

#  config.vm.define :zookeeper do |node|
#    node.vm.box = "generic/rhel8"
#    node.vm.provider "virtualbox" do |vb|
#      vb.name = "zookeeper"
#      vb.memory = 1024
#      vb.cpus = 1
#    end
#    node.vm.hostname = "zookeeper.myredhat.com"
#    node.vm.box = "generic/rhel8"
#    node.vm.network :private_network, ip:"192.168.33.14", virtualbox__intnet:"kafka"
#    node.vm.provision :hosts, :sync_hosts => true 
#    node.vm.provision :shell, run: "always", inline: <<-EOF
#      echo "root:vagrant" | chpasswd
#      # SSH の設定を変更
#      sed -i -e "s/#PubkeyAuthentication/PubkeyAuthentication/g" /etc/ssh/sshd_config
#      sed -i -e 's/PasswordAuthentication no/PasswordAuthentication yes/' /etc/ssh/sshd_config
#      systemctl restart sshd
#    EOF
#  end
  
  config.vm.define :ansible do |node|
    node.vm.provider "virtualbox" do |vb|
      vb.name = "ansible"
      vb.memory = 1024
      vb.cpus = 1
    end
    node.vm.hostname = "ansible.myredhat.com"
    node.vm.box = "centos/7"
    node.vm.network :private_network, ip:"192.168.33.31", virtualbox__intnet:"kubernetes"
    node.vm.provision :hosts, :sync_hosts => true
    node.vm.provision :shell, run: "always", inline: <<-EOF
      yum -y install epel-release
      # ansible インストール
      yum -y install ansible --enablerepo=epel-testing
      # expect コマンドインストール 
      yum -y install expect
      # ssh-keygen で ssh 用の鍵生成
      cp -f /vagrant/sendkey.expect ~/sendkey.expect
      chmod +x ~/sendkey.expect
      ssh-keygen -t rsa  -N "" -f ~/.ssh/id_rsa
      for i in root@192.168.33.10 root@192.168.33.11 root@192.168.33.12 root@192.168.33.13; do ~/sendkey.expect $i;done
      # ansible で設定
      ansible-playbook -i /vagrant/inventory.txt /vagrant/kubernetes.yml
    EOF
  end
end
