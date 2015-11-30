<properties
   pageTitle="Como coletar logs com WAD e Operational Insights | Microsoft Azure"
   description="Este artigo descreve como você pode configurar o Windows Azure Diagnostics e o Operational Insights para coletar logs de um cluster de Malha de Serviços em execução no Azure"
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
   ms.date="10/20/2015"
   ms.author="kunalds"/>


# Coletando logs de um cluster de Malha de Serviço no Azure usando WAD (Windows Azure Diagnostics) e Operational Insights

Ao executar um cluster de Malha de Serviço no Azure, você deve coletar os logs de todos os nós em um local central. Ter os logs em um local central facilita analisar e solucionar quaisquer problemas que você possa observar no cluster ou nos aplicativos e serviços em execução nesse cluster. Uma das maneiras de carregar e coletar logs é usar a extensão WAD (Windows Azure Diagnostics) que carrega os logs no armazenamento de tabela do Azure. Operational Insights (parte do Microsoft Operations Management Suite) é uma solução de SaaS que torna facilita a análise e pesquisa dos logs. As etapas a seguir descrevem como você pode definir WAD nas VMs no cluster para carregar logs para um repositório central e, em seguida, configurar Insights Operacionais para extrair os logs de forma que você possa exibi-los no portal Operational Insights. O Operational Insights identifica as fontes dos diferentes tipos de log carregados de um cluster de Malha de Serviço pelos nomes das tabelas de armazenamento do Azure que nas quais são armazenados, então o WAD precisa ser configurado para carregar os logs em tabelas de armazenamento do Azure com nomes que coincidem com o que o Operational Insights procurará, os exemplos de definições de configuração neste documento mostrarão quais devem ser os nomes das tabelas de armazenamento.

