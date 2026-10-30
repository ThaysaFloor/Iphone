# Implementação e validação

Foi criado um projeto novo. Nenhuma versão antiga foi reaproveitada. O projeto novo foi colocado no repositório ThaysaFloor/Iphone a pedido do usuário. Nenhuma configuração de ambiente foi publicada nesta implementação.

Documentação consultada: llms.txt, agent.md, language.md, actions.json, project.schema.json, types.md, variables.md, control.md, values.md, calls.md, functions.md, projects.md, recipes.md, start.md e referência de ações, todos em https://studio.shortcuty.app/. Assinaturas de arquivos e dicionários foram verificadas no catálogo oficial.

Decisões técnicas:

- Laços usam a forma documentada e aceita `for item in ...`; não há `break` nem `continue`.
- Nenhum cast para List. A lista do cadastro mantém a estrutura de coleção.
- Campos opcionais têm verificações de ausência antes da utilização. `require` aparece somente após testes explícitos; não é usado como substituto de validação nem como mecanismo de erro.
- JSON é serializado de Dictionary para Text e convertido explicitamente para File antes de `files.save`. Na leitura, o conteúdo é convertido em Text e depois Dictionary.
- O registro por ID dispensa busca em revisões e evita duplicações na retomada.
- Cada item só é gravado depois de confirmar as três quantidades. Cancelar antes dessa confirmação preserva a gravação anterior.
- Backup anterior à substituição; identificação independente para novas sessões; nenhum apagamento de históricos.
- Nomes dos produtos são escapados para HTML; quantidades exibidas passam por conversão numérica.
- Apenas ações nativas de Atalhos: nenhum aplicativo auxiliar, serviço web ou credencial durante o uso.

## Evidência real

O projeto final foi enviado ao endpoint de compilação usado pela interface oficial do Shortcuty Studio, com sessão e CSRF normais. Resultado de Build: `complete`, `ok: true`, compilador `0.1.4`, **0 erros, 37 avisos SS2101 e 553 ações nativas**. O resultado completo está em `compilacao-oficial.json`, com parâmetros das ações e localizações dos avisos.

A checagem inicial encontrou problemas de sintaxe e de opcionais, que foram corrigidos antes da compilação final. O esquema oficial do projeto foi validado localmente com JSON Schema. O conteúdo do projeto importável coincide com o arquivo fonte; o cadastro entregue foi comparado byte a byte com o anexo original.

Avisos restantes abrangem conversões File/Text/Dictionary/Number, campos tipados como Value pelo catálogo, comparação numérica e entrada do Quick Look. Eles NÃO foram ocultados nem tratados como prova de execução correta. O catálogo também não confirma uma versão mínima de iOS para todas as ações. Compilar não executa Arquivos, iCloud, diálogos ou Quick Look no aparelho.

## Testes obrigatórios no iPhone — ainda não executados

Use dados de teste antes de adotar no trabalho:

1. Criar conferência: verificar pasta, extensão `.json`, UTF-8 e conteúdo JSON válido; conferir os 60 produtos e as nove categorias na cópia do cadastro.
2. Confirmar estoque 0, comprar 0 e processar 0; verificar que consta como conferido. Confirmar outro item com comprar e processar maiores que zero; ele deve aparecer nas duas listas.
3. Digitar quantidade negativa ou fracionária; verificar rejeição e preservação do registro anterior. Verificar cancelamento durante cada uma das três perguntas.
4. Confirmar um item, pausar, encerrar Atalhos e retomar. Deve abrir o primeiro não conferido sem duplicar itens nem perder o estoque zero.
5. Deixar um item para depois e retomar em outra execução. Ele deve continuar não conferido; tabela com campos vazios, não zeros.
6. Após duas gravações, abrir o `.bak`: deve representar a gravação imediatamente anterior. Testar recuperação manual com uma cópia, sem danificar os únicos dados reais.
7. Finalizar com itens faltantes e cancelar a confirmação; depois confirmar. Uma sessão finalizada deve abrir somente para consulta, indicando itens não conferidos.
8. Criar outra conferência e verificar que a primeira e seu backup permanecem intactos. Criar mais de cinco sessões e verificar ordenação e acesso às anteriores.
9. Abrir o HTML no Quick Look: testar acentos, tabela completa, listas e quantidades. Confirmar que os 60 produtos aparecem, incluindo os sem pendências.
10. Conferir permissões e destino real em iCloud Drive, sincronização, funcionamento offline com arquivos disponíveis e comportamento após falha de gravação. Nenhuma ação deve apagar históricos.

A assinatura e a instalação pelo Download do Studio também estão pendentes. Não se declara o projeto validado no iPhone nem pronto para uso de produção enquanto esses testes não forem concluídos.

