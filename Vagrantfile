Vagrant.configure("2") do |config|
    HOSTS=5
    (1..HOSTS).each do |host_id|
        config.vm.box = "centos/7"
        config.vm.define "mariadb#{host_id}" do |mariadb|
            mariadb.vm.hostname = "mariadb#{host_id}"
            mariadb.vm.network "private_network", ip: "192.168.13.#{1+host_id}"
            mariadb.vm.boot_timeout = 300
            #mariadb.vm.synced_folder '.', '/vagrant', disabled: true
            mariadb.vm.provider "virtualbox" do |v|
                v.name = "mariadb#{host_id}"
                v.memory = 1024
                v.cpus = 1
                device=0
                [ "data", "log" ].each do |volume|
                # MariaDB volumes
                file_to_disk = "mariadb#{host_id}_#{volume}.vdi"
                  unless File.exist?(file_to_disk)
                    v.customize ['createhd', '--filename', "#{file_to_disk}", '--size', (5 * 1024).floor]
                  end
                  v.customize ['storageattach', :id, '--storagectl', 'IDE Controller', '--port', 1, '--device', device, '--type', 'hdd', '--medium', "#{file_to_disk}"]
                  device+=1
                end
            end
            mariadb.vm.provision :shell, path: "bash/bootstrap_avahi.sh", run: "always"
            # All vms have been provisioned. Run Ansible
            if host_id == HOSTS
                mariadb.vm.provision :ansible do |ansible|
                    ansible.limit = "all" # Connect to all machines
                    ansible.playbook = "mariadb.yaml"
                    ansible.groups = {
                      "masters" => ["mariadb1", "mariadb2"],
                      "slaves" => ["mariadb3", "mariadb4", "mariadb5"]
                    }
                end
            end
        end
    end
end
