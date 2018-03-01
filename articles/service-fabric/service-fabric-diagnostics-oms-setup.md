---
title: "Azure Service Fabric – Configurar o monitoramento com o Log Analytics do OMS | Microsoft Docs"
description: Saiba como configurar o Operations Management Suite para visualizar e analisar eventos para monitorar os clusters do Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 1/17/2017
ms.author: dekapur
ms.openlocfilehash: 288c7482058cd9f824b6001bb9ad36d1a5e0f8bf
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="set-up-operations-management-suite-log-analytics-for-a-cluster"></a>Configurar o Log Analytics do Operations Management Suite para um cluster

Você pode configurar um espaço de trabalho do OMS (Operations Management Suite) por meio do Azure Resource Manager, do PowerShell ou do Azure Marketplace. Se você mantém um modelo do Resource Manager atualizado de sua implantação para uso futuro, utilize o mesmo modelo para configurar o ambiente do OMS. A implantação por meio do Marketplace será mais fácil se você já tiver um cluster implantado com o diagnóstico habilitado. Caso não tenha acesso ao nível de assinatura na conta na qual está implantando o OMS, faça a implantação usando o PowerShell ou o modelo do Resource Manager.

> [!NOTE]
> Para configurar o OMS para monitorar seu cluster, você precisa ter o diagnóstico habilitado para exibir eventos no nível do cluster ou da plataforma.

## <a name="deploy-oms-by-using-azure-marketplace"></a>Implantar o OMS usando o Azure Marketplace

Se você deseja adicionar um espaço de trabalho de OMS depois de implantar um cluster, vá para o Azure Marketplace no portal e procure **Análise do Service Fabric**:

1. Selecione **Novo** no menu de navegação esquerdo. 

2. Pesquise **Análise do Service Fabric**. Selecione o recurso que é exibido.

