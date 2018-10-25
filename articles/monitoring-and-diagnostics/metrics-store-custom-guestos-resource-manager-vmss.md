---
title: Enviar métricas do SO convidado para o repositório de métricas do Azure Monitor usando um modelo do Resource Manager para um conjunto de dimensionamento de máquinas virtuais do Windows
description: Enviar métricas do SO convidado para o repositório de métricas do Azure Monitor usando um modelo do Resource Manager para um conjunto de dimensionamento de máquinas virtuais do Windows
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: howto
ms.date: 09/24/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: 7b600bd699ce7f9e4a6c7cba1a41b6bdece16bf0
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49343717"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-using-a-resource-manager-template-for-a-windows-virtual-machine-scale-set"></a>Enviar métricas do SO convidado para o repositório de métricas do Azure Monitor usando um modelo do Resource Manager para um conjunto de dimensionamento de máquinas virtuais do Windows

A [extensão WAD (Diagnóstico do Azure do Windows)](azure-diagnostics.md) do Azure Monitor permite que você colete logs e métricas do SO (sistema operacional) convidado executado como parte de uma Máquina Virtual, do Serviço de Nuvem ou do cluster do Service Fabric.  A extensão pode enviar telemetria para vários locais diferentes listados no artigo vinculado anteriormente.  

O artigo descreve o processo para enviar métricas de desempenho do SO convidado de um conjunto de dimensionamento de máquinas virtuais do Windows para o armazenamento de dados do Azure Monitor. Começando com o WAD versão 1.11, você pode gravar as métricas diretamente no armazenamento de métricas do Azure Monitor, no qual as métricas da plataforma padrão já são coletadas. Armazená-las nesse local permite que você acesse as mesmas ações disponíveis para as métricas da plataforma.  As ações incluem alertas quase em tempo real, criação de gráficos, roteamento, acesso a partir da API REST e muito mais.  Anteriormente, a extensão WAD gravava no Armazenamento do Azure, mas não no armazenamento de dados do Azure Monitor.  

Se você não estiver familiarizado com os modelos do Resource Manager, saiba mais sobre as [implantações do modelo](../azure-resource-manager/resource-group-overview.md), sua estrutura e sintaxe.  

## <a name="pre-requisites"></a>Pré-requisitos

- Sua assinatura precisará ser registrada em [Microsoft.Insights](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) 

