#!/bin/bash

# Função para exibir mensagens padronizadas
function exibir_mensagem() {
    numero_item=$1
    nome_item=$2
    conformidade=$3
    echo "$conformidade: $numero_item - $nome_item"
}

# Função para verificar se arquivos de log em /var/log estão com permissões, dono e grupo corretos
function verificar_permissoes_logs() {
    numero_item=$1
    nome_item=$2

    arquivos_incorretos=$(find -L /var/log -type f -perm /0137 -o ! -user root -o ! -group root -print)

    if [ -z "$arquivos_incorretos" ]; then
        exibir_mensagem "$numero_item" "$nome_item" "CONFORME"
    else
        exibir_mensagem "$numero_item" "$nome_item" "ATENÇÃO"
        echo "Arquivos com problemas de permissões ou propriedade:"
        echo "$arquivos_incorretos"
    fi
}

########## INÍCIO DO ITEM 5.1.4 ##########

numero_item="5.1.4"
nome_item="Garantir que todos os arquivos de log tenham permissões apropriadas configuradas"
verificar_permissoes_logs "$numero_item" "$nome_item"

echo
echo "##### Verificação concluída! #####"
