# -*- mode: ruby -*-
# vi: set ft=ruby :

## Configurações globais das VMs:

# Definir o provider como VirtualBox
# Definir a imagem base como debian/bookworm64
# Desativar a geração automática de chaves ssh pelo Vagrant

Vagrant.configure("2") do |config|
  config.vm.provider 'virtualbox'
  config.vm.box = 'debian/bookworm64'
  config.ssh.insert_key = false

# Criar gatilho para desabilitar DHCP do VirtualBox
  config.trigger.before :up do |trigger|
    trigger.info = "Desabilitando servidor DHCP do VirtualBox"
    trigger.run = {
      inline: "VBoxManage dhcpserver remove --netname HostInterfaceNetworking-vboxnet0 || true"
    }
  end

 # Desabilitar verificação/atualização automática do Guest Additions
  if Vagrant.has_plugin?("vagrant-vbguest")
    config.vbguest.auto_update = false
  end
  

  
## Configurações do provider para todas as VMs:
 
# Definir memória RAM de 512 MB para todas as VMs (será sobrescrito para cli)
# Habilitar clones vinculados para economizar espaço em disco
# Desabilitar verificação/atualização automática do Guest Additions

  config.vm.provider 'virtualbox' do |v|
    v.memory = '512'
    v.linked_clone = true
    v.check_guest_additions = false
  end


## Configurações do servidor de arquivos [arq]:

# Hostname
# Endereço IP estático
# Discos adicionais

    config.vm.define "arq" do |arq|
      arq.vm.hostname = "arq.ana.anderson.devops"
      arq.vm.network :private_network, ip: "192.168.56.131"
      arq.vm.provider "virtualbox" do |vb|
        (1..3).each do |i|
          disk_filename = "arq-disk#{i}.vdi"
          vb.customize ['createhd', '--filename', disk_filename, '--size', 10 * 1024]
          vb.customize ['storageattach', :id, 
                     '--storagectl', 'SATA Controller',
                     '--port', i + 1,
                     '--device', 0,
                     '--type', 'hdd',
                     '--medium', disk_filename]
        end
      end
    end

## Configurações do servidor de banco de dados [db]:

# Hostname
# Endereço IP via DHCP com atribuição estática baseada no MAC

   config.vm.define "db" do |db|
    db.vm.hostname = "db.ana.anderson.devops"
    db.vm.network :private_network, 
      type: "dhcp",
      mac: "080027000001"  
  end

## Configurações do servidor de aplicação [app]:

# Hostname
# Endereço IP via DHCP com atribuição estática baseada no MAC

  config.vm.define "app" do |app|
    app.vm.hostname = "app.ana.anderson.devops"
    app.vm.network :private_network, 
      type: "dhcp",
      mac: "080027000002"  
  end


## Configurações do host cliente [cli]:

# Hostname
# Endereço IP via DHCP (dinâmico, sem reserva)
# Configuração da VM: Memória RAM: 1024 MB

  config.vm.define "cli" do |cli|
    cli.vm.hostname = "cli.ana.anderson.devops"
    cli.vm.network :private_network, type: "dhcp"
    cli.vm.provider "virtualbox" do |vb|
      vb.memory = 1024  
    end
  end


## Configuração do provisionamento Ansible:

# Executar playbook comum em todas as VMs
  config.vm.provision "ansible" do |ansible|
    ansible.compatibility_mode = "2.0"
    ansible.playbook = "ansible/playbooks/common.yml"
    ansible.inventory_path = "ansible/inventory.yml"
    ansible.become = true
    ansible.limit = "all"  
    ansible.verbose = "v"
  end

# Executar playbooks específicos para o servidor de arquivos
  config.vm.provision "ansible" do |ansible|
    ansible.compatibility_mode = "2.0"
    ansible.playbook = "ansible/playbooks/arq-dhcp-dns.yml"
    ansible.inventory_path = "ansible/inventory.yml"
    ansible.become = true
    ansible.limit = "arq"  
    ansible.verbose = "v"
  end

  config.vm.provision "ansible" do |ansible|
    ansible.compatibility_mode = "2.0"
    ansible.playbook = "ansible/playbooks/arq-lvm-nfs.yml"
    ansible.inventory_path = "ansible/inventory.yml"
    ansible.become = true
    ansible.limit = "arq"  
    ansible.verbose = "v"
  end

# Executar playbook para o servidor de banco de dados
  config.vm.provision "ansible" do |ansible|
    ansible.compatibility_mode = "2.0"
    ansible.playbook = "ansible/playbooks/db.yml"
    ansible.inventory_path = "ansible/inventory.yml"
    ansible.become = true
    ansible.limit = "db"  
    ansible.verbose = "v"
  end

# Executar playbook para o servidor de aplicação
  config.vm.provision "ansible" do |ansible|
    ansible.compatibility_mode = "2.0"
    ansible.playbook = "ansible/playbooks/app.yml"
    ansible.inventory_path = "ansible/inventory.yml"
    ansible.become = true
    ansible.limit = "app" 
    ansible.verbose = "v"
  end

# Executar playbook para o host cliente
  config.vm.provision "ansible" do |ansible|
    ansible.compatibility_mode = "2.0"
    ansible.playbook = "ansible/playbooks/cli.yml"
    ansible.inventory_path = "ansible/inventory.yml"
    ansible.become = true
    ansible.limit = "cli" 
    ansible.verbose = "v"
  end
end
