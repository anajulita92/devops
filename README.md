# Projeto devOps - Vagrant e Ansible

## Informações do projeto
**Disciplina**: Administração de Sistemas Abertos  
**Curso:** Redes de Computadores
**Professor**: Leonidas Lima  
**Período**: 2025.2  
**Instituto Federal da Paraíba - Campus João Pessoa**

## Integrantes da equipe
- **Ana Julita Oliveira da Silva** **20241380031**
- **Anderson Gabriel Souza do Nascimento** **20241380008**
  
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

## Máquinas específicas:

## Servidor de Arquivos (arq):

- 3 discos adicionais de 10 GB cada , -IP fixo: 192.168.56.131 (31 = matrícula do primeiro integrante)

- Hostname: arq.nome1.nome2.devops


## Servidor de Banco de Dados (db):

- IP via DHCP (reservado por MAC) , -Hostname: db.ana.anderson.devops

## Servidor de Aplicação (app):

- IP via DHCP (reservado por MAC)  -Hostname: app.ana.anderson.devops

## Cliente (cli):

- RAM: 1024 MB , -IP via DHCP , -Hostname: cli.ana.anderson.devops


## Configuração Automática com Ansible
Os playbooks do Ansible serão responsáveis por configurar cada máquina.

Configurações comuns a todas as máquinas:
- Atualização do sistema (apt update && apt upgrade)

- Instalação e configuração do NTP (chrony) → servidor pool.ntp.br

- Ajuste da timezone → America/Recife

- Criação do grupo ifpb

- Criação dos usuários nome1 e nome2 (primeiros nomes da equipe)

Configuração do SSH:

- Apenas autenticação por chave pública

- Bloqueio de login root

- Acesso restrito aos grupos vagrant e ifpb

- Mensagem de login: "Acesso apenas para pessoas com autorização expressa!!!"

- Instalação do cliente NFS

- Permitir que usuários do grupo ifpb usem sudo

Servidor de Arquivos (arq):
- DHCP autoritativo

- Domínio: ana.anderson.devops

- DNS: 192.168.56.131

- Lease-time padrão: 180s / máximo: 3600s

- Escopo: 192.168.56.0/24 (faixa 50–100)

- Gateway: 192.168.56.1

- IPs fixos para db e app conforme matrícula

DNS autoritativo:

- Aceitar requisições da rede interna

- Encaminhar para 1.1.1.1 e 8.8.8.8

- Criar zonas direta e reversa para ana.anderson.devops

LVM:

- Criar VG dados com os 3 discos

- Criar LV ifpb com 15 GB

- Formatar em ext4 e montar em /dados

NFS:

- Compartilhar /dados/nfs para a rede interna

- Criar usuário nfs-ifpb sem shell

- Permitir escrita apenas para nfs-ifpb

- Mapear usuários remotos para nfs-ifpb

- Forçar flush imediato no disco


Servidor de Banco de Dados (db):
- Instalar MariaDB Server

- Configurar autofs para montar /dados/nfs em /var/nfs

Servidor de Aplicação (app):
- Instalar Apache2

- Configurar autofs para montar /dados/nfs em /var/nfs

Cliente (cli):
- Instalar Firefox ESR e xauth

- Configurar SSH para exportar interface gráfica (X11 forwarding)

- Configurar autofs para montar /dados/nfs em /var/nfs
