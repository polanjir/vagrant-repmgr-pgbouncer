Vagrant.configure("2") do |config|
  (1..3).each do |n|
    config.vm.define "node#{n}" do |define|
      define.ssh.insert_key = false
      define.vm.box = "ubuntu/bionic64"
      define.vm.hostname = "node#{n}"
      define.vm.network :private_network, ip: "172.16.1.1#{n}"
      config.vbguest.auto_update = false # Disable VirtualBox Guest Additions
      # if you would like to use port forwarding, uncomment the line below
      # define.vm.network :forwarded_port, guest: 5432, host: "543#{n}"

      define.vm.provider :virtualbox do |v|
        v.cpus = 2
        v.memory = 1024
        v.name = "node#{n}"
        v.customize ["modifyvm", :id, "--groups", "/PG-cluster"]
      end

      if n == 3
        define.vm.provision :ansible do |ansible|
          ansible.limit = "all"
          ansible.playbook = "provisioning/playbook.yaml"

          ansible.host_vars = {
            "node1" => {:connection_host => "172.16.1.11",
                        :node_id => 1,
                        :role => "primary" },

            "node2" => {:connection_host => "172.16.1.12",
                        :node_id => 2,
                        :role => "standby" },

            "node3" => {:connection_host => "172.16.1.13",
                        :node_id => 3,
                        :role => "witness" }
          }
          # to disable ansible playbook verbose mode, comment the line below
          ansible.verbose = "v"
        end
      end

    end
  end
end
