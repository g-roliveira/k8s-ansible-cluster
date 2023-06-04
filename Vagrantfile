# -*- mode: ruby -*-
# vi: set ft=ruby :

# Variaveis
VAGRANTFILE_API_VERSION = 2

# Chamando modulo YAML
require 'yaml'

# Lendo o arquivo YAML com as configuracoes do ambiente
environments = YAML.load_file('provision/ansible/group_vars/all.yml')['k8s_machines']

# Limitando apenas a ultima versao estavel do Vagrant instalada
Vagrant.require_version '>= 2.0.0'

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  # Iteracao com os servidores do ambiente
  environments.each do |env|
    config.vm.define env['name'] do |srv|
      srv.vm.box      = env['box']
      srv.vm.hostname = env['hostname']
      srv.vm.network 'private_network', ip: env['ipaddress']
      
      # Verifica se a chave 'additional_interface' existe e é verdadeira
      if env.key?('additional_interface') && env['additional_interface'] == true
        srv.vm.network 'private_network', ip: '1.0.0.100',
          auto_config: false
      end
      
      srv.vm.provider 'virtualbox' do |vb|
        vb.name   = env['name']
        vb.memory = env['memory']
        vb.cpus   = env['cpus']

        # Adiciona a VM a um grupo especificado pela chave 'vbox_group'
        if env.key?('vbox_group')
          vb.customize ['modifyvm', :id, '--groups', env['vbox_group']]
        end
      end
      
      srv.vm.provision 'ansible' do |ansible|
        ansible.playbook           = env['provision']
        # ansible.inventory_path     = 'ansible_inventory'
        ansible.limit              = 'all'
        ansible.become             = true
        ansible.become_user        = 'root'
        ansible.compatibility_mode = '2.0'
      end
    end
  end
end