## Correção após teste no iPhone

A captura recebida mostrou “A localização não existe”, mas não identificou a ação. A primeira implementação usava `files.create_folder` sem uma pasta-base explícita, dependendo da localização padrão de Arquivos. Essa dependência foi removida: após o menu, o usuário escolhe uma pasta real pelo seletor nativo (`files.choose`, modo Folders), e todas as leituras e gravações recebem essa seleção. Sair não abre o seletor. O cadastro e o formato dos históricos permanecem iguais.

Nova compilação oficial concluída: zero erros, 33 avisos e 403 ações. Os parâmetros compilados foram inspecionados: há seleção explícita de pasta e não há ação de criação de pasta padrão. Isso ainda não comprova que o erro da captura foi resolvido no aparelho. Se persistir, tocar em Mostrar no alerta permite identificar a ação exata.

Para testar a correção: criar/selecionar PapaPizza no iCloud Drive, iniciar uma conferência, confirmar um item, pausar e retomar selecionando a mesma pasta. Verificar também o backup e o resumo. Não mover ou apagar históricos existentes; se já houver uma pasta com conferências, selecioná-la.

## Observações da gravação e correção dos títulos de menu

A gravação recebida mostra a seleção de uma pasta, as perguntas do primeiro produto (Abacaxi Picado), a confirmação com estoque 1, comprar 0 e processar 1, e avanço até a pergunta de estoque de Açúcar. Não aparece o alerta de localização inexistente nesse trecho. Isso evidencia avanço do fluxo, mas não comprova conteúdo dos arquivos, retomada nem resumo.

Os menus dos produtos exibiam o título genérico “Qual?”. O build anterior ligava a variável diretamente a WFMenuPrompt como WFTextTokenAttachment. O código agora usa texto interpolado `Conferir: ...`, compilado como WFTextTokenString, com o nome anexado ao texto. O título dinâmico do menu de encerramento recebeu a mesma correção. Os parâmetros gerados foram inspecionados e confirmados nos dois menus. A confirmação visual no iPhone continua pendente.

Build desta correção: zero erros, 33 avisos SS2101, 402 ações nativas. Cadastro e formato de persistência permanecem iguais.

## Escolha livre e marcação de conferidos

A navegação sequencial foi substituída por uma lista nativa Choose from List. Duas passagens pelo cadastro montam os produtos não conferidos (⬜) primeiro e os conferidos (✅) depois, com ordem alfabética em ambos. A lista é reconstruída após cada escolha. O rótulo selecionado é mapeado para o ID estável; não se extrai o ID de texto de nomes. Conferido significa existência das três quantidades, incluindo zero.

É possível escolher um item já conferido e substituir suas três quantidades na sessão aberta. Não há anexação de registros duplicados. A opção Voltar sem salvar preserva o registro anterior. As três atualizações são feitas somente no ramo Salvar item, seguido por uma única gravação do JSON. Campos vazios ou valores inválidos retornam o sentinela interno -1; as perguntas seguintes e o salvamento são condicionados a valor >= 0. O sentinela nunca é gravado. O Cancelar do diálogo nativo pode terminar a execução; não é tratado como retorno programático à lista.

Pausar encerra a execução; Ver resumo mantém o estado aberto; Finalizar oferece confirmação e informa quantos faltam. São opções na própria lista. Sem while/break na linguagem, há um limite explícito de 120 escolhas por execução; atingido o limite, abre-se o resumo e pode-se retomar depois. Ao pedir resumo/finalizar antes disso, os ciclos restantes passam pelo guard de navegação sem reconstruir a lista; o tempo desse término depende do iPhone. Não se afirma que essa latência foi medida.

Build oficial final: complete/ok, compilador 0.1.4, zero erros, 37 avisos SS2101, 553 ações nativas. Foram inspecionados os parâmetros nativos de Append to Variable e Choose from List: ambos usam a variável opcoes, reconstruída a cada escolha. O esquema oficial do projeto foi validado; fonte e projeto estão sincronizados; o cadastro original continua idêntico byte a byte. Não houve teste de execução da lista no iPhone.

Testar no aparelho: escolher diretamente um produto no fim da lista; salvar com três zeros e observar ✅; verificar que passa para depois dos ⬜; revisar o mesmo produto e confirmar ausência de duplicação; dar OK vazio em cada uma das três perguntas e verificar retorno sem alterações; usar Voltar sem salvar; pausar e retomar, verificando as marcas; conferir resumo, finalização e backup. Confirmar que a lista exibe exatamente os 60 produtos mais três opções de controle, sem itens duplicados, e medir o tempo de abertura/reabertura e de saída para o resumo.
