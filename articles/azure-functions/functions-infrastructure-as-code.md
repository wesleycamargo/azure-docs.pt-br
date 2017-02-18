---
title: "Como automatizar a implantação de recursos no aplicativo Azure Functions | Microsoft Docs"
description: Aprenda a criar um modelo do Azure Resource Manager que implanta o aplicativo Azure Functions.
services: Functions
documtationcenter: na
author: mattchenderson
manager: erikre
editor: 
tags: 
keywords: "azure functions, funções, arquitetura sem servidores, infraestrutura como código, azure resource manager"
ms.assetid: 
ms.server: functions
ms.devlang: multiple
ms.topic: 
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/23/2017
ms.author: cfowler;glenga
translationtype: Human Translation
ms.sourcegitcommit: 360abaa575e473e18e55d0784730f4bd5635f3eb
ms.openlocfilehash: 979537bfe6b0e14a9208871fc9862661d2fb2e6c


---

# <a name="automate-resource-deployment-for-your-azure-functions-app"></a>Como automatizar a implantação de recursos no aplicativo Azure Functions

Você pode usar um Azure Resource Manager para implantar um aplicativo Azure Functions. Aprenda a definir a linha de base de recursos necessários para um aplicativo Azure Functions e os parâmetros especificados durante a implantação. Dependendo dos [disparadores e associações](functions-triggers-bindings.md) no seu aplicativo de funções, para uma infraestrutura bem-sucedida, como configuração de código do seu aplicativo, você talvez precise implantar recursos adicionais.

