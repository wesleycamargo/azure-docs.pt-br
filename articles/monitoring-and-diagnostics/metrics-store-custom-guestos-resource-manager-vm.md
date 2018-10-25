---
title: Enviar métricas do SO convidado para o repositório de métricas do Azure Monitor usando um modelo do Resource Manager para uma máquina virtual do Windows
description: Enviar métricas do SO convidado para o repositório de métricas do Azure Monitor usando um modelo do Resource Manager para uma máquina virtual do Windows
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: howto
ms.date: 09/24/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: f3076054eb6e18eb5143a34ba558c1f9e43ea4a5
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49345179"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-using-a-resource-manager-template-for-a-windows-virtual-machine"></a>Enviar métricas do SO convidado para o repositório de métricas do Azure Monitor usando um modelo do Resource Manager para uma máquina virtual do Windows

A [extensão WAD (Diagnóstico do Azure do Windows)](azure-diagnostics.md) do Azure Monitor permite que você colete logs e métricas do SO convidado (sistema operacional convidado) executado como parte de uma Máquina Virtual, Serviço de Nuvem ou Service Fabric.  A extensão pode enviar telemetria para vários locais diferentes listados no artigo vinculado anteriormente.  

Este artigo descreve o processo para enviar métricas de desempenho do SO convidado de uma Máquina Virtual do Windows para o armazenamento de dados do Azure Monitor. Começando no WAD versão 1.11, você pode gravar as métricas diretamente no repositório de métricas do Azure Monitor em que as métricas da plataforma padrão já são coletadas. Armazená-las nesse local permite que você acesse as mesmas ações disponíveis para as métricas da plataforma.  As ações incluem alertas quase em tempo real, criação de gráficos, roteamento, acesso a partir da API REST e muito mais.  Anteriormente, a extensão do WAD gravava no Armazenamento do Azure, mas não no armazenamento de dados do Azure Monitor.   

Se estiver familiarizado com modelos do Resource Manager, saiba mais sobre as [implantações de modelo](../azure-resource-manager/resource-group-overview.md) e sua estrutura e sintaxe.  

## <a name="pre-requisites"></a>Pré-requisitos

- Sua assinatura precisará ser registrada em [Microsoft.Insights](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) 

