---
title: "Adicionar o conector do Dynamics 365 (online) para os seus Aplicativos Lógicos do Azure | Microsoft Docs"
description: "Crie aplicativos lógicos com o serviço de Aplicativo do Azure. O Provedor de Conexão do Dynamics 365 (online) fornece uma API para trabalhar com as entidades no Dynamics 365 (online)."
services: logic-apps
cloud: Azure Stack
documentationcenter: 
author: Mattp123
manager: anneta
ms.assetid: 0dc2abef-7d2c-4a2d-87ca-fad21367d135
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: matp
translationtype: Human Translation
ms.sourcegitcommit: fa426f474f4efd4023da5dfd9954dacf96f635ab
ms.openlocfilehash: 99d5379ad1e6965dd9ed88de456cc850d7e40d5a


---
# <a name="create-a-logic-app-with-the-dynamics-365-connector"></a>Criar um aplicativo lógico com o conector do Dynamics 365

Com Aplicativos Lógicos, você pode se conectar ao Dynamics 365 (online) e criar fluxos de negócios úteis que criam novos registros, atualizam itens ou retornam uma lista de registros. Com o conector do Dynamics 365, você pode:

* Compilar seu fluxo de negócios com base nos dados que obtém do Dynamics 365 (online).
* Usar ações que obtêm uma resposta e disponibilizar a saída para outras ações. Por exemplo, quando um item é atualizado no Dynamics 365 (online), você pode enviar um e-mail usando o Office 365.

Este tópico mostra como criar um aplicativo lógico que cria uma tarefa no Dynamics 365 sempre que um novo cliente em potencial é criado no Dynamics 365.

## <a name="prerequisites"></a>Pré-requisitos
* Uma conta do Azure.
* Uma conta do Dynamics 365 (online).