- Você precisará ter o [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) instalado ou poderá usar o [Azure CloudShell](https://docs.microsoft.com/azure/cloud-shell/overview.md) 


## <a name="set-up-azure-monitor-as-a-data-sink"></a>Configurar o Azure Monitor como um coletor de dados 
A extensão do Diagnóstico do Azure usa um recurso chamado "coletores de dados" para encaminhar as métricas e os logs para locais diferentes.  As etapas a seguir mostram como usar um modelo do Resource Manager e o PowerShell para implantar uma VM usando o novo coletor de dados do "Azure Monitor". 

## <a name="author-resource-manager-template"></a>Criar modelo do Resource Manager 
Para este exemplo, você pode usar um modelo de exemplo disponível publicamente. Os modelos iniciais estão em https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-autoscale  

- **Azuredeploy.json** é um modelo do Resource Manager pré-configurado para a implantação de um conjunto de dimensionamento de máquinas virtuais

- **Azuredeploy.parameters.json** é um arquivo de parâmetros que armazena informações como o nome de usuário e a senha que você deseja definir para sua VM. Durante a implantação, o modelo do Resource Manager usa os parâmetros definidos no arquivo. 

Baixe e salve os dois arquivos localmente. 

###  <a name="modify-azuredeployparametersjson"></a>Modificar azuredeploy.parameters.json
Abra o arquivo *azuredeploy.parameters.json* 

- Forneça a **vmSKU** que você gostaria de implantar (recomendamos Standard_D2_v3) 
- Especifique a **windowsOSVersion** que você desejaria usar em seu conjunto de dimensionamento de máquinas virtuais (recomendamos 2016-Datacenter) 
- Nomeie o recurso do conjunto de dimensionamento de máquinas virtuais a ser implantado usando uma propriedade **vmssName**. Por exemplo, *VMSS-WAD-TEST*.    
- Especifique o número de VMs que você gostaria de ter em execução no conjunto de dimensionamento de máquinas virtuais usando a propriedade **instanceCount**
- Insira valores para **adminUsername** e **adminPassword** para o conjunto de dimensionamento de máquinas virtuais. Esses parâmetros são usados para o acesso remoto às VMs no conjunto de dimensionamento. Esses parâmetros são usados para acesso remoto à VM. NÃO use os valores fornecidos no modelo para evitar que sua VM seja sequestrada. Os bots varrem a internet em busca de nomes de usuário e senhas em repositórios públicos do GitHub. É provável que eles testem as VMs com esses padrões. 


###  <a name="modify-azuredeployjson"></a>Modificar azuredeploy.json
Abra o arquivo *azuredeploy.json* 

Adicione uma variável para conter as informações da conta de armazenamento no modelo do Resource Manager. Você ainda precisa fornecer uma Conta de Armazenamento como parte da instalação da extensão de diagnóstico. Todos os logs e/ou contadores de desempenho especificados no arquivo de configuração de diagnóstico são gravados na conta de armazenamento especificada, além de serem enviados para o repositório de métricas do Azure Monitor. 

```json
"variables": { 
//add this line       
"storageAccountName": "[concat('storage', uniqueString(resourceGroup().id))]", 
 ```
 
Localize a definição do Conjunto de Dimensionamento de Máquinas Virtuais na seção de recursos e adicione a seção "identity" à configuração. Isso faz com que o Azure atribua a ele uma identidade do sistema. Essa etapa faz com que as VMs no conjunto de dimensionamento possam emitir métricas de convidado sobre si mesmas para o Azure Monitor.  

```json
  { 
      "type": "Microsoft.Compute/virtualMachineScaleSets", 
      "name": "[variables('namingInfix')]", 
      "location": "[resourceGroup().location]", 
      "apiVersion": "2017-03-30", 
      //add these lines below
      "identity": { 
           "type": "systemAssigned" 
       }, 
       //end of lines to add
 ```

No recurso do conjunto de dimensionamento de máquinas virtuais, localize a seção **virtualMachineProfile**. Adicione um novo perfil chamado **extensionsProfile** para gerenciar as extensões.  


Em **extensionProfile**, adicione uma nova extensão ao modelo, como mostrado na **seção VMSS-WAD-extension**.  Esta seção é de identidades gerenciadas para a extensão de recursos do Azure que garante que as métricas que estão sendo emitidas sejam aceitas pelo Azure Monitor. O campo **nome** pode conter qualquer nome. 

O código abaixo da extensão MSI também adiciona a configuração e a extensão de diagnóstico, como um recurso de extensão, ao recurso do conjunto de dimensionamento de máquinas virtuais. Fique à vontade adicionar ou remover os contadores de desempenho conforme necessário. 

```json
          "extensionProfile": { 
            "extensions": [ 
            // BEGINNING of added code  
            // Managed identites for Azure resources   
                { 
                 "name": "VMSS-WAD-extension", 
                 "properties": { 
                       "publisher": "Microsoft.ManagedIdentity", 
                       "type": "ManagedIdentityExtensionForWindows", 
                       "typeHandlerVersion": "1.0", 
                       "autoUpgradeMinorVersion": true, 
                       "settings": { 
                             "port": 50342 
                           }, 
                       "protectedSettings": {} 
                     } 
                                
            }, 
            // add diagnostic extension. (Remove this comment after pasting.)
            { 
              "name": "[concat('VMDiagnosticsVmExt','_vmNodeType0Name')]", 
              "properties": { 
                   "type": "IaaSDiagnostics", 
                   "autoUpgradeMinorVersion": true, 
                   "protectedSettings": { 
                        "storageAccountName": "[variables('storageAccountName')]", 
                        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')),'2015-05-01-preview').key1]", 
                        "storageAccountEndPoint": "https://core.windows.net/" 
                   }, 
                   "publisher": "Microsoft.Azure.Diagnostics", 
                   "settings": { 
                        "WadCfg": { 
                              "DiagnosticMonitorConfiguration": { 
                                   "overallQuotaInMB": "50000", 
                                   "PerformanceCounters": { 
                                       "scheduledTransferPeriod": "PT1M", 
                                       "sinks": "AzMonSink", 
                                       "PerformanceCounterConfiguration": [ 
                                          { 
              "counterSpecifier": "\\Memory\\% Committed Bytes In Use", 
                                              "sampleRate": "PT15S" 
           }, 
           { 
              "counterSpecifier": "\\Memory\\Available Bytes", 
              "sampleRate": "PT15S" 
           }, 
           { 
              "counterSpecifier": "\\Memory\\Committed Bytes", 
              "sampleRate": "PT15S" 
           } 
                                       ] 
                                 }, 
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
                               }, 
                               "SinksConfig": { 
                                     "Sink": [ 
                                          { 
                                              "name": "AzMonSink", 
                                              "AzureMonitor": {} 
                                          } 
                                      ] 
                               } 
                         }, 
                         "StorageAccount": "[variables('storageAccountName')]" 
                         }, 
                        "typeHandlerVersion": "1.11" 
                  } 
           } 
            ] 
          }
          }
      }
    },
    //end of added code plus a few brackets. Be sure that the number and type of brackets match properly when done. 
    {
      "type": "Microsoft.Insights/autoscaleSettings",
...
```


Adicione dependsOn à conta de armazenamento para garantir que ela será criada na ordem correta. 
```json
"dependsOn": [ 
"[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]", 
"[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]" 
//add this line below
"[concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]" 
 ```

Crie uma conta de armazenamento, se já não houver uma no modelo.  
```json
"resources": [
  // add this code    
  {
     "type": "Microsoft.Storage/storageAccounts",
     "name": "[variables('storageAccountName')]",
     "apiVersion": "2015-05-01-preview",
     "location": "[resourceGroup().location]",
     "properties": {
       "accountType": "Standard_LRS"
      }
  },
  // end added code
  { 
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[variables('virtualNetworkName')]",
```

Salve e feche ambos os arquivos. 

## <a name="deploy-the-resource-manager-template"></a>Implantar o modelo do Resource Manager 

> [!NOTE]
> Você precisa ter a versão 1.5 ou posterior da extensão do Diagnóstico do Azure E precisa ter a propriedade "autoUpgradeMinorVersion": definida para *true* no modelo do Resource Manager.  Então, o Azure carregará a extensão apropriada ao iniciar a VM. Se você não tiver essas configurações em seu modelo, altere-as e reimplante o modelo. 


Para implantar o modelo do Resource Manager, use o Azure PowerShell.  

1. Inicializar o PowerShell 
1. Entre no Azure usando `Login-AzureRmAccount`
1. Obtenha sua lista de assinaturas usando `Get-AzureRmSubscription`
1. Defina a assinatura em que você criará ou atualizará a máquina virtual 

   ```PowerShell
   Select-AzureRmSubscription -SubscriptionName "<Name of the subscription>" 
   ```
1. Crie um novo grupo de recursos para a VM sendo implantada e execute o comando abaixo 

   ```PowerShell
    New-AzureRmResourceGroup -Name "VMSSWADtestGrp" -Location "<Azure Region>" 
   ```

   > [!NOTE]  
   > Lembre-se de usar uma região do Azure habilitada para métricas personalizadas. Consulte 
 
1. Execute os seguintes comandos para implantar a VM com  
   > [!NOTE] 
   > Se quiser atualizar um conjunto de dimensionamento existente, adicione *-Mode Incremental* ao final do comando a seguir. 
 
   ```PowerShell
   New-AzureRmResourceGroupDeployment -Name "VMSSWADTest" -ResourceGroupName "VMSSWADtestGrp" -TemplateFile "<File path of your azuredeploy.JSON file>" -TemplateParameterFile "<File path of your azuredeploy.parameters.JSON file>"  
   ```

1. Depois da implantação bem-sucedida, você deverá ser capaz de encontrar o conjunto de dimensionamento de máquinas virtuais no portal do Azure e ele deverá emitir métricas para o Azure Monitor. 

   > [!NOTE] 
   > Você pode encontrar erros relacionados ao vmSkuSize selecionado. Se isso acontecer, volte para o arquivo azuredeploy.json e atualize o valor padrão do parâmetro vmSkuSize. Nesse caso, é recomendável tentar "Standard_DS1_v2". 


## <a name="chart-your-metrics"></a>Fazer um gráfico das métricas 

1. Entre no Portal do Azure 

1. No menu à esquerda, clique em **Monitor** 

1. Na página do Monitor, clique em **Métricas**. 

   ![Página de métricas](./media/metrics-store-custom-rest-api/metrics.png) 

1. Altere o período de agregação para **Últimos 30 minutos**.  

1. Na lista suspensa do recurso, selecione o conjunto de dimensionamento de máquinas virtuais que você acabou de criar.  

1. Na lista suspensa de namespaces, selecione **azure.vm.windows.guest** 

1. Na lista suspensa de métricas, selecione **Memória\%Bytes Confirmados em Uso**.  

Em seguida, também é possível usar as dimensões nessa métrica a fim de gerar seu gráfico para determinada VM no conjunto de dimensionamento, ou plotar cada VM no conjunto de dimensionamento. 



## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [métricas personalizadas](metrics-custom-overview.md).

