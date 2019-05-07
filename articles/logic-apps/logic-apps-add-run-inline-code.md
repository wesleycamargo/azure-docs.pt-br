---
title: Adicionar e executar trechos de código - aplicativos lógicos do Azure
description: Adicionar e executar trechos de código com o código embutido em aplicativos lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: derek1ee, LADocs
ms.topic: article
ms.date: 05/06/2019
ms.openlocfilehash: 9ef11eb2099ff617fb4da4b9a924dc3f0550f226
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65160539"
---
# <a name="add-and-run-code-snippets-by-using-inline-code-in-azure-logic-apps"></a>Adicionar e executar trechos de código usando o código embutido em aplicativos lógicos do Azure

Quando você quiser executar um trecho de código dentro de seu aplicativo lógico, você pode adicionar o interno **código embutido** ação como uma etapa no fluxo de trabalho do aplicativo lógico. Essa ação funciona melhor quando você deseja executar o código que se encaixa nesse cenário:

* É executado em JavaScript. Mais linguagens em breve.
* Fim da execução em cinco segundos ou menos.
* Lida com dados de até 50 MB de tamanho.
* Usa o Node. js versão 8.11.1. Para obter mais informações, consulte [objetos internos padrão](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects).

Esta ação executa o trecho de código e retorna a saída do trecho de código como um token nomeado **resultado**, que você pode usar em ações subsequentes em seu aplicativo lógico. Para outros cenários em que você deseja criar uma função para seu código, tente [criando e chamando uma função do Azure](../logic-apps/logic-apps-azure-functions.md) em seu aplicativo lógico.

Neste artigo, os gatilhos de aplicativo lógico do exemplo quando um novo email chega em uma conta do Outlook do Office 365. O trecho de código extrai e retorna os endereços de email que aparecem no corpo do email.

