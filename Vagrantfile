# -*- mode: ruby -*-
# # vi: set ft=ruby :

#################### VAGRANT YML ####################
# Project : https://github.com/bfolliot/vagrant-yml #
# Author  : Bryan Folliot                           #
# Version : 1.5.1                                   #
#####################################################

# Specify Vagrant API version
VAGRANTFILE_API_VERSION = "2"
CONFIG_FILE             ='vagrant.yml'
CONFIG_FILE_LOCAL       ='vagrant.local.yml'
# Require YAML module
require 'yaml'

puts "This project use Vagrant YAML (https://github.com/bfolliot/vagrant-yml) in version 1.5.1"

# Read YAML file with box details
vagrantRoot = File.dirname(__FILE__)

if not Pathname(vagrantRoot + "/" + CONFIG_FILE).exist?
    fail "vagrant.yml not found"
end

vagrantSettings = YAML.load_file(vagrantRoot + "/" + CONFIG_FILE)

if Pathname(vagrantRoot + "/" + CONFIG_FILE_LOCAL).exist?
    vagrantSettingsLocal = YAML.load_file(vagrantRoot + "/" + CONFIG_FILE_LOCAL)
    vagrantSettings.deep_merge!(vagrantSettingsLocal)
end

# Create environments
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
    # General settings for hostmanager
    if Vagrant.has_plugin?("vagrant-hostmanager") && vagrantSettings['global'] && vagrantSettings['global']['hostmanager']
        config.hostmanager.enabled           = true
        config.hostmanager.manage_host       = vagrantSettings['global']['hostmanager']['manage_host']
        config.hostmanager.ignore_private_ip = vagrantSettings['global']['hostmanager']['ignore_private_ip']
        config.hostmanager.include_offline   = vagrantSettings['global']['hostmanager']['include_offline']
    end

    if !vagrantSettings['environments'] && vagrantSettings['servers']
        warn "[DEPRECATION] `servers` is deprecated.  Please use `environments` instead."
        vagrantSettings['environments'] = vagrantSettings['servers']
    end

    vagrantSettings['environments'].each do |environmentName, environmentSettings|
        # Create environment
        config.vm.define environmentName do |environment|
            environment.vm.box      = environmentSettings['box']['name']
            environment.vm.box_url  = environmentSettings['box']['url'] if environmentSettings['box']['url']
            environment.vm.hostname = environmentSettings['hostname']   if environmentSettings['hostname']

            # Forward SSH agent
            if environmentSettings['ssh'] && environmentSettings['ssh']['forward_agent']
                config.ssh.forward_agent = environmentSettings['ssh']['forward_agent']
            end

            # Server settings for networking
            if environmentSettings['network']
                if environmentSettings['network']['private']
                    if environmentSettings['network']['private'] == 'dhcp'
                        environment.vm.network :private_network, type: "dhcp"
                    else
                        environment.vm.network :private_network, ip: environmentSettings['network']['private']
                    end
                end
                if environmentSettings['network']['public']
                    if environmentSettings['network']['public'] == 'dhcp'
                        environment.vm.network :public_network
                    else
                        environment.vm.network :public_network, ip: environmentSettings['network']['public']
                    end
                end
            end

            # Server settings for virtualbox
            if environmentSettings['virtualbox']
                environment.vm.provider :virtualbox do |v|
                    v.gui    = environmentSettings['virtualbox']['gui']    if environmentSettings['virtualbox']['gui']
                    v.name   = environmentSettings['virtualbox']['name']   if environmentSettings['virtualbox']['name']
                    v.memory = environmentSettings['virtualbox']['memory'] if environmentSettings['virtualbox']['memory']
                    v.cpus   = environmentSettings['virtualbox']['cpus']   if environmentSettings['virtualbox']['cpus']
                    if environmentSettings['virtualbox']['other']
                        environmentSettings['virtualbox']['other'].each do |type, params|
                            params.each do |key, value|
                                v.customize [type, :id, key, value ]
                            end
                        end
                    end
                end
            end

            # Server settings for synced_folder
            if environmentSettings['synced_folders']
                environmentSettings['synced_folders'].each do |name, synced_folder|

                    if name == "vagrant" && !synced_folder
                        environment.vm.synced_folder ".", "/vagrant", id: "vagrant-root", disabled: true
                    else
                        if synced_folder['params']
                            environment.vm.synced_folder synced_folder['host_path'], synced_folder['guest_path'], **synced_folder['params']
                        else
                            environment.vm.synced_folder synced_folder['host_path'], synced_folder['guest_path']
                        end
                    end
                end
            end

            # Server settings for Port Forwarding
            if environmentSettings['port_forward']
                environmentSettings['port_forward'].each do |name, forwarded_port|
                    environment.vm.network :forwarded_port, guest: forwarded_port["guest_port"], host: forwarded_port["host_port"]
                end
            end

            # Server settings for vbguest
            if Vagrant.has_plugin?("vagrant-vbguest") && environmentSettings['vbguest']
                environment.vbguest.auto_update = environmentSettings['vbguest']['auto_update']
                environment.vbguest.no_remote   = environmentSettings['vbguest']['no_remote']
                environment.vbguest.auto_reboot = environmentSettings['vbguest']['auto_reboot']
            end

            # Server settings for hostmanager
            if Vagrant.has_plugin?("vagrant-hostmanager") && vagrantSettings['global'] && vagrantSettings['global']['hostmanager']
                environment.hostmanager.aliases = environmentSettings['aliases'] if environmentSettings['aliases']
                environment.vm.provision :hostmanager
            end

            # Server settings for provisioning
            if environmentSettings['provision']
                environmentSettings['provision'].each do |name, provider|
                    if provider['params']
                        environment.vm.provision provider['type'], **provider['params']
                    else
                        environment.vm.provision provider['type']
                    end
                end
            end
        end
    end
end
