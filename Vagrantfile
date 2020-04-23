# Verbose
VERBOSE = ENV.fetch('VERBOSE', false)

# Hack on MacOS guest machine
if (/darwin/ =~ RUBY_PLATFORM) != nil
  # See https://blog.phusion.nl/2017/10/13/why-ruby-app-servers-break-on-macos-high-sierra-and-what-can-be-done-about-it/
  ENV["OBJC_DISABLE_INITIALIZE_FORK_SAFETY"] = "YES"
end

Vagrant.configure("2") do |config|

  # Better multi-machines support
  config.hostmanager.enabled = true
  config.hostmanager.manage_host = true # Ansible is executed from local machine and requires machines present in /etc/hosts
  config.hostmanager.manage_guest = true
  config.hostmanager.ignore_private_ip = false
  config.hostmanager.include_offline = true

  config.ssh.insert_key = false

  config.vm.box = "bento/ubuntu-18.04"
  config.vm.provider "virtualbox" do |v|
    v.linked_clone = true
    v.customize ["modifyvm", :id, "--audio", "none"]
  end

  # List of machines to provision
  machines = {
    "vag1-prm01"         => { :ip => "192.168.50.20", :cpus => 1, :mem => 1024 },
    "vag1-grf01"         => { :ip => "192.168.50.21", :cpus => 1, :mem => 1024 },
    "vag1-csl01"         => { :ip => "192.168.50.22", :cpus => 1, :mem => 1024 },
    "vag1-vic01"         => { :ip => "192.168.50.23", :cpus => 1, :mem => 1024 },
  }

  # Detect single machine provisioning
  SINGLE_MACHINE = machines.key?(ARGV[-1])

  # Provision each machine sequentially
  machines.each_with_index do |(hostname, info), i|
    config.vm.define hostname do |cfg|

      cfg.vm.provider :virtualbox do |vb, override|
        override.vm.network :private_network, ip: "#{info[:ip]}"
        override.vm.hostname = hostname
        vb.customize ["modifyvm", :id, "--memory", info[:mem], "--cpus", info[:cpus], "--hwvirtex", "on" ]
      end

      # Only execute once the Ansible provisioner,
      # when all the machines are up and ready.
      # Caution: the provisioning should run even if a single machine is specified!
      # https://www.vagrantup.com/docs/provisioning/ansible.html#ansible-parallel-execution
      if SINGLE_MACHINE || i == machines.size - 1

        ansible_vars = {}
        cfg.vm.provision "ansible", type: 'ansible' do |ansible|
          ansible.playbook = "ansible/playbook.yml"
          ansible.inventory_path = "ansible/inventory/local/hosts.ini"
          ansible.become = true
          ansible.limit = "all"
          ansible.galaxy_roles_path = "ansible/.galaxy_roles"
          ansible.galaxy_role_file = "ansible/roles/requirements.yml"
          ansible.verbose = VERBOSE
          ansible.extra_vars = ansible_vars
        end
      end
    end
  end
end
