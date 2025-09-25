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

# Função para verificar permissão de arquivo
function verificar_permissao_arquivo() {
    arquivo=$1
    permissao_esperada=$2
    numero_item=$3
    nome_item=$4

    if [ ! -f "$arquivo" ]; then
        exibir_mensagem "$numero_item" "$nome_item" "ATENÇÃO (Arquivo não encontrado)"
        return
    fi

    permissao_atual=$(stat -c %a "$arquivo")
    if [ "$permissao_atual" -le "$permissao_esperada" ]; then
        exibir_mensagem "$numero_item" "$nome_item" "CONFORME"
    else
        exibir_mensagem "$numero_item" "$nome_item" "ATENÇÃO"
    fi
}

# Função para verificar propriedade de arquivo
function verificar_propriedade_arquivo() {
    arquivo=$1
    numero_item=$2
    nome_item=$3

    if [ ! -f "$arquivo" ]; then
        exibir_mensagem "$numero_item" "$nome_item" "ATENÇÃO (Arquivo não encontrado)"
        return
    fi

    proprietario=$(stat -c %U "$arquivo")
    grupo=$(stat -c %G "$arquivo")

    if [[ "$proprietario" == "root" && "$grupo" == "root" ]]; then
        exibir_mensagem "$numero_item" "$nome_item" "CONFORME"
    else
        exibir_mensagem "$numero_item" "$nome_item" "ATENÇÃO"
    fi
}

echo

# 1.7.1
numero_item="1.7.1"
nome_item="Garantir que a mensagem do /etc/motd esteja configurada apropriadamente"
verificar_configuracao 'grep -Eq "(\\v|\\r|\\m|\\s)" /etc/motd && echo "ATENÇÃO: /etc/motd contém informações do sistema" || echo "CONFORME: /etc/motd OK"'

# 1.7.2
numero_item="1.7.2"
nome_item="Garantir que a mensagem do /etc/issue esteja configurada apropriadamente"
verificar_configuracao 'grep -Eq "(\\v|\\r|\\m|\\s)" /etc/issue && echo "ATENÇÃO: /etc/issue contém informações do sistema" || echo "CONFORME: /etc/issue OK"'

# 1.7.3
numero_item="1.7.3"
nome_item="Garantir que a mensagem do /etc/issue.net esteja configurada apropriadamente"
verificar_configuracao 'grep -Eq "(\\v|\\r|\\m|\\s)" /etc/issue.net && echo "ATENÇÃO: /etc/issue.net contém informações do sistema" || echo "CONFORME: /etc/issue.net OK"'

# 1.7.4
numero_item="1.7.4"
nome_item="Garantir permissões adequadas no /etc/issue"
verificar_permissao_arquivo "/etc/issue" 644 "$numero_item" "$nome_item"

# 1.7.5
numero_item="1.7.5"
nome_item="Garantir que /etc/issue seja de propriedade de root:root"
verificar_propriedade_arquivo "/etc/issue" "$numero_item" "$nome_item"

# 1.7.6
numero_item="1.7.6"
nome_item="Garantir que /etc/issue.net seja de propriedade de root:root"
verificar_propriedade_arquivo "/etc/issue.net" "$numero_item" "$nome_item"

echo
echo "##### Verificação concluída! #####"
