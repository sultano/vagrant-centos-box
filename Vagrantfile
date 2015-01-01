require 'yaml'

# Configs from config file
dir = File.dirname(File.expand_path(__FILE__))
configValues = YAML.load_file("#{dir}/config/vm.yaml")
cfg = configValues['default']

# Vagrantfile API/syntax version
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  # Every Vagrant virtual environment requires a box to build off of.
  config.vm.box = "#{cfg['vm']['box']}"
  config.vm.box_url = "#{cfg['vm']['box_url']}"

  # Set the hostname
  config.vm.hostname = "#{cfg['vm']['hostname']}"

  # Automatic box update checking
  config.vm.box_check_update = true

  # Forwarded ports mapping
  cfg['vm']['network']['forwarded_port'].each do |i, port|
    config.vm.network "forwarded_port", guest: port['guest'].to_i, host: port['host'].to_i
  end

  # Create a private network, allowing host-only access using a specific IP
  config.vm.network "private_network", ip: "#{cfg['vm']['network']['ip']}"

  # Shared folders to the guest VM
  cfg['vm']['synced_folder'].each do |i, folder|
    config.vm.synced_folder "#{folder['source']}", "#{folder['target']}", id: "#{i}", type: "nfs", mount_options: ["nolock"]
  end

  # Virtualbox provider configuration
  ENV['VAGRANT_DEFAULT_PROVIDER'] = 'virtualbox'

  config.vm.provider :virtualbox do |vb|
    vb.name = "#{cfg['vm']['name']}"
    vb.customize ['modifyvm', :id, '--memory', "#{cfg['vm']['memory']}"]
    vb.customize ['modifyvm', :id, '--natdnshostresolver1', 'on']
  end

  # SSH connections with agent forwarding
  config.ssh.forward_agent = cfg['ssh']['forward_agent']
end
