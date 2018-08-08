---
title: Adicionar e executar código personalizado em aplicativos de lógica do Azure com funções do Azure | Microsoft Docs
description: Saiba como adicionar e executar trechos de código personalizados nos aplicativos de lógica do Azure com as funções do Azure
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.topic: article
ms.date: 07/25/2018
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 20ad738541554279ff9fd6dd6babe90a38676c00
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2018
ms.locfileid: "39263183"
---
# <a name="add-and-run-custom-code-snippets-in-azure-logic-apps-with-azure-functions"></a>Adicionar e executar trechos de código personalizados em aplicativos de lógica do Azure com funções do Azure

Quando você deseja criar e executar somente código suficiente que trata de um problema específico em seus aplicativos lógicos, é possível criar suas próprias funções usando [Funções do Azure](../azure-functions/functions-overview.md). Esse serviço fornece a capacidade de criar e executar snippets de código personalizados escritos com Node.js ou C# em seus aplicativos lógicos, sem se preocupar em criar um aplicativo inteiro ou a infraestrutura para executar seu código. O Azure Functions fornece computação sem servidor na nuvem e é útil para executar tarefas, como estes exemplos:

* Estenda o comportamento do seu aplicativo lógico com funções compatíveis com o Node. js ou C#.
* Execute cálculos em seu fluxo de trabalho do aplicativo lógico.
* Aplicar a formatação avançada ou computação de campos em seus aplicativos lógicos.