## <a name="walkthrough-create-a-task-whenever-a-new-lead-is-created-in-dynamics-365"></a>Passo a passo: criar uma tarefa sempre que um novo cliente em potencial é criado no Dynamics 365
1.    [Entrar no Azure](https://portal.azure.com).
2.    Digite *Aplicativos Lógicos* na caixa **Pesquisa** e pressione ENTER.
3.    Na área de serviço de Aplicativo Lógico, clique em **Adicionar**.

  ![Adicionar LogicApp](./media/connectors-create-api-crmonline/add-logic-app.png)

4.    Complete os campos **Nome**, **Assinatura**, **Grupo de recursos** e **Local** para criar o objeto de aplicativo lógico e, em seguida, clique em **Criar**.

5.    Selecione o novo aplicativo lógico. Quando você receber a notificação **Implantação bem-sucedida**, clique em **Atualizar**.

6.    Em Ferramentas de desenvolvimento, clique em **Designer de aplicativos lógicos**e na lista de modelos disponíveis, clique em **LogicApp em branco**.

7.    Digite *Dynamics 365*. Na lista há vários gatilhos Dynamics 365, clique em **Dynamics 365 – quando um registro é criado**.

8.    Se for solicitado que você entre no Dynamics 365, faça isso agora.

9.    Nos detalhes do gatilho, insira as seguintes informações.

  * **Nome da organização**. Selecione a instância do Dynamics 365 que você deseja que o aplicativo lógico ouça.

  * **Nome da entidade**. Selecione a entidade que você deseja ouvir, que agirá como gatilho para iniciar o aplicativo lógico. Neste passo a passo, **Clientes em potencial** está selecionado.

  * **Com que frequência você deseja verificar os itens?** Esses valores definem com que frequência o aplicativo lógico verifica atualizações relacionadas ao gatilho. A configuração padrão é verificar se há atualizações a cada três minutos.

    * **Frequência**. Selecione segundos, minutos, horas ou dias.

    * **Intervalo**. Insira um número que indique o número de segundos, minutos, horas ou dias passados antes da próxima verificação.

    ![Detalhes do Gatilho de Aplicativo Lógico](./media/connectors-create-api-crmonline/trigger-details.png)

10.    Clique na caixa **Nova etapa** e depois clique em **Adicionar uma ação**.

11.    Digite *Dynamics 365* e, na lista, clique em **Dynamics 365 – Criar um novo registro**.

12.    Insira as seguintes informações.
  * **Nome da organização**. Selecione a instância do Dynamics 365 na qual você deseja que o fluxo crie o registro. Observe que ela não precisa ser a mesma instância da qual o evento é disparado.
  * **Nome da entidade**. Selecione a entidade que você deseja que crie um registro quando o evento é disparado. Neste passo a passo, **Tarefas** está selecionado.

13.    Aparece uma caixa de Assunto. Quando você clica na caixa, um painel de conteúdo dinâmico é exibido, onde você pode selecionar qualquer um dos campos a seguir.
  * **Sobrenome**. Selecionar este campo irá inserir o sobrenome do cliente em potencial no campo Assunto da tarefa quando a tarefa de registro é criada.
  * **Tópico**. Selecionar este campo irá inserir o campo Tópico do cliente em potencial no campo Assunto da tarefa quando o registro de tarefas é criado.
Clique em **Tópico** para adicioná-lo à caixa **Assunto**.

  ![Criar novos detalhes do registro do Aplicativo Lógico](./media/connectors-create-api-crmonline/create-record-details.png)

14.    Clique em **Salvar** na barra de ferramentas do Designer de Aplicativo Lógico.

  ![Barra de ferramentas do Designer do Aplicativo Lógico Salvar](./media/connectors-create-api-crmonline/designer-toolbar-save.png)

15.    Para iniciar o Aplicativo Lógico, clique em **Executar**.

  ![Barra de ferramentas do Designer do Aplicativo Lógico Salvar](./media/connectors-create-api-crmonline/designer-toolbar-run.png)

16. Agora, crie um registro de cliente em potencial no Dynamics 365 para Vendas e veja seu fluxo em ação!

## <a name="using-advanced-options"></a>Usando opções avançadas
Quando você adiciona uma etapa a um aplicativo lógico, clicar em **Mostrar opções avançadas** permite que você controle como os dados são filtrados na etapa adicionando um filtro ou ordem pela consulta.

Por exemplo, você pode usar uma consulta de filtro para recuperar apenas contas ativas e ordenar pelo nome da conta. Para fazer isso, insira a consulta de filtro OData **statuscode eq 1** e selecione **Nome da conta ** no painel de conteúdo dinâmico. Mais informações: [MSDN: $filter](https://msdn.microsoft.com/library/gg309461.aspx#Anchor_1) e [$orderby](https://msdn.microsoft.com/library/gg309461.aspx#Anchor_2).

  ![Opções avançadas de LogicApp](./media/connectors-create-api-crmonline/advanced-options.png)

### <a name="best-practices-when-using-advanced-options"></a>Práticas recomendadas ao usar o opções avançadas
Observe que, quando adiciona um valor a um campo, você deve corresponder ao tipo de campo se digitar um valor ou selecioná-lo do conteúdo dinâmico que é exibido.

Tipo de campo  |Como usar  |Onde encontrar  |Nome  |Tipo de dados  
---------|---------|---------|---------|---------
Campos de texto|Campos de texto exigem uma única linha de texto ou conteúdo dinâmico que seja um campo de tipo de texto. Exemplos incluem os campos Categoria e Subcategoria.|Configurações > Personalizações > Personalizar o sistema > Entidades > Tarefa > Campos |categoria |Única linha de texto.       
Campos de número inteiro | Alguns campos exigem conteúdo inteiro ou dinâmico que seja um campo de tipo inteiro. Exemplos incluem Porcentagem concluída e Duração. |Configurações > Personalizações > Personalizar o sistema > Entidades > Tarefa > Campos |percentcomplete |Número inteiro         
Campos de data | Alguns campos exigem a data inserida no formato dd/mm/aaaa ou conteúdo dinâmico que seja um campo do tipo data. Exemplos incluem Criado em, Data de início, Início real, Último tempo de espera, Término real e Data de vencimento. | Configurações > Personalizações > Personalizar o sistema > Entidades > Tarefa > Campos |createdon |Data e hora         
Campos que exigem um registro de ID e tipo de pesquisa |Alguns campos que fazem referência a outro registro de entidade exigem a ID do registro e o tipo de pesquisa. |Configurações > Personalizações > Personalizar o sistema > Entidades > Conta > Campos  | accountid   | Chave primária

### <a name="more-examples-of-fields-that-require-both-a-record-id-and-lookup-type"></a>Mais exemplos de campos que exigem uma ID de registro e tipo de pesquisa
Expandindo a tabela anterior, aqui há mais exemplos de campos que não funcionam com valores selecionados da lista de conteúdo dinâmica. Em vez disso, esses campos exigem que uma ID de registro e o tipo de pesquisa sejam inseridos nos campos no PowerApps.  
*  Proprietário e Tipo de proprietário. O campo Proprietário deve ser uma ID de registro de usuário ou de equipe válida. O Tipo de proprietário deve ser **systemusers** ou **equipes**.
* Cliente e Tipo de cliente. O campo Cliente deve ser uma ID de registro de contato ou conta válida. O Tipo de proprietário deve ser **contas** ou **contatos**.
* Referente e Referente ao tipo. O campo Referente deve ser uma ID de registro válida, como uma ID de registro de contato ou conta. Referente ao tipo deve ser o tipo de pesquisa para o registro, como **contas** ou **contatos**.

O seguinte exemplo de ação de criação de tarefa adiciona um registro de conta que corresponde à ID de registro que a adiciona ao campo referente da tarefa.

  ![Fluxo recordId e conta tipo](./media/connectors-create-api-crmonline/recordid-type-account.png)

Este exemplo também atribui a tarefa a um usuário específico com base na ID de registro do usuário.
  ![Fluxo recordId e conta tipo](./media/connectors-create-api-crmonline/recordid-type-user.png)

Para encontrar uma ID de registro, consulte a seção *Encontrar a ID de registro* abaixo.

## <a name="find-the-record-id"></a>Localize a ID de registro
1. Abra um registro, como um registro de conta.

2. Na barra de ferramentas Ações, clique em **Pop Out** ![registro de pop-up](./media/connectors-create-api-crmonline/popout-record.png).
Como alternativa, na barra de ferramentas Ações, clique em **ENVIAR LINK POR E-MAIL** para copiar a URL completa no seu programa de e-mail padrão.

3. A ID de registro é exibida entre os caracteres de codificação %&7;b e %7 da URL.

  ![Fluxo recordId e conta tipo](./media/connectors-create-api-crmonline/recordid.png)

## <a name="troubleshooting"></a>Solucionar problemas
Para solucionar uma falha de uma etapa em um aplicativo lógico, exiba os detalhes de status do evento.

1. Na área de Aplicativos Lógicos, clique no seu aplicativo lógico e, em seguida, em **Visão geral**. A Área de resumo é exibida, fornecendo o status de execução para o aplicativo lógico. Se houver execuções com falha, clique no evento com falha do qual você deseja ter mais informações.

  ![Solucionar problemas de LogicApp etapa 1](./media/connectors-create-api-crmonline/tshoot1.png)

2. Clique na etapa com falha para expandi-la.

  ![Solucionar problemas de LogicApp etapa 2](./media/connectors-create-api-crmonline/tshoot2.png)

3. São exibidos os detalhes da etapa que podem ajudar a diagnosticar a causa da falha.

    ![Solucionar problemas de LogicApp etapa 2](./media/connectors-create-api-crmonline/tshoot3.png)

Para obter mais informações sobre como solucionar problemas de aplicativos lógicos, consulte [Diagnosticando falhas do aplicativo lógico](../logic-apps/logic-apps-diagnosing-failures.md).

## <a name="technical-details"></a>Detalhes técnicos
## <a name="triggers"></a>Gatilhos
| Gatilho | Descrição |
| --- | --- |
| Quando um registro é criado |Dispara um fluxo quando um objeto é criado no Dynamics 365. |
| Quando um registro é atualizado |Dispara um fluxo quando um objeto é modificado no Dynamics 365. |
| Quando um registro é excluído |Dispara um fluxo quando um objeto é excluído no Dynamics 365. |

## <a name="actions"></a>Ações
| Ação | Descrição |
| --- | --- |
| Listar registros |Esta operação obtém os registros de uma entidade. |
| Criar um novo registro |Esta operação cria um novo registro de uma entidade. |
| Obter registro |Esta operação obtém o registro específico de uma entidade. |
| Excluir um registro |Essa operação exclui um registro de uma coleção de entidades. |
| Atualizar um registro |Esta operação atualiza um registro existente de uma entidade. |

### <a name="trigger-and-action-details"></a>Detalhes de gatilho e ação
Nesta seção, consulte os detalhes específicos sobre cada gatilho e ação, incluindo todas as propriedades de entrada obrigatórias ou opcionais, assim como toda saída correspondente associada ao conector.

#### <a name="when-a-record-is-created"></a>Quando um registro é criado
Dispara um fluxo quando um objeto é criado no Dynamics 365.

| Nome da propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| dataset* |Nome da Organização |Nome da organização do Dynamics 365, como Contoso |
| table* |Nome da entidade |O nome da entidade |
| $filter |Consulta de filtro |Uma consulta de filtro ODATA para restringir as entradas retornadas |
| $orderby |Ordenar por |Uma consulta orderBy do ODATA para especificar a ordem das entradas |

Um asterisco (*) significa que a propriedade obrigatória.

##### <a name="output-details"></a>Detalhes da Saída
ItemsList

| Nome da Propriedade | Tipo de Dados |
| --- | --- |
| value |array |

#### <a name="when-a-record-is-updated"></a>Quando um registro é atualizado
Dispara um fluxo quando um objeto é modificado no Dynamics 365.

| Nome da propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| dataset* |Nome da Organização |Nome da organização do Dynamics 365, como Contoso |
| table* |Nome da entidade |O nome da entidade |

Um asterisco (*) significa que a propriedade obrigatória.

##### <a name="output-details"></a>Detalhes da Saída
ItemsList

| Nome da Propriedade | Tipo de Dados |
| --- | --- |
| value |array |

#### <a name="when-a-record-is-deleted"></a>Quando um registro é excluído
Dispara um fluxo quando um objeto é excluído no Dynamics 365.

| Nome da propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| dataset* |Nome da Organização |Nome da organização do Dynamics 365, como Contoso |
| table* |Nome da entidade |O nome da entidade |


Um asterisco (*) significa que a propriedade obrigatória.

##### <a name="output-details"></a>Detalhes da Saída
ItemsList

| Nome da Propriedade | Tipo de Dados |
| --- | --- |
| value |array |

#### <a name="list-records"></a>Listar registros
Esta operação obtém os registros de uma entidade.

| Nome da propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| dataset* |Nome da Organização |Nome da organização do Dynamics 365, como Contoso |
| table* |Nome da entidade |O nome da entidade |
| $filter |Consulta de filtro |Uma consulta de filtro ODATA para restringir as entradas retornadas |
| $orderby |Ordenar por |Uma consulta orderBy do ODATA para especificar a ordem das entradas |

Um asterisco (*) significa que a propriedade obrigatória.

##### <a name="output-details"></a>Detalhes da Saída
ItemsList

| Nome da Propriedade | Tipo de Dados |
| --- | --- |
| value |array |

#### <a name="create-a-new-record"></a>Criar um novo registro
Esta operação cria um novo registro de uma entidade.

| Nome da propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| dataset* |Nome da Organização |Nome da organização do Dynamics 365, como Contoso |
| table* |Nome da entidade |O nome da entidade |

Um asterisco (*) significa que a propriedade obrigatória.

##### <a name="output-details"></a>Detalhes da Saída
Nenhuma.

#### <a name="get-record"></a>Obter registro
Esta operação obtém o registro específico de uma entidade.

| Nome da propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| dataset* |Nome da Organização |Nome da organização do Dynamics 365, como Contoso |
| table* |Nome da entidade |O nome da entidade |
| id* |Identificador do item |Especificar o Identificador do registro |

Um asterisco (*) significa que a propriedade obrigatória.

##### <a name="output-details"></a>Detalhes da Saída
Nenhuma.

#### <a name="delete-a-record"></a>Excluir um registro
Essa operação exclui um registro de uma coleção de entidades.

| Nome da propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| dataset* |Nome da Organização |Nome da organização do Dynamics 365, como Contoso |
| table* |Nome da entidade |O nome da entidade |
| id* |Identificador do item |Especificar o identificador do registro |

Um asterisco (*) significa que a propriedade obrigatória.

#### <a name="update-a-record"></a>Atualizar um registro
Esta operação atualiza um registro existente de uma entidade.

| Nome da propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| dataset* |Nome da Organização |Nome da organização do Dynamics 365, como Contoso |
| table* |Nome da entidade |O nome da entidade |
| id* |Identificador do registro |Especificar o identificador do registro |

Um asterisco (*) significa que a propriedade obrigatória.

##### <a name="output-details"></a>Detalhes da Saída
Nenhuma.

## <a name="http-responses"></a>Respostas HTTP
As ações e os gatilhos podem retornar um ou mais dos seguintes códigos de status HTTP:

| Nome | Descrição |
| --- | --- |
| 200 |OK |
| 202 |Aceita |
| 400 |Solicitação incorreta |
| 401 |Não Autorizado |
| 403 |Proibido |
| 404 |Não encontrado |
| 500 |Erro Interno do Servidor. Ocorreu um erro desconhecido. |
| padrão |Falha na Operação. |

## <a name="next-steps"></a>Próximas etapas
Explore os outros conectores disponíveis nos Aplicativos Lógicos em nossa [lista de APIs](apis-list.md).



<!--HONumber=Feb17_HO2-->


