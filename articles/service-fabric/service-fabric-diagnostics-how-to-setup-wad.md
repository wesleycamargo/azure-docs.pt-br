---
title: "Coletar logs usando o Diagnóstico do Azure | Microsoft Docs"
description: "Este artigo descreve como configurar o Diagnóstico do Azure para coletar logs de um cluster do Service Fabric em execução no Azure."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 9f7e1fa5-6543-4efd-b53f-39510f18df56
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/17/2017
ms.author: dekapur
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 7c3311800af27016b7f993b375055bbc65d9a727
ms.lasthandoff: 04/27/2017


---
# <a name="collect-logs-by-using-azure-diagnostics"></a>Coletar logs usando o Diagnóstico do Azure
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-setup-wad.md)
> * [Linux](service-fabric-diagnostics-how-to-setup-lad.md)
> 
> 

Quando você estiver executando um cluster de Service Fabric do Azure, é uma boa ideia coletar os logs de todos os nós em um local central. Ter os logs em um local central ajuda a analisar e solucionar problemas no cluster ou nos aplicativos e serviços em execução nesse cluster.

Uma forma de carregar e coletar logs é usar a extensão de Diagnóstico do Azure, que carrega os logs no Armazenamento do Azure, no Application Insights do Azure ou nos Hubs de Eventos do Azure. Os logs não são tão úteis diretamente no armazenamento ou em Hubs de Eventos. Mas você pode usar um processo externo para ler os eventos do armazenamento e colocá-los em um produto, como o [Log Analytics](../log-analytics/log-analytics-service-fabric.md) ou em outra solução de análise de log. O [Application Insights do Azure](https://azure.microsoft.com/services/application-insights/) apresenta um serviço abrangente interno de pesquisa e análise de logs.

## <a name="prerequisites"></a>Pré-requisitos
Você pode usar essas ferramentas para executar algumas das operações neste documento:

* [Diagnóstico do Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) (relacionado aos Serviços de Nuvem do Azure, mas tem boas informações e exemplos)
* [Gerenciador de Recursos do Azure](../azure-resource-manager/resource-group-overview.md)
* [PowerShell do Azure](/powershell/azure/overview)
* [Cliente do Azure Resource Manager](https://github.com/projectkudu/ARMClient)
* [Modelo do Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="log-sources-that-you-might-want-to-collect"></a>Fontes de log que você talvez queira coletar
* **Logs do Service Fabric:** emitidos pela plataforma para canais de ETW (Rastreamento de Eventos para Windows) e EventSource padrão. Os logs podem ser de vários tipos:
  * Eventos operacionais: logs para operações executadas pela plataforma do Service Fabric. Os exemplos incluem criação de aplicativos e serviços, alterações de estado do nó e informações de atualização.
  * [Eventos do modelo de programação Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
  * [Eventos do modelo de programação Reliable Services](service-fabric-reliable-services-diagnostics.md)
* **Eventos do aplicativo:** eventos emitidos do código de serviços e escritos usando a classe auxiliar EventSource fornecida nos modelos do Visual Studio. Para obter mais informações sobre como gravar logs de seu aplicativo, consulte [Monitorar e diagnosticar serviços em uma configuração de desenvolvimento do computador local](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).

## <a name="deploy-the-diagnostics-extension"></a>Implantar a extensão de Diagnóstico
A primeira etapa para coletar logs será implantar a extensão Diagnóstico em cada uma das VMs no cluster do Service Fabric. A extensão de Diagnóstico coleta logs em cada VM e os carrega para a conta de armazenamento especificada. As etapas variam um pouco com base em seu uso do Portal do Azure ou do Azure Resource Manager. As etapas também variam com base em se a implantação faz parte da criação do cluster ou é para um cluster que já existe. Vejamos as etapas para cada cenário.

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-through-the-portal"></a>Implantar a extensão de Diagnóstico como parte da criação de cluster por meio do portal
Para implantar a extensão de Diagnóstico nas VMs no cluster como parte da criação do cluster, você usa o painel de configurações de Diagnóstico mostrado na imagem abaixo. Para habilitar a coleta de eventos de Reliable Actors ou Reliable Services, certifique-se de que o diagnóstico esteja definido como **Ativado** (a configuração padrão). Depois de criar o cluster, você não poderá alterar essas configurações por meio do portal.

![Configuração de Diagnóstico do Azure no portal para a criação do cluster](./media/service-fabric-diagnostics-how-to-setup-wad/portal-cluster-creation-diagnostics-setting.png)

A equipe de suporte do Azure *requer* os logs de suporte para ajudar a resolver as solicitações de suporte que você criar. Esses logs são coletados em tempo real e são armazenados em uma das contas de armazenamento criadas no grupo de recursos. As definições de Diagnóstico configuram eventos de nível de aplicativo. Esses eventos incluem eventos de [Reliable Actors](service-fabric-reliable-actors-diagnostics.md), eventos de [Reliable Services](service-fabric-reliable-services-diagnostics.md) e alguns eventos de Service Fabric no nível de sistema a serem armazenados no Armazenamento do Azure.

Os produtos como a [Elasticsearch](https://www.elastic.co/guide/index.html), ou seu próprio processo, podem obter os eventos na conta de armazenamento. Atualmente, não há nenhuma maneira de filtrar ou limpar os eventos que são enviados para a tabela. Se você não implantar um processo para remover eventos da tabela, a tabela continuará crescendo.

Ao criar um cluster usando o portal, é altamente recomendável que você baixe o modelo **antes de clicar em OK** para criar o cluster. Para obter detalhes, confira [Setup a Service Fabric cluster by using an Azure Resource Manager template](service-fabric-cluster-creation-via-arm.md) (Configurar um cluster do Service Fabric usando um modelo do Azure Resource Manager). Você precisará do modelo para fazer alterações posteriormente, porque não é possível fazer algumas alterações usando o portal.

Você pode exportar modelos por meio do portal usando as etapas a seguir. No entanto, esses modelos podem ser mais difíceis de usar porque podem ter valores nulos em que estão faltando informações necessárias.

1. Abra seu grupo de recursos.
2. Selecione **Configurações** para exibir o painel Configurações.
3. Selecione **Implantações** para exibir o painel de histórico de implantação.
4. Selecione uma implantação para exibir os detalhes da implantação.
5. Selecione **Exportar Modelo** para exibir o painel de modelo.
6. Selecione **Salvar no arquivo** para exportar um arquivo .zip contendo os arquivos de modelo, parâmetro e do PowerShell.

Depois de exportar os arquivos, é necessário fazer uma modificação. Edite o arquivo parameters.json e remova o elemento **adminPassword**. Isso fará com que a senha seja solicitada quando o script de implantação for executado. Ao executar o script de implantação, você pode precisar corrigir os valores de parâmetro nulo.

Para usar o modelo baixado para atualizar uma configuração:

1. Extraia o conteúdo para uma pasta no computador local.
2. Modifique o conteúdo para refletir a nova configuração.
3. Inicie o PowerShell e altere para a pasta para a qual você extraiu o conteúdo.
4. Execute **deploy.ps1** e preencha a ID da assinatura, o nome do grupo de recursos (use o mesmo nome para atualizar a configuração) e um nome exclusivo da implantação.

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-by-using-azure-resource-manager"></a>Implantar a extensão de diagnóstico como parte da criação de cluster usando o Gerenciador de Recursos do Azure
Para criar um cluster usando o Resource Manager, você precisa adicionar a configuração de Diagnóstico JSON para o modelo do Resource Manager completo do cluster antes de criar o cluster. Fornecemos um exemplo de modelo de Gerenciador de Recursos de cluster de cinco VMs com configuração de Diagnóstico adicionada a ele como parte dos exemplos do modelo de Gerenciador de Recursos. Você pode vê-lo nesse local na Galeria de exemplos do Azure: [cluster cinco nós com exemplo de modelo do Gerenciador de Recursos de Diagnóstico](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype-wad).

Para ver a configuração do Diagnóstico no modelo do Resource Manager, abra o arquivo azuredeploy.json e pesquise **IaaSDiagnostics**. Para criar um cluster usando este modelo, selecione o botão **Implantar no Azure** disponível no link anterior.

Como alternativa você pode baixar o exemplo de Gerenciador de Recursos, fazer suas alterações e criar um cluster com o modelo modificado usando o comando `New-AzureRmResourceGroupDeployment` em uma janela do Azure PowerShell. Consulte no código a seguir para ver os parâmetros que você passa para o comando. Para obter informações detalhadas sobre como implantar um grupo de recursos usando o PowerShell, consulte o artigo [Deploy a resource group with the Azure Resource Manager template](../azure-resource-manager/resource-group-template-deploy.md) (Implantar o grupo de recursos com o modelo do Azure Resource Manager).

```powershell

New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -Name $deploymentName -TemplateFile $pathToARMConfigJsonFile -TemplateParameterFile $pathToParameterFile –Verbose
```

### <a name="deploy-the-diagnostics-extension-to-an-existing-cluster"></a>Implantar a extensão de diagnóstico em um cluster existente
Se você tiver um cluster existente que não tenha o Diagnóstico implantado ou se você quiser modificar uma configuração existente, você poderá adicioná-lo ou atualizá-lo. Modifique o modelo do Resource Manager usado para criar o cluster existente ou baixe o modelo do portal, conforme descrito anteriormente. Modifique o arquivo template.json executando as seguintes tarefas.

Adicione um novo recurso de armazenamento ao modelo, adicionando à seção de recursos.

```json
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[parameters('applicationDiagnosticsStorageAccountName')]",
  "location": "[parameters('computeLocation')]",
  "properties": {
    "accountType": "[parameters('applicationDiagnosticsStorageAccountType')]"
  },
  "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
  }
},
```

 Em seguida, adicione à seção de parâmetros logo após as definições da conta de armazenamento, entre `supportLogStorageAccountName` e `vmNodeType0Name`. Substitua o texto do espaço reservado *nome da conta de armazenamento aqui* pelo nome da conta de armazenamento.

```json
    "applicationDiagnosticsStorageAccountType": {
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS"
      ],
      "defaultValue": "Standard_LRS",
      "metadata": {
        "description": "Replication option for the application diagnostics storage account"
      }
    },
    "applicationDiagnosticsStorageAccountName": {
      "type": "string",
      "defaultValue": "storage account name goes here",
      "metadata": {
        "description": "Name for the storage account that contains application diagnostics data from the cluster"
      }
    },
```
Em seguida, atualize a seção `VirtualMachineProfile` do arquivo template.json adicionando o conteúdo a seguir dentro da matriz extensions. Adicione uma vírgula no início ou no fim, dependendo de onde será inserido.

```json
{
    "name": "[concat(parameters('vmNodeType0Name'),'_Microsoft.Insights.VMDiagnosticsSettings')]",
    "properties": {
        "type": "IaaSDiagnostics",
        "autoUpgradeMinorVersion": true,
        "protectedSettings": {
        "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
        "storageAccountEndPoint": "https://core.windows.net/"
        },
        "publisher": "Microsoft.Azure.Diagnostics",
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
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
                {
                    "provider": "Microsoft-ServiceFabric-Services",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableServiceEventTable"
                    }
                }
                ],
                "EtwManifestProviderConfiguration": [
                {
                    "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                    "scheduledTransferLogLevelFilter": "Information",
                    "scheduledTransferKeywordFilter": "4611686018427387904",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricSystemEventTable"
                    }
                }
                ]
            }
            }
        },
        "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountName')]"
        },
        "typeHandlerVersion": "1.5"
    }
}
```

Após modificar o arquivo template.json conforme descrito, republique o modelo do Resource Manager. Se o modelo tiver sido exportado, a execução do arquivo deploy.ps1 republicará o modelo. Após implantar, verifique se o status de **ProvisioningState** é **Com êxito**.

## <a name="update-diagnostics-to-collect-health-and-load-events"></a>Atualizar o diagnóstico para coletar eventos de integridade e de carga

Começando com a versão 5.4 do Service Fabric, eventos de métrica de carga e integridade estão disponíveis para coleta. Esses eventos refletir os eventos gerados pelo sistema ou seu código usando a integridade ou APIs de relatórios de carga como [ReportPartitionHealth](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportpartitionhealth.aspx) ou [ReportLoad](https://msdn.microsoft.com/library/azure/system.fabric.iservicepartition.reportload.aspx). Isso permite agregar e exibir a integridade do sistema ao longo do tempo e para alertas com base em eventos de integridade ou de carga. Para exibir esses eventos no Visualizador de Eventos de Diagnóstico do Visual Studio, adicione "Microsoft-ServiceFabric:4:0x4000000000000008" à lista de provedores de ETW.

Para coletar os eventos, modificar o modelo do Resource Manager para incluir

```json
  "EtwManifestProviderConfiguration": [
    {
      "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
      "scheduledTransferLogLevelFilter": "Information",
      "scheduledTransferKeywordFilter": "4611686018427387912",
      "scheduledTransferPeriod": "PT5M",
      "DefaultEvents": {
        "eventDestination": "ServiceFabricSystemEventTable"
      }
    }
```

## <a name="update-diagnostics-to-collect-and-upload-logs-from-new-eventsource-channels"></a>Atualizar o Diagnóstico para coletar e carregar os logs de novos canais EventSource
Para atualizar o Diagnóstico para coletar logs de novos canais de EventSource que representam um novo aplicativo que você está prestes a implantar, basta executar as mesmas etapas da [seção acima](#deploywadarm) para configurar o Diagnóstico para um cluster existente.

Atualize a seção `EtwEventSourceProviderConfiguration` no arquivo template.json para adicionar entradas para novos canais de EventSource antes de aplicar a atualização de configuração usando o comando `New-AzureRmResourceGroupDeployment` do PowerShell. O nome da origem do evento é definido como parte do seu código no arquivo Visual Studio-generated ServiceEventSource.cs.

Por exemplo, se a origem do evento for denominada My-Eventsource, adicione o seguinte código para colocar os eventos de My-Eventsource em uma tabela chamada MyDestinationTableName.

```json
        {
            "provider": "My-Eventsource",
            "scheduledTransferPeriod": "PT5M",
            "DefaultEvents": {
            "eventDestination": "MyDestinationTableName"
            }
        }
```

Para coletar contadores de desempenho ou logs de eventos, modifique o modelo do Resource Manager usando os exemplos fornecidos em [Criar uma máquina virtual do Windows com monitoramento e diagnóstico usando um modelo do Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Em seguira, republique o modelo do Resource Manager.

## <a name="next-steps"></a>Próximas etapas
Para compreender com mais detalhes quais eventos você deve analisar enquanto soluciona problemas, consulte os eventos de diagnóstico para [Reliable Actors](service-fabric-reliable-actors-diagnostics.md) e [Reliable Services](service-fabric-reliable-services-diagnostics.md).

## <a name="related-articles"></a>Artigos relacionados
* [Aprenda a coletar contadores de desempenho ou logs usando a extensão de Diagnóstico](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Solução do Service Fabric no Log Analytics](../log-analytics/log-analytics-service-fabric.md)