3. Selecione **Criar**.

    ![Análise do OMS SF no Marketplace](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

4. Na janela de criação de Análise do Service Fabric, selecione **Selecionar um espaço de trabalho** para o campo **Espaço de trabalho do OMS** e, em seguida, **Criar um novo espaço de trabalho**. Preencha as entradas necessárias. O único requisito é que a assinatura para o cluster do Service Fabric e o espaço de trabalho de OMS devem ser iguais. Quando as entradas forem validadas, o espaço de trabalho do OMS começará a ser implantado. A implantação leva apenas alguns minutos.

5. Quando terminar, selecione **Criar** novamente na parte inferior da janela de criação de Análise do Service Fabric. Verifique se o novo espaço de trabalho será exibido em **Espaço de trabalho do OMS**. Essa ação adiciona a solução ao espaço de trabalho criado.

Se estiver usando o Windows, continue com as seguintes etapas para conectar o OMS à conta de Armazenamento onde seus eventos de cluster são armazenados. 

>[!NOTE]
>A habilitação dessa experiência para clusters do Linux ainda não está disponível. 

### <a name="add-the-oms-agent-to-your-cluster"></a>Adicionar o Agente do OMS a um cluster 

1. O espaço de trabalho precisa estar conectado aos dados de diagnóstico provenientes do seu cluster. Vá para o grupo de recursos em que você criou a solução Análise do Service Fabric. Selecione **ServiceFabric\<nomeDoEspaçodeTrabalhoOMS\>**  e vá para a página de visão geral. A partir daí, você pode alterar as configurações da solução,as configurações do espaço de trabalho e acessar o portal do OMS.

2. No menu de navegação à esquerda, selecione **Logs das contas de armazenamento** em **Fontes de Dados de Espaço de Trabalho**.

3. Na página **Logs das contas de armazenamento**, selecione **Adicionar** na parte superior para adicionar os logs do cluster ao espaço de trabalho.

4. Selecione **Conta de armazenamento** para adicionar a conta apropriada criada no cluster. Se você usou o nome padrão, a conta de armazenamento será **sfdg\<resourceGroupName\>**. Você também pode confirmar isso com o modelo do Azure Resource Manager usado para implantar o cluster verificando o valor usado para o **applicationDiagnosticsStorageAccountName**. Se o nome não aparecer, role para baixo e selecione **Carregar mais**. Selecione o nome da conta de armazenamento.

5. Especifique o tipo de dados. Defina-o como **Eventos do Service Fabric**.

6. Verifique se a Origem está definida automaticamente como **WADServiceFabric\*EventTable**.

7. Selecione **OK** para conectar o espaço de trabalho aos logs do seu cluster.

    ![Adicionar logs de conta de armazenamento ao OMS](media/service-fabric-diagnostics-event-analysis-oms/add-storage-account.png)

A conta agora aparece como parte dos seus logs de conta de armazenamento nas fontes de dados do seu espaço de trabalho.

Você adicionou a solução Análise do Service Fabric em um espaço de trabalho do Log Analytics do OMS que agora está conectado corretamente à plataforma do cluster e à tabela de log do aplicativo. Você pode adicionar outras fontes ao espaço de trabalho dessa mesma forma.


## <a name="deploy-oms-by-using-a-resource-manager-template"></a>Implantar o OMS usando um modelo do Resource Manager

Ao implantar um cluster usando um modelo do Resource Manager, o modelo cria um novo espaço de trabalho de OMS, adiciona a Solução Service Fabric a ele e o configura para ler dados das tabelas de armazenamento apropriadas.

Você pode usar e modificar [este modelo de exemplo](https://azure.microsoft.com/resources/templates/service-fabric-oms/) para atender às suas necessidades. Modelos que oferecem diferentes opções para configurar um espaço de trabalho do OMS podem ser encontrados em [Modelos do Service Fabric e do OMS](https://azure.microsoft.com/resources/templates/?term=service+fabric+OMS).

Faça as seguintes modificações:
1. Adicione `omsWorkspaceName` e `omsRegion` aos seus parâmetros adicionando o trecho de código a seguir aos parâmetros definidos em seu arquivo *template.json*. Fique à vontade para modificar os valores padrão como desejar. Além disso, adicione dois novos parâmetros em seu arquivo *parameters.json* para definir os valores para a implantação de recursos:
    
    ```json
    "omsWorkspacename": {
        "type": "string",
        "defaultValue": "sfomsworkspace",
        "metadata": {
            "description": "Name of your OMS Log Analytics Workspace"
        }
    },
    "omsRegion": {
        "type": "string",
        "defaultValue": "East US",
        "allowedValues": [
            "West Europe",
            "East US",
            "Southeast Asia"
        ],
        "metadata": {
            "description": "Specify the Azure Region for your OMS workspace"
        }
    }
    ```

    Os valores `omsRegion` devem estar em conformidade com um conjunto específico de valores. Escolha um que seja mais próximo à implantação do cluster.

2. Se você envia logs do aplicativo ao OMS, confirme se o `applicationDiagnosticsStorageAccountType` e o `applicationDiagnosticsStorageAccountName` estão incluídos como parâmetros em seu modelo. Se não estiverem, adicione-os à seção de variáveis e edite seus valores conforme necessário. Você também pode inclui-los como parâmetros seguindo o formato anterior.

    ```json
    "applicationDiagnosticsStorageAccountType": "Standard_LRS",
    "applicationDiagnosticsStorageAccountName": "[toLower(concat('oms', uniqueString(resourceGroup().id), '3' ))]"
    ```

3. Adicione a solução do OMS do Service Fabric às variáveis do seu modelo:

    ```json
    "solution": "[Concat('ServiceFabric', '(', parameters('omsWorkspacename'), ')')]",
    "solutionName": "ServiceFabric"
    ```

4. Adicione o seguinte ao final da seção de recursos, depois de onde o recurso de cluster do Service Fabric é declarado:

    ```json
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[parameters('omsWorkspacename')]",
        "type": "Microsoft.OperationalInsights/workspaces",
        "properties": {
            "sku": {
                "name": "Free"
            }
        },
        "resources": [
            {
                "apiVersion": "2015-11-01-preview",
                "name": "[concat(parameters('applicationDiagnosticsStorageAccountName'),parameters('omsWorkspacename'))]",
                "type": "storageinsightconfigs",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]",
                    "[concat('Microsoft.Storage/storageAccounts/', parameters('applicationDiagnosticsStorageAccountName'))]"
                ],
                "properties": {
                    "containers": [ ],
                    "tables": [
                        "WADServiceFabric*EventTable",
                        "WADWindowsEventLogsTable",
                        "WADETWEventTable"
                    ],
                    "storageAccount": {
                        "id": "[resourceId('Microsoft.Storage/storageaccounts/', parameters('applicationDiagnosticsStorageAccountName'))]",
                        "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-06-15').key1]"
                    }
                }
            }
        ]
    },
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[variables('solution')]",
        "type": "Microsoft.OperationsManagement/solutions",
        "id": "[resourceId('Microsoft.OperationsManagement/solutions/', variables('solution'))]",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('OMSWorkspacename'))]"
        ],
        "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        },
        "plan": {
            "name": "[variables('solution')]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('solutionName'))]",
            "promotionCode": ""
        }
    }
    ```
    
    > [!NOTE]
    > Se você adicionou `applicationDiagnosticsStorageAccountName` como uma variável, certifique-se de modificar cada referência para `variables('applicationDiagnosticsStorageAccountName')` em vez de `parameters('applicationDiagnosticsStorageAccountName')`.

5. Implante o modelo como uma atualização do Gerenciador de Recursos para o cluster usando a API `New-AzureRmResourceGroupDeployment` no módulo PowerShell do AzureRM. Um exemplo de comando pode ser:

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName "sfcluster1" -TemplateFile "<path>\template.json" -TemplateParameterFile "<path>\parameters.json"
    ``` 

    O Azure Resource Manager detecta que esse comando é uma atualização de um recurso existente. Ele processa somente as alterações entre o modelo que gera a implantação existente e o novo modelo fornecido.

## <a name="deploy-oms-by-using-azure-powershell"></a>Implantar o OMS usando o Azure PowerShell

Você também pode implantar o recurso Log Analytics do OMS por meio do PowerShell usando o comando `New-AzureRmOperationalInsightsWorkspace`. Para usar esse método, verifique se o [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.1.1) está instalado. Use esse script para criar um novo espaço de trabalho do OMS Log Analytics e adicionar a solução do Service Fabric a ele: 

```PowerShell

$SubscriptionName = "<Name of your subscription>"
$ResourceGroup = "<Resource group name>"
$Location = "<Resource group location>"
$WorkspaceName = "<OMS Log Analytics workspace name>"
$solution = "ServiceFabric"

# Log in to Azure and access the correct subscription
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId $SubID 

# Create the resource group if needed
try {
    Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzureRmResourceGroup -Name $ResourceGroup -Location $Location
}

New-AzureRmOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true

```

Quando terminar, siga as etapas na seção anterior para conectar o Log Analytics do OMS à conta de armazenamento apropriada.

Também é possível adicionar outras soluções ou fazer outras modificações no espaço de trabalho do OMS usando o PowerShell. Para saber mais, confira [Gerenciar o Log Analytics usando o PowerShell](../log-analytics/log-analytics-powershell-workspace-configuration.md).

## <a name="next-steps"></a>Próximas etapas
* [Implantar o Agente do OMS](service-fabric-diagnostics-oms-agent.md) para os nós para coletar os contadores de desempenho e coletar logs e estatísticas do docker para seus contêineres
* Familiarize-se com os recursos de [pesquisa e consulta de logs](../log-analytics/log-analytics-log-searches.md) oferecidos como parte do Log Analytics
* [Use o Designer de Exibição para criar exibições personalizadas no Log Analytics](../log-analytics/log-analytics-view-designer.md)
