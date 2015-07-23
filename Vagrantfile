# -*- mode: ruby -*-
# vi: set ft=ruby ts=2 sw=2 tw=0 et :

Vagrant.require_version ">= 1.5"

Vagrant.configure("2") do |config|

    name = File.basename(Dir.pwd)

    ipfile = '.ip'
    if File.file?(ipfile)
        require 'open-uri'
        ip = open(ipfile).read
    else
        ip = "192.168." + (1 + rand(254)).to_s + "." + (1 + rand(254)).to_s
        File.open(ipfile, "w") {|f| f.puts ip }
    end

    config.vm.provider :virtualbox do |v|
        v.name = name
        v.customize [
            "modifyvm", :id,
            "--name", name,
            "--memory", 512,
            "--natdnshostresolver1", "on",
            "--cpus", 1,
        ]
    end

    config.vm.box = "ubuntu/trusty64"
    
    config.vm.network :private_network, ip: ip
    config.ssh.forward_agent = true 

    config.vm.provision "ansible" do |ansible|
        ansible.playbook = "playbook.yml"
        # ansible.inventory_path = "inventories/comehireme"
        ansible.limit = 'all'
        ansible.extra_vars = {
            private_interface: ip,
            hostname: name,
            servername: name
        }
        # ansible.verbose = 'vvvv'
    end
    config.vm.synced_folder "./", "/vagrant", type: "nfs", disabled: true
end