Você também pode [chamar aplicativos lógicos de dentro de funções do Azure](#call-logic-app).

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este artigo, estes são os itens que você precisa:

* Caso você ainda não tenha uma assinatura do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se em uma conta gratuita do Azure</a>. 

* O aplicativo lógico no qual você deseja adicionar a função

  Se você não estiver familiarizado com os Aplicativos Lógicos, examine [O que são Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md) e [Início rápido: crie seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts) como a primeira etapa no seu aplicativo lógico 

  Antes de adicionar ações para executar funções, seu aplicativo lógico deve começar com um acionador.

* Um aplicativo de função do Azure, que é um contêiner para funções do Azure e sua função do Azure. Se você não tiver um aplicativo de função, deverá [criar seu aplicativo de função primeiro](../azure-functions/functions-create-first-azure-function.md). Em seguida, você pode criar sua função seja [separadamente fora do seu aplicativo lógico](#create-function-external), ou [de dentro de seu aplicativo lógico](#create-function-designer) no Designer do aplicativo lógico.

  Os aplicativos e funções de função do Azure novos e existentes têm os mesmos requisitos para trabalhar com seus aplicativos lógicos:

  * Seu aplicativo de função deve pertencer à mesma assinatura do Azure que seu aplicativo lógico.

  * Sua função deve usar o **webhook genérico** modelo de função para uma **JavaScript** ou **C#**. Esse modelo pode aceitar o conteúdo que tenha `application/json` tipo do seu aplicativo lógico. Esses modelos também ajudam o Logic App Designer a localizar e mostrar as funções personalizadas que você cria com esses modelos quando adiciona essas funções a seus aplicativos lógicos.

  * Verifique se a propriedade **Mode** do modelo de função está definida como **Webhook** e a propriedade **Webhook type** está definida como **JSON Genérico**.

    1. Entre no <a href="https://portal.azure.com" target="_blank">Portal do Azure</a>.
    2. No menu principal do Azure, selecione **aplicativos de funções**. 
    3. No **aplicativos de funções** lista, selecione seu aplicativo de função, expanda sua função e selecione **integrar**. 
    4. Verificar seu modelo **modo** estiver definida como **Webhook** e que o **tipo de Webhook** propriedade é definida como **JSON genérico**. 

  * Se sua função tiver um [definição de API](../azure-functions/functions-openapi-definition.md), anteriormente conhecido como um [arquivo do Swagger](http://swagger.io/), Designer de aplicativos lógicos oferece uma experiência mais rica para trabalhar com parâmetros de função. 
  Antes de seu aplicativo lógico pode localizar e acessar funções que têm descrições de Swagger [configurar seu aplicativo de função seguindo estas etapas](#function-swagger).

<a name="create-function-external"></a>

## <a name="create-functions-outside-logic-apps"></a>Criar funções de aplicativos lógicos externa

No <a href="https://portal.azure.com" target="_blank">portal do Azure</a>, crie seu aplicativo de função do Azure, que deve ter a mesma assinatura do Azure do seu aplicativo lógico e, em seguida, crie sua função do Azure. Se você é novo no Azure Functions, saiba como [criar sua primeira função no Portal do Azure](../azure-functions/functions-create-first-azure-function.md), mas observe esses requisitos para criar funções do Azure que podem ser adicionadas e chamadas de aplicativos lógicos.

* Certifique-se de selecionar o modelo de função **Webhook genérico** para **JavaScript** ou **C#**.

  ![Webhook genérico - JavaScript ou C#](./media/logic-apps-azure-functions/generic-webhook.png)

* Depois de criar a função do Azure, verifique se o modelo **modo** e **tipo de Webhook** propriedades estão definidas corretamente.

  1. No **aplicativos de funções** listar, expanda sua função e selecione **integrar**. 

  2. Verifique se seu modelo **modo** estiver definida como **Webhook** e que o **tipo de Webhook** propriedade é definida como **JSON genérico**. 

     ![Propriedades de "Integração" do seu modelo de função](./media/logic-apps-azure-functions/function-integrate-properties.png)

<a name="function-swagger"></a>

* Opcionalmente, se você [gerar uma definição de API](../azure-functions/functions-openapi-definition.md), anteriormente conhecida como [arquivo Swagger](http://swagger.io/), para sua função, poderá obter uma experiência mais rica ao trabalhar com parâmetros de função no Logic Apps Designer. Para configurar seu aplicativo de função, seu aplicativo lógico pode encontrar e acessar funções com descrições de Swagger:

  * Verifique se que seu aplicativo de função está sendo executado ativamente.

  * No aplicativo de função, configure [Cross-Origin Resource Sharing (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) para que todas as origens são permitidas:

    1. A partir de **aplicativos de funções** , selecione seu aplicativo de função > **recursos da plataforma** > **CORS**.

       ![Selecione seu aplicativo de função> "Recursos da plataforma"> "CORS"](./media/logic-apps-azure-functions/function-platform-features-cors.png)

    2. Em **CORS**, adicione o caractere curinga, `*`  mas remova todas as outras origens da lista e escolha **Salvar**.

       ![Selecione seu aplicativo de função> "Recursos da plataforma"> "CORS"](./media/logic-apps-azure-functions/function-platform-features-cors-origins.png)

### <a name="access-property-values-inside-http-requests"></a>Valores de propriedade de acesso dentro de solicitações HTTP

As funções do Webhook podem aceitar solicitações HTTP como entradas e passar essas solicitações para outras funções. Por exemplo, embora o Logic Apps possua [funções que convertem valores de DateTime](../logic-apps/workflow-definition-language-functions-reference.md), essa função JavaScript de amostra básica mostra como você pode acessar uma propriedade dentro de um objeto de solicitação transmitido à função e executar operações nesse valor de propriedade. Para acessar propriedades dentro de objetos, este exemplo usa o [operador ponto (.)](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/Property_accessors): 

```javascript
function convertToDateString(request, response){
   var data = request.body;
   response = {
      body: data.date.ToDateString();
   }
}
```

Aqui está o que acontece dentro dessa função:

1. A função cria uma variável `data` e atribui o objeto `body` dentro do objeto `request` a essa variável. A função usa o operador ponto (.) para fazer referência a `body` dentro do objeto a `request` objeto: 

   ```javascript
   var data = request.body;
   ```

2. A função agora pode acessar a propriedade `date` por meio da variável `data` e converter esse valor de propriedade do tipo DateTime para o tipo DateString chamando a função `ToDateString()`. A função também retorna o resultado através da propriedade `body` na resposta da função: 

   ```javascript
   body: data.date.ToDateString();
   ```

Agora que você criou sua função do Azure, siga as etapas de como [ adicionar funções a aplicativos lógicos ](#add-function-logic-app).

<a name="create-function-designer"></a>

## <a name="create-functions-inside-logic-apps"></a>Criar funções dentro de aplicativos lógicos

Antes de poder criar uma função do Azure a partir de dentro de seu aplicativo lógico no Logic App Designer, você deve primeiro ter um aplicativo de função do Azure, que é um contêiner para suas funções. Se você não tiver um aplicativo de funções, crie primeiro o aplicativo de função. Veja [Crie sua primeira função no portal do Azure](../azure-functions/functions-create-first-azure-function.md). 

1. No <a href="https://portal.azure.com" target="_blank">portal do Azure</a>, abra o aplicativo lógico no Designer do aplicativo lógico. 

2. Na etapa onde você deseja criar e adicionar a função, escolha **nova etapa** > **adicionar uma ação**. 

3. Na caixa de pesquisa, insira "funções azure" como seu filtro.
Na lista de ações, selecione esta ação: **Escolha uma função do Azure - Funções do Azure** 

   ![Localizar "Funções do Azure"](./media/logic-apps-azure-functions/find-azure-functions-action.png)

4. Na lista de aplicativos de função, selecione seu aplicativo de função. Depois que as ações de listam é aberta, selecione esta ação: **funções do Azure – criar nova função**

   ![Selecione seu aplicativo de funções](./media/logic-apps-azure-functions/select-function-app-create-function.png)

5. No editor de definição de função, defina sua função:

   1. Na caixa **Nome da função**, forneça um nome para sua função. 

   2. Na caixa **Código**, adicione seu código de função ao modelo, incluindo a resposta e a carga útil que você deseja que sejam retornadas ao seu aplicativo lógico depois que sua função terminar de ser executada. 
   O objeto de contexto no código do modelo descreve a mensagem e o conteúdo que seu aplicativo lógico transmite para sua função, por exemplo:

      ![Definir sua função](./media/logic-apps-azure-functions/function-definition.png)

      Dentro de sua função, você pode fazer referência às propriedades no objeto de contexto usando esta sintaxe:

      ```text
      context.<token-name>.<property-name>
      ```
      Neste exemplo, aqui está a sintaxe que você usaria:

      ```text
      context.body.content
      ```

   3. Quando terminar, escolha **Criar**.

6. Na caixa **Request Body**, especifique o objeto de contexto a ser passado como a entrada de sua função, que deve ser formatada em JavaScript Object Notation (JSON). Quando você clica na caixa **Request Body**, a lista de conteúdo dinâmico é aberta para que você possa selecionar os tokens das propriedades disponíveis nas etapas anteriores. 

   Este exemplo passa o objeto de **corpo** token de gatilho de e-mail:  

   ![Exemplo de "Corpo da solicitação" – o conteúdo do objeto de contexto](./media/logic-apps-azure-functions/function-request-body-example.png)

   Com base no conteúdo de um objeto de contexto, o Designer do aplicativo lógico gera um modelo de função que você pode editar em linha. 
   O Logic Apps também cria variáveis com base no objeto de contexto de entrada.

   Neste exemplo, o objeto de contexto não é convertido como uma string, portanto, o conteúdo é adicionado diretamente à carga útil do JSON. 
   No entanto, se o objeto não for um token JSON, que deve ser uma cadeia de caracteres, de um objeto JSON ou em uma matriz JSON, você obterá um erro. 
   Para converter o objeto de contexto como uma cadeia de caracteres, adicione marcas de aspas duplas, por exemplo:

   ![Converter um objeto como cadeia de caracteres](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

7. Para especificar outros detalhes como o método de uso, os cabeçalhos de solicitação ou parâmetros de consulta, escolha **Mostrar opções avançadas**.

<a name="add-function-logic-app"></a>

## <a name="add-existing-functions-to-logic-apps"></a>Adicionar funções existentes para aplicativos lógicos

Para chamar funções existentes do Azure de seus aplicativos lógicos, você pode adicionar funções do Azure como qualquer outra ação no Logic App Designer. 

1. No <a href="https://portal.azure.com" target="_blank">portal do Azure</a>, abra o aplicativo lógico no Designer do aplicativo lógico. 

2. Na etapa em que você deseja adicionar a função, escolha **Nova etapa** > **Adicione uma ação**. 

3. Na caixa de pesquisa, insira "funções azure" como seu filtro.
Na lista de ações, selecione esta ação: **Escolha uma função do Azure - Funções do Azure** 

   ![Localizar "Funções do Azure"](./media/logic-apps-azure-functions/find-azure-functions-action.png)

4. Na lista de aplicativos de função, selecione seu aplicativo de função. Depois que a lista de funções aparecer, selecione sua função. 

   ![Selecione seu aplicativo de função e a função do Azure](./media/logic-apps-azure-functions/select-function-app-existing-function.png)

   Para funções que têm definições de API (descrições de Swagger) e que são [configuradas para que seu aplicativo lógico possa encontrar e acessar essas funções](#function-swagger), é possível selecionar **Ações de Swagger**:

   ![Selecione seu aplicativo de função, "Ações de Swagger" "e sua função do Azure](./media/logic-apps-azure-functions/select-function-app-existing-function-swagger.png)

5. Na caixa **Request Body**, especifique o objeto de contexto a ser passado como a entrada de sua função, que deve ser formatada em JavaScript Object Notation (JSON). Este objeto de contexto descreve a mensagem e o conteúdo que seu aplicativo lógico envia para sua função. 

   Quando você clica na caixa **Request Body**, a lista de conteúdo dinâmico é aberta para que você possa selecionar os tokens das propriedades disponíveis nas etapas anteriores. 
   Este exemplo passa o objeto de **corpo** token de gatilho de e-mail:

   ![Exemplo de "Corpo da solicitação" – o conteúdo do objeto de contexto](./media/logic-apps-azure-functions/function-request-body-example.png)

   Neste exemplo, o objeto de contexto não é convertido como uma string, portanto, o conteúdo é adicionado diretamente à carga útil do JSON. 
   No entanto, se o objeto não for um token JSON, que deve ser uma cadeia de caracteres, de um objeto JSON ou em uma matriz JSON, você obterá um erro. 
   Para converter o objeto de contexto como uma cadeia de caracteres, adicione marcas de aspas duplas, por exemplo:

   ![Converter um objeto como cadeia de caracteres](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

6. Para especificar outros detalhes como o método de uso, os cabeçalhos de solicitação ou parâmetros de consulta, escolha **Mostrar opções avançadas**.

<a name="call-logic-app"></a>

## <a name="call-logic-apps-from-functions"></a>Chamar aplicativos lógicos de funções

Para acionar um aplicativo lógico de dentro de uma função do Azure, esse aplicativo lógico deve ter um ponto de extremidade que possa ser chamado ou, mais especificamente, um gatilho **Solicitação**. Em seguida, de dentro de sua função, envie uma solicitação HTTP POST para a URL para que **solicitação** disparar e incluir o conteúdo que você deseja que esse aplicativo lógico para processar. Para obter mais informações, consulte [Chamar, acionar ou aninhar aplicativos lógicos](../logic-apps/logic-apps-http-endpoint.md). 

## <a name="get-support"></a>Obtenha suporte

* Em caso de dúvidas, visite o [Fórum dos Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para enviar ou votar em ideias de recurso, visite o [site de comentários do usuário de Aplicativos Lógicos](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [Conectores de Aplicativos Lógicos](../connectors/apis-list.md)
