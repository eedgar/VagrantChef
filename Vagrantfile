require 'berkshelf/vagrant'

# We'll mount the Chef::Config[:file_cache_path] so it persists between
# Vagrant VMs
host_cache_path = File.expand_path("../.cache", __FILE__)
guest_cache_path = "/tmp/vagrant-cache"

# ensure the cache path exists
FileUtils.mkdir(host_cache_path) unless File.exist?(host_cache_path)

Vagrant::Config.run do |config|
  #config.vm.customize ["modifyvm", :id, "--cpus", 2]
  #config.vm.customize ["modifyvm", :id, "--memory", 1024]
  # All Vagrant configuration is done here. The most common configuration
  # options are documented and commented below. For a complete reference,
  # please see the online documentation at vagrantup.com.

  # The path to the Berksfile to use with Vagrant Berkshelf
  # config.berkshelf.berksfile_path = "./Berksfile"

  # An array of symbols representing groups of cookbook described in the Vagrantfile
  # to skip installing and copying to Vagrant's shelf.
  # config.berkshelf.only = []

  # An array of symbols representing groups of cookbook described in the Vagrantfile
  # to skip installing and copying to Vagrant's shelf.
  # config.berkshelf.except = []

  config.vm.host_name = "chef-server-berkshelf"

  config.vm.box = "ubuntu-12.04.1-server-amd64"
  config.vm.box_url = "https://opscode-vm.s3.amazonaws.com/vagrant/boxes/opscode-ubuntu-12.04.box"

  # Boot with a GUI so you can see the screen. (Default is headless)
  # config.vm.boot_mode = :gui

  # Assign this VM to a host-only network IP, allowing you to access it
  # via the IP. Host-only networks can talk to the host machine as well as
  # any other machines on the same network, but cannot be accessed (through this
  # network interface) by any external networks.
  config.vm.network :hostonly, "33.33.33.10"

  # Assign this VM to a bridged network, allowing you to connect directly to a
  # network using the host's network device. This makes the VM appear as another
  # physical device on your network.

  # config.vm.network :bridged

  # Forward a port from the guest to the host, which allows for outside
  # computers to access the VM, whereas host only networking does not.
  #config.vm.forward_port 80, 8080
  config.vm.forward_port 4000, 4000
  config.vm.forward_port 4040, 4040
  config.vm.forward_port 80, 8080

  # Share an additional folder to the guest VM. The first argument is
  # an identifier, the second is the path on the guest to mount the
  # folder, and the third is the path on the host to the actual folder.
  config.vm.share_folder "cache", guest_cache_path, host_cache_path

  # Mkae a shared www folder
  config.vm.share_folder "v-data", "/var/www", "./www"

  config.ssh.max_tries = 40
  config.ssh.timeout   = 120

  # Enable SSH agent forwarding for git clones
  config.ssh.forward_agent = true

  config.vm.provision :chef_solo do |chef|
    chef.provisioning_path = guest_cache_path

    chef.json = {
       :chef_server => {
          :server_url =>"http://localhost.localdomain:4000",
          :webui_enabled => true,
          :web_ui_admin_default_password => "p@sswOrd1",
       },
      :chefpem => {
          'path' => '/etc/chef',
      },
      "apache" => {
        :listen_ports => ["8080"],
      }
    }

    chef.run_list = [
      "recipe[apt::default]",
      "recipe[build-essential::default]",
      "recipe[chef-server::rubygems-install]",
      "recipe[iptables::disabled]",
      "recipe[openssh]",
      "recipe[apache2]",
      "recipe[ntp]",
      "recipe[chefpem]"
    ]
  end
end
