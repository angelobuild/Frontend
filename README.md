#!/bin/bash

# Função para exibir mensagens de conformidade ou atenção
function exibir_mensagem() {
    numero_item=$1
    nome_item=$2
    conformidade=$3
    echo "$conformidade: $numero_item - $nome_item"
}

echo

# Item 1.4.1
numero_item="1.4.1"
nome_item="Garantir que a senha do bootloader esteja configurada"
if grep -Eq '^GRUB2_PASSWORD=grub.pbkdf2.sha512' /boot/grub2/user.cfg 2>/dev/null; then
    exibir_mensagem "$numero_item" "$nome_item" "CONFORME"
else
    exibir_mensagem "$numero_item" "$nome_item" "ATENÇÃO"
fi

echo

# Item 1.4.2 - Permissões dos arquivos do bootloader

numero_item="1.4.2"
nome_item="Garantir permissões seguras nos arquivos do GRUB2"

arquivo1="/boot/grub2/grub.cfg"
arquivo2="/boot/grub2/grubenv"
arquivo3="/boot/grub2/user.cfg"

# Função auxiliar para verificar permissões e propriedade
verificar_arquivo_boot() {
    arquivo=$1
    permissao_esperada=$2
    dono_esperado="root"
    grupo_esperado="root"

    if [ -f "$arquivo" ]; then
        permissao_atual=$(stat -c "%a" "$arquivo")
        dono_atual=$(stat -c "%U" "$arquivo")
        grupo_atual=$(stat -c "%G" "$arquivo")

        if [ "$permissao_atual" -le "$permissao_esperada" ] && \
           [ "$dono_atual" == "$dono_esperado" ] && \
           [ "$grupo_atual" == "$grupo_esperado" ]; then
            exibir_mensagem "$numero_item" "$nome_item ($arquivo)" "CONFORME"
        else
            exibir_mensagem "$numero_item" "$nome_item ($arquivo)" "ATENÇÃO"
        fi
    else
        exibir_mensagem "$numero_item" "$nome_item ($arquivo)" "ATENÇÃO (Arquivo não encontrado)"
    fi
}

verificar_arquivo_boot "$arquivo1" 700
verificar_arquivo_boot "$arquivo2" 600
verificar_arquivo_boot "$arquivo3" 600

echo
echo "##### Verificação concluída! #####"
