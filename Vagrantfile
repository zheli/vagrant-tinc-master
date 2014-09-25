# -*- mode: ruby -*-
# vi: set ft=ruby :
require './Settings'
include DigitalOcean
include General

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  config.vm.network :forwarded_port, guest: 80, host: 9980

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network :public_network

  # If true, then any SSH connections made will enable agent forwarding.
  # Default value: false
  # config.ssh.forward_agent = true

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  config.vm.synced_folder "sync_folder", "/vagrant"
  config.vm.synced_folder "salt/roots", "/srv"

  config.vm.hostname = General.HOSTNAME
  
  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  config.vm.provider :virtualbox do |vb, override|
    override.vm.box = "trusty-server-cloudimg-amd64"
    override.vm.box_url = "https://cloud-images.ubuntu.com/vagrant/trusty/current/trusty-server-cloudimg-amd64-vagrant-disk1.box"
    # override.vm.network "private_network", ip: "192.168.33.10"
    # override.vm.network "public_network", bridge: 'Realtek PCIe GBE Family Controller'
    # override.vm.synced_folder "sync_folder", "/vagrant", id: "core", :nfs => true,  :mount_options   => ['nolock,vers=3,udp']
    # override.vm.synced_folder "salt/roots", "/srv", id: "core", :nfs => true,  :mount_options   => ['nolock,vers=3,udp']
   
    # Don't boot with headless mode
    #   vb.gui = true

    # Use VBoxManage to customize the VM. For example to change memory:
    vb.customize ["modifyvm", :id, "--memory", "512"]
  end

  ## Configuration for DigitalOcean
  config.vm.provider :digital_ocean do |provider, override|
    override.vm.box = "digital_ocean"
    override.vm.box_url = "https://github.com/smdahlen/vagrant-digitalocean/raw/master/box/digital_ocean.box"
    override.ssh.private_key_path = "keys/id_rsa"
    override.ssh.username = "vagrant"
    provider.token = DO_TOKEN
    provider.image = "Ubuntu 13.10 x64"
    provider.region = "Amsterdam 2"
    provider.size = "512MB"
    provider.ssh_key_name = DO_SSH_KEY_NAME
    provider.private_networking = true
    provider.setup = true
  end

  #
  # View the documentation for the provider you're using for more
  # information on available options.

  config.vm.provision :salt do |salt|
    # Configure the minion
    salt.minion_config = "salt/minion.conf"

    # Show the output of salt
    salt.verbose = true
    
    # Pre-distribute these keys on our local installation
    salt.minion_key = "salt/keys/salt.pem"
    salt.minion_pub = "salt/keys/salt.pub"

    # Run the highstate on start
    salt.run_highstate = true

    # Install the latest version of SaltStack
    salt.install_type = "git"

  end
end