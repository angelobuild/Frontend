#!/bin/bash

echo

Função para exibir mensagens de conformidade ou atenção

function exibir_mensagem() { numero_item=$1 nome_item=$2 conformidade=$3 echo "$conformidade: $numero_item - $nome_item" }

Função para verificar configurações específicas

function verificar_configuracao() { configuracao=$1 if eval $configuracao; then exibir_mensagem "$numero_item" "$nome_item" "CONFORME" else exibir_mensagem "$numero_item" "$nome_item" "ATENÇÃO" fi }

Função para verificar configurações persistentes

function verificar_persistencia() { parametro=$1 valor_esperado=$2 caminhos=( "/etc/sysctl.conf" "/etc/sysctl.d/.conf" "/usr/lib/sysctl.d/.conf" "/lib/sysctl.d/*.conf" )

encontrado=""

for caminho in "${caminhos[@]}"; do
    if grep -Pqs "^\s*$parametro\s*=\s*$valor_esperado" $caminho 2>/dev/null; then
        encontrado="$caminho"
        break
    fi
done

if [ -n "$encontrado" ]; then
    echo "Configurado de forma persistente em: $encontrado"
else
    echo "ATENÇÃO: Não encontrado em arquivos persistentes. Pode se perder após reboot."
fi

}

echo

Item 1.4.1

numero_item="1.4.1" nome_item="Garantir que o ASLR (Address Space Layout Randomization) esteja habilitado" verificar_configuracao 'sysctl kernel.randomize_va_space | grep -q "kernel.randomize_va_space = 2"'

Verificação persistente

verificar_persistencia "kernel.randomize_va_space" "2"

echo

Item 1.4.2

numero_item="1.4.2" nome_item="Garantir que o ptrace_scope esteja restrito" verificar_configuracao 'sysctl kernel.yama.ptrace_scope | grep -q "kernel.yama.ptrace_scope = 1"'

Verificação persistente

verificar_persistencia "kernel.yama.ptrace_scope" "1"

echo

Item 1.4.3

numero_item="1.4.3" nome_item="Garantir que o core dump backtraces esteja desabilitado" verificar_configuracao 'grep -Pq "^\sProcessSizeMax\s=\s*0" /etc/systemd/coredump.conf'

echo

Item 1.4.4

numero_item="1.4.4" nome_item="Garantir que o core dump storage esteja desabilitado" verificar_configuracao 'grep -Pq "^\sStorage\s=\s*none" /etc/systemd/coredump.conf'

echo

Finalização do script

echo "##### Verificação concluída! #####" echo

