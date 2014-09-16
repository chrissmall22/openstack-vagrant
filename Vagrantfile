# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!


# This uses Vagrant Cloud for simplicity so it needs to have Vagrant 1.5
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  config.vm.provision "shell", path: "resources/puppet/scripts/bootstrap.sh"

  config.vm.provision "puppet" do |puppet|
      puppet.hiera_config_path = "resources/puppet/hiera.yaml"
      puppet.working_directory = "/vagrant/resources/puppet"
      puppet.manifests_path = "resources/puppet/manifests"
      puppet.manifest_file  = "base.pp"
  end

  num_compute_nodes = (ENV['DEVSTACK_NUM_COMPUTE_NODES'] || 1).to_i

  # ip configuration
  control_ip = "192.168.50.20"
  compute_ip_base = "192.168.50."
  compute_ips = num_compute_nodes.times.collect { |n| compute_ip_base + "#{n+21}" }

  config.vm.define "mininet" do |mininet|
    mininet.vm.box = "ubunty/trusty64"
    mininet.vm.hostname = "mininet"
    mininet.vm.network "private_network", ip: "192.168.50.15"
    mininet.vm.provider :virtualbox do |vb|
      vb.memory = 2048
    end
    mininet.vm.provider "vmware_fusion" do |vf|
      vf.vmx["memsize"] = "2048"
    end
    mininet.vm.provision "puppet" do |puppet|
      puppet.hiera_config_path = "resources/puppet/hiera.yaml"
      puppet.working_directory = "/vagrant/resources/puppet"
      puppet.manifests_path = "resources/puppet/manifests"
      puppet.manifest_file  = "mininet.pp"
    end
  end

  config.vm.define "devstack-control" do |control|
    control.vm.box = "ubuntu/trusty64"
    control.vm.hostname = "devstack-control"
    control.vm.network "private_network", ip: "#{control_ip}"
    control.vm.network "forwarded_port", guest: 8080, host: 8081
    control.vm.provider :virtualbox do |vb|
      vb.memory = 4096
    end
    control.vm.provider "vmware_fusion" do |vf|
      vf.vmx["memsize"] = "4096"
    end
    control.vm.provision "puppet" do |puppet|
      puppet.hiera_config_path = "resources/puppet/hiera.yaml"
      puppet.working_directory = "/vagrant/resources/puppet"
      puppet.manifests_path = "resources/puppet/manifests"
      puppet.manifest_file  = "devstack-control.pp"
    end
  end

  # Devstack Compute Nodes
  num_compute_nodes.times do |n|
    config.vm.define "devstack-compute-#{n+1}" do |compute|
      compute_ip = compute_ips[n]
      compute_index = n+1
      compute.vm.box = "ubuntu/trusty64"
      compute.vm.hostname = "devstack-compute-#{compute_index}"
      compute.vm.network "private_network", ip: "#{compute_ip}"
      compute.vm.provider :virtualbox do |vb|
        vb.memory = 4096
      end
      compute.vm.provider "vmware_fusion" do |vf|
        vf.vmx["memsize"] = "4096"
      end
      compute.vm.provision "puppet" do |puppet|
        puppet.hiera_config_path = "resources/puppet/hiera.yaml"
        puppet.working_directory = "/vagrant/resources/puppet"
        puppet.manifests_path = "resources/puppet/manifests"
        puppet.manifest_file  = "devstack-compute.pp"
      end
    end
  end
end
