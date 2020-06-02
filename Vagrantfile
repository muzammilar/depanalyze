# -*- mode: ruby -*-
# vi: set ft=ruby :

######################
# Setup Instructions #
######################

# 1. Install Vagrant plugins
# * vagrant plugin install vagrant-timezone
# * vagrant plugin install vagrant-disksize
# * vagrant plugin install vagrant-autodns
# * vagrant plugin install vagrant-vbguest

# 2. Run `vagrant up` on this vagrantfile to start all the VMs

# 3. Run `vagrant ssh <vm-name>` on this vagrantfile to ssh to the VM

# 4. Run `vagrant halt` on this vagrantfile to stop the VMs

# 5. Run `vagrant destroy` on this vagrantfile and destroy to the VMs

################
# Vagrant file #
################

# Bootup scripts go here.
$bootup_provision_coordinator = <<-SHELL
SHELL


$bootup_provision_worker = <<-SHELL

  # update clickhosue
  apt-get update
  apt-get install -y clickhouse-server clickhouse-client

  # restart clickhouse
  service clickhouse-server restart
SHELL

# One-off provision scripts here.
$provision_worker = <<-SHELL
  apt-get update
  apt-get install -y git
  apt-get install -y python3 python3-pip python3-dev python-tox python3-requests

  apt-get install -y gcc g++
    
SHELL


$provision_worker_clickhouse = <<-SHELL
  apt-get update
  
  # installation instructions take from https://clickhouse.tech/docs/en/getting-started/install/
  apt-get install apt-transport-https ca-certificates dirmngr
  apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv E0C56BD4  
  
  echo "deb https://repo.clickhouse.tech/deb/stable/ main/" | sudo tee \
    /etc/apt/sources.list.d/clickhouse.list

  DEBIAN_FRONTEND=noninteractive apt-get install -y clickhouse-server clickhouse-client
    
SHELL


$provision_coordinator = <<-SHELL
  apt-get update
  apt-get install -y git
  apt-get install -y python3 python3-pip python3-dev python-tox python3-requests
SHELL



################
# Vagrant file #
################

Vagrant.configure("2") do |config|

  config.timezone.value = "UTC"    # set timezone for all VMs

  ##################
  # setup a worker #
  ##################
  config.vm.define "worker" do |worker|

    worker.autodns.enable  # autodns configurations
    worker.vm.network "private_network", type: "dhcp"  

    worker.vm.hostname = "WORKER001.DC1"  # Machine hostname
    worker.vm.box = "ubuntu/bionic64"  # Customize OS Version
    worker.disksize.size = "5GB"  # Customize the amount of memory on the VM

    worker.vm.provider "virtualbox" do |vb|
        
       vb.gui = false  # Display the VirtualBox GUI when booting the machine

       vb.name = "Ubuntu 18.04 - worker - machine 01"  # machine name in virtualbox

       vb.memory = "1024"  # 1GB Memory
    end

    worker.vm.provision "shell", inline: $provision_worker  # basic provision script to automate installation of processes
    worker.vm.provision "shell", inline: $provision_worker_clickhouse  # basic provision script to automate installation of clickhouse
    worker.vm.provision "shell", inline: $bootup_provision_worker, run: "always"  # bootup provision script
  end


  ############################
  # setup the coordinator vm #
  ############################
  config.vm.define "coordinator" do |coordinator|

    coordinator.autodns.enable
    coordinator.vm.network "private_network", type: "dhcp"
    coordinator.vm.network "forwarded_port", guest: 443, host: 10443, auto_correct: true  # port forwarding configurations

    coordinator.vm.hostname = "COORDINATOR01.DC1"
    coordinator.vm.box = "ubuntu/bionic64"
    coordinator.disksize.size = "6GB"

    coordinator.vm.provider "virtualbox" do |vb|
      vb.gui = false  
      vb.name = "Ubuntu 18.04 - coordinator - machine 01"
      vb.memory = "1024"
    end

    # add all the stuff here
    coordinator.vm.provision "shell", inline: $coordinator_provision
    coordinator.vm.provision "shell", inline: $bootup_provision_coordinator, run: "always"
  end

end
