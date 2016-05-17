# -*- mode: ruby -*-
# # vi: set ft=ruby :

#################### VAGRANT YML ####################
# Project : https://github.com/bfolliot/vagrant-yml #
# Author  : Bryan Folliot                           #
# Version : 1.3.4                                   #
#####################################################


# Specify Vagrant API version
VAGRANTFILE_API_VERSION = "2"
CONFIG_FILE             ='vagrant.yml'
CONFIG_FILE_LOCAL       ='vagrant.local.yml'
# Require YAML module
require 'yaml'

# Read YAML file with box details
vagrantRoot          = File.dirname(__FILE__)

if not Pathname(vagrantRoot + "/" + CONFIG_FILE).exist?
    fail "vagrant.yml not found"
end

vagrantSettings      = YAML.load_file(vagrantRoot + "/" + CONFIG_FILE)

if Pathname(vagrantRoot + "/" + CONFIG_FILE_LOCAL).exist?
    vagrantSettingsLocal = YAML.load_file(vagrantRoot + "/" + CONFIG_FILE_LOCAL)
    vagrantSettings.deep_merge!(vagrantSettingsLocal)
end

# Create servers
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
    # General settings for hostmanager
    if Vagrant.has_plugin?("vagrant-hostmanager") && vagrantSettings['global'] && vagrantSettings['global']['hostmanager']
        config.hostmanager.enabled           = true
        config.hostmanager.manage_host       = vagrantSettings['global']['hostmanager']['manage_host']
        config.hostmanager.ignore_private_ip = vagrantSettings['global']['hostmanager']['ignore_private_ip']
        config.hostmanager.include_offline   = vagrantSettings['global']['hostmanager']['include_offline']
    end

    vagrantSettings['servers'].each do |serverName, serverSettings|
        # Create server
        config.vm.define serverName do |server|
            server.vm.box      = serverSettings['box']['name']
            server.vm.box_url  = serverSettings['box']['url'] if serverSettings['box']['url']
            server.vm.hostname = serverSettings['hostname']   if serverSettings['hostname']

            # Forward SSH agent
            if serverSettings['ssh'] && serverSettings['ssh']['forward_agent']
                config.ssh.forward_agent = serverSettings['ssh']['forward_agent']
            end

            # Server settings for networking
            if serverSettings['network']
                if serverSettings['network']['private']
                    if serverSettings['network']['private'] == 'dhcp'
                        server.vm.network :private_network, type: "dhcp"
                    else
                        server.vm.network :private_network, ip: serverSettings['network']['private']
                    end
                end
                if serverSettings['network']['public']
                    if serverSettings['network']['public'] == 'dhcp'
                        server.vm.network :public_network
                    else
                        server.vm.network :public_network, ip: serverSettings['network']['public']
                    end
                end
            end

            # Server settings for virtualbox
            if serverSettings['virtualbox']
                server.vm.provider :virtualbox do |v|
                    v.gui    = serverSettings['virtualbox']['gui']    if serverSettings['virtualbox']['gui']
                    v.name   = serverSettings['virtualbox']['name']   if serverSettings['virtualbox']['name']
                    v.memory = serverSettings['virtualbox']['memory'] if serverSettings['virtualbox']['memory']
                    v.cpus   = serverSettings['virtualbox']['cpus']   if serverSettings['virtualbox']['cpus']
                    if serverSettings['virtualbox']['other']
                        serverSettings['virtualbox']['other'].each do |type, params|
                            params.each do |key, value|
                                v.customize [type, :id, key, value ]
                            end
                        end
                    end
                end
            end

            # Server settings for synced_folder
            if serverSettings['synced_folders']
                serverSettings['synced_folders'].each do |name, synced_folder|

                    if name == "vagrant" && !synced_folder
                        server.vm.synced_folder ".", "/vagrant", id: "vagrant-root", disabled: true
                    else
                        if synced_folder['params']
                            server.vm.synced_folder synced_folder['host_path'], synced_folder['guest_path'], **synced_folder['params']
                        else
                            server.vm.synced_folder synced_folder['host_path'], synced_folder['guest_path']
                        end
                    end
                end
            end

            # Server settings for Port Forwarding
            if serverSettings['port_forward']
                serverSettings['port_forward'].each do |name, forwarded_port|
                    server.vm.network :forwarded_port, guest: forwarded_port["guest_port"], host: forwarded_port["host_port"]
                end
            end

            # Server settings for vbguest
            if Vagrant.has_plugin?("vagrant-vbguest") && serverSettings['vbguest']
                server.vbguest.auto_update = serverSettings['vbguest']['auto_update']
                server.vbguest.no_remote   = serverSettings['vbguest']['no_remote']
                server.vbguest.auto_reboot = serverSettings['vbguest']['auto_reboot']
            end

            # Server settings for hostmanager
            if Vagrant.has_plugin?("vagrant-hostmanager") && vagrantSettings['global'] && vagrantSettings['global']['hostmanager']
                server.hostmanager.aliases = serverSettings['aliases'] if serverSettings['aliases']
                server.vm.provision :hostmanager
            end

            # Server settings for provisioning
            if serverSettings['provision']
                serverSettings['provision'].each do |name, provider|
                    server.vm.provision provider['type'], **provider['params']
                end
            end
        end
    end
end