## Sugestões de leitura
* [Windows Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md) (serviços de nuvem relacionados, mas oferece a você algumas boas informações e exemplos)
* [Insights Operacionais](https://azure.microsoft.com/services/operational-insights/)
* [Gerenciador de Recursos do Azure](https://azure.microsoft.com/documentation/articles/resource-group-overview/)

## Pré-requisitos
Essas ferramentas serão usadas para executar algumas das operações neste documento: * [Azure Powershell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/) * [ARM cliente](https://github.com/projectkudu/ARMClient)

## Fontes de log diferentes que você talvez queira coletar
1. Logs da Malha de Serviço: emitidos pela plataforma para canais ETW e EventSource padrões. Podem ser um destes tipos:
  - Eventos operacionais: Esses são logs para operações executadas na plataforma de Malha de Serviço. Os exemplos incluem a criação de aplicativo e serviço, as alterações de estado do Nó e informações de atualização.
  - [Eventos do modelo de programação do ator](https://azure.microsoft.com/documentation/articles/service-fabric-reliable-actors-diagnostics/)
  - [Eventos do modelo de Reliable Services](https://azure.microsoft.com/documentation/articles/service-fabric-reliable-services-diagnostics/)
2. Eventos do aplicativo: Esses são os eventos emitidos de seu código de serviços e escritos usando a classe auxiliar EventSource fornecida nos modelos do Visual Studio. Para obter mais informações sobre como gravar logs do aplicativo consulte este [artigo](https://azure.microsoft.com/documentation/articles/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/).


## Implantar o WAD (Windows Azure Diagnostics) em um cluster de Malha de Serviço para coletar e carregar logs
A primeira etapa para coletar logs será implantar a extensão WAD em cada uma das máquinas virtuais no cluster de Malha de Serviço. O WAD coletará os logs em cada VM e irá carregá-los na conta de armazenamento que você especificar. As etapas variam um pouco conforme você usa o Portal ou ARM e se a implantação está sendo feita como parte da criação do cluster ou para um cluster que já existe. Vejamos as etapas para cada cenário.

### Implantar WAD como parte da criação de cluster por meio do Portal
Para implantar o WAD nas VMs no cluster como parte da criação de cluster é usada a configuração de diagnóstico mostrada na imagem abaixo. Ele está ON por padrão. ![Configuração do WAD no Portal para criação de cluster](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/portal-cluster-creation-diagnostics-setting.png)

### Implantar WAD como parte da criação de cluster usando ARM
Para criar um cluster usando o ARM, você precisa adicionar a configuração JSON do WAD para o modelo de ARM de cluster completo antes de criar o cluster. Nós fornecemos um modelo de ARM de Cluster com 5 VMs com configuração WAD adicionada a ele como parte de nossos exemplos de modelo do ARM, você pode vê-lo nesse local na galeria de exemplos do Azure: [Cluster com cinco nós com exemplo de modelo do ARM WAD](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-cluster-5-node-1-nodetype-wad).

Para ver a configuração WAD na pesquisa de modelo ARM procure por "WadCfg". Para criar um cluster com este modelo basta pressionar o botão de "Implantar no Azure" disponível no link acima. Como alternativa você pode baixar o exemplo de ARM, fazer suas alterações e criar um cluster com o modelo modificado usando o comando `New-AzureResourceGroupDeployment` em uma janela do Azure Powershell. Veja abaixo os parâmetros que você precisará para passar para o comando. Além disso, antes de chamar esse comando de implantação você pode precisar fazer algumas configurações, incluindo a adição de sua conta do Azure (`Add-AzureAccount`), escolher uma assinatura (`Select-AzureSubscription`), mudar para o modo ARM (`Switch-AzureMode AzureResourceManager`) e criar o grupo de recursos se você ainda não fez isso (`New-AzureResourceGroup`).

```powershell
New-AzureResourceGroupDeployment -ResourceGroupName $resourceGroupName -Name $deploymentName -TemplateFile $pathToARMConfigJsonFile -TemplateParameterFile $pathToParameterFile –Verbose
```

### <a name="deploywadarm"></a>Implantar WAD em um cluster existente
Se você tiver um cluster que não tenha WAD implantado você pode adicionar o WAD com estas etapas. Crie dois arquivos WadConfigUpdate.json e WadConfigUpdateParams.json com o JSON abaixo.

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
                    "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountNamee')]"
                },
                "protectedSettings": {
                    "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountNamee')]",
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
Substitua o vmNamePrefix pelo prefixo que você escolheu para os nomes da máquina virtual durante a criação do cluster e edite o vmStorageAccountName para ser a conta de armazenamento onde você deseja carregar os logs das VMs. ```json
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
``` Depois de criar os arquivos json como mencionado acima e ter alterado-os para as especificidades de seu ambiente, chame este comando, passando o nome do grupo de recursos para o cluster da Malha de Serviço. Quando esse comando for executado com êxito o WAD será implantado em todas as máquinas virtuais e começará a carregar os logs do cluster para as tabelas na conta de armazenamento do Azure especificada. Além disso, antes de chamar esse comando de implantação você pode precisar fazer algumas configurações, incluindo a adição de sua conta do Azure (`Add-AzureAccount`), escolher a assinatura correta (`Select-AzureSubscription`) e mudar para o modo ARM (`Switch-AzureMode AzureResourceManager`). ```powershell
New-AzureResourceGroupDeployment -ResourceGroupName $resourceGroupName -Name $deploymentName -TemplateFile $pathToWADConfigJsonFile -TemplateParameterFile $pathToParameterFile –Verbose
```

## Configurar o Operational Insights para exibir e pesquisar os logs de cluster
Quando o WAD está configurado no cluster e está carregando logs para uma conta de armazenamento a próxima etapa é a configuração do Operational Insights para que você possa exibir, pesquisar e consultar todos os logs de cluster através do Portal da interface do usuário do Operational Insights.

### Criar um espaço de trabalho do Operational Insights
Para ver as etapas para criar um espaço de trabalho do Operational Insights consulte o artigo abaixo. Observe que ele descreve duas maneiras diferentes de criar um espaço de trabalho, escolha a abordagem do Portal do Azure e a de Assinatura. Você precisará do nome do espaço de trabalho do Operational Insights operacionais nas seções posteriores deste documento. Crie o espaço de trabalho do Operational Insights usando a mesma assinatura que você usou para criar todos os recursos de cluster, incluindo contas de armazenamento.

[Operational Insights Integrado](https://technet.microsoft.com/library/mt484118.aspx)

### Configurar o espaço de trabalho do Operational Insights para exibir os logs do cluster
Depois de criar o espaço de trabalho do Operational Insights conforme descrito acima a próxima etapa é configurar o espaço de trabalho para extrair os logs das tabelas do Azure, onde eles estão sendo carregados do cluster pelo WAD. Atualmente, essa configuração não é possível por meio do Portal do Operational Insights e só pode ser feita por meio de comandos do Powershell. Execute este script de PS. ```powershell <# Esse script irá configurar um espaço de trabalho do Operations Management Suite (também conhecido como espaço de trabalho do Operational Insights) para ler o Windows Azure Diagnostics a partir de uma conta de armazenamento do Azure.

    It will enable all supported data types (currently Windows Event Logs, Syslog, Service Fabric Events, ETW Events and IIS Logs).

    It supports both classic and ARM storage accounts.

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

$workspace = Select-Workspace $storageAccount = Select-StorageAccount

$insightsName = $storageAccount.Name + $workspace.Name

$existingConfig = ""

try { $existingConfig = Get-AzureOperationalInsightsStorageInsight -Workspace $workspace -Name $insightsName -ErrorAction Stop } catch [Hyak.Common.CloudException] { # HTTP Not Found é retornado se o storage insight não existir }

if ($existingConfig) { Set-AzureOperationalInsightsStorageInsight -Workspace $workspace -Name $insightsName -Tables $validTables -Containers $validContainers

} else { if ($storageAccount.ResourceType -eq "Microsoft.ClassicStorage/storageAccounts") { Switch-AzureMode -Name AzureServiceManagement $key = (Get-AzureStorageKey -StorageAccountName $storageAccount.Name).Primary Switch-AzureMode -Name AzureResourceManager } else { $key = (Get-AzureStorageAccountKey -ResourceGroupName $storageAccount.ResourceGroupName -Name $storageAccount.Name).Key1 } New-AzureOperationalInsightsStorageInsight -Workspace $workspace -Name $insightsName -StorageAccountResourceId $storageAccount.ResourceId -StorageAccountKey $key -Tables $validTables -Containers $validContainers } ``` Após configurar o espaço de trabalho do Operational Insights para ler das tabelas do Azure na sua conta de armazenamento, você deve fazer logon no Portal do Azure e procurar na guia Armazenamento o recurso Operational Insights. O resultado deve ser semelhante a esse: ![Configuração de armazenamento do Operational Insights no Portal do Azure](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/oi-connected-tables-list.png)

### Pesquise e exiba os logs no Operational Insights
Depois de configurar o seu espaço de trabalho do Operational Insights para ler os logs da conta de armazenamento especificada pode levar até 10 minutos para que os logs sejam exibidos na interface do usuário do Operational Insights. Para garantir que há novos logs gerados, você deve implantar um aplicativo da Malha de Serviço para seu cluster já que isso irá gerar eventos operacionais da plataforma de Malha de Serviço.

1. Para exibir os logs, selecione LogSearch na página principal do portal Operational Insights. ![Opção de Pesquisa de Log do Operational Insights](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/log-search-button-oi.png)

2. Na página de pesquisa de log, use esta consulta "Type=ServiceFabricOperationalEvent" e você verá os logs operacionais do cluster conforme mostrado abaixo. Para ver todos os logs do modelo de programação do Ator que você precisa consultar para "Type=ServiceFabricReliableActorEvent" e para ver todos os logs dos Reliable Services do tipo de modelo de programação "Type=ServiceFabricReliableServiceEvent" ![Exibição e Consulta de Log do Operational Insights](./media/service-fabric-diagnostics-how-to-setup-wad-operational-insights/view-logs-oi.png)

### Consultas de exemplo para ajudar a solucionar problemas
Estes são exemplos de alguns cenários e as consultas que você pode usar para solucionar esses problemas.

1. Para saber se o RunAsync foi chamado para um serviço específico pela Malha de Serviço. Você talvez queira fazer isso para descartar a possibilidade de um serviço estar falhando ao iniciar. Para fazer isso pesquise usando uma consulta semelhante à mostrada abaixo tendo substituído o nome do serviço e do aplicativo para coincidir com o que você implantou em veja se algum resultado é retornado.

    ```
    Type=ServiceFabricReliableServiceEvent AND ServiceName="fabric:/Application2/Stateless1" AND "RunAsync has been invoked"
    ```

2. Se você estiver executando um serviço com estado e gostaria de ver se alguma exceção que foi lançada por ele foi marcadas como falha pela Malha de Serviço, então você pode encontrar esses eventos com uma consulta semelhante a esta.

    ```
    Type=ServiceFabricReliableServiceEvent AND ServiceName="fabric:/Application2/Stateful1" AND TaskName=StatefulRunAsyncFailure
    ```

3. Para localizar eventos correspondentes para todas as exceções geradas por métodos de Ator em todos os aplicativos e serviços implantados uma consulta como esta pode ser usada.

    ```
    Type=ServiceFabricReliableActorEvent AND TaskName=ActorMethodThrewException
    ```

## Atualizar o WAD para coletar e carregar os logs de novos canais EventSource
Para atualizar o WAD para coletar logs de um novo canal EventSource que representa um novo aplicativo que você está prestes a implantar você apenas precisa executar as mesmas etapas como na [seção acima](#deploywadarm) que descreve a configuração do WAD para um cluster existente. Você precisará atualizar a seção EtwEventSourceProviderConfiguration no WadConfigUpdate.json para adicionar entradas para o novo EventSources antes de aplicar a atualização de configuração por meio do comando ARM. A tabela para o carregamento será a mesma (ETWEventTable) já que esta é a tabela que está configurada da qual o Operational Insights irá ler os eventos de ETW do aplicativo.


## Próximas etapas
Verifique os eventos de diagnóstico emitidos para [Reliable Actors](service-fabric-reliable-actors-diagnostics.md) e [Reliable Services](service-fabric-reliable-services-diagnostics.md) para entender mais detalhadamente os eventos que você deve examinar na solução de problemas.

<!---HONumber=Nov15_HO4-->