![Visão geral de exemplo](./media/logic-apps-add-run-inline-code/inline-code-example-overview.png)

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* O aplicativo lógico no qual você deseja adicionar seu trecho de código, incluindo um gatilho. Se você não tiver um aplicativo lógico, consulte [guia de início rápido: criar seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

   O aplicativo lógico de exemplo neste tópico usa esse gatilho do Outlook do Office 365: **Quando um novo email chegar**

* Uma [conta de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) que esteja vinculado ao seu aplicativo lógico

## <a name="add-inline-code"></a>Adicione o código embutido

1. Se você ainda não fez isso, nos [portal do Azure](https://portal.azure.com), abra o aplicativo lógico no Designer do aplicativo lógico.

1. No designer, adicione a **código embutido** ação no local desejado no fluxo de trabalho do aplicativo lógico.

   * Para adicionar a ação no final do fluxo de trabalho, escolha **nova etapa**.

   * Para adicionar a ação entre etapas existentes, mova o ponteiro do mouse sobre a seta que conecta-se essas etapas. Escolha o sinal de adição (**+**) e selecione **adicionar uma ação**.

   Este exemplo adiciona o **código embutido** ação sob o disparador do Outlook do Office 365.

   ![Adicionar nova etapa](./media/logic-apps-add-run-inline-code/add-new-step.png)

1. Sob **escolher uma ação**, na caixa de pesquisa, insira "código embutido" como filtro. Na lista de ações, selecione esta ação: **Executar o código do JavaScript**

   ![Selecione "Executar o código JavaScript"](./media/logic-apps-add-run-inline-code/select-inline-code-action.png)

   A ação é exibida no designer e contém algum código de exemplo do padrão, incluindo uma instrução return.

   ![Ação de código embutido com código de exemplo do padrão](./media/logic-apps-add-run-inline-code/inline-code-action-default.png)

1. No **código** caixa, excluir o código de exemplo e insira o código que você deseja executar. Escreva código que você colocaria dentro de um método, mas sem definir a assinatura do método. 

   Quando você digita uma palavra-chave reconhecida, a lista de preenchimento automático é exibida para que você possa selecionar das palavras-chave disponíveis, por exemplo:

   ![Lista de preenchimento automático de palavra-chave](./media/logic-apps-add-run-inline-code/auto-complete.png)

   Este trecho de código de exemplo primeiro cria uma variável que armazena uma *expressão regular*, que especifica um padrão para corresponder no texto de entrada. O código, em seguida, cria uma variável que armazena os dados do corpo do email do gatilho.

   ![Criar variáveis](./media/logic-apps-add-run-inline-code/save-email-body-variable.png)

   Para facilitar a referência os resultados do gatilho e ações anteriores, a lista de conteúdo dinâmica é exibida enquanto o cursor estiver dentro de **código** caixa. Neste exemplo, a lista mostra os resultados disponíveis do gatilho, incluindo o **corpo** token, que agora você pode selecionar.

   Depois de selecionar o **corpo** resolve a ação de código embutido token, o token para um `workflowContext` objeto que referencia o email `Body` valor da propriedade:

   ![Resultados de Select](./media/logic-apps-add-run-inline-code/inline-code-example-select-outputs.png)

   No **código** caixa, seu trecho de código pode usar somente leitura `workflowContext` objeto como entrada. Esse objeto tem subpropriedades que dão acesso de código para os resultados do gatilho e ações anteriores no fluxo de trabalho.
   Para obter mais informações, consulte esta seção neste tópico: [Resultados de gatilho e ação em seu código de referência](#workflowcontext).

   > [!NOTE]
   >
   > Se seu trecho de código faz referência a nomes de ação que usam o operador ponto (.), você deve adicionar esses nomes de ação para o [ **ações** parâmetro](#add-parameters). Essas referências também devem incluir os nomes de ação com colchetes ([]) e aspas, por exemplo:
   >
   > `// Correct`</br> 
   > `workflowContext.actions["my.action.name"].body`</br>
   >
   > `// Incorrect`</br>
   > `workflowContext.actions.my.action.name.body`

   A ação de código embutido não requer uma `return` instrução, mas os resultados de uma `return` instrução estão disponíveis para referência em ações posteriores por meio do **resultado** token. 
   Por exemplo, o trecho de código retorna o resultado ao chamar o `match()` função, que localiza correspondências no corpo do email em relação à expressão regular. O **redigir** ação usa o **resultado** a referência de token os resultados de embutido ação de código e cria um único resultado.

   ![Aplicativo lógico concluído](./media/logic-apps-add-run-inline-code/inline-code-complete-example.png)

1. Quando terminar, salve o aplicativo lógico.

<a name="workflowcontext"></a>

### <a name="reference-trigger-and-action-results-in-your-code"></a>Resultados de gatilho e ação de referência em seu código

O `workflowContext` objeto tem esta estrutura, que inclui o `actions`, `trigger`, e `workflow` subpropriedades:

```json
{
   "workflowContext": {
      "actions": {
         "<action-name-1>": @actions('<action-name-1>'),
         "<action-name-2>": @actions('<action-name-2>')
      },
      "trigger": {
         @trigger()
      },
      "workflow": {
         @workflow()
      }
   }
}
```

Esta tabela contém mais informações sobre estas subpropriedades:

| Propriedade | Type | DESCRIÇÃO |
|----------|------|-------|
| `actions` | Coleção de objetos | Objetos de resultado de ações que são executadas antes de seu trecho de código é executado. Cada objeto tem um *chave-valor* par em que a chave é o nome de uma ação e o valor é equivalente a chamar o [função actions()](../logic-apps/workflow-definition-language-functions-reference.md#actions) com `@actions('<action-name>')`. Nome da ação usa o mesmo nome de ação que é usado na definição de fluxo de trabalho subjacente, que substitui espaços ("") no nome da ação por sublinhados (_). Esse objeto fornece acesso aos valores de propriedade de ação da instância atual do fluxo de trabalho execute. |
| `trigger` | Object | Objeto de resultado do gatilho e equivalente a chamar o [trigger() função](../logic-apps/workflow-definition-language-functions-reference.md#trigger). Esse objeto fornece acesso aos valores de propriedade de gatilho da instância atual do fluxo de trabalho execute. |
| `workflow` | Object | O objeto de fluxo de trabalho e o equivalente a chamar o [Workflow () função](../logic-apps/workflow-definition-language-functions-reference.md#workflow). Esse objeto fornece acesso aos valores de propriedade de fluxo de trabalho, como o nome do fluxo de trabalho, ID de execução e assim por diante, da instância atual do fluxo de trabalho execute. |
|||

No exemplo deste tópico, o `workflowContext` objeto tem essas propriedades que pode acessar o seu código:

```json
{
   "workflowContext": {
      "trigger": {
         "name": "When_a_new_email_arrives",
         "inputs": {
            "host": {
               "connection": {
                  "name": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Web/connections/office365"
               }
            },
            "method": "get",
            "path": "/Mail/OnNewEmail",
            "queries": {
               "includeAttachments": "False"
            }
         },
         "outputs": {
            "headers": {
               "Pragma": "no-cache",
               "Content-Type": "application/json; charset=utf-8",
               "Expires": "-1",
               "Content-Length": "962095"
            },
            "body": {
               "Id": "AAMkADY0NGZhNjdhLTRmZTQtNGFhOC1iYjFlLTk0MjZlZjczMWRhNgBGAAAAAABmZwxUQtCGTqSPpjjMQeD",
               "DateTimeReceived": "2019-03-28T19:42:16+00:00",
               "HasAttachment": false,
               "Subject": "Hello World",
               "BodyPreview": "Hello World",
               "Importance": 1,
               "ConversationId": "AAQkADY0NGZhNjdhLTRmZTQtNGFhOC1iYjFlLTk0MjZlZjczMWRhNgAQ",
               "IsRead": false,
               "IsHtml": true,
               "Body": "Hello World",
               "From": "<sender>@<domain>.com",
               "To": "<recipient-2>@<domain>.com;<recipient-2>@<domain>.com",
               "Cc": null,
               "Bcc": null,
               "Attachments": []
            }
         },
         "startTime": "2019-05-03T14:30:45.971564Z",
         "endTime": "2019-05-03T14:30:50.1746874Z",
         "scheduledTime": "2019-05-03T14:30:45.8778117Z",
         "trackingId": "1cd5ffbd-f989-4df5-a96a-6e9ce31d03c5",
         "clientTrackingId": "08586447130394969981639729333CU06",
         "originHistoryName": "08586447130394969981639729333CU06",
         "code": "OK",
         "status": "Succeeded"
      },
      "workflow": {
         "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<logic-app-workflow-name>",
         "name": "<logic-app-workflow-name>",
         "type": "Microsoft.Logic/workflows",
         "location": "<Azure-region>",
         "run": {
            "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<logic-app-workflow-name>/runs/08586453954668694173655267965CU00",
            "name": "08586453954668694173655267965CU00",
            "type": "Microsoft.Logic/workflows/runs"
         }
      }
   }
}
```

<a name="add-parameters"></a>

## <a name="add-parameters"></a>Adicionar parâmetros

Em alguns casos, talvez você precise explicitamente exigem que o **código embutido** ação inclui os resultados do gatilho ou ações específicas que seu código referencia como dependências, adicionando o **gatilho** ou **Ações** parâmetros. Essa opção é útil para cenários em que os resultados referenciados não são encontrados em tempo de execução.

> [!TIP]
> Se você planeja reutilizar seu código, adicione referências a propriedades usando o **código** caixa para que seu código inclui as referências de token resolvidas, em vez de adicionar o gatilho ou a ações como dependências explícitas.

Por exemplo, suponha que você tiver um código que faz referência a **SelectedOption** incidir o **enviar email de aprovação** ação para o conector do Outlook do Office 365. No momento da criação, o mecanismo dos aplicativos lógicos analisa o código para determinar se você já referenciou qualquer gatilho ou ação e incluirão os resultados automaticamente. Em tempo de execução, deve você receber um erro que o resultado de gatilho ou ação referenciado não está disponível no especificado `workflowContext` do objeto, você pode adicionar esse gatilho ou ação como uma dependência explícita. Neste exemplo, você adiciona o **ações** parâmetro e especifique que o **código embutido** ação inclua explicitamente o resultado do **enviar email de aprovação** ação.

Para adicionar esses parâmetros, abra o **adicionar novo parâmetro** lista e, em seguida, selecione os parâmetros que você deseja:

   ![Adicionar parâmetros](./media/logic-apps-add-run-inline-code/inline-code-action-add-parameters.png)

   | Parâmetro | DESCRIÇÃO |
   |-----------|-------------|
   | **Ações** | Inclua resultados de ações anteriores. Ver [incluir resultados de ação](#action-results). |
   | **Gatilho** | Inclua resultados do gatilho. Ver [resultados de gatilho de inclusão](#trigger-results). |
   |||

<a name="trigger-results"></a>

### <a name="include-trigger-results"></a>Incluir resultados de gatilho

Se você selecionar **gatilhos**, você será solicitado se para incluir o disparador os resultados.

* Dos **disparador** lista, selecione **Sim**.

<a name="action-results"></a>

### <a name="include-action-results"></a>Incluir resultados de ação

Se você selecionar **ações**, você será solicitado para as ações que você deseja adicionar. No entanto, antes de começar a adicionar ações, você precisa da versão do nome de ação que aparece na definição de fluxo de trabalho subjacente do aplicativo lógico.

* Esse recurso não dá suporte a variáveis, loops e índices de iteração.

* Nomes de definição de fluxo de trabalho do aplicativo lógico usam um caractere de sublinhado (_), não um espaço.

* Para nomes de ação que usam o operador ponto (.), incluem esses operadores, por exemplo:

  `My.Action.Name`

1. Na barra de ferramentas designer, escolha **exibição de código**e pesquisar dentro do `actions` atributo para o nome da ação.

   Por exemplo, `Send_approval_email_` é o nome JSON para o **enviar email de aprovação** ação.

   ![Localizar o nome da ação em JSON](./media/logic-apps-add-run-inline-code/find-action-name-json.png)

1. Para retornar ao modo de exibição designer, na barra de ferramentas de exibição de código, escolha **Designer**.

1. Para adicionar a primeira ação, o **ações de Item - 1** , digite o nome JSON da ação.

   ![Insira a primeira ação](./media/logic-apps-add-run-inline-code/add-action-parameter.png)

1. Para adicionar outra ação, escolha **adicionar novo item**.

## <a name="reference"></a>Referência

Para obter mais informações sobre o **executar o código JavaScript** da ação estrutura e sintaxe na definição de fluxo de trabalho subjacente do seu aplicativo lógico usando a linguagem de definição de fluxo de trabalho, consulte esta ação [seção de referência ](../logic-apps/logic-apps-workflow-actions-triggers.md#run-javascript-code).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [conectores para aplicativos lógicos do Azure](../connectors/apis-list.md)
