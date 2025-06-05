# 2.2.22
numero_item="2.2.22"
nome_item="Garantir que apenas serviços autorizados estejam escutando em interfaces de rede"

saida_servicos="/tmp/servicos_escutando.txt"
ss -plntu > "$saida_servicos"

if [ -s "$saida_servicos" ]; then
    exibir_mensagem "$numero_item" "$nome_item" "ATENÇÃO (Verificação manual necessária - veja $saida_servicos)"
else
    exibir_mensagem "$numero_item" "$nome_item" "CONFORME"
fi
