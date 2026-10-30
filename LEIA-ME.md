# Papa Pizza — projeto novo e simples

Implementação criada do zero, independente do repositório e das versões antigas.

## Uso

1. Importe `PapaPizza.shortcutystudio` no Shortcuty Studio. A cópia `.shortcutystudio.json` contém o mesmo projeto, caso prefira essa extensão. O arquivo `.shortcuty` permite inspecionar o código isoladamente.
2. Execute Check e Build, consulte os avisos e use o fluxo Download do Studio para assinar e instalar. Esta entrega não contém um atalho assinado.
3. No iPhone, autorize acesso a Arquivos/iCloud Drive. O destino esperado é iCloud Drive/Shortcuts/PapaPizza, usando a pasta padrão da ação Arquivos. Confirme esse destino no primeiro teste.

O cadastro dos 60 produtos foi incorporado ao código para dispensar uma instalação manual do JSON no aparelho. O arquivo original é entregue intacto. Os nove grupos, nomes e IDs permanecem no cadastro e na cópia dentro de cada conferência; a navegação usa uma lista alfabética única.

Menu: Nova conferência, Continuar conferência, Histórico e resumo, Sair.
Cada produto solicita estoque, comprar e processar. As três quantidades são inteiros não negativos, confirmadas juntas. Zero é válido. Comprar e processar podem coexistir. Antes de cada produto é possível deixar para depois ou pausar. Cancelar uma pergunta interrompe a execução; permanecem salvos os itens anteriormente confirmados.

O histórico oferece as cinco conferências mais recentes ou a lista completa. O resumo exibe a tabela inteira e as listas Não conferidos, Comprar e Processar. Itens não conferidos ficam com quantidades em branco. Uma conferência aberta retoma os itens que faltam; os deixados para depois reaparecem na próxima retomada. Ao terminar a lista é possível pausar ou finalizar, com indicação de quantos faltam. Finalizar pede confirmação e torna a sessão somente para consulta pela interface.

## Persistência

Cada nova conferência recebe nome com data/hora local do aparelho e sufixo aleatório. Antes de criar, o código verifica se o nome existe e interrompe em caso de colisão. Apenas a conferência atual é atualizada.

Formato `papa-pizza-simples-1`: data, estado, cadastro original, ordem alfabética e dicionário `valores`. As chaves usam IDs estáveis, por exemplo `1_estoque`, `1_comprar`, `1_processar`. Os valores são textos numéricos porque a ação oficial `data.set_value` recebe Text; são convertidos explicitamente em Number para o resumo. Ausência das chaves significa não conferido. Cada confirmação substitui as três chaves do mesmo ID, sem acrescentar registros duplicados.

Antes de atualizar o JSON existente, o atalho copia o arquivo anterior para `<nome>.json.bak`. Há somente um backup por conferência, além do JSON principal. As gravações usam ações sequenciais de Arquivos: não há garantia de transação atômica do iCloud nem proteção contra duas execuções simultâneas. Use uma execução por vez e aguarde a sincronização antes de trocar de dispositivo.

Recuperação manual: preserve o JSON com problema; copie o `.json.bak` pelo aplicativo Arquivos e dê à cópia um nome terminado somente em `.json`. Não apague o original até conferir a recuperação. JSON malformado pode encerrar o atalho na conversão; não existe recuperação automática que ignore erros de leitura.

## Escopo deliberadamente reduzido

Não há navegação/estados por grupo, Lembretes, filtros interativos, edição de sessões finalizadas ou migração de históricos antigos. Uma quantidade digitada errada pode ser corrigida antes de confirmar, cancelando e retomando; revisão de um item já salvo não faz parte desta primeira versão. As categorias são preservadas como dados, sem adicionar menus. Não são somadas quantidades de produtos diferentes.

O HTML é mostrado pelo Quick Look, sem criar um arquivo permanente a cada consulta. O menu principal é reaberto executando o atalho novamente.