Para saber mais sobre a criação de modelos, consulte [Criação de modelos do Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

Para ver exemplos de modelos completos, veja [Criar um aplicativo Azure Functions com base no plano de consumo](https://github.com/Azure/azure-quickstart-templates/blob/052db5feeba11f85d57f170d8202123511f72044/101-function-app-create-dynamic/azuredeploy.json) e [Criar um aplicativo Azure Functions com base no plano do serviço de aplicativo](https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json).

## <a name="required-resources"></a>Recursos necessários

Você pode usar os exemplos neste artigo para criar um aplicativo Azure Functions de linha de base. Você precisará desses recursos para o seu aplicativo:

* Conta de [Armazenamento do Azure](../storage/index.md)
* Plano de hospedagem (plano de consumo ou plano do Serviço de Aplicativo do Azure)
* Aplicativo de funções (`type`: **Microsoft.Web/Site**, `kind`: **functionapp**)

## <a name="parameters"></a>parameters

Você pode usar o Azure Resource Manager para definir parâmetros para os valores que deseja especificar quando o modelo é implantado. Uma seção de **parâmetros** de modelo tem todos os valores de parâmetro. Defina os parâmetros para os valores que variam de acordo com o projeto que você está implantando ou com o ambiente em que a implantação ocorre.

[Variáveis](../azure-resource-manager/resource-group-authoring-templates.md#variables) são úteis para valores que não são alterados com base em uma implantação individual, e para parâmetros que requerem transformação antes de serem usados em um modelo (por exemplo, para passar as regras de validação).

Ao definir parâmetros, use o campo **allowedValues** para especificar quais valores um usuário pode fornecer durante a implantação. Use o campo **defaultValue** para atribuir um valor para o parâmetro, se nenhum valor for fornecido durante a implantação.

Um modelo do Azure Resource Manager requer os seguintes valores de parâmetros.

### <a name="appname"></a>appName

O nome do aplicativo Azure Functions que você deseja criar.

```json
"appName": {
    "type": "string"
}
```

### <a name="location"></a>location

Onde implantar o aplicativo de funções.

> [!NOTE]
> Use o parâmetro **defaultValue** para herdar o local do grupo de recursos, ou se um valor de parâmetro não for especificado durante a implantação do Powershell ou CLI do Azure . Se você implantar seu aplicativo no portal do Azure, selecione um valor na caixa da lista suspensa de parâmetros **allowedValues**.

> [!TIP]
> Para obter uma lista atualizada de regiões onde você pode usar o Azure Functions, acesse a página [Produtos disponíveis por região](https://azure.microsoft.com/regions/services/).

```json
"location": {
    "type": "string",
    "allowedValues": [
        "Brazil South",
        "East US",
        "East US 2",
        "Central US",
        "North Central US",
        "South Central US",
        "West US",
        "West US 2"
    ],
    "defaultValue": "[resourceGroup().location]"
}
```

### <a name="sourcecoderepositoryurl-optional"></a>sourceCodeRepositoryURL (opcional)

```json
"sourceCodeRepositoryURL": {
    "type": "string",
    "defaultValue": "",
    "metadata": {
    "description": "Source code repository URL"
}
```

### <a name="sourcecodebranch-optional"></a>sourceCodeBranch (opcional)

```json
    "sourceCodeBranch": {
      "type": "string",
      "defaultValue": "master",
      "metadata": {
        "description": "Source code repository branch"
      }
    }
```

### <a name="sourcecodemanualintegration-optional"></a>sourceCodeManualIntegration (opcional)

```json
"sourceCodeManualIntegration": {
    "type": "bool",
    "defaultValue": false,
    "metadata": {
        "description": "Use 'true' if you are deploying from the base repo. Use 'false' if you are deploying from your own fork. If you use 'false', make sure that you have Administrator rights in the repo. If you get an error, manually add GitHub integration to another web app, to associate a GitHub access token with your Azure subscription."
    }
}
```

## <a name="variables"></a>variáveis

Modelos do Azure Resource Manager usam variáveis para incorporar os parâmetros, então você pode usar configurações mais específicas em seu modelo.

No próximo exemplo, para atender aos [requisitos de nomenclatura](../storage/storage-create-storage-account.md#create-a-storage-account) da conta de armazenamento do Azure, usamos variáveis para aplicar as [funções de modelo do Azure Resource Manager](../azure-resource-manager/resource-group-template-functions.md) para converter o valor **appName** inserido em minúsculas.

```json
"variables": {
    "lowerSiteName": "[toLower(parameters('appName'))]",
    "storageAccountName": "[concat(variables('lowerSiteName'))]"
}
```

## <a name="resources-to-deploy"></a>Recursos a implantar

### <a name="storage-account"></a>Conta de armazenamento

Uma conta de armazenamento do Azure é necessária para o aplicativo Azure Functions.

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2015-05-01-preview",
    "location": "[variables('storageLocation')]",
    "properties": {
        "accountType": "[variables('storageAccountType')]"
    }
}
```

### <a name="hosting-plan-consumption-vs-app-service"></a>Plano de hospedagem: consumo x Serviço de Aplicativo

Em alguns cenários, convém dimensionar suas funções sob demanda pela plataforma, também chamado de dimensionamento totalmente gerenciado (usando um plano de hospedagem de consumo). Ou você pode escolher dimensionamento gerenciado pelo usuário para as suas funções. Em dimensionamento gerenciado pelo usuário, as suas funções são executadas 24x7 em um hardware dedicado (usando um plano de hospedagem de serviço de aplicativo). O número de instâncias pode ser definido manual ou automaticamente. A escolha do plano de hospedagem pode ser baseada nos recursos disponíveis no plano ou na arquitetura por custo. Confira [dimensionamento do Azure Functions](functions-scale.md) para saber mais sobre planos de hospedagem.

#### <a name="consumption-plan"></a>Plano de consumo

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "computeMode": "Dynamic",
        "sku": "Dynamic"
    }
}
```

#### <a name="app-service-plan"></a>Plano do Serviço de Aplicativo

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "sku": "[parameters('sku')]",
        "workerSize": "[parameters('workerSize')]",
        "hostingEnvironment": "",
        "numberOfWorkers": 1
    }
}
```

### <a name="functions-app-a-site"></a>Aplicativo de funções (em um site)

Depois de selecionar uma opção de dimensionamento, crie um aplicativo de funções. O aplicativo é o contêiner que mantém todas as suas funções.

Um aplicativo de funções tem muitos recursos filho que podem ser usados na sua implantação, incluindo configurações do aplicativo e opções de controle de código-fonte. Você também pode optar por remover o recurso filho **sourcecontrols** e usar uma outra [opção de implantação](functions-continuous-deployment.md).

> [!IMPORTANT]
> É importante entender como os recursos são implantados no Azure para que você possa criar uma infraestrutura bem-sucedida como configuração do código do seu aplicativo ao usar o Azure Resource Manager. No exemplo a seguir, as configurações de nível superior são aplicadas usando **siteConfig**. É importante definir essas configurações em um nível superior porque transmitem informações para o mecanismo de implantação e de tempo de execução do Azure Functions. Informações de nível superior são necessárias antes do recurso filho **sourcecontrols/web** ser aplicado. Embora seja possível definir essas configurações no nível de recurso filho **config/appSettings**, em alguns cenários, o aplicativo de funções e as funções precisam ser implantados *antes* de **config/appSettings** ser aplicado. Nesses casos, por exemplo, em [Aplicativos Lógicos](../logic-apps/index.md), suas funções são uma dependência de outro recurso.

```json
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('appName')]",
  "type": "Microsoft.Web/sites",
  "kind": "functionapp",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Web/serverfarms', parameters('appName'))]"
  ],
  "properties": {
     "serverFarmId": "[variables('appServicePlanName')]",
     "siteConfig": {
        "alwaysOn": true,
        "appSettings": [
            { "name": "FUNCTIONS_EXTENSION_VERSION", "value": "~1" },
            { "name": "Project", "value": "src" }
        ]
     }
  },
  "resources": [
     {
        "apiVersion": "2015-08-01",
        "name": "appsettings",
        "type": "config",
        "dependsOn": [
          "[resourceId('Microsoft.Web/Sites', parameters('appName'))]",
          "[resourceId('Microsoft.Web/Sites/sourcecontrols', parameters('appName'), 'web')]",
          "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
        ],
        "properties": {
          "AzureWebJobsStorage": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]",
          "AzureWebJobsDashboard": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
        }
     },
     {
          "apiVersion": "2015-08-01",
          "name": "web",
          "type": "sourcecontrols",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites/', parameters('appName'))]"
          ],
          "properties": {
            "RepoUrl": "[parameters('sourceCodeRepositoryURL')]",
            "branch": "[parameters('sourceCodeBranch')]",
            "IsManualIntegration": "[parameters('sourceCodeManualIntegration')]"
          }
     }
  ]
}
```
> [!TIP]
> Este modelo usa o valor das configurações do aplicativo [Projeto](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file), que determinam o diretório-base cujo mecanismo de implantação de funções (Kudu) procura o código implantável. Em nosso repositório, nossas funções estão em uma subpasta da pasta **src**. Assim, no exemplo anterior, definimos o valor de configurações do aplicativo para `src`. Se as funções estão na raiz do seu repositório, ou se não está implantando do controle de origem, você pode remover esse valor de configurações do aplicativo.

## <a name="deploy-your-template"></a>Implantar o modelo

* [PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
* [CLI do Azure](../azure-resource-manager/resource-group-template-deploy-cli.md)
* [Portal do Azure](../azure-resource-manager/resource-group-template-deploy-portal.md)
* [API REST](../azure-resource-manager/resource-group-template-deploy-rest.md)

### <a name="deploy-to-azure-button"></a>Botão Implantar no Azure

Substitua ```<url-encoded-path-to-azuredeploy-json>``` por uma versão [codificada de URL](https://www.bing.com/search?q=url+encode) do caminho bruto de seu `azuredeploy.json` arquivo no GitHub.

#### <a name="markdown"></a>Markdown

```markdown
[![Deploy to Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>)
```

#### <a name="html"></a>HTML

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"></a>
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como desenvolver e configurar o Azure Functions.

* [Referência do desenvolvedor do Azure Functions](functions-reference.md)
* [Como definir configurações de aplicativos do Azure Functions](functions-how-to-use-azure-function-app-settings.md)
* [Como criar a sua primeira função do Azure](functions-create-first-azure-function.md)



<!--HONumber=Feb17_HO1-->


