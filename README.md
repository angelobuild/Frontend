#!/bin/bash

# Função para exibir mensagens de conformidade ou atenção
function exibir_mensagem() {
    numero_item=$1
    nome_item=$2
    conformidade=$3
    echo "$conformidade: $numero_item - $nome_item"
}

# Função para verificar se um pacote está instalado
function verificar_pacote() {
    pacote=$1
    if rpm -q $pacote &> /dev/null; then
        exibir_mensagem "$numero_item" "$nome_item" "ATENÇÃO"
    else
        exibir_mensagem "$numero_item" "$nome_item" "CONFORME"
    fi
}

# Função para verificar se um serviço está desabilitado e parado
function verificar_servico() {
    servico=$1
    if systemctl is-enabled $servico &>/dev/null || systemctl is-active $servico &>/dev/null; then
        exibir_mensagem "$numero_item" "$nome_item" "ATENÇÃO"
    else
        exibir_mensagem "$numero_item" "$nome_item" "CONFORME"
    fi
}

echo

########## INÍCIO DOS ITENS 2.2.1 A 2.2.22 ##########

# 2.2.1
numero_item="2.2.1"
nome_item="Garantir que o serviço autofs não esteja instalado nem habilitado"
verificar_pacote "autofs"
verificar_servico "autofs.service"

# 2.2.2
numero_item="2.2.2"
nome_item="Garantir que o serviço Avahi não esteja instalado nem habilitado"
verificar_pacote "avahi"
verificar_servico "avahi-daemon.service"
verificar_servico "avahi-daemon.socket"

# 2.2.3
numero_item="2.2.3"
nome_item="Garantir que o servidor DHCP não esteja instalado nem habilitado"
verificar_pacote "dhcp"
verificar_servico "dhcpd.service"
verificar_servico "dhcpd6.service"

# 2.2.4
numero_item="2.2.4"
nome_item="Garantir que o servidor DNS (bind) não esteja instalado nem habilitado"
verificar_pacote "bind"
verificar_servico "named.service"

# 2.2.5
numero_item="2.2.5"
nome_item="Garantir que o serviço Dnsmasq não esteja instalado nem habilitado"
verificar_pacote "dnsmasq"
verificar_servico "dnsmasq.service"

# 2.2.6
numero_item="2.2.6"
nome_item="Garantir que o servidor Samba não esteja instalado nem habilitado"
verificar_pacote "samba"
verificar_servico "smb.service"

# 2.2.7
numero_item="2.2.7"
nome_item="Garantir que o servidor FTP não esteja instalado nem habilitado"
verificar_pacote "vsftpd"
verificar_servico "vsftpd.service"

# 2.2.8
numero_item="2.2.8"
nome_item="Garantir que servidores de e-mail IMAP e POP3 não estejam instalados nem habilitados"
verificar_pacote "dovecot"
verificar_pacote "cyrus-imapd"
verificar_servico "dovecot.service"
verificar_servico "dovecot.socket"
verificar_servico "cyrus-imapd.service"

# 2.2.9
numero_item="2.2.9"
nome_item="Garantir que o servidor NFS não esteja instalado nem habilitado"
verificar_pacote "nfs-utils"
verificar_servico "nfs-server.service"

# 2.2.10
numero_item="2.2.10"
nome_item="Garantir que o servidor NIS não esteja instalado nem habilitado"
verificar_pacote "ypserv"
verificar_servico "ypserv.service"

# 2.2.11
numero_item="2.2.11"
nome_item="Garantir que o servidor de impressão (CUPS) não esteja instalado nem habilitado"
verificar_pacote "cups"
verificar_servico "cups.service"
verificar_servico "cups.socket"

# 2.2.12
numero_item="2.2.12"
nome_item="Garantir que o serviço rpcbind não esteja instalado nem habilitado"
verificar_pacote "rpcbind"
verificar_servico "rpcbind.service"
verificar_servico "rpcbind.socket"

# 2.2.13
numero_item="2.2.13"
nome_item="Garantir que o serviço rsync não esteja instalado nem habilitado"
verificar_pacote "rsync"
verificar_servico "rsyncd.service"
verificar_servico "rsyncd.socket"

# 2.2.14
numero_item="2.2.14"
nome_item="Garantir que o servidor SNMP não esteja instalado nem habilitado"
verificar_pacote "net-snmp"
verificar_servico "snmpd.service"

# 2.2.15
numero_item="2.2.15"
nome_item="Garantir que o servidor Telnet não esteja instalado nem habilitado"
verificar_pacote "telnet-server"
verificar_servico "telnet.socket"

# 2.2.16
numero_item="2.2.16"
nome_item="Garantir que o servidor TFTP não esteja instalado nem habilitado"
verificar_pacote "tftp-server"
verificar_servico "tftp.service"
verificar_servico "tftp.socket"

# 2.2.17
numero_item="2.2.17"
nome_item="Garantir que o servidor de proxy web (Squid) não esteja instalado nem habilitado"
verificar_pacote "squid"
verificar_servico "squid.service"

# 2.2.18
numero_item="2.2.18"
nome_item="Garantir que o servidor web não esteja instalado nem habilitado"
verificar_pacote "httpd"
verificar_pacote "nginx"
verificar_servico "httpd.service"
verificar_servico "httpd.socket"
verificar_servico "nginx.service"

# 2.2.19
numero_item="2.2.19"
nome_item="Garantir que o serviço xinetd não esteja instalado nem habilitado"
verificar_pacote "xinetd"
verificar_servico "xinetd.service"

# 2.2.20
numero_item="2.2.20"
nome_item="Garantir que o X Windows não esteja instalado"
verificar_pacote "xorg-x11-server-common"

# 2.2.21
numero_item="2.2.21"
nome_item="Garantir que o agente de transferência de e-mail (MTA) esteja configurado para modo local"
ss -plntu | grep -P -- ':25\\b|:465\\b|:587\\b' | grep -Pv -- '\\s+(127\\.0\\.0\\.1|\?::1\?):(25|465|587)\\b' &> /dev/null
if [ $? -eq 0 ]; then
    exibir_mensagem "$numero_item" "$nome_item" "ATENÇÃO"
else
    exibir_mensagem "$numero_item" "$nome_item" "CONFORME"
fi

# 2.2.22
numero_item="2.2.22"
nome_item="Garantir que apenas serviços autorizados estejam escutando em interfaces de rede"
ss -plntu &> /dev/null
if [ $? -eq 0 ]; then
    exibir_mensagem "$numero_item" "$nome_item" "ATENÇÃO (Verificação manual necessária)"
else
    exibir_mensagem "$numero_item" "$nome_item" "CONFORME"
fi

echo
echo "##### Verificação concluída! #####"