- Você precisará ter o [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) instalado ou poderá usar o [Azure CloudShell](https://docs.microsoft.com/azure/cloud-shell/overview.md) 

 
## <a name="set-up-azure-monitor-as-a-data-sink"></a>Configurar o Azure Monitor como um coletor de dados 
A extensão do Diagnóstico do Azure usa um recurso chamado "coletores de dados" para encaminhar as métricas e os logs para locais diferentes.  As etapas a seguir mostram como usar um modelo do Resource Manager e o PowerShell para implantar uma VM usando o novo coletor de dados do "Azure Monitor". 

## <a name="author-resource-manager-template"></a>Criar modelo do Resource Manager 
Para este exemplo, você pode usar um modelo de exemplo disponível publicamente. Os modelos iniciais estão em https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows 

- **Azuredeploy.json** é um modelo do Resource Manager pré-configurado para implantação de uma máquina Virtual. 

- **Azuredeploy.parameters.json** é um arquivo de parâmetros que armazena informações como o nome de usuário e a senha você deseja definir para sua VM. Durante a implantação, o modelo do Resource Manager usa os parâmetros definidos no arquivo. 

Baixe e salve os dois arquivos localmente. 

###  <a name="modify-azuredeployparametersjson"></a>Modificar azuredeploy.parameters.json
Abra o arquivo *azuredeploy.parameters.json* 

1. Insira valores de *adminUsername* e *adminPassword* para a VM. Esses parâmetros são usados para acesso remoto à VM. NÃO use os valores fornecidos no modelo para evitar que sua VM seja sequestrada. Os bots varrem a internet em busca de nomes de usuário e senhas em repositórios públicos do GitHub. É provavelmente que eles testem VMs com esses padrões.  

1. Crie um dnsname exclusivo para a VM.  

### <a name="modify-azuredeployjson"></a>Modificar azuredeploy.json

Abra o arquivo *azuredeploy.json* 

Adicione uma ID da conta de armazenamento à seção **variables** do modelo após a entrada para **storageAccountName**.  

```json
// Find these lines 
"variables": { 
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'sawinvm')]", 

// Add this line directly below.  
    "accountid": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]", 
```

Adicione este identidade gerenciada para a extensão de recursos do Azure para o modelo na parte superior da seção "recursos".  A extensão garante que o Azure Monitor aceite as métricas que estão sendo emitidas.  

```json
//Find this code 
"resources": [
// Add this code directly below
    { 
        "type": "Microsoft.Compute/virtualMachines/extensions", 
        "name": "WADExtensionSetup", 
        "apiVersion": "2015-05-01-preview", 
        "location": "[resourceGroup().location]", 
        "dependsOn": [ 
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]" ], 
        "properties": { 
            "publisher": "Microsoft.ManagedIdentity", 
            "type": "ManagedIdentityExtensionForWindows", 
            "typeHandlerVersion": "1.0", 
            "autoUpgradeMinorVersion": true, 
            "settings": { 
                "port": 50342 
            } 
        } 
    }, 
```

Adicione a configuração de "identity" ao recurso da VM para garantir que o Azure atribua a extensão de MSI uma identidade do sistema. Essa etapa garante que a VM possa emitir métricas de convidado sobre si mesma para o Azure Monitor 

```json
// Find this section
                "subnet": {
            "id": "[variables('subnetRef')]"
            }
        }
        }
    ]
    }
},
{ 
    "apiVersion": "2017-03-30", 
    "type": "Microsoft.Compute/virtualMachines", 
    "name": "[variables('vmName')]", 
    "location": "[resourceGroup().location]", 
    // add these 3 lines below
    "identity": {  
    "type": "SystemAssigned" 
    }, 
    //end of added lines
    "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
    "hardwareProfile": {   
    ...
```

Adicione a seguinte configuração para habilitar a extensão de diagnóstico em uma Máquina Virtual do Windows.  Para uma Máquina Virtual simples baseada no Resource Manager, podemos adicionar a configuração de extensão à matriz de recursos da Máquina Virtual. A linha "sinks": "AzMonSink" e a "SinksConfig" correspondente mais adiante na seção habilitam a extensão a emitir métricas diretamente para o Azure Monitor. Fique à vontade adicionar ou remover contadores de desempenho conforme necessário.  


```json
        "networkProfile": {
            "networkInterfaces": [
            {
                "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
            }
            ]
        },
"diagnosticsProfile": { 
    "bootDiagnostics": { 
    "enabled": true, 
    "storageUri": "[reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob]" 
    } 
} 
}, 
//Start of section to add 
"resources": [        
{ 
            "type": "extensions", 
            "name": "Microsoft.Insights.VMDiagnosticsSettings", 
            "apiVersion": "2015-05-01-preview", 
            "location": "[resourceGroup().location]", 
            "dependsOn": [ 
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]" 
            ], 
            "properties": { 
            "publisher": "Microsoft.Azure.Diagnostics", 
            "type": "IaaSDiagnostics", 
            "typeHandlerVersion": "1.4", 
            "autoUpgradeMinorVersion": true, 
            "settings": { 
                "WadCfg": { 
                "DiagnosticMonitorConfiguration": { 
    "overallQuotaInMB": 4096, 
    "DiagnosticInfrastructureLogs": { 
                    "scheduledTransferLogLevelFilter": "Error" 
        }, 
                    "Directories": { 
                    "scheduledTransferPeriod": "PT1M", 
    "IISLogs": { 
                        "containerName": "wad-iis-logfiles" 
                    }, 
                    "FailedRequestLogs": { 
                        "containerName": "wad-failedrequestlogs" 
                    } 
                    }, 
                    "PerformanceCounters": { 
                    "scheduledTransferPeriod": "PT1M", 
                    "sinks": "AzMonSink", 
                    "PerformanceCounterConfiguration": [ 
                        { 
                        "counterSpecifier": "\\Memory\\Available Bytes", 
                        "sampleRate": "PT15S" 
                        }, 
                        { 
                        "counterSpecifier": "\\Memory\\% Committed Bytes In Use", 
                        "sampleRate": "PT15S" 
                        }, 
                        { 
                        "counterSpecifier": "\\Memory\\Committed Bytes", 
                        "sampleRate": "PT15S" 
                        } 
                    ] 
                    }, 
                    "WindowsEventLog": { 
                    "scheduledTransferPeriod": "PT1M", 
                    "DataSource": [ 
                        { 
                        "name": "Application!*" 
                        } 
                    ] 
                    }, 
                    "Logs": { 
                    "scheduledTransferPeriod": "PT1M", 
                    "scheduledTransferLogLevelFilter": "Error" 
                    } 
                }, 
                "SinksConfig": { 
                    "Sink": [ 
                    { 
                        "name" : "AzMonSink", 
                        "AzureMonitor" : {} 
                    } 
                    ] 
                } 
                }, 
                "StorageAccount": "[variables('storageAccountName')]" 
            }, 
            "protectedSettings": { 
                "storageAccountName": "[variables('storageAccountName')]", 
                "storageAccountKey": "[listKeys(variables('accountid'),'2015-06-15').key1]", 
                "storageAccountEndPoint": "https://core.windows.net/" 
            } 
            } 
        } 
        ] 
//End of section to add 
```


Salve e feche os dois arquivos 
 

## <a name="deploy-the-resource-manager-template"></a>Implantar o modelo do Resource Manager 

> [!NOTE]
> Você precisa estar executando a versão 1.5 ou posterior da extensão do Diagnóstico do Azure E precisa ter a propriedade "autoUpgradeMinorVersion": definida como "true" no modelo do Resource Manager.  O Azure, então, carrega a extensão apropriada ao iniciar a VM. Se não tiver essas configurações em seu modelo, altere-as e reimplante o modelo. 


Para implantar o modelo do Resource Manager, use o Azure PowerShell.  

1. Inicializar o PowerShell 
1. Faça logon no Azure usando `Login-AzureRmAccount`
1. Obtenha sua lista de assinaturas usando `Get-AzureRmSubscription`
1. Defina a assinatura em que você criará ou atualizará a máquina virtual 

   ```PowerShell
   Select-AzureRmSubscription -SubscriptionName "<Name of the subscription>" 
   ```
1. Crie um novo grupo de recursos para a VM que está sendo implantada e execute o comando abaixo 

   ```PowerShell
    New-AzureRmResourceGroup -Name "<Name of Resource Group>" -Location "<Azure Region>" 
   ```
   > [!NOTE] 
   > Lembre-se de [usar uma região do Azure habilitada para métricas personalizadas](metrics-custom-overview.md). 
 
1. Execute os seguintes comandos para implantar a VM com o  
   > [!NOTE] 
   > Se quiser atualizar uma VM existente, adicione *-Mode Incremental* ao final do comando a seguir. 
 
   ```PowerShell
   New-AzureRmResourceGroupDeployment -Name "<NameThisDeployment>" -ResourceGroupName "<Name of the Resource Group>" -TemplateFile "<File path of your Resource Manager template>" -TemplateParameterFile "<File path of your parameters file>" 
   ```
  
1. Depois que a implantação for bem-sucedida, você deverá ser capaz de encontrar a VM no Portal do Azure e ela deverá estar emitindo métricas para o Azure Monitor. 

   > [!NOTE] 
   > Você poderá encontrar erros relacionados ao vmSkuSize selecionado. Se isso acontecer, volte para o arquivo azuredeploy.json e atualize o valor padrão do parâmetro vmSkuSize. Nesse caso, é recomendável tentar "Standard_DS1_v2". 

## <a name="chart-your-metrics"></a>Traçar um gráfico das métricas 

1. Entre no portal do Azure 

1. No menu à esquerda, clique em **Monitorar** 

1. Na página Monitorar, clique em **Métricas**. 

   ![Página de métricas](./media/metrics-store-custom-rest-api/metrics.png) 

1. Altere o período de agregação para **Últimos 30 minutos**.  

1. Na lista suspensa de recursos, selecione a VM recém-criada. Se você não alterou o nome do modelo, ele deve ser *SimpleWinVM2*.  

1. Na lista suspensa de namespaces, selecione **azure.vm.windows.guest** 

1. Na lista suspensa de métricas, selecione **Memória\%Bytes Confirmados em Uso**.  
 

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [métricas personalizadas](metrics-custom-overview.md).