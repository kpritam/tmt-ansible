servers=[
  {
    :hostname => "seed-1",
    :ip => "192.168.100.10",
    :box => "centos/7",
    :ram => 2048,
    :cpu => 4
  },
  {
    :hostname => "node-1",
    :ip => "192.168.100.11",
    :box => "centos/7",
    :ram => 2048,
    :cpu => 4
  }
]

Vagrant.configure(2) do |config|
    servers.each do |machine|
        config.vm.define machine[:hostname] do |node|
          node.ssh.insert_key = false
          node.vm.box = machine[:box]
          node.vm.hostname = machine[:hostname]
          node.vm.network "private_network", ip: machine[:ip]
          node.vm.provider "virtualbox" do |vb|
              vb.customize ["modifyvm", :id, "--memory", machine[:ram]]
          end

          if machine[:hostname] == "seed-1"
            node.vm.network "forwarded_port", guest: 9999, host: 9999
          end

          # Only execute once the Ansible provisioner,
          # when all the machines are up and ready.
          if machine[:hostname] == "node-1"
            node.vm.provision :ansible do |ansible|
              # Disable default limit to connect to all the machines
              ansible.limit = "all"
              ansible.verbose = "v"
              ansible.playbook = "provisioning/playbook.yml"
            end
          end
        end
    end
end
