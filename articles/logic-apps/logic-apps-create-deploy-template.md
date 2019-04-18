---
title: Criar modelos de implantação para Aplicativos Lógicos do Azure | Microsoft Docs
description: Criar modelos do Azure Resource Manager para implantar aplicativos lógicos
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.assetid: 85928ec6-d7cb-488e-926e-2e5db89508ee
ms.date: 10/18/2016
ms.openlocfilehash: 624539557b0bf57e9d919a3a46337f1cf93a4f07
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "58894227"
---
# <a name="create-azure-resource-manager-templates-for-deploying-logic-apps"></a>Criar modelos do Azure Resource Manager para implantar aplicativos lógicos

Quando você cria um aplicativo lógico, você pode expandir a definição do aplicativo lógico em um [modelo do Resource Manager](../azure-resource-manager/resource-group-overview.md). Em seguida, você pode usar este modelo para automatizar implantações definindo os recursos e parâmetros que você deseja usar para a implantação e fornecendo os valores de parâmetro por meio de um [arquivo de parâmetros](../azure-resource-manager/resource-group-template-deploy.md#parameter-files).
Dessa forma, você pode implantar aplicativos lógicos mais facilmente e em qualquer ambiente ou o grupo de recursos do Azure que você deseja. 

Os aplicativos lógicos do Azure fornecem uma [modelo de Azure Resource Manager de aplicativos lógicos predefinidos](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json) que você pode reutilizar, não apenas para a criação de aplicativos lógicos, mas também para definir os recursos e os parâmetros a serem usados para implantação. Você pode usar esse modelo para seus próprios cenários de negócios ou personalize o modelo para atender às suas necessidades. Saiba mais sobre [estrutura de modelo do Resource Manager e a sintaxe](../azure-resource-manager/resource-group-authoring-templates.md). Saiba mais sobre a sintaxe e as propriedades de JSON em [tipos de recurso do Microsoft.Logic](/azure/templates/microsoft.logic/allversions).

Para obter mais informações sobre modelos do Azure Resource Manager, consulte estes artigos:

* [Criar modelos do Gerenciador de Recursos do Azure](../azure-resource-manager/resource-group-authoring-templates.md)
* [Desenvolver modelos do Azure Resource Manager para consistência de nuvem](../azure-resource-manager/templates-cloud-consistency.md)

## <a name="logic-app-structure"></a>Estrutura de aplicativo lógico

Definição de aplicativo lógico tem as seguintes seções básicas, o que você pode exibir ao alternar da "exibição de designer" para "exibição de código", ou usando uma ferramenta como [Azure Resource Explorer](http://resources.azure.com). Definições de aplicativo lógico usar notação JSON (Javascript Object), portanto, para obter mais informações sobre a sintaxe JSON e propriedades, consulte [tipos de recurso Logic](/azure/templates/microsoft.logic/allversions).

* **Recurso de aplicativo lógico**: Descreve informações como o local do seu aplicativo lógico ou uma região, plano de preços e definição de fluxo de trabalho.

* **Definição de fluxo de trabalho**: Descreve o do seu aplicativo lógico gatilhos e ações e como o serviço de aplicativo lógico do Azure executa o fluxo de trabalho. Na exibição de código, você pode encontrar a definição de fluxo de trabalho no `definition` seção.

* **Conexões**: Se você usar conectores gerenciados em seu aplicativo lógico, o `$connections` seção faz referência a outros recursos que armazenam com segurança os metadados sobre essas conexões entre seu aplicativo lógico e outros sistemas ou serviços, como cadeias de caracteres de conexão e tokens de acesso. Dentro de sua definição de aplicativo lógico, as referências a essas conexões aparecem dentro de sua definição de aplicativo lógico `parameters` seção.

Para criar um modelo de aplicativo lógico que você pode usar com implantações do grupo de recursos do Azure, você deve definir os recursos e parametrizar conforme necessário. Por exemplo, se você estiver implantando em um ambiente de produção, desenvolvimento e teste, provavelmente, desejará usar cadeias de conexão diferentes para um banco de dados SQL em cada ambiente.
Ou talvez você queira fazer implantações em diferentes assinaturas ou grupos de recursos.

## <a name="create-logic-app-deployment-templates"></a>Criar modelos de implantação de aplicativo de lógica

Para a maneira mais fácil de criar um modelo de implantação do aplicativo lógico válido, use o Visual Studio e as ferramentas de aplicativos lógicos do Azure para a extensão do Visual Studio. Ao baixar o aplicativo lógico do portal do Azure no Visual Studio, você obtém um modelo de implantação válido que você pode usar com qualquer assinatura do Azure e local. Além disso, baixar automaticamente o seu aplicativo lógico parametriza a definição do aplicativo lógico é inserida no modelo.
Para obter mais informações sobre como criar e gerenciar aplicativos lógicos no Visual Studio, consulte [criar aplicativos lógicos com o Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) e [gerenciar aplicativos lógicos com o Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).

Diferente do Visual Studio ou criando manualmente o seu modelo e os parâmetros necessários, seguindo as diretrizes neste tópico, você também pode usar o [módulo do PowerShell para criar modelos de aplicativo de lógica](https://github.com/jeffhollan/LogicAppTemplateCreator). Esse módulo de código-fonte aberto primeiro avalia seu aplicativo lógico e todas as conexões que usa o aplicativo lógico. O módulo, em seguida, gera recursos de modelo com os parâmetros necessários para a implantação. Por exemplo, suponha que você tem um aplicativo lógico que recebe uma mensagem de uma fila do barramento de serviço do Azure e adiciona dados a um banco de dados SQL do Azure. A ferramenta de módulo preserva toda a lógica de orquestração e parametrizará as cadeias de conexão SQL e do barramento de serviço para que você possa definir esses valores na implantação.

> [!IMPORTANT]
> Conexões devem existir no mesmo grupo de recursos do Azure que o aplicativo lógico.
> Para o módulo do PowerShell trabalhar com qualquer uso do Azure locatário e a assinatura access token, o módulo com o [ferramenta de cliente do Azure Resource Manager](https://github.com/projectkudu/ARMClient). Para obter mais informações, consulte este [artigo sobre a ferramenta de cliente do Azure Resource Manager](https://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) discute o ARMClient mais detalhadamente.

### <a name="install-powershell-module-for-logic-app-templates"></a>Instalar o módulo do PowerShell para modelos de aplicativo lógico

Para a maneira mais fácil de instalar o módulo com o [da Galeria do PowerShell](https://www.powershellgallery.com/packages/LogicAppTemplate/0.1), use este comando:

`Install-Module -Name LogicAppTemplate`

Você também pode instalar manualmente o módulo do PowerShell:

1. Baixe o versão mais recente [criador de modelos de aplicativo lógico](https://github.com/jeffhollan/LogicAppTemplateCreator/releases).

1. Extraia a pasta na pasta de módulo do PowerShell, que geralmente é `%UserProfile%\Documents\WindowsPowerShell\Modules`.

### <a name="generate-logic-app-template---powershell"></a>Gerar modelo de aplicativo lógico - PowerShell

Depois que o PowerShell for instalado, você poderá gerar um modelo usando o seguinte comando:

`armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp MyApp -ResourceGroup MyRG -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json`

`$SubscriptionId` é a ID da assinatura do Azure. Essa linha primeiro obtém um token de acesso via ARMClient, depois redireciona o script do PowerShell e, em seguida, cria o modelo em um arquivo JSON.

## <a name="parameters-in-logic-app-templates"></a>Parâmetros em modelos de aplicativo lógico

Depois de criar seu modelo de aplicativo lógico, você pode adicionar e editar todos os parâmetros necessários. O modelo tiver mais de um `parameters` seção, por exemplo: 

* Definição de fluxo de trabalho do aplicativo lógico tem seu próprio [ `parameters` seção](../logic-apps/logic-apps-workflow-definition-language.md#parameters) onde é possível definir todos os parâmetros que seu aplicativo lógico usa para aceitar entradas na implantação.

* Modelo do Resource Manager tem seu próprio [ `parameters` seção](../azure-resource-manager/resource-group-authoring-templates.md#parameters)separado do aplicativo lógico `parameters` seção. Por exemplo: 

  [!INCLUDE [logic-deploy-parameters](../../includes/app-service-logic-deploy-parameters.md)]

Por exemplo, suponha que sua definição de aplicativo lógico faz referência a uma ID de recurso que representa uma função do Azure ou um fluxo de trabalho do aplicativo lógico aninhado, e você deseja implantar essa ID de recurso junto com seu aplicativo lógico como uma única implantação. Você pode adicionar essa ID como um recurso em seu modelo e parametrizar esse ID. Você pode usar essa mesma abordagem para referências a pontos de extremidade personalizados de APIs ou OpenAPI (anteriormente conhecido como "Swagger") que você deseja que sejam distribuídas com cada grupo de recursos do Azure.

Ao usar parâmetros em seu modelo de implantação, siga estas diretrizes para que o Designer de aplicativos lógicos possa mostrar corretamente esses parâmetros:

* Use parâmetros somente nesses gatilhos e ações:

  * Aplicativo do Azure Functions
  * Aninhados ou fluxo de aplicativo lógico filho
  * Chamada de gerenciamento de API
  * URL do tempo de execução da conexão de API
  * Caminho de conexão da API

* Quando você define parâmetros, certifique-se de que você fornecer valores padrão usando o `defaultValue` valor da propriedade, por exemplo:

  ```json
  "parameters": {
     "IntegrationAccount": {
        "type":"string",
        "minLength": 1,
        "defaultValue": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource=group-name>/providers/Microsoft.Logic/integrationAccounts/<integration-account-name>"
     }
  },
  ```

* Para proteger ou ocultar informações confidenciais em modelos, proteja seus parâmetros. Saiba mais sobre [como usar parâmetros protegidos](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-workflow).

Aqui está um exemplo que mostra como é possível parametrizar a ação de "Enviar mensagem" do barramento de serviço do Azure:

```json
"Send_message": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['servicebus']['connectionId']"
         },
         // If the `host.runtimeUrl` property appears in your template, 
         // you can remove this property, which is optional, for example:
         "runtimeUrl": {}
      },
      "method": "POST",
      "path": "[concat('/@{encodeURIComponent(''', parameters('<Azure-Service-Bus-queue-name>'), ''')}/messages')]",
      "body": {
         "ContentData": "@{base64(triggerBody())}"
      },
      "queries": {
         "systemProperties": "None"
      }
   },
   "runAfter": {}
},
```

### <a name="reference-dependent-resources"></a>Recursos dependentes de referência

Se seu aplicativo lógico requer referências a recursos dependentes, você pode usar [funções de modelo do Azure Resource Manager](../azure-resource-manager/resource-group-template-functions.md) no modelo de implantação do aplicativo lógico. Por exemplo, suponha que você deseja que seu aplicativo lógico para fazer referência a uma função do Azure ou uma conta de integração com as definições para parceiros, contratos e outros artefatos que você deseja implantados juntamente com seu aplicativo lógico.
Você pode usar funções de modelo do Gerenciador de recursos, como `parameters`, `variables`, `resourceId`, `concat`e assim por diante.

Aqui está um exemplo que mostra como você pode substituir a ID de recurso para uma função do Azure definindo esses parâmetros:

``` json
"parameters": {
   "<Azure-function-name>": {
      "type": "string",
      "minLength": 1,
      "defaultValue": "<Azure-function-name>"
   }
},
```

Aqui está como você usar esses parâmetros ao fazer referência a função do Azure:

```json
"MyFunction": {
   "type": "Function",
   "inputs": {
      "body": {},
      "function": {
         "id":"[resourceid('Microsoft.Web/sites/functions','<Azure-Functions-app-name>', parameters('<Azure-function-name>'))]"
      }
   },
   "runAfter": {}
},
```

## <a name="add-logic-app-to-resource-group-project"></a>Adicionar o aplicativo lógico para o projeto do grupo de recursos

Se você tiver um projeto existente do grupo de recursos do Azure, você pode adicionar seu aplicativo lógico para o projeto usando a janela de estrutura de tópicos JSON. Você também pode adicionar outro aplicativo lógico junto com o aplicativo criado anteriormente.

1. No Gerenciador de Soluções, abra o arquivo `<template>.json`.

2. Dos **modo de exibição** menu, selecione **Other Windows** > **estrutura de tópicos JSON**.

3. Para adicionar um recurso ao arquivo de modelo, escolha **adicionar recurso** na parte superior da janela estrutura de tópicos JSON. Ou, na janela Estrutura de tópicos JSON, clique com o botão direito do mouse em **recursos**e selecione **Adicionar Novo Recurso**.

   ![Janela Estrutura de tópicos JSON](./media/logic-apps-create-deploy-template/jsonoutline.png)

4. Na caixa de diálogo **Adicionar Recurso**, localize e selecione **Aplicativo Lógico**. Nomeie seu aplicativo lógico e escolha **Adicionar**.

   ![Adicionar recurso](./media/logic-apps-create-deploy-template/addresource.png)

## <a name="get-support"></a>Obtenha suporte

Em caso de dúvidas, visite o [Fórum dos Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Implantar modelos de aplicativo lógico](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md)
