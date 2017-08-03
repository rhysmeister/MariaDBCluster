Vagrant.configure("2") do |config|
    HOSTS=5
    (1..HOSTS).each do |host_id|
        config.vm.box = "centos/7"
        config.vm.define "mariadb#{host_id}" do |mariadb|
            mariadb.vm.hostname = "mariadb#{host_id}"
            mariadb.vm.network "private_network", ip: "192.168.13.#{1+host_id}"
            mariadb.vm.synced_folder '.', '/vagrant', disabled: true
            mariadb.vm.provider "virtualbox" do |v|
                v.name = "mariadb#{host_id}"
                v.memory = 1024
                v.cpus = 1
            end
            # All vms have been provisioned. Run Ansible
            if host_id == HOSTS
                mariadb.vm.provision :ansible do |ansible|
                    ansible.limit = "all" # Connect to all machines
                    ansible.playbook = "mariadb.yml"
                end
            end
        end
    end
end