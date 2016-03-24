<properties
   pageTitle="Como coletar logs com o Diagnóstico do Azure e o Insights Operacionais do Azure | Microsoft Azure"
   description="Este artigo descreve como configurar o Diagnóstico do Azure e o Insights Operacionais para coletar logs de um cluster de Service Fabric em execução no Azure."
   services="service-fabric"
   documentationCenter=".net"
   authors="kunaldsingh"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="02/12/2016"
   ms.author="toddabel"/>


# Coletar logs de um cluster do Service Fabric usando o Diagnóstico do Azure e Insights Operacionais

Quando você estiver executando um cluster de Service Fabric do Azure, é uma boa ideia coletar os logs de todos os nós em um local central. Ter os logs em um local central facilita analisar e solucionar quaisquer problemas no cluster ou nos aplicativos e serviços em execução nesse cluster. Uma forma de carregar e coletar logs é usar a extensão de Diagnóstico do Azure, que carrega os logs no Armazenamento do Azure.

O Insights Operacionais do Azure (parte do Microsoft Operations Management Suite) é uma solução de SaaS que facilita analisar e pesquisar logs. As etapas a seguir descrevem como configurar a extensão Diagnósticos do Azure em VMs em um cluster para carregar logs para um repositório central e então configurar Insights Operacionais para extrair os logs de modo a poder exibi-los no portal Insights Operacionais.

O Insights Operacionais identifica as fontes dos diferentes tipos de logs carregados de um cluster de Service Fabric pelos nomes das tabelas de armazenamento em que são armazenados. Isso significa que a extensão de Diagnóstico do Azure deve ser configurada para carregar os logs em tabelas de armazenamento com nomes que correspondam aos que o Insights Operacionais irá procurar. Os exemplos de definições de configuração neste documento mostrarão quais devem ser os nomes das tabelas de armazenamento.

## Sugestões de leitura
* [Diagnóstico do Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) (relacionado aos Serviços de Nuvem do Azure, mas tem boas informações e exemplos)
* [Insights Operacionais](https://azure.microsoft.com/services/operational-insights/)
* [Gerenciador de Recursos do Azure](https://azure.microsoft.com/resource-group-overview/)

## Pré-requisitos
Essas ferramentas serão usadas para executar algumas das operações neste documento:
* [PowerShell do Azure](https://azure.microsoft.com/powershell-install-configure/)
* [Cliente do Azure Resource Manager](https://github.com/projectkudu/ARMClient)

## Fontes de log diferentes que você talvez queira coletar
1. **Logs do Service Fabric:** emitidos pela plataforma para canais ETW e EventSource padrões. Os logs podem ser de vários tipos:
  - Eventos operacionais: logs para operações executadas na plataforma do Service Fabric. Os exemplos incluem criação de aplicativos e serviços, alterações de estado do nó e informações de atualização.
  - [Eventos do modelo de programação do ator](service-fabric-reliable-actors-diagnostics.md)
  - [Eventos do modelo de Reliable Services](service-fabric-reliable-services-diagnostics.md)
2. **Eventos do aplicativo:** eventos emitidos do código de serviços e escritos usando a classe auxiliar EventSource fornecida nos modelos do Visual Studio. Para obter mais informações sobre como gravar logs de seu aplicativo, consulte [este artigo sobre como monitorar e diagnosticar serviços em uma configuração de máquina local](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).


## Implantar a extensão de diagnóstico em um cluster do Service Fabric para coletar e carregar logs
A primeira etapa para coletar logs será implantar a extensão Diagnóstico em cada uma das VMs no cluster do Service Fabric. A extensão de Diagnóstico coleta logs em cada VM e carrega-os para a conta de armazenamento especificada. As etapas variam um pouco dependendo de se você usa o Portal do Azure ou o Gerenciador de Recursos do Azure e se a implantação está sendo feita como parte da criação do cluster ou para um cluster que já existe. Vejamos as etapas para cada cenário.

### Implantar a extensão de Diagnóstico como parte da criação de cluster por meio do portal
Para implantar o Diagnóstico nas VMs no cluster como parte da criação de cluster, é usada a configuração de Diagnóstico mostrada na imagem abaixo. Ele está **LIGADO** por padrão. ![Configuração de Diagnóstico do Azure no portal para a criação do cluster](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/portal-cluster-creation-diagnostics-setting.png)

### Implantar a extensão de diagnóstico como parte da criação de cluster usando o Gerenciador de Recursos do Azure
Para criar um cluster usando o Gerenciador de recursos, você precisa adicionar a configuração de Diagnóstico JSON para o modelo do Gerenciador de recursos completo do cluster antes de criar o cluster. Fornecemos um exemplo de modelo de Gerenciador de Recursos de cluster de cinco VMs com configuração de Diagnóstico adicionada a ele como parte dos exemplos do modelo de Gerenciador de Recursos. Você pode vê-lo nesse local na Galeria de exemplos do Azure: [cluster cinco nós com exemplo de modelo do Gerenciador de Recursos de Diagnóstico](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-cluster-5-node-1-nodetype-wad).

Para ver a configuração de diagnóstico no modelo do Gerenciador de Recursos, pesquise **WadCfg.** Para criar um cluster com este modelo basta pressionar o botão **Implantar no Azure** disponível no link acima. Como alternativa você pode baixar o exemplo de Gerenciador de Recursos, fazer suas alterações e criar um cluster com o modelo modificado usando o comando `New-AzureResourceGroupDeployment` em uma janela do Azure PowerShell. Veja as informações abaixo para os parâmetros que você precisará para passar para o comando.

Além disso, antes de chamar esse comando de implantação você pode precisar fazer algumas configurações, incluindo a adição de sua conta do Azure (`Add-AzureAccount`), escolher uma assinatura (`Select-AzureSubscription`), mudar para o modo Gerenciador de Recursos (`Switch-AzureMode AzureResourceManager`) e criar o grupo de recursos, se você ainda não tiver feito isso (`New-AzureResourceGroup`).

```powershell

New-AzureResourceGroupDeployment -ResourceGroupName $resourceGroupName -Name $deploymentName -TemplateFile $pathToARMConfigJsonFile -TemplateParameterFile $pathToParameterFile –Verbose
```

### <a name="deploywadarm"></a>Implantar a extensão de diagnóstico em um cluster existente
Se você tiver um cluster existente que não tenha Diagnóstico implantado, pode adicioná-lo seguindo estas etapas. Crie os dois arquivos WadConfigUpdate.json e WadConfigUpdateParams.json, usando o JSON abaixo.

##### WadConfigUpdate.json

```json

{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",

  "parameters": {
        "vmNamePrefix": {
      "type": "string"
    },
        "applicationDiagnosticsStorageAccountName": {
      "type": "string"
    },
        "vmCount": {
            "type": "int"
    }
  },

  "resources": [
    {
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(parameters('vmNamePrefix'),copyIndex(0),'/Microsoft.Insights.VMDiagnosticsSettings')]",
            "location": "[resourceGroup().location]",
      "properties": {
        "type": "IaaSDiagnostics",
                "typeHandlerVersion": "1.5",
        "publisher": "Microsoft.Azure.Diagnostics",
                "autoUpgradeMinorVersion": true,
        "settings": {
                    "WadCfg": {
            "DiagnosticMonitorConfiguration": {
                            "overallQuotaInMB": "50000",
                "EtwProviders": {
                    "EtwEventSourceProviderConfiguration": [
                        {
                            "provider": "Microsoft-ServiceFabric-Actors",
                            "scheduledTransferKeywordFilter": "1",
                                        "scheduledTransferPeriod": "PT5M",
                            "DefaultEvents": { "eventDestination": "ServiceFabricReliableActorEventTable" }
                        },
                        {
                            "provider": "Microsoft-ServiceFabric-Services",
                            "DefaultEvents": { "eventDestination": "ServiceFabricReliableServiceEventTable" },
                                        "scheduledTransferPeriod": "PT5M"
                        }
                    ],
                    "EtwManifestProviderConfiguration": [
                        {
                            "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                            "scheduledTransferKeywordFilter": "4611686018427387904",
                                        "scheduledTransferLogLevelFilter": "Information",
                                        "scheduledTransferPeriod": "PT5M",
                            "DefaultEvents": { "eventDestination": "ServiceFabricSystemEventTable" }
                        }
                    ]
                }
            }
    },
                    "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountName')]"
                },
                "protectedSettings": {
                    "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
                    "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
                    "storageAccountEndPoint": "https://core.windows.net/"
                }
            },
            "copy": {
                "name": "vmExtensionLoop",
                "count": "[parameters('vmCount')]"
            }
        }
    ],

    "outputs": {
    }
}
```

##### WadConfigUpdateParams.json
Substitua o vmNamePrefix pelo prefixo que você escolheu para nomes de VM ao criar o cluster. Em seguida, edite o vmStorageAccountName como a conta de armazenamento em que deseja carregar os logs das VMs.

```json

{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmNamePrefix": {
            "value": "VM"
        },
        "applicationDiagnosticsStorageAccountName": {
            "value": "testdiagacc"
        },
        "vmCount": {
            "value": 5
        }
    }
}
```

Depois de criar os arquivos JSON, conforme descrito acima, altere-os para as especificidades de seu ambiente. Em seguida, chame o seguinte comando, passando o nome do grupo de recursos para o cluster do Service Fabric. Quando esse comando for executado com êxito o Diagnóstico será implantado em todas as máquinas virtuais e começará a carregar os logs do cluster para as tabelas na conta de armazenamento do Azure especificada.

Além disso, antes de chamar esse comando de implantação, pode ser necessário fazer algumas configurações, incluindo a adição de sua conta do Azure (`Add-AzureAccount`), escolhendo a assinatura correta (`Select-AzureSubscription`) e mudando para o modo Gerenciador de Recursos (`Switch-AzureMode AzureResourceManager`).

```ps

New-AzureResourceGroupDeployment -ResourceGroupName $resourceGroupName -Name $deploymentName -TemplateFile $pathToWADConfigJsonFile -TemplateParameterFile $pathToParameterFile –Verbose
```

## Configurar o Operational Insights para exibir e pesquisar os logs de cluster
Quando o Diagnóstico está configurado no cluster e está carregando logs para uma conta de armazenamento, a próxima etapa é a configuração de Insights Operacionais para que você possa exibir, pesquisar e consultar todos os logs de cluster usando o Portal do Insights Operacionais.

### Criar um espaço de trabalho do Operational Insights
Para ver as etapas para criar um espaço de trabalho do Operational Insights, vá para o artigo abaixo. Observe que ele descreve duas maneiras diferentes de criar um espaço de trabalho. Escolha o portal do Azure e a abordagem baseada em assinatura. Você precisará do nome do espaço de trabalho do Operational Insights operacionais nas seções posteriores deste documento. Crie o espaço de trabalho do Insights Operacionais usando a mesma assinatura usada para criar todos os recursos de cluster, incluindo contas de armazenamento.

[Operational Insights Integrado](https://technet.microsoft.com/library/mt484118.aspx)

### Configurar o espaço de trabalho do Insights Operacionais para exibir os logs do cluster
Depois de criar o espaço de trabalho do Insights Operacionais conforme descrito acima, a próxima etapa é configurar o espaço de trabalho para extrair os logs das tabelas do armazenamento do Azure, em que eles estão sendo carregados do cluster pela extensão Diagnóstico. Atualmente, não é possível realizar essa configuração por meio do Portal do Insights Operacionais; ela só pode ser feita por comandos do PowerShell. Execute o seguinte script do PowerShell:

```powershell

    <#
    This script will configure an Operations Management Suite workspace (aka Operational Insights workspace) to read Diagnostics from an Azure Storage account.

    It will enable all supported data types (currently Windows Event Logs, Syslog, Service Fabric Events, ETW Events and IIS Logs).

    It supports both classic and Resource Manager storage accounts.

    If you have more than one OMS workspace you will be prompted for the workspace to configure.

    If you have more than one storage account you will be prompted for which storage account to configure.
    #>

Add-AzureAccount

Switch-AzureMode -Name AzureResourceManager

$validTables = "WADServiceFabric*EventTable", "WADETWEventTable"

function Select-Workspace {

    $workspace = ""

    $allWorkspaces = Get-AzureOperationalInsightsWorkspace  

    switch ($allWorkspaces.Count) {
        0 {Write-Error "No Operations Management Suite workspaces found"}
        1 {return $allWorkspaces}
        default {
            $uiPrompt = "Enter the number corresponding to the workspace you want to configure.`n"

            $count = 1
            foreach ($workspace in $allWorkspaces) {
                $uiPrompt += "$count. " + $workspace.Name + " (" + $workspace.CustomerId + ")`n"
                $count++
            }
            $answer = (Read-Host -Prompt $uiPrompt) - 1
            $workspace = $allWorkspaces[$answer]
        }  
    }
    return $workspace
}

function Select-StorageAccount {

    $storage = ""

    $allStorageAccounts = (get-azureresource -ResourceType Microsoft.ClassicStorage/storageAccounts) + (get-azureresource -ResourceType Microsoft.Storage/storageAccounts)

    switch ($allStorageAccounts.Count) {
        0 {Write-Error "No storage accounts found"}
        1 {$storage = $allStorageAccounts}
        default {

            $uiPrompt = "Enter the number corresponding to the storage account with diagnostics.`n"

            $count = 1
            foreach ($storageAccount in $allStorageAccounts) {
                $uiPrompt += "$count. " + $storageAccount.Name + " (" + $storageAccount.Location + ")`n"
                $count++
            }
            $answer = (Read-Host -Prompt $uiPrompt)

            $storage = $allStorageAccounts[$answer - 1]
        }
    }
    return $storage
}

$workspace = Select-Workspace
$storageAccount = Select-StorageAccount

$insightsName = $storageAccount.Name + $workspace.Name

$existingConfig = ""

try
{
    $existingConfig = Get-AzureOperationalInsightsStorageInsight -Workspace $workspace -Name $insightsName -ErrorAction Stop
}
catch [Hyak.Common.CloudException]
{
    # HTTP Not Found is returned if the storage insight doesn't exist
}

if ($existingConfig) {
    Set-AzureOperationalInsightsStorageInsight -Workspace $workspace -Name $insightsName -Tables $validTables -Containers $validContainers

} else {
    if ($storageAccount.ResourceType -eq "Microsoft.ClassicStorage/storageAccounts") {
        Switch-AzureMode -Name AzureServiceManagement
        $key = (Get-AzureStorageKey -StorageAccountName $storageAccount.Name).Primary
        Switch-AzureMode -Name AzureResourceManager
    } else {
        $key = (Get-AzureStorageAccountKey -ResourceGroupName $storageAccount.ResourceGroupName -Name $storageAccount.Name).Key1
    }
    New-AzureOperationalInsightsStorageInsight -Workspace $workspace -Name $insightsName -StorageAccountResourceId $storageAccount.ResourceId -StorageAccountKey $key -Tables $validTables -Containers $validContainers
}
```

Depois de configurar o espaço de trabalho do Insights Operacionais para ler das tabelas do Azure em sua conta de armazenamento, entre no portal e acesse a guia **Armazenamento** do recurso Insights Operacionais. O resultado deve ser semelhante a este: ![Configuração de armazenamento do Insights Opcionais no portal do Azure](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/oi-connected-tables-list.png)

### Pesquise e exiba os logs no Insights Operacionais
Depois de configurar o seu espaço de trabalho do Insights Operacionais para ler os logs da conta de armazenamento especificada pode levar até 10 minutos para que os logs sejam exibidos na interface do usuário do Insights Operacionais. Para garantir que novos logs sejam gerados, você deve implantar um aplicativo do Service Fabric para seu cluster, pois isso irá gerar eventos operacionais da plataforma do Service Fabric.

1. Para exibir os logs, selecione **Pesquisa de Log** na página principal do portal Insights Operacionais. ![Opção de pesquisa de log de Insights Operacionais](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/log-search-button-oi.png)

2. Na página de pesquisa de log, use a consulta **Type=ServiceFabricOperationalEvent** e você deverá ver os logs operacionais do cluster conforme mostrado abaixo. Para ver todos o logs do modelo de programação Ator, consulte **Type=ServiceFabricReliableActorEvent**. Para ver todos os logs do modelo de programação de Reliable Services, digite **Type=ServiceFabricReliableServiceEvent**. ![Exibição e consulta de log de Insights Operacionais](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/view-logs-oi.png)

### Consultas de exemplo para ajudar a solucionar problemas
Estes são exemplos de alguns cenários e as consultas que você pode usar para solucionar esses problemas:

1. **Para descobrir se o RunAsync foi chamado para um serviço específico pelo Service Fabric:** você pode querer fazer isso se precisar determinar se um serviço está falhando ao iniciar. Para fazer isso, pesquise usando uma consulta semelhante à mostrada abaixo, mas substitua o nome do serviço e do aplicativo para coincidir com o que você implantou em veja se algum resultado é retornado:

    ```
    Type=ServiceFabricReliableServiceEvent AND ServiceName="fabric:/Application2/Stateless1" AND "RunAsync has been invoked"
    ```

2. **Se você estiver executando um serviço com estado e desejar ver se ele lançou alguma exceção que tenha sido marcada como falha pelo Service Fabric:** localize esses eventos usando uma consulta semelhante a esta:

    ```
    Type=ServiceFabricReliableServiceEvent AND ServiceName="fabric:/Application2/Stateful1" AND TaskName=StatefulRunAsyncFailure
    ```

3. **Para localizar eventos correspondentes a qualquer exceção geradas por métodos Ator em todos os aplicativos e serviços implantados:** é possível usar uma consulta como esta:

    ```
    Type=ServiceFabricReliableActorEvent AND TaskName=ActorMethodThrewException
    ```

## Atualizar o Diagnóstico para coletar e carregar os logs de novos canais EventSource
Para atualizar o Diagnóstico para coletar logs de novos canais EventSource que representam um novo aplicativo que você está prestes a implantar, é preciso apenas executar as mesmas etapas que na [seção acima](#deploywadarm) que descreve a configuração do Diagnóstico para um cluster existente.

Você precisará atualizar a seção EtwEventSourceProviderConfiguration no WadConfigUpdate.json para adicionar entradas para o novo EventSources antes de aplicar a atualização de configuração por meio do comando Gerenciador de Recurso. A tabela para o carregamento será a mesma (ETWEventTable), já que é a tabela configurada para o Insights Operacionais ler os eventos de ETW do aplicativo.


## Próximas etapas
Verifique os eventos de diagnóstico emitidos para [Reliable Actors](service-fabric-reliable-actors-diagnostics.md) e [Reliable Services](service-fabric-reliable-services-diagnostics.md) para entender mais detalhadamente os eventos que você deve examinar na solução de problemas.

<!---HONumber=AcomDC_0309_2016-->