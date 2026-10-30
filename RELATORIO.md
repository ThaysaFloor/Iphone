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

O projeto final foi enviado ao endpoint de compilação usado pela interface oficial do Shortcuty Studio, com sessão e CSRF normais. Resultado de Build: `complete`, `ok: true`, compilador `0.1.4`, **0 erros, 32 avisos SS2101 e 393 ações nativas**. O resultado completo está em `compilacao-oficial.json`, com parâmetros das ações e localizações dos avisos.

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
