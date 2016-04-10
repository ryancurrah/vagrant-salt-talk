# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure(2) do |config|
  config.vm.define "jenkins" do |jenkins|
    jenkins.vm.box = "bento/centos-7.2"

    jenkins.vm.hostname = "jenkins"

    jenkins.vm.network "private_network", ip: "10.0.0.10"
    jenkins.vm.network "forwarded_port", guest: 22, host: 2210, id: 'ssh'
    jenkins.vm.network "forwarded_port", guest: 8080, host: 8080

    jenkins.vm.provider "virtualbox" do |vb|
      vb.memory = "1024"
      vb.cpus = 2
    end

    jenkins.vm.provision "shell", inline: <<-SHELL 
      yum -y install git
      yum -y install docker
      yum -y install ruby
      # Allow jenkins user to run docker commands
      sed -i "s/OPTIONS='--selinux-enabled'/OPTIONS='--selinux-enabled -G jenkins'/g" /etc/sysconfig/docker
      # Ignore docker warnings about storage loop0
      sed -i "s/DOCKER_STORAGE_OPTIONS=/DOCKER_STORAGE_OPTIONS=--storage-opt dm.no_warn_on_loop_devices=true/g" /etc/sysconfig/docker-storage
      systemctl enable docker
      systemctl start docker
      # Install pip and bumpversion
      curl -o "/tmp/get-pip.py" "https://bootstrap.pypa.io/get-pip.py"; python /tmp/get-pip.py; rm -rf /tmp/get-pip.py
      pip install bumpversion
      rpm -ivh /vagrant/jdk-8u77-linux-x64.rpm
      rpm -ivh /vagrant/jenkins-2.0-1.1.noarch.rpm
      systemctl enable jenkins
      systemctl start jenkins
    SHELL
  end

  config.vm.define "splunk" do |splunk|
    splunk.vm.box = "bento/centos-7.2"

    splunk.vm.hostname = "splunk"

    splunk.vm.network "private_network", ip: "10.0.0.20"
    splunk.vm.network "forwarded_port", guest: 22, host: 2220, id: 'ssh'
    splunk.vm.network "forwarded_port", guest: 8000, host: 8000
    splunk.vm.network "forwarded_port", guest: 8089, host: 8089

    splunk.vm.provider "virtualbox" do |vb|
      vb.memory = "1024"
      vb.cpus = 2
    end

    splunk.vm.provision "shell", inline: <<-SHELL 
      rpm -ivh /vagrant/splunklight-6.4.0-f2c836328108-linux-2.6-x86_64.rpm
      /opt/splunk/bin/splunk enable boot-start --answer-yes --no-prompt --accept-license
      systemctl enable splunk
      systemctl start splunk
    SHELL
  end
end