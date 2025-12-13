# Projeto devOps - Vagrant e Ansible

## Informações do projeto
**Disciplina**: Administração de Sistemas Abertos  
**Curso:** Redes de Computadores
**Professor**: Leonidas Lima  
**Período**: 2025.2  
**Instituto Federal da Paraíba - Campus João Pessoa**

## Integrantes da equipe
- **Ana Julita Oliveira da Silva**
- **Anderson Gabriel Souza do Nascimento**
  
## Descrição do projeto
O projeto tem como finalidade desenvolver competências práticas em DevOps e Infraestrutura como Código (IaC)onde será utilizado o Vagrant para provisionar máquinas virtuais e Ansible para automatizar a configuração do sistema operacional e serviços essenciais. O foco é simular um ambiente corporativo com servidores de arquivos, banco de dados, aplicação e um cliente, todos configurados automaticamente.

## Escopo do projeto
Provisionamento da Infraestrutura com Vagrant
O Vagrantfile será responsável por criar quatro máquinas virtuais no VirtualBox:

Configurações comuns a todas as VMs:

-Provider: VirtualBox

-Box: debian/bookworm64

-Memória RAM: 512 MB (exceto cliente, que terá 1024 MB);

-Clones com linked_clone;

-Desativar geração de chaves SSH;

-Desabilitar DHCP do VirtualBox via gatilho;

-Guest additions desativado;
