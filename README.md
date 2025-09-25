Script: Verificação dos itens CIS 1.6.1.1 a 1.6.1.5 (SELinux) — Padrão Ângelo

#!/bin/bash

echo

# Função para exibir mensagens de conformidade ou atenção
function exibir_mensagem() {
    numero_item=$1
    nome_item=$2
    conformidade=$3
    echo "$conformidade: $numero_item - $nome_item"
}

# Função para verificar configurações específicas
function verificar_configuracao() {
    comando=$1
    if eval "$comando"; then
        exibir_mensagem "$numero_item" "$nome_item" "CONFORME"
    else
        exibir_mensagem "$numero_item" "$nome_item" "ATENÇÃO"
    fi
}

echo

# Item 1.6.1.1 - Verificar se o SELinux não está desabilitado no GRUB
numero_item="1.6.1.1"
nome_item="Garantir que o SELinux não esteja desabilitado no bootloader"
verificar_configuracao '! grep -E "^\s*linux" /boot/grub2/grub.cfg | grep -q "selinux=0"'

echo

# Item 1.6.1.2 - Verificar se o SELinux está em modo enforcing
numero_item="1.6.1.2"
nome_item="Garantir que o estado do SELinux esteja como enforcing"
verificar_configuracao '[ "$(getenforce)" = "Enforcing" ]'

echo

# Item 1.6.1.3 - Verificar se a política do SELinux está definida como targeted
numero_item="1.6.1.3"
nome_item="Garantir que a política do SELinux esteja definida como targeted"
verificar_configuracao 'grep -Eq "^\s*SELINUXTYPE\s*=\s*targeted" /etc/selinux/config'

echo

# Item 1.6.1.4 - Verificar se nenhum arquivo está rotulado incorretamente
numero_item="1.6.1.4"
nome_item="Garantir que não existam arquivos com rótulos incorretos no SELinux"
verificar_configuracao '[ "$(fixfiles onboot | grep -c "Relabeling")" -eq 0 ]'

echo

# Item 1.6.1.5 - Verificar se os comandos auditctl e seinfo estão disponíveis
numero_item="1.6.1.5"
nome_item="Garantir que ferramentas SELinux estejam instaladas (auditctl, seinfo)"
verificar_configuracao 'command -v auditctl &>/dev/null && command -v seinfo &>/dev/null'

echo
echo "##### Verificação concluída! #####"


Seção 1.7 - Banners do CIS Red Hat Enterprise Linux 9 Benchmark v1.0.0

#!/bin/bash

echo

# Função para exibir mensagens de conformidade ou atenção
function exibir_mensagem() {
    numero_item=$1
    nome_item=$2
    conformidade=$3
    echo "$conformidade: $numero_item - $nome_item"
}

# Função para verificar configurações específicas
function verificar_configuracao() {
    configuracao=$1
    if eval $configuracao; then
        exibir_mensagem "$numero_item" "$nome_item" "CONFORME"
    else
        exibir_mensagem "$numero_item" "$nome_item" "ATENÇÃO"
    fi
}

echo

# Item 1.7.1
numero_item="1.7.1"
nome_item="Garantir que o arquivo /etc/motd esteja configurado corretamente"
verificar_configuracao '[ -f /etc/motd ] && ! grep -Eq "(\\v|\\r|\\m|\\s)" /etc/motd'

echo

# Item 1.7.2
numero_item="1.7.2"
nome_item="Garantir que o arquivo /etc/issue esteja configurado corretamente"
verificar_configuracao '[ -f /etc/issue ] && ! grep -Eq "(\\v|\\r|\\m|\\s)" /etc/issue'

echo

# Item 1.7.3
numero_item="1.7.3"
nome_item="Garantir que o arquivo /etc/issue.net esteja configurado corretamente"
verificar_configuracao '[ -f /etc/issue.net ] && ! grep -Eq "(\\v|\\r|\\m|\\s)" /etc/issue.net'

echo

# Item 1.7.4
numero_item="1.7.4"
nome_item="Garantir que a permissão do arquivo /etc/motd seja 644"
verificar_configuracao '[ -f /etc/motd ] && stat -c "%a" /etc/motd | grep -q "^644$"'

echo

# Item 1.7.5
numero_item="1.7.5"
nome_item="Garantir que a propriedade do arquivo /etc/motd seja root:root"
verificar_configuracao '[ -f /etc/motd ] && [ "$(stat -c "%U:%G" /etc/motd)" = "root:root" ]'

echo

# Item 1.7.6
numero_item="1.7.6"
nome_item="Garantir que a permissão do arquivo /etc/issue seja 644"
verificar_configuracao '[ -f /etc/issue ] && stat -c "%a" /etc/issue | grep -q "^644$"'

echo

# Item 1.7.7
numero_item="1.7.7"
nome_item="Garantir que a propriedade do arquivo /etc/issue seja root:root"
verificar_configuracao '[ -f /etc/issue ] && [ "$(stat -c "%U:%G" /etc/issue)" = "root:root" ]'

echo

# Item 1.7.8
numero_item="1.7.8"
nome_item="Garantir que a permissão do arquivo /etc/issue.net seja 644"
verificar_configuracao '[ -f /etc/issue.net ] && stat -c "%a" /etc/issue.net | grep -q "^644$"'

echo

# Item 1.7.9
numero_item="1.7.9"
nome_item="Garantir que a propriedade do arquivo /etc/issue.net seja root:root"
verificar_configuracao '[ -f /etc/issue.net ] && [ "$(stat -c "%U:%G" /etc/issue.net)" = "root:root" ]'

echo
echo "##### Verificação concluída! #####"

