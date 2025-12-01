# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  # Configuracoes globais das VMs:
  config.vm.box = 'debian/bookworm64'
  config.ssh.insert_key = false # Desativar geracao de chaves ssh
  if Vagrant.has_plugin?("vagrant-vbguest") # Verificar se o plugin esta instalado
    config.vbguest.auto_update = false # Desativar as atualizacoes automaticas
  end
  
  # Desabilitar servidor DHCP do virtualbox antes de iniciar as VMs:
  config.trigger.before :"Vagrant::Action::Builtin::WaitForCommunicator", type: :action do |t|
    t.warn = "Interrompe o servidor dhcp do virtualbox"
    t.run = {inline: "vboxmanage dhcpserver stop --interface vboxnet0"}
  end
  
  # Configuracoes do provider para todas as VMs:
  config.vm.provider 'virtualbox' do |v|
    v.gui = false # Iniciar as VMs sem interface grafica
    v.memory = '512' # Memoria RAM
    v.linked_clone = true # Criar clones vinculados
    v.check_guest_additions = false # Desativar verificacao das guest additions
  end

# Servidor de arquivos [arq]
  config.vm.define "arq" do |arq|
    arq.vm.hostname = "arq.ana.anderson.devops"
    arq.vm.network :private_network, ip: "192.168.56.131" # Atribuir IP fixo na rede privada
    (0..2).each do |x| # Criar discos
       arq.vm.disk :disk, size: "10GB", name: "disk-#{x}"
    end
    arq.vm.provision "ansible" do |ansible| # Provisionamento Ansible
      ansible.compatibility_mode = "2.0"
      ansible.playbook = "playbooks/conf-arq.yml" # Playbook especifico para o servidor de arquivos
    end
  end

# Servidor de banco de dados [db]
  config.vm.define "db" do |db|
    db.vm.hostname = "db.ana.anderson.devops"
    db.vm.network :private_network, mac: "0800273A0001", type: "dhcp" 
    db.vm.provision "ansible" do |ansible| # Provisionamento Ansible
      ansible.compatibility_mode = "2.0"
      ansible.playbook = "playbooks/conf-db.yml" # Playbook especifico para o banco de dados
    end
  end

# Servidor de aplicacao [app]
  config.vm.define "app" do |app|
    app.vm.hostname = "app.ana.anderson.devops"
    app.vm.network :private_network, mac: "0800273A0002", type: "dhcp"
    app.vm.provision "ansible" do |ansible| # Provisionamento Ansible
      ansible.compatibility_mode = "2.0"
      ansible.playbook = "playbooks/conf-app.yml" # Playbook especifico para o servidor de aplicacao
    end
  end

# Host cliente [cli]
  config.vm.define "cli" do |cli|
    cli.vm.hostname = "cli.ana.anderson.devops"
    cli.vm.network :private_network, type: "dhcp"
    
    cli.vm.provider 'virtualbox' do |vb|
      vb.memory = 1024 # Sobrescrever a configuracao global
    end

    cli.vm.provision "ansible" do |ansible| # Provisionamento Ansible
      ansible.compatibility_mode = "2.0"
      ansible.playbook = "playbooks/conf-cli.yml" # Playbook especifico para o host cliente
    end
  end
end
