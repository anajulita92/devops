# Projeto devOps - Vagrant e Ansible

## Informações do projeto
**Disciplina**: Administração de Sistemas Abertos  
**Curso:** Redes de Computadores
**Professor**: Leonidas Lima  
**Período**: 2025.2  
**Instituto Federal da Paraíba - Campus João Pessoa**

## Integrantes da equipe
- **Ana Julita Oliveira da Silva** - **20241380031**
- **Anderson Gabriel Souza do Nascimento** - **20241380008**
  
## Descrição do projeto
O projeto tem como finalidade desenvolver competências práticas em DevOps e Infraestrutura como Código (IaC)onde será utilizado o Vagrant para provisionar máquinas virtuais e Ansible para automatizar a configuração do sistema operacional e serviços essenciais. O foco é simular um ambiente corporativo com servidores de arquivos, banco de dados, aplicação e um cliente, todos configurados automaticamente.

## Escopo do projeto
Provisionamento da Infraestrutura com Vagrant
O Vagrantfile será responsável por criar quatro máquinas virtuais no VirtualBox:

Configurações comuns a todas as VMs:

- Provider: VirtualBox

- Box: debian/bookworm64

- Memória RAM: 512 MB (exceto cliente, que terá 1024 MB);

- Clones com linked_clone;

- Desativar geração de chaves SSH;

- Desabilitar DHCP do VirtualBox via gatilho;

- Guest additions desativado;

# Máquinas específicas:

- Servidor de Arquivos (arq):

3 discos adicionais de 10 GB cada , -IP fixo: 192.168.56.131 (31 = matrícula do primeiro integrante)

Hostname: arq.nome1.nome2.devops


- Servidor de Banco de Dados (db):

IP via DHCP (reservado por MAC) , -Hostname: db.ana.anderson.devops

- Servidor de Aplicação (app):

IP via DHCP (reservado por MAC)  -Hostname: app.ana.anderson.devops

- Cliente (cli):

RAM: 1024 MB , -IP via DHCP , -Hostname: cli.ana.anderson.devops


## Configuração Automática com Ansible
Os playbooks do Ansible serão responsáveis por configurar cada máquina.

- Common.yml

I. Preparação do Sistema
Aplicar em todos os hosts (hosts: all) com privilégios de root (become: yes).

Atualizar Cache APT e garantir a validade por 1 hora.

Executar Upgrade Completo do sistema (upgrade: dist).



II.Sincronização e Fuso Horário
Instalar o pacote chrony (para sincronização de tempo).

Configurar chrony para usar o servidor brasileiro pool.ntp.br e notificar a reinicialização do serviço.

Configurar o Fuso Horário do sistema para America/Recife.



III.Gerenciamento de Usuários e Sudo
Criar o grupo ifpb.

Criar usuários ana e anderson:

Ambos adicionados aos grupos ifpb e vagrant.

Com shell /bin/bash e criação de diretório home.

Configurar Sudo: Criar arquivo /etc/sudoers.d/ifpb para permitir que o grupo ifpb execute comandos como root sem senha (NOPASSWD: ALL).



IV. Configuração de Segurança SSH
Preparar diretórios SSH: Criar /home/ana/.ssh e /home/anderson/.ssh com permissões restritas (0700).

Gerar Chaves SSH (2048 bits) para os usuários ana e anderson (armazenadas em .ssh/id_rsa).

Reforçar sshd_config (Notificando restart sshd para cada alteração):

Desativar autenticação por senha (PasswordAuthentication no).

Bloquear acesso direto de root (PermitRootLogin no).

Permitir login apenas para usuários dos grupos vagrant e ifpb (AllowGroups vagrant ifpb).

Configurar Banner SSH: Criar e configurar o arquivo /etc/issue.net para exibir uma mensagem de boas-vindas/alerta no login.



V. Outras Configurações
Instalar cliente NFS (nfs-common) para permitir montagens de compartilhamentos de rede.

VI. Manipuladores (Handlers)
restart chrony: Reinicia o serviço chrony quando a configuração de tempo é alterada.

restart sshd: Reinicia o serviço ssh (SSHD) sempre que as configurações de segurança SSH são alteradas.
