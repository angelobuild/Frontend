#!/bin/bash

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
    numero_item=$2
    nome_item=$3
    if eval "$comando"; then
        exibir_mensagem "$numero_item" "$nome_item" "CONFORME"
    else
        exibir_mensagem "$numero_item" "$nome_item" "ATENÇÃO"
    fi
}

echo

# Item 1.5.1
numero_item="1.5.1"
nome_item="Garantir que o armazenamento de core dumps esteja desabilitado"
verificar_configuracao "grep -iE '^\s*Storage\s*=\s*none' /etc/systemd/coredump.conf" "$numero_item" "$nome_item"

echo

# Item 1.5.2
numero_item="1.5.2"
nome_item="Garantir que o Core Dump Backtrace esteja desabilitado"
verificar_configuracao "grep -Pi '^\s*ProcessSizeMax\s*=\s*0\b' /etc/systemd/coredump.conf" "$numero_item" "$nome_item"

echo

# Item 1.5.3
numero_item="1.5.3"
nome_item="Garantir que o ASLR (Address Space Layout Randomization) esteja habilitado"
verificar_configuracao "sysctl kernel.randomize_va_space | grep -q 'kernel.randomize_va_space = 2'" "$numero_item" "$nome_item"

echo
echo "##### Verificação concluída! #####"
