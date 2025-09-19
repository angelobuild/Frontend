#!/bin/bash

# Função para exibir mensagens de conformidade ou atenção
function exibir_mensagem() {
    numero_item=$1
    nome_item=$2
    conformidade=$3
    echo "$conformidade: $numero_item - $nome_item"
}

echo

######################
# Início da verificação
######################

# 1.2.1
numero_item="1.2.1"
nome_item="Garantir que as chaves GPG estejam configuradas"
if grep -qri "gpgkey" /etc/yum.repos.d/ /etc/dnf/dnf.conf; then
    exibir_mensagem "$numero_item" "$nome_item" "CONFORME"
else
    exibir_mensagem "$numero_item" "$nome_item" "ATENÇÃO"
fi

# 1.2.2
numero_item="1.2.2"
nome_item="Garantir que a verificação de GPG esteja globalmente ativada"
if grep -Eq '^\s*gpgcheck\s*=\s*1' /etc/dnf/dnf.conf && \
   ! grep -P '\bgpgcheck\s*=\s*0\b' /etc/yum.repos.d/* &>/dev/null; then
    exibir_mensagem "$numero_item" "$nome_item" "CONFORME"
else
    exibir_mensagem "$numero_item" "$nome_item" "ATENÇÃO"
fi

# 1.2.3
numero_item="1.2.3"
nome_item="Garantir que os repositórios do gerenciador de pacotes estejam corretamente configurados"
if [ -f /etc/yum.repos.d/redhat.repo ]; then
    exibir_mensagem "$numero_item" "$nome_item" "CONFORME"
else
    exibir_mensagem "$numero_item" "$nome_item" "ATENÇÃO"
fi

# 1.2.4
numero_item="1.2.4"
nome_item="Garantir que o repo_gpgcheck esteja globalmente ativado"
if grep -q "^repo_gpgcheck=1" /etc/dnf/dnf.conf && \
   ! grep -r "repo_gpgcheck=0" /etc/yum.repos.d/ &>/dev/null; then
    exibir_mensagem "$numero_item" "$nome_item" "CONFORME"
else
    exibir_mensagem "$numero_item" "$nome_item" "ATENÇÃO"
fi

echo
echo "##### Verificação concluída! #####"
