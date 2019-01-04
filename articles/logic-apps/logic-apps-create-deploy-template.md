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
ms.openlocfilehash: c953d4635ce43ee05fe7c507076c5bd11e9d8c81
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/03/2019
ms.locfileid: "54000556"
---
# <a name="create-azure-resource-manager-templates-for-deploying-logic-apps"></a>Criar modelos do Azure Resource Manager para implantar aplicativos lógicos

Após um aplicativo lógico ter sido criado, talvez você queira criá-lo como um modelo do Azure Resource Manager.
Dessa forma, você pode facilmente implantar o aplicativo lógico em qualquer ambiente ou grupo de recursos em que você possa precisar dele.
Para obter mais informações sobre modelos do Resource Manager, consulte [Criando modelos do Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) e [Implantando recursos usando modelos do Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="logic-app-deployment-template"></a>Modelo de implantação do aplicativo lógico

Um aplicativo lógico tem três componentes básicos:

* **Recurso de aplicativo lógico**: Contém informações sobre coisas como plano de preços, localização e definição do fluxo de trabalho.
* **Definição de fluxo de trabalho**: Descreve as etapas do fluxo de trabalho do aplicativo lógico e como o mecanismo dos Aplicativos Lógicos deve executar o fluxo de trabalho.
É possível exibir essa definição na janela **Modo de Exibição de Código** do aplicativo lógico.
No recurso do aplicativo lógico, é possível encontrar essa definição na propriedade `definition`.
* **Conexões**: Refere-se a recursos separados para armazenar com segurança os metadados sobre quaisquer conexões do conector, como uma cadeia de conexão e um token de acesso.
No recurso do aplicativo lógico, o aplicativo lógico referencia esses recursos na seção `parameters`.

É possível exibir todas essas partes de aplicativos lógicos existentes usando uma ferramenta como o [Gerenciador de Recursos do Azure](http://resources.azure.com).

Para criar um modelo para um aplicativo lógico a ser usado com implantações do grupo de recursos, é necessário definir os recursos e parametrizá-los, conforme necessário.
Por exemplo, se você estiver implantando em um ambiente de produção, desenvolvimento e teste, provavelmente, desejará usar cadeias de conexão diferentes para um banco de dados SQL em cada ambiente.
Ou talvez você queira fazer implantações em diferentes assinaturas ou grupos de recursos.  

## <a name="create-a-logic-app-deployment-template"></a>Criar um modelo de implantação do aplicativo lógico

A maneira mais fácil ter um modelo de implantação do aplicativo lógico válido é usar as [Ferramentas do Visual Studio para aplicativos lógicos](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites).
As ferramentas do Visual Studio geram um modelo de implantação válido que pode ser usado em qualquer assinatura ou local.

Algumas ferramentas podem ajudar você a criar um modelo de implantação de aplicativo lógico.
Você pode criar manualmente, ou seja, usando os recursos já discutidos aqui para criar parâmetros conforme necessário.
Você também pode usar um módulo do PowerShell do [criador de modelos de aplicativo lógico](https://github.com/jeffhollan/LogicAppTemplateCreator) . Este módulo de software livre primeiro avalia o aplicativo lógico e quaisquer conexões que ele esteja usando, depois gera recursos de modelo com os parâmetros necessários para a implantação.
Por exemplo, se você tiver um aplicativo lógico que recebe uma mensagem de uma fila do Barramento de Serviço do Azure e adiciona dados a um banco de dados SQL do Azure, a ferramenta preservará toda a lógica de orquestração e parametrizará as cadeias de conexão do SQL e do Barramento de Serviço para que elas possam ser definidas na implantação.

> [!NOTE]
> As conexões devem estar dentro do mesmo grupo de recursos que o aplicativo lógico.
>
>

### <a name="install-the-logic-app-template-powershell-module"></a>Instalar o módulo do PowerShell do modelo de aplicativo lógico
A maneira mais fácil de instalar o módulo é por meio da [Galeria do PowerShell](https://www.powershellgallery.com/packages/LogicAppTemplate/0.1), com o comando `Install-Module -Name LogicAppTemplate`.  

Você também pode instalar o módulo do PowerShell manualmente:

1. Baixe a versão mais recente do [criador de modelos de aplicativo lógico](https://github.com/jeffhollan/LogicAppTemplateCreator/releases).  
2. Extraia a pasta para sua pasta do módulo do PowerShell (geralmente `%UserProfile%\Documents\WindowsPowerShell\Modules`).

Para que o módulo funcione com qualquer token de acesso do locatário e da assinatura, recomendamos usá-lo com a ferramenta de linha de comando [ARMClient](https://github.com/projectkudu/ARMClient).  Esta [postagem no blog](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) aborda o ARMClient mais detalhadamente.

### <a name="generate-a-logic-app-template-by-using-powershell"></a>Gerar um modelo de aplicativo lógico usando o PowerShell
Depois que o PowerShell for instalado, você poderá gerar um modelo usando o seguinte comando:

`armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp MyApp -ResourceGroup MyRG -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json`

`$SubscriptionId` é a ID da assinatura do Azure. Essa linha primeiro obtém um token de acesso via ARMClient, depois redireciona o script do PowerShell e, em seguida, cria o modelo em um arquivo JSON.

## <a name="add-parameters-to-a-logic-app-template"></a>Adicionar parâmetros a um modelo de aplicativo lógico
Depois de criar o modelo de aplicativo lógico, você pode continuar a adicionar ou modificar os parâmetros de que pode precisar. Por exemplo, se sua definição incluir uma ID do recurso para uma função do Azure ou um fluxo de trabalho aninhado que você planeja colocar em uma única implantação, você poderá adicionar mais recursos ao modelo e parametrizar as IDs conforme necessário. O mesmo aplica-se a quaisquer referências às APIs personalizadas ou pontos de extremidade Swagger que você espera implantar com cada grupo de recursos.

### <a name="add-references-for-dependent-resources-to-visual-studio-deployment-templates"></a>Adicione referências de recursos dependentes para modelos de implantação do Visual Studio

Quando desejar que seu aplicativo lógico faça referência a recursos dependentes, use [Funções de modelo do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-functions) em seu modelo de implantação de aplicativo lógico. Por exemplo, talvez você queira seu aplicativo lógico para fazer referência a uma conta de integração ou da Função do Azure que você deseja implantar junto com seu aplicativo lógico. Siga estas diretrizes sobre como usar parâmetros em seu modelo de implantação para que o Designer de Aplicativo Lógico seja renderizado corretamente. 

Você pode usar parâmetros do aplicativo lógico nesses tipos de gatilhos e ações:

*   Fluxo de trabalho filho
*   Aplicativo de funções
*   Chamada APIM
*   URL do tempo de execução da conexão de API
*   Caminho de conexão da API

E você pode utilizar funções de modelo, como parâmetros, variáveis, resourceId, concat, etc. Por exemplo, eis como você pode substituir a ID de recurso da Função do Azure:

```
"parameters":{
    "functionName": {
        "type":"string",
        "minLength":1,
        "defaultValue":"<FunctionName>"
    }
},
```

E onde os parâmetros seriam utilizados:

```
"MyFunction": {
    "type": "Function",
    "inputs": {
        "body":{},
        "function":{
            "id":"[resourceid('Microsoft.Web/sites/functions','functionApp',parameters('functionName'))]"
        }
    },
    "runAfter":{}
}
```
Como outro exemplo, é possível parametrizar a operação de mensagem de envio do Barramento de Serviço:

```
"Send_message": {
    "type": "ApiConnection",
        "inputs": {
            "host": {
                "connection": {
                    "name": "@parameters('$connections')['servicebus']['connectionId']"
                }
            },
            "method": "post",
            "path": "[concat('/@{encodeURIComponent(''', parameters('queueuname'), ''')}/messages')]",
            "body": {
                "ContentData": "@{base64(triggerBody())}"
            },
            "queries": {
                "systemProperties": "None"
            }
        },
        "runAfter": {}
    }
```
> [!NOTE] 
> host.runtimeUrl é opcional e pode ser removido do seu modelo, se houver.
> 


> [!NOTE] 
> Para o Designer de Aplicativo Lógico funcionar quando ao usar parâmetros, você deve fornecer valores padrão, por exemplo:
> 
> ```
> "parameters": {
>     "IntegrationAccount": {
>     "type":"string",
>     "minLength":1,
>     "defaultValue":"/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Logic/integrationAccounts/<integrationAccountName>"
>     }
> },
> ```

## <a name="add-your-logic-app-to-an-existing-resource-group-project"></a>Adicione seu aplicativo lógico a um projeto existente do Grupo de Recursos

Se você tiver um projeto existente do Grupo de Recursos, é possível adicionar seu aplicativo lógico a esse projeto na janela Estrutura de tópicos JSON. Você também pode adicionar outro aplicativo lógico junto com o aplicativo criado anteriormente.

1. Abra o arquivo `<template>.json` .

2. Para abrir a janela de Estrutura de tópicos JSON, acesse **Exibir** > **Outras Janelas** > **Estrutura de tópicos JSON**.

3. Para adicionar um recurso ao arquivo de modelo, clique em **Adicionar Recurso** na parte superior da janela Estrutura de tópicos JSON. Ou, na janela Estrutura de tópicos JSON, clique com o botão direito do mouse em **recursos**e selecione **Adicionar Novo Recurso**.

    ![Janela Estrutura de tópicos JSON](./media/logic-apps-create-deploy-template/jsonoutline.png)
    
4. Na caixa de diálogo **Adicionar Recurso**, localize e selecione **Aplicativo Lógico**. Nomeie seu aplicativo lógico e escolha **Adicionar**.

    ![Adicionar recurso](./media/logic-apps-create-deploy-template/addresource.png)


## <a name="deploy-a-logic-app-template"></a>Implantar um modelo de aplicativo lógico

É possível implantar o modelo usando ferramentas como PowerShell, API REST, [Azure DevOps Azure Pipelines](#team-services) e implantação de modelo por meio do portal do Azure.
Além disso, para armazenar os valores de parâmetros, recomendamos criar um [arquivo de parâmetro](../azure-resource-manager/resource-group-template-deploy.md#parameter-files).
Saiba como [implantar recursos com modelos do Azure Resource Manager e o PowerShell](../azure-resource-manager/resource-group-template-deploy.md) ou [implantar recursos com modelos do Azure Resource Manager e o portal do Azure](../azure-resource-manager/resource-group-template-deploy-portal.md).

### <a name="authorize-oauth-connections"></a>Autorizar Conexões OAuth

Após a implantação, o aplicativo lógico funciona de ponta a ponta com parâmetros válidos.
No entanto, ainda é necessário autorizar conexões OAuth para gerar um token de acesso válido.
Para autorizar conexões OAuth, abra o aplicativo lógico no Designer de Aplicativos Lógicos e autorize essas conexões. Se preferir, para uma implantação automatizada, é possível usar um script para fornecer consentimento a cada conexão OAuth.
Há um script de exemplo no GitHub sob o projeto [LogicAppConnectionAuth](https://github.com/logicappsio/LogicAppConnectionAuth) .

<a name="team-services"></a>
## <a name="azure-devops-azure-pipelines"></a>Azure DevOps Azure Pipelines

Um cenário comum para implantar e gerenciar um ambiente é usar uma ferramenta como o Azure Pipelines no Azure DevOps com um modelo de implantação de aplicativo lógico. O Azure DevOps inclui uma tarefa [Implantar Grupo de Recursos do Azure](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks/AzureResourceGroupDeploymentV2) que pode ser adicionada a qualquer build ou pipeline de lançamento. Você precisa ter uma [entidade de serviço](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/) da autorização a implantar e pode, então, gerar o pipeline de lançamento.

1. No Azure Pipelines, selecione **Vazio** para criar um pipeline vazio.

    ![Criar um pipeline vazio][1]

2. Escolha os recursos necessários para essa tarefa, provavelmente, incluindo o modelo de aplicativo lógico gerado manualmente ou como parte do processo de build.
3. Adicione uma tarefa de **Implantação do Grupo de Recursos do Azure** .
4. Configure com uma [Entidade de Serviço](https://blogs.msdn.microsoft.com/visualstudioalm/2015/10/04/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/)e faça referência aos arquivos de Modelo e de Parâmetros do Modelo.
5. Continue a compilar as etapas no processo de versão para quaisquer outros ambientes, testes automatizados ou aprovadores, conforme necessário.

<!-- Image References -->
[1]: ./media/logic-apps-create-deploy-template/emptyreleasedefinition.png
