<properties
   pageTitle="Como coletar logs com o Diagnóstico do Azure | Microsoft Azure"
   description="Este artigo descreve como configurar o Diagnóstico do Azure para coletar logs de um cluster do Service Fabric em execução no Azure."
   services="service-fabric"
   documentationCenter=".net"
   authors="ms-toddabel"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/28/2016"
   ms.author="toddabel"/>


# Como coletar logs com o Diagnóstico do Azure

> [AZURE.SELECTOR]
- [Windows](service-fabric-diagnostics-how-to-setup-wad.md)
- [Linux](service-fabric-diagnostics-how-to-setup-lad.md)

Quando você estiver executando um cluster de Service Fabric do Azure, é uma boa ideia coletar os logs de todos os nós em um local central. Ter os logs em um local central facilita analisar e solucionar quaisquer problemas no cluster ou nos aplicativos e serviços em execução nesse cluster. Uma forma de carregar e coletar logs é usar a extensão de Diagnóstico do Azure, que carrega os logs no Armazenamento do Azure. Os logs realmente não são tão úteis diretamente no armazenamento, mas um processo externo pode ser usado para ler os eventos do armazenamento e colocá-los em um produto, como o [Log Analytics](../log-analytics/log-analytics-service-fabric.md) ou a [Pesquisa Elástica](service-fabric-diagnostic-how-to-use-elasticsearch.md), ou outra solução de análise de log.

## Pré-requisitos
Essas ferramentas serão usadas para executar algumas das operações neste documento:

* [Diagnóstico do Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) (relacionado aos Serviços de Nuvem do Azure, mas tem boas informações e exemplos)
* [Gerenciador de Recursos do Azure](../resource-group-overview.md)
* [PowerShell do Azure](../powershell-install-configure.md)
* [Cliente do Azure Resource Manager](https://github.com/projectkudu/ARMClient)
* [Criar uma máquina virtual do Windows com monitoramento e diagnóstico usando o modelo do Gerenciador de Recursos do Azure](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md)


## Fontes de log diferentes que você talvez queira coletar
1. **Logs do Service Fabric:** emitidos pela plataforma para canais ETW e EventSource padrões. Os logs podem ser de vários tipos:
  - Eventos operacionais: logs para operações executadas na plataforma do Service Fabric. Os exemplos incluem criação de aplicativos e serviços, alterações de estado do nó e informações de atualização.
  - [Eventos do modelo de programação do ator](service-fabric-reliable-actors-diagnostics.md)
  - [Eventos do modelo de Reliable Services](service-fabric-reliable-services-diagnostics.md)
2. **Eventos do aplicativo:** eventos emitidos do código de serviços e escritos usando a classe auxiliar EventSource fornecida nos modelos do Visual Studio. Para obter mais informações sobre como gravar logs de seu aplicativo, consulte [este artigo sobre como monitorar e diagnosticar serviços em uma configuração de máquina local](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).


## Implantar as extensões de diagnóstico
A primeira etapa para coletar logs será implantar a extensão Diagnóstico em cada uma das VMs no cluster do Service Fabric. A extensão de Diagnóstico coleta logs em cada VM e carrega-os para a conta de armazenamento especificada. As etapas variam um pouco dependendo de se você usa o Portal do Azure ou o Gerenciador de Recursos do Azure e se a implantação está sendo feita como parte da criação do cluster ou para um cluster que já existe. Vejamos as etapas para cada cenário.

### Implante a extensão de diagnóstico como parte da criação do cluster por meio do portal
Para implantar o a extensão de diagnóstico nas VMs do cluster como parte da criação do cluster, é usado o painel Configurações de Diagnóstico mostrado na imagem abaixo. Para habilitar a coleta de eventos de Ator ou Serviço Confiável, certifique-se de que o diagnóstico esteja definido como **Ativado**, que é a configuração padrão. Depois que o cluster tiver sido criado, essas configurações não poderão ser alteradas usando o portal.

![Configuração de Diagnóstico do Azure no portal para a criação do cluster](./media/service-fabric-diagnostics-how-to-setup-wad/portal-cluster-creation-diagnostics-setting.png)

Os Logs de Suporte são **necessários** para que a equipe de suporte do Azure resolva as solicitações de suporte que você criar. Esses logs são coletados em tempo real e serão armazenados em uma das contas de armazenamento criadas no grupo de recursos. A definição Diagnóstico configura eventos no nível do aplicativo, incluindo eventos de [Ator](service-fabric-reliable-actors-diagnostics.md), os eventos de [Serviço Confiável](service-fabric-reliable-services-diagnostics.md) e alguns eventos do Service Fabric no nível do sistema a serem armazenados no armazenamento do Azure. Os produtos como a [Pesquisa Elástica](service-fabric-diagnostic-how-to-use-elasticsearch.md), ou seu próprio processo, podem selecionar os eventos na conta de armazenamento. Atualmente, não há nenhuma maneira de filtrar ou limpar os eventos que são enviados para a tabela. Se um processo para remover eventos da tabela não for implementado, a tabela continuará a crescer.

Ao criar um cluster usando o porta, é altamente recomendável que você baixe o modelo *antes de clicar em OK* para criar o cluster. Para obter detalhes, confira [Setup a Service Fabric cluster by using an Azure Resource Manager template](service-fabric-cluster-creation-via-arm.md) (Configurar um cluster do Service Fabric usando um modelo do Azure Resource Manager). Isso lhe dará um modelo ARM que pode ser usado para o cluster que você está prestes a criar. Isso é necessário para fazer alterações posteriormente. Nem todas as alterações podem ser feitas usando o portal. Os modelos podem ser exportados do portal usando as etapas a seguir, mas pode ser mais difícil usar esses modelos porque eles podem ter alguns valores nulos que deverão ter valores fornecidos ou nem todas as informações necessárias estarão presentes.

1. Abra seu grupo de recursos
2. Selecione Configurações para exibir o painel Configurações
3. Selecione Implantações para exibir o painel Histórico de implantação
4. Selecione uma implantação para exibir os detalhes da implantação
5. Selecione Exportar Modelo para exibir o painel Modelo
6. Selecione Salvar em arquivo para exportar um arquivo. zip contendo os arquivos de modelo, parâmetro e do PowerShell.

Depois de exportar os arquivos, uma modificação será necessária. Edite o arquivo **parameters.json** e remova o elemento **adminPassword**. Isso fará com que a senha seja solicitada quando o script de implantação for executado. Ao executar o script de implantação, você precisará corrigir os valores de parâmetro nulo. Usar o modelo baixado para atualizar uma configuração

1. Extraia o conteúdo para uma pasta no computador local
2. Modificar o conteúdo para refletir a nova configuração
3. Inicie o PowerShell e altere para a pasta para a qual você extraiu o conteúdo
4. Execute **deploy.ps1** e preencha subscriptionId, o nome do grupo de recursos (use o mesmo nome para atualizar a configuração) e um nome exclusivo da implantação


### Implante a extensão de diagnóstico como parte da criação do cluster usando o Azure Resource Manager
Para criar um cluster usando o Gerenciador de recursos, você precisa adicionar a configuração de Diagnóstico JSON para o modelo do Gerenciador de recursos completo do cluster antes de criar o cluster. Fornecemos um exemplo de modelo de Gerenciador de Recursos de cluster de cinco VMs com configuração de Diagnóstico adicionada a ele como parte dos exemplos do modelo de Gerenciador de Recursos. Você pode vê-lo nesse local na Galeria de exemplos do Azure: [cluster cinco nós com exemplo de modelo do Gerenciador de Recursos de Diagnóstico](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype-wad). Para ver a configuração do Diagnóstico no modelo do Resource Manager, abra o arquivo **azuredeploy.json** e pesquise **IaaSDiagnostics**. Para criar um cluster com este modelo basta pressionar o botão **Implantar no Azure** disponível no link acima.

Como alternativa você pode baixar o exemplo de Gerenciador de Recursos, fazer suas alterações e criar um cluster com o modelo modificado usando o comando `New-AzureRmResourceGroupDeployment` em uma janela do Azure PowerShell. Veja as informações abaixo para os parâmetros que você precisará para passar para o comando. Para obter informações detalhadas sobre como implantar um Grupo de Recursos usando o PowerShell, confira o artigo [Deploy a Resource Group with Azure Resource Manager template](../resource-group-template-deploy.md) (Implantar um grupo de recursos com modelos do Azure Resource Manager)

```powershell

New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -Name $deploymentName -TemplateFile $pathToARMConfigJsonFile -TemplateParameterFile $pathToParameterFile –Verbose
```

### Implantar a extensão de diagnóstico em um cluster existente
Se você tiver um cluster existente que não tenha implantado diagnóstico ou se você quiser modificar uma configuração existente, adicione ou atualize-o seguindo estas etapas. Modifique o modelo ARM usado para criar o cluster existente ou baixe o modelo do portal, conforme descrito acima. Modifique o arquivo **template.json** executando as seguintes tarefas:

Adicione um novo recurso de armazenamento ao modelo, adicionando à seção de recursos.

##### Atualizar a seção de recursos
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

 Em seguida, adicione à seção de parâmetros logo após as definições da conta de armazenamento, entre "supportLogStorageAccountName" e "vmNodeType0Name". Substitua o texto do espaço reservado *nome da conta de armazenamento aqui* pelo nome da conta de armazenamento desejada.

##### Atualizar a seção de parâmetros
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
Em seguida, atualize a seção *VirtualMachineProfile* do arquivo **template.json**, adicionando o conteúdo a seguir dentro da matriz "extensions". Adicione uma vírgula no início ou no fim, dependendo de onde será inserido.

##### Adicionar à matriz de extensões de VirtualMachineProfile
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

Após modificar o arquivo **template.json** conforme descrito, republique o modelo ARM. Se o modelo tiver sido exportado, a execução do arquivo **deploy.ps1** republicará o modelo. Após implantar, verifique se o status de *ProvisioningState* é *Com êxito*.


## Atualizar o Diagnóstico para coletar e carregar os logs de novos canais EventSource
Para atualizar o diagnóstico para coletar logs de novos canais de EventSource que representam um novo aplicativo que você está prestes a implantar, basta executar as mesmas etapas da [seção acima](#deploywadarm), que descrevem a configuração do diagnóstico para um cluster existente. Você precisará atualizar a seção *EtwEventSourceProviderConfiguration* no arquivo **template.json** para adicionar entradas ao novo EventSources antes de aplicar a atualização de configuração por meio do comando *New-AzureRmResourceGroupDeployment* do PowerShell. O nome da origem do evento é definido como parte do seu código no arquivo **ServiceEventSource.cs** gerado pelo Visual Studio.


## Próximas etapas
Verifique os eventos de diagnóstico emitidos para [Reliable Actors](service-fabric-reliable-actors-diagnostics.md) e [Reliable Services](service-fabric-reliable-services-diagnostics.md) para entender mais detalhadamente os eventos que você deve examinar na solução de problemas.


## Artigos relacionados
* [Aprenda a coletar contadores de desempenho ou logs usando extensões de diagnóstico](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md)
* [Service Fabric Solution in Log Analytics (Solução do Service Fabric no Log Analytics)](../log-analytics/log-analytics-service-fabric.md)

<!---HONumber=AcomDC_0928_2016-->