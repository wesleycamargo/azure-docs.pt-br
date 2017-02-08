---
title: "Automatizar a implantação de recursos no Azure Functions | Microsoft Docs"
description: "Saiba como criar um modelo do Azure Resource Manager que implanta o Aplicativo de Funções para o Microsoft Azure."
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
ms.sourcegitcommit: d11ef8865d21dfe4e56bcb6fa08ce58c53f3d309
ms.openlocfilehash: 7c55a1d34df71c2c958f42f43810fbce7bd74e5d


---

# <a name="automate-the-deployment-of-resources-for-your-azure-functions-app"></a>Automatizar a implantação de recursos para seu aplicativo do Azure Functions

Neste tópico, você aprenderá a criar um modelo do Azure Resource Manager que implanta um aplicativo de funções. Você aprenderá como definir a linha de base de recursos necessários para uma função do Azure e os parâmetros que são especificados quando a implantação é executada. Dependendo do [disparadores e associações](functions-triggers-bindings.md) que são usadas em sua função, você pode exigir recursos adicionais de implantação para abranger todo o seu aplicativo como infraestrutura como código.

Para saber mais sobre a criação de modelos, veja [Criação de Modelos do Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md)

Para obter exemplos de um modelo completo, [Criar função do Azure baseado em consumo](https://github.com/Azure/azure-quickstart-templates/blob/052db5feeba11f85d57f170d8202123511f72044/101-function-app-create-dynamic/azuredeploy.json) e/ou [criar uma função do Azure do plano do serviço de aplicativo com base em](https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json)

## <a name="what-is-deployed"></a>O que é implantado

Com os exemplos abaixo, você criará uma aplicativo de função do Azure da linha de base. Os recursos necessários para um aplicativo de função são as seguintes:

* Conta do [Armazenamento do Azure](../storage/index.md)
* Plano de hospedagem (plano de consumo ou plano do serviço de aplicativo)
* Função de aplicativo (Microsoft.Web/Site de tipo **functionapp**)

## <a name="parameters"></a>Parâmetros

Com o Gerenciador de Recursos do Azure, você define parâmetros para os valores que deseja especificar quando o modelo é implantado. O modelo inclui uma seção chamada Parâmetros, que contém todos os valores de parâmetro. Você deve definir parâmetros para os valores que variam de acordo com o projeto que você está implantando ou com o ambiente em que a implantação ocorre.

[Variáveis](../azure-resource-manager/resource-group-authoring-templates.md#variables) são úteis para valores que não são alterados com base em uma implantação individual ou parâmetros que requerem transformação antes usados em um modelo (por exemplo, para passar as regras de validação).

Ao definir parâmetros, use o campo **allowedValues** para especificar quais valores um usuário pode fornecer durante a implantação. Use o campo **defaultValue** para atribuir um valor para o parâmetro, se nenhum valor for fornecido durante a implantação.

Vamos descrever os parâmetros para nosso modelo.

### <a name="appname"></a>appName

O nome da função que você deseja criar.

```json
"appName": {
    "type": "string"
}
```

### <a name="location"></a>location

O local no qual deseja implantar o aplicativo de função.

> [!NOTE]
> O **defaultValue** parâmetro é usado para herdar o local do grupo de recursos, ou se um valor de parâmetro não for especificado durante a implantação do Powershell ou CLI. Se a implantação do Portal, uma caixa suspensa é fornecida para selecionar o **allowedValues**.

> [!TIP]
> Para obter uma lista atualizada de regiões do Azure Functions está disponível em visite o [produtos disponíveis por região](https://azure.microsoft.com/regions/services/) página.

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
        "description": "Sourcecode Repo branch"
      }
    }
```

### <a name="sourcecodemanualintegration-optional"></a>sourceCodeManualIntegration (opcional)

```json
"sourceCodeManualIntegration": {
    "type": "bool",
    "defaultValue": false,
    "metadata": {
        "description": "Use 'true' if you are deploying from the base repo, 'false' if you are deploying from your own fork. If you're using 'false', make sure you have admin permissions to the repo. If you get an error, you should add GitHub integration to another web app manually, so that you get a GitHub access token associated with your Azure Subscription."
    }
}
```

## <a name="variables"></a>Variáveis

Além dos parâmetros, modelos do Azure Resource Manager também têm um conceito de variáveis que podem incorporar parâmetros para criar configurações mais específicas a ser usado pelo seu modelo.

No exemplo abaixo, você pode ver que estamos aproveitando variáveis para serem aplicadas [funções de modelo do Azure Resource Manager](../azure-resource-manager/resource-group-template-functions.md) pegar o nome do aplicativo fornecido e convertê-la em minúsculas para garantir que o [requisitos de nomenclatura](../storage/storage-create-storage-account.md#create-a-storage-account) do armazenamento do Azure contas forem atendidas.

```json
"variables": {
    "lowerSiteName": "[toLower(parameters('appName'))]",
    "storageAccountName": "[concat(variables('lowerSiteName'))]"
}
```

## <a name="resources-to-deploy"></a>Recursos a implantar

### <a name="storage-account"></a>Conta de armazenamento

Uma conta de armazenamento do Azure é um recurso necessário no Azure Functions.

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

### <a name="hosting-plan-consumption-vs-app-service-plan"></a>Plano de hospedagem: plano do serviço de aplicativo do consumo vs

Há situações em que a criação de funções em que convém suas funções para ser totalmente gerenciados dimensionamento ou seja dimensionada sob demanda pela plataforma (consumo). Como alternativa, você pode escolher o dimensionamento de usuário gerenciado no qual as funções executam 24/7 em hardware dedicado (plano do serviço de aplicativo) em que o número de instâncias pode ser manual ou automaticamente configurado. A decisão de usar um plano em vez de outro pode se basear recursos disponíveis no plano, ou uma decisão que é orientada pela arquitetura pelo custo. Para saber mais sobre os diferentes planos de hospedagem, leia o artigo [Dimensionamento do Azure Functions](functions-scale.md).

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

#### <a name="app-service-plan"></a>Plano do serviço de aplicativo

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

### <a name="function-app-site"></a>Aplicativo de funções (site)

Depois que tiver sido selecionada a opção de escala, é hora de criar o contêiner que manterá todas as suas funções, isso é conhecido como o aplicativo de função.

Um aplicativo de função tem muitos recursos filho no qual você pode tirar proveito de inclusão **configurações do aplicativo** e **opções de controle do código-fonte**. Você pode optar por remover o **sourcecontrols** recurso filho em favor de outro [opção de implantação](functions-continuous-deployment.md).

> [!IMPORTANT]
> É importante entender como os recursos são implantados no Azure para garantir que você crie uma infra-estrutura bem-sucedida como configuração de código de seu aplicativo ao usar o Azure Resource Manager. Neste exemplo, você observará que existem configurações de nível superior que está sendo aplicada usando **siteConfig**, eles são importantes para definir um nível superior, conforme eles transmitem significar ao mecanismo de tempo de execução e a implantação do Azure Functions que são necessários antes do recurso de filho **sourcecontrols/web** é aplicado. Enquanto essas configurações podem ser configuradas no recurso de nível filho **config/appSettings**, há cenários em que seu aplicativo de função e funções precisará ser implantada *antes de* o **config/appsettings** são aplicadas como suas funções são uma dependência de outro recurso, por exemplo um [aplicativo lógico](../logic-apps/index.md).

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
> Este modelo usa o [projeto](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file) configuração do aplicativo, que é a configuração do diretório base no qual o mecanismo de implantação de funções (Kudu) irá procurar código implantável. Neste exemplo, definimos esse valor como `src` já que nosso GitHub repositório contém uma pasta `src` da qual nossas funções são filhas. Se você tiver um repositório no qual as funções são diretamente na raiz do repositório, ou não estiver implantando do controle de origem, essa configuração de aplicativo podem ser removida.

## <a name="deploying-your-template"></a>Implantando o modelo

* [Powershell](../azure-resource-manager/resource-group-template-deploy.md)
* [CLI](../azure-resource-manager/resource-group-template-deploy-cli.md)
* [Portal](../azure-resource-manager/resource-group-template-deploy-portal.md)
* [API REST](../azure-resource-manager/resource-group-template-deploy-rest.md)

### <a name="deploy-to-azure-button"></a>Botão Implantar no Azure

Substitua ```<url-encoded-path-to-azuredeploy-json>``` com um [codificada de URL](https://www.bing.com/search?q=url+encode) versão do caminho raw de seu `azuredeploy.json` arquivo no GitHub.

#### <a name="markdown"></a>Markdown

```markdown
[![Deploy to Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>)
```

#### <a name="html"></a>HTML

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"></a>
```

## <a name="next-steps"></a>Próximas etapas

Agora que você tem a capacidade de implantar um aplicativo de função no código, aproveite a oportunidade para saber mais sobre como desenvolver e configurar o Azure Functions:

* [Referência do desenvolvedor do Azure Functions](functions-reference.md)
* [Como definir configurações de aplicativos do Azure Functions](functions-how-to-use-azure-function-app-settings.md)
* [Criar sua primeira Função do Azure](functions-create-first-azure-function.md)


<!--HONumber=Jan17_HO4-->


