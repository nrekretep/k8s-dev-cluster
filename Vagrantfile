IMAGE_NAME = "generic/ubuntu1804"
N = 2 

Vagrant.configure("2") do |config|
    config.ssh.insert_key = false

    config.vm.provider "virtualbox" do |v|
        v.memory = 1024
        v.cpus = 2
    end
  
    config.vm.provider "libvirt" do |vm|
      vm.cpus = 2
      vm.driver = "kvm"
      vm.memory = 4096
      vm.nic_model_type = "e1000"
      vm.qemu_use_session = false
      vm.management_network_address = '192.170.0.0/24'
    end

    config.vm.define "master" do |master|
        master.vm.box = IMAGE_NAME
        master.vm.network "private_network", ip: "192.169.50.10"
        master.vm.hostname = "master"
        master.vm.provision "ansible" do |ansible|
            ansible.playbook = "master-playbook.yml"
            ansible.extra_vars = {
                node_ip: "192.169.50.10",
            }
        end
    end

    (1..N).each do |i|
        config.vm.define "node-#{i}" do |node|
            node.vm.box = IMAGE_NAME
            node.vm.network "private_network", ip: "192.169.50.#{i + 10}"
            node.vm.hostname = "node-#{i}"
            node.vm.provision "ansible" do |ansible|
                ansible.playbook = "node-playbook.yml"
                ansible.extra_vars = {
                    node_ip: "192.169.50.#{i + 10}",
                }
            end
        end
    end
end
