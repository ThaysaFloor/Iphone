# Papa Pizza — escolha livre de produtos

## Instalação

Importe `PapaPizza.shortcutystudio` no Shortcuty Studio, execute Check e Build e use Download para assinar e instalar. A cópia `.shortcutystudio.json` contém o mesmo projeto. O código isolado está em `PapaPizza.shortcuty`.

No aplicativo Arquivos do iPhone, crie a pasta `PapaPizza` no iCloud Drive, se necessário. O atalho pede que você selecione essa pasta. Use sempre a mesma pasta para salvar, continuar e consultar conferências.

O cadastro original foi incorporado ao código, sem necessidade de copiar o JSON para o aparelho. O arquivo original é entregue intacto. Os 60 produtos, nove categorias, nomes e IDs são preservados nas conferências.

## Conferir somente o que você quiser

1. Escolha Nova conferência ou Continuar conferência.
2. Na lista, escolha diretamente o produto desejado. Você não precisa passar pelos anteriores.
3. Digite estoque, quantidade para comprar e quantidade para processar. Use **0** quando a quantidade for zero.
4. Escolha **Salvar item** para confirmar as três quantidades juntas, ou **Voltar sem salvar** para descartá-las.
5. A lista reaparece atualizada para escolher outro produto.

Sinais da lista:

- **⬜**: ainda não conferido; aparece primeiro.
- **✅**: conferido; aparece depois dos não conferidos.

Cada grupo fica em ordem alfabética. Os produtos não desaparecem. Escolher um produto ✅ permite informar novamente as três quantidades; salvar substitui os valores daquele ID, sem duplicá-lo. Uma revisão exige redigitar as três quantidades; o formulário não preenche os valores antigos automaticamente.

**OK sem digitar** em qualquer quantidade volta à lista e preserva os dados anteriores daquele produto. Zero digitado é válido e marca como conferido após salvar. O botão Cancelar do próprio diálogo de entrada do iOS pode encerrar o atalho; os itens anteriormente salvos permanecem no JSON.

Na própria lista há **Ver resumo**, **Pausar** e **Finalizar conferência**. Ver resumo mantém a conferência aberta. Pausar encerra a execução com os itens já salvos. Finalizar informa quantos faltam e pede confirmação; a sessão passa a ser somente para consulta. Voltar à lista cancela a finalização.

O histórico oferece as cinco conferências mais recentes ou todas as conferências. A tabela exibe todos os produtos e as listas Não conferidos, Comprar e Processar. Itens não conferidos ficam com quantidades em branco. Um produto pode aparecer nas listas Comprar e Processar simultaneamente.

## Dados e recuperação

Cada conferência tem um JSON próprio, identificado por data/hora e sufixo aleatório, com verificação de colisão antes de criar. Cada confirmação atualiza apenas a conferência atual. Antes de substituir o JSON, uma cópia anterior é salva em `<nome>.json.bak`.

O formato continua sendo `papa-pizza-simples-1`. Em `valores`, as chaves usam o ID do cadastro: `1_estoque`, `1_comprar`, `1_processar`. Os valores são textos numéricos, conforme o contrato da ação `data.set_value`, convertidos para Number na exibição. A ausência de uma das três quantidades mantém o item como não conferido. Históricos deste projeto anteriores à lista continuam usando o mesmo formato.

Recuperação manual: preserve o JSON com problema, copie o `.json.bak` pelo aplicativo Arquivos e dê à cópia um nome terminado somente em `.json`. Não apague o original até verificar a recuperação. Arquivos JSON malformados podem encerrar a leitura; não há recuperação automática.

As gravações do iCloud não são transações atômicas. Use uma execução por vez e aguarde sincronização antes de trocar de dispositivo. O funcionamento offline depende de os arquivos estarem disponíveis localmente.

## Limites

Atalhos/Shortcuty não oferece `while` ou `break` nesta linguagem. A navegação permite até 120 escolhas por execução e então abre o resumo, mantendo a sessão aberta. Para continuar, execute novamente e escolha Continuar conferência. Ver resumo e Finalizar podem exigir aguardar o término dos ciclos restantes do laço nativo; esse tempo precisa ser medido no iPhone.

Não há Lembretes, navegação por categorias, edição de conferências finalizadas nem migração das versões antigas. O resumo HTML abre no Quick Look, sem salvar uma cópia permanente a cada consulta.

Esta versão compilou oficialmente sem erros, mas a lista, revisão e retorno de campos vazios ainda precisam de teste no iPhone.
