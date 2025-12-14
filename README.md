# Projeto devOps - Infraestrutura como código com Vagrant e Ansible

## Informações do projeto
**Disciplina**: Administração de Sistemas Abertos  
**Curso:** Redes de Computadores
**Professor**: Leonidas Lima  
**Período**: 2025.2  
**Instituto Federal da Paraíba - Campus João Pessoa**

## Integrantes da equipe
- **Ana Julita Oliveira da Silva** - **20241380031**
- **Anderson Gabriel Souza do Nascimento** - **20241380008**
  
## Visão geral
Este projeto implementa uma infraestrutura virtual completa utilizando Vagrant e Ansible, com quatro servidores especializados que se comunicam em uma rede privada. A solução automatiza desde a criação das máquinas virtuais até a configuração avançada de serviços de rede e armazenamento, demonstrando os princípios de Infraestrutura como Código.

## Arquitetura do sistema
A infraestrutura consiste em quatro máquinas virtuais interconectadas:

Servidor de Arquivos (arq): Configurado com IP fixo 192.168.56.131, fornece serviços de DHCP, DNS, armazenamento LVM e compartilhamento NFS. Possui três discos adicionais de 10GB cada para o sistema de armazenamento.

Servidor de Banco de Dados (db): Obtém IP via DHCP com reserva estática (192.168.56.108) e executa o MariaDB. Monta automaticamente o compartilhamento NFS do servidor de arquivos.

Servidor de Aplicação (app): Também com IP reservado via DHCP (192.168.56.138), executa o Apache com uma página web personalizada. Acessa o compartilhamento NFS para armazenamento.

Cliente (cli): Configurado com 1024MB de RAM, possui Firefox e suporte a interface gráfica via X11. Serve como estação de trabalho para acesso aos demais servidores.

Todos os servidores utilizam Debian 12 (bookworm) como sistema operacional base.

## Configuração automatizada
O provisionamento ocorre em duas etapas sequenciais. Primeiro, o Vagrant cria as máquinas virtuais com as especificações de hardware e rede. Em seguida, o Ansible executa playbooks especializados para cada servidor.

As configurações comuns aplicadas a todas as máquinas incluem: atualização do sistema, configuração de timezone para America/Recife, sincronização de horário com servidores NTP brasileiros, criação de usuários com chaves SSH, e hardening do serviço SSH com autenticação apenas por chaves, bloqueio de acesso root e restrição por grupos de usuários.

## Serviços de rede
O servidor de arquivos implementa um servidor DHCP autoritativo que gerencia a faixa de IPs 192.168.56.50 a 100, com reservas estáticas baseadas em endereços MAC para os servidores db e app. O serviço DNS configurado no mesmo servidor resolve nomes internos no domínio ana.anderson.devops e encaminha requisições externas para os servidores 1.1.1.1 e 8.8.8.8.

## Sistema de armazenamento
Três discos de 10GB são combinados através de LVM para criar um volume único de 15GB formatado com ext4 e montado em /dados. Este diretório contém um compartilhamento NFS (/dados/nfs) acessível por toda a rede interna, configurado com medidas de segurança que incluem um usuário dedicado sem acesso a shell.

## Serviços especializados
O servidor de banco de dados executa MariaDB, enquanto o servidor de aplicação roda Apache com uma página HTML personalizada que apresenta informações do projeto. Ambos montam automaticamente o compartilhamento NFS usando autofs. O cliente, por sua vez, possui Firefox e suporte a X11 forwarding para permitir a execução remota de aplicações gráficas.

## Segurança implementada
O projeto incorpora várias práticas de segurança: autenticação SSH exclusivamente por chaves públicas, bloqueio de acesso root via SSH, restrição de acesso por grupos de usuários, criação de banner de aviso legal, e configuração de um usuário NFS sem shell para limitar privilégios. Usuários do grupo ifpb têm permissão para usar sudo sem senha.

## Validação da solução
Para verificar o funcionamento correto da infraestrutura, é possível testar a resolução de nomes internos (ping db.ana.anderson.devops), verificar o acesso SSH seguro com chaves, confirmar o compartilhamento NFS montado em /var/nfs nos servidores, e acessar a página web no servidor de aplicação. O DHCP pode ser validado observando os IPs atribuídos automaticamente aos servidores conforme suas reservas por MAC.

## Execução do projeto
A infraestrutura completa é provisionada com um único comando: vagrant up. Este comando baixa a imagem do sistema, cria as máquinas virtuais, configura a rede e executa todas as automações do Ansible. O processo leva aproximadamente 15-20 minutos na primeira execução devido ao download da imagem base.

Esta documentação representa um projeto de infraestrutura como código reproduzível, que demonstra competências em provisionamento automatizado, configuração de serviços de rede e implementação de medidas de segurança em ambiente Linux.
