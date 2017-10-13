---
title: "Dimensionar automaticamente os conjuntos de dimensionamento de máquinas virtuais do Linux | Microsoft Docs"
description: "Configurar o dimensionamento automático para um conjunto de escala de máquina virtual do Linux usando a CLI do Azure"
services: virtual-machine-scale-sets
documentationcenter: 
author: Thraka
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 83e93d9c-cac0-41d3-8316-6016f5ed0ce4
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: adegeo
ms.openlocfilehash: eff4add1cb16fe25022787668dc1d2277845dd95
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="automatically-scale-linux-machines-in-a-virtual-machine-scale-set"></a>Dimensionar automaticamente computadores Linux em um conjunto de escala de máquina virtual
Os conjuntos de escala de máquina virtual facilitam a implantação e o gerenciamento de máquinas virtuais idênticas como um conjunto. Os conjuntos de dimensionamento fornecem uma camada de computação altamente escalonável e personalizável para aplicativos de hiperescala e suporte a imagens da plataforma Windows, imagens da plataforma Linux, imagens personalizadas e extensões. Para saber mais, consulte [Visão geral de conjuntos de escala de máquina virtual](virtual-machine-scale-sets-overview.md).

Este tutorial mostra como criar um conjunto de escala de máquinas virtuais Linux usando a versão mais recente do Ubuntu Linux. O tutorial também mostra como dimensionar automaticamente as máquinas no conjunto. Crie o conjunto de escala e defina o dimensionamento criando um modelo do Azure Resource Manager e implantando-o com o uso da CLI do Azure. Para obter mais informações sobre modelos, confira [Criação de modelos do Gerenciador de Recursos do Azure](../azure-resource-manager/resource-group-authoring-templates.md). Para saber mais sobre o dimensionamento automático de conjuntos de escala, consulte [Dimensionamento automático e conjuntos de escala de máquina virtual](virtual-machine-scale-sets-autoscale-overview.md).

Neste tutorial, você pode implantar os seguintes recursos e extensões:

* Microsoft.Storage/storageAccounts
* Microsoft.Network/virtualNetworks
* Microsoft.Network/publicIPAddresses
* Microsoft.Network/loadBalancers
* Microsoft.Network/networkInterfaces
* Microsoft.Compute/virtualMachines
* Microsoft.Compute/virtualMachineScaleSets
* Microsoft.Insights.VMDiagnosticsSettings
* Microsoft.Insights/autoscaleSettings

Para obter mais informações sobre os recursos do Resource Manager, consulte [Azure Resource Manager versus implantação clássica](../azure-resource-manager/resource-manager-deployment-model.md).

Antes de começar as etapas neste tutorial, [instale a CLI do Azure](../cli-install-nodejs.md).

## <a name="step-1-create-a-resource-group-and-a-storage-account"></a>Etapa 1: Criar um grupo de recursos e uma conta de armazenamento

1. **Entrar no Microsoft Azure**  
Em sua interface de linha de comando (Bash, Terminal, prompt de comando), mude para o modo do Resource Manager e [entre com sua ID corporativa ou de estudante](../xplat-cli-connect.md#scenario-1-azure-login-with-interactive-login). Siga os prompts para ter uma experiência de logon interativo em sua conta do Azure.

    ```cli   
    azure config mode arm

    azure login
    ```
   
    > [!NOTE]
    > Se tiver uma ID de trabalho ou de estudante e não tiver a autenticação de dois fatores habilitada, você poderá usar `azure login -u` com a ID de trabalho ou de estudante para fazer logon sem uma sessão interativa. Caso não tenha uma ID de trabalho ou de estudante, você poderá [criar uma ID de trabalho ou de estudante em sua conta pessoal da Microsoft](../active-directory/active-directory-users-create-azure-portal.md).
    
2. **Criar um grupo de recursos**  
Todos os recursos devem estar implantados em um grupo de recursos. Para este tutorial, nomeie o grupo de recursos **vmsstest1**.
   
    ```cli
    azure group create vmsstestrg1 centralus
    ```

3. **Implantar uma conta de armazenamento para o novo grupo de recursos**  
É nessa conta de armazenamento que o modelo é armazenado. Crie uma conta de armazenamento denominada **vmsstestsa**.
   
    ```cli
    azure storage account create -g vmsstestrg1 -l centralus --kind Storage --sku-name LRS vmsstestsa
    ```

## <a name="step-2-create-the-template"></a>Etapa 2: Criar o modelo
Um modelo do Gerenciador de Recursos do Azure permite implantar e gerenciar recursos do Azure juntos usando uma descrição JSON dos recursos e parâmetros de implantação associados.

1. Em seu editor favorito, crie o arquivo VMSSTemplate.json e adicione a estrutura inicial do JSON para dar suporte ao modelo.

    ```json
    {
      "$schema":"http://schema.management.azure.com/schemas/2014-04-01-preview/VM.json",
      "contentVersion": "1.0.0.0",
      "parameters": {
      },
      "variables": {
      },
      "resources": [
      ]
    }
    ```

2. Parâmetros nem sempre são necessários, mas eles fornecem uma maneira de entrar valores quando o modelo é implantado. Adicione esses parâmetros sob o elemento pai de parâmetros que você adicionou ao modelo.

    ```json
    "vmName": { "type": "string" },
    "vmSSName": { "type": "string" },
    "instanceCount": { "type": "string" },
    "adminUsername": { "type": "string" },
    "adminPassword": { "type": "securestring" },
    "resourcePrefix": { "type": "string" }
    ```
   
   * Um nome para a máquina virtual separada que é usado para acessar as máquinas no conjunto de escala.
   * Um nome para a conta de armazenamento onde o modelo é armazenado.
   * O número de instâncias de máquinas virtuais para criar inicialmente no conjunto de dimensionamento.
   * Um nome e senha da conta de administrador nas máquinas virtuais.
   * Um prefixo de nome para os recursos criados para dar suporte ao conjunto de escala.

3. As variáveis podem ser usadas em um modelo para especificar valores que podem ser alterados com frequência ou que precisam ser criados com base em uma combinação de valores de parâmetros. Adicione essas variáveis sob o elemento pai de variáveis que você adicionou ao modelo.

    ```json
    "dnsName1": "[concat(parameters('resourcePrefix'),'dn1')]",
    "dnsName2": "[concat(parameters('resourcePrefix'),'dn2')]",
    "publicIP1": "[concat(parameters('resourcePrefix'),'ip1')]",
    "publicIP2": "[concat(parameters('resourcePrefix'),'ip2')]",
    "loadBalancerName": "[concat(parameters('resourcePrefix'),'lb1')]",
    "virtualNetworkName": "[concat(parameters('resourcePrefix'),'vn1')]",
    "nicName": "[concat(parameters('resourcePrefix'),'nc1')]",
    "lbID": "[resourceId('Microsoft.Network/loadBalancers',variables('loadBalancerName'))]",
    "frontEndIPConfigID": "[concat(variables('lbID'),'/frontendIPConfigurations/loadBalancerFrontEnd')]",
    "storageAccountSuffix": [ "a", "g", "m", "s", "y" ],
    "diagnosticsStorageAccountName": "[concat(parameters('resourcePrefix'), 'a')]",
    "accountid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/','Microsoft.Storage/storageAccounts/', variables('diagnosticsStorageAccountName'))]",
    "wadlogs": "<WadCfg><DiagnosticMonitorConfiguration>",
    "wadperfcounter": "<PerformanceCounters scheduledTransferPeriod=\"PT1M\"><PerformanceCounterConfiguration counterSpecifier=\"\\Processor\\PercentProcessorTime\" sampleRate=\"PT15S\" unit=\"Percent\"><annotation displayName=\"CPU percentage guest OS\" locale=\"en-us\"/></PerformanceCounterConfiguration></PerformanceCounters>",
    "wadcfgxstart": "[concat(variables('wadlogs'),variables('wadperfcounter'),'<Metrics resourceId=\"')]",
    "wadmetricsresourceid": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name ,'/providers/','Microsoft.Compute/virtualMachineScaleSets/',parameters('vmssName'))]",
    "wadcfgxend": "[concat('\"><MetricAggregation scheduledTransferPeriod=\"PT1H\"/><MetricAggregation scheduledTransferPeriod=\"PT1M\"/></Metrics></DiagnosticMonitorConfiguration></WadCfg>')]"
    ```

   * Nomes DNS que são usados pelas interfaces de rede.
   * Os nomes de endereços IP e prefixos para a rede virtual e sub-redes.
   * Os nomes e os identificadores da rede virtual, balanceador de carga e interfaces de rede.
   * Nomes de conta de armazenamento para as contas associadas com as máquinas no conjunto de dimensionamento.
   * Configurações para a Extensão de diagnóstico que é instalada nas máquinas virtuais. Para obter mais informações sobre a Extensão de diagnóstico, confira [Criar uma máquina virtual do Windows com monitoramento e diagnóstico usando o Modelo do Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

4. Adicione o recurso de conta de armazenamento sob o elemento pai de recursos que você adicionou ao modelo. Este modelo usa um loop para criar as cinco contas de armazenamento recomendadas nas quais os discos do sistema operacional e os dados de diagnóstico estão armazenados. Este conjunto de contas pode oferecer suporte a até 100 máquinas virtuais em um conjunto de dimensionamento, que é o máximo atual. Cada conta de armazenamento é nomeada com um designador que foi definido nas variáveis combinadas com o sufixo que você fornecer nos parâmetros do modelo.
   
        {
          "type": "Microsoft.Storage/storageAccounts",
          "name": "[concat(parameters('resourcePrefix'), variables('storageAccountSuffix')[copyIndex()])]",
          "apiVersion": "2015-06-15",
          "copy": {
            "name": "storageLoop",
            "count": 5
          },
          "location": "[resourceGroup().location]",
          "properties": { "accountType": "Standard_LRS" }
        },

5. Adicione o recurso de rede virtual. Confira [Provedor de Recurso de Rede](../virtual-network/resource-groups-networking.md)para obter mais informações.

    ```json
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Network/virtualNetworks",
      "name": "[variables('virtualNetworkName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "addressSpace": { "addressPrefixes": [ "10.0.0.0/16" ] },
        "subnets": [
          {
            "name": "subnet1",
            "properties": { "addressPrefix": "10.0.0.0/24" }
          }
        ]
      }
    },
    ```

6. Adicione os recursos de endereço IP público que são usados pelo balanceador de carga e interface de rede.

    ```json
    {
      "apiVersion": "2016-03-30",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "[variables('publicIP1')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "publicIPAllocationMethod": "Dynamic",
        "dnsSettings": {
          "domainNameLabel": "[variables('dnsName1')]"
        }
      }
    },
    {
      "apiVersion": "2016-03-30",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "[variables('publicIP2')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "publicIPAllocationMethod": "Dynamic",
        "dnsSettings": {
          "domainNameLabel": "[variables('dnsName2')]"
        }
      }
    },
    ```

7. Adicione o recurso de balanceador de carga que é usado pelo conjunto de dimensionamento. Para obter mais informações, confira [Suporte do Gerenciador de Recursos do Azure para Balanceador de Carga](../load-balancer/load-balancer-arm.md).

    ```json   
    {
      "apiVersion": "2015-06-15",
      "name": "[variables('loadBalancerName')]",
      "type": "Microsoft.Network/loadBalancers",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIP1'))]"
      ],
      "properties": {
        "frontendIPConfigurations": [
          {
            "name": "loadBalancerFrontEnd",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses/', variables('publicIP1'))]"
              }
            }
          }
        ],
        "backendAddressPools": [ { "name": "bepool1" } ],
        "inboundNatPools": [
          {
            "name": "natpool1",
            "properties": {
              "frontendIPConfiguration": {
                "id": "[variables('frontEndIPConfigID')]"
              },
              "protocol": "tcp",
              "frontendPortRangeStart": 50000,
              "frontendPortRangeEnd": 50500,
              "backendPort": 22
            }
          }
        ]
      }
    },
    ```

8. Adicione o recurso de interface de rede que é usado pela máquina virtual separada. Como máquinas em um conjunto de escala não são diretamente acessíveis usando um endereço IP público, uma máquina virtual separada é criada na mesma rede virtual para acessar remotamente as máquinas.

    ```json
    {
      "apiVersion": "2016-03-30",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[variables('nicName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIP2'))]",
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
      ],
      "properties": {
        "ipConfigurations": [
          {
            "name": "ipconfig1",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicIP2'))]"
              },
              "subnet": {
                "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/virtualNetworks/',variables('virtualNetworkName'),'/subnets/subnet1')]"
              }
            }
          }
        ]
      }
    },
    ```

9. Adicione a máquina virtual separada à mesma rede do conjunto de escala.

    ```json
    {
      "apiVersion": "2016-03-30",
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[parameters('vmName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "storageLoop",
        "[concat('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
      ],
      "properties": {
        "hardwareProfile": { "vmSize": "Standard_A1" },
        "osProfile": {
          "computername": "[parameters('vmName')]",
          "adminUsername": "[parameters('adminUsername')]",
          "adminPassword": "[parameters('adminPassword')]"
        },
        "storageProfile": {
          "imageReference": {
            "publisher": "Canonical",
            "offer": "UbuntuServer",
            "sku": "14.04.4-LTS",
            "version": "latest"
          },
          "osDisk": {
            "name": "[concat(parameters('resourcePrefix'), 'os1')]",
            "vhd": {
              "uri":  "[concat('https://',parameters('resourcePrefix'),'a.blob.core.windows.net/vhds/',parameters('resourcePrefix'),'os1.vhd')]"
            },
            "caching": "ReadWrite",
            "createOption": "FromImage"
          }
        },
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
            }
          ]
        }
      }
    },
    ```

10. Adicione o recurso do conjunto de escala de máquina virtual e especifique a extensão de diagnóstico que é instalada em todas as máquinas virtuais no conjunto de escala. Muitas das configurações desse recurso são semelhantes ao recurso de máquina virtual. As principais diferenças são o elemento de capacidade que especifica o número de máquinas virtuais no conjunto de escala e upgradePolicy, que especifica como as atualizações são feitas em máquinas virtuais. O conjunto de escala não será criado até que todas as contas de armazenamento sejam criadas conforme especificado no elemento dependsOn.

    ```json
    {
      "type": "Microsoft.Compute/virtualMachineScaleSets",
      "apiVersion": "2016-03-30",
      "name": "[parameters('vmSSName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "storageLoop",
        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]",
        "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]"
      ],
      "sku": {
        "name": "Standard_A1",
        "tier": "Standard",
        "capacity": "[parameters('instanceCount')]"
      },
      "properties": {
        "upgradePolicy": {
          "mode": "Manual"
        },
        "virtualMachineProfile": {
          "storageProfile": {
            "osDisk": {
              "vhdContainers": [
                "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[0],'.blob.core.windows.net/vmss')]",
                "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[1],'.blob.core.windows.net/vmss')]",
                "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[2],'.blob.core.windows.net/vmss')]",
                "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[3],'.blob.core.windows.net/vmss')]",
                "[concat('https://', parameters('resourcePrefix'), variables('storageAccountSuffix')[4],'.blob.core.windows.net/vmss')]"
              ],
              "name": "vmssosdisk",
              "caching": "ReadOnly",
              "createOption": "FromImage"
            },
            "imageReference": {
              "publisher": "Canonical",
              "offer": "UbuntuServer",
              "sku": "14.04.4-LTS",
              "version": "latest"
            }
          },
          "osProfile": {
            "computerNamePrefix": "[parameters('vmSSName')]",
            "adminUsername": "[parameters('adminUsername')]",
            "adminPassword": "[parameters('adminPassword')]"
          },
          "networkProfile": {
            "networkInterfaceConfigurations": [
              {
                "name": "networkconfig1",
                "properties": {
                  "primary": "true",
                  "ipConfigurations": [
                    {
                      "name": "ip1",
                      "properties": {
                        "subnet": {
                          "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/virtualNetworks/',variables('virtualNetworkName'),'/subnets/subnet1')]"
                        },
                        "loadBalancerBackendAddressPools": [
                          {
                            "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/loadBalancers/',variables('loadBalancerName'),'/backendAddressPools/bepool1')]"
                          }
                        ],
                        "loadBalancerInboundNatPools": [
                          {
                            "id": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Network/loadBalancers/',variables('loadBalancerName'),'/inboundNatPools/natpool1')]"
                          }
                        ]
                      }
                    }
                  ]
                }
              }
            ]
          },
          "extensionProfile": {
            "extensions": [
              {
                "name":"LinuxDiagnostic",
                "properties": {
                  "publisher":"Microsoft.OSTCExtensions",
                  "type":"LinuxDiagnostic",
                  "typeHandlerVersion":"2.1",
                  "autoUpgradeMinorVersion":false,
                  "settings": {
                    "xmlCfg":"[base64(concat(variables('wadcfgxstart'),variables('wadmetricsresourceid'),variables('wadcfgxend')))]",
                    "storageAccount":"[variables('diagnosticsStorageAccountName')]"
                  },
                  "protectedSettings": {
                    "storageAccountName":"[variables('diagnosticsStorageAccountName')]",
                    "storageAccountKey":"[listkeys(variables('accountid'), '2015-06-15').key1]",
                    "storageAccountEndPoint":"https://core.windows.net"
                  }
                }
              }
            ]
          }
        }
      }
    },
    ```

11. Adicione o recurso autoscaleSettings, que define como o conjunto de escalas se ajusta com base no uso do processador nas máquinas no conjunto.

    ```json
    {
      "type": "Microsoft.Insights/autoscaleSettings",
      "apiVersion": "2015-04-01",
      "name": "[concat(parameters('resourcePrefix'),'as1')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]"
      ],
      "properties": {
        "enabled": true,
        "name": "[concat(parameters('resourcePrefix'),'as1')]",
        "profiles": [
          {
            "name": "Profile1",
            "capacity": {
              "minimum": "1",
              "maximum": "10",
              "default": "1"
            },
            "rules": [
              {
                "metricTrigger": {
                  "metricName": "\\Processor\\PercentProcessorTime",
                  "metricNamespace": "",
                  "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().name,'/providers/Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]",
                  "timeGrain": "PT1M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "GreaterThan",
                  "threshold": 50.0
                },
                "scaleAction": {
                  "direction": "Increase",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              }
            ]
          }
        ],
        "targetResourceUri": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/', resourceGroup().name,'/providers/Microsoft.Compute/virtualMachineScaleSets/',parameters('vmSSName'))]"
      }
    }
    ```
    
    Para este tutorial, estes valores são importantes:
    
    * **metricName**  
    Este valor é o mesmo que o contador de desempenho que definimos na variável wadperfcounter. Usando essa variável, a Extensão de diagnóstico coleta o contador **Processor\PercentProcessorTime**.
    
    * **metricResourceUri**  
    Este valor é o identificador de recurso do conjunto de dimensionamento de máquinas virtuais.
    
    * **timeGrain**  
    Este valor é a granularidade das métricas que são coletadas. Neste modelo, ele é definido como um minuto.
    
    * **statistic**  
    Este valor determina como as métricas são combinadas para acomodar a ação de dimensionamento automático. Os valores possíveis são: Média, Mín, Máx. Neste modelo, o uso médio de CPU total das máquinas virtuais é coletado.

    * **timeWindow**  
    Este valor é o intervalo em que os dados da instância são coletados. Deve estar entre 5 minutos e 12 horas.
    
    * **timeAggregation**  
    Este valor determina como os dados coletados devem ser combinados ao longo do tempo. O valor padrão é Average. Os valores possíveis são: Média, Mínimo, Máximo, Último, Total, Contagem.
    
    * **operator**  
    Este valor é o operador usado para comparar os dados de métrica e o limite. Os valores possíveis são: Equals, NotEquals, GreaterThan, GreaterThanOrEqual, LessThan, LessThanOrEqual.
    
    * **threshold**  
    Este é o valor que dispara a ação de dimensionamento. Neste modelo, máquinas são adicionadas ao conjunto de dimensionamento quando o uso médio de CPU entre máquinas no conjunto é mais de 50%.
    
    * **direction**  
    Este valor determina a ação que é executada quando o valor de limite for atingido. Os valores possíveis são Aumentar ou Diminuir. Neste modelo, o número de máquinas virtuais no conjunto de dimensionamento é aumentado se o limite for acima de 50% na janela de tempo definido.

    * **tipo**  
    Este valor é o tipo de ação que deve ocorrer e deve ser definido como ChangeCount.
    
    * **valor**  
    Este valor é o número de máquinas virtuais que são adicionadas ou removidas do conjunto de dimensionamento. Este valor deve ser 1 ou maior. O valor padrão é 1. Neste modelo, o número de máquinas no conjunto de dimensionamento aumenta em 1 quando o limite é atingido.

    * **cooldown**  
    Este valor é a quantidade de tempo de espera desde a última ação de dimensionamento antes que ocorra a próxima ação. Esse valor deve estar entre um minuto e uma semana.

12. Salvar o arquivo de modelo.    

## <a name="step-3-upload-the-template-to-storage"></a>Etapa 3: Carregar o modelo para armazenamento
O modelo pode ser carregado, desde que você saiba o nome e a chave primária da conta de armazenamento que você criou na etapa 1.

1. Em sua interface de linha de comando (Bash, Terminal, Prompt de comando), execute esses comandos para definir as variáveis de ambiente necessárias para acessar a conta de armazenamento:

    ```cli   
    export AZURE_STORAGE_ACCOUNT={account_name}
    export AZURE_STORAGE_ACCESS_KEY={key}
    ```
    
    Você pode obter a chave clicando no ícone de chave ao exibir o recurso de conta de armazenamento no Portal do Azure. Ao usar um prompt de comando do Windows, digite **set** em vez de export.

2. Crie o contêiner para armazenar o modelo.
   
    ```cli
    azure storage container create -p Blob templates
    ```

3. Carregue o arquivo de modelo para o novo contêiner.
   
    ```cli
    azure storage blob upload VMSSTemplate.json templates VMSSTemplate.json
    ```

## <a name="step-4-deploy-the-template"></a>Etapa 4: Implantar o modelo
Agora que você criou o modelo, pode começar a implantar os recursos. Use este comando para iniciar o processo:

```cli
azure group deployment create --template-uri https://vmsstestsa.blob.core.windows.net/templates/VMSSTemplate.json vmsstestrg1 vmsstestdp1
```

Quando você pressiona a tecla enter, é solicitado a fornecer valores para as variáveis que você atribuiu. Forneça esses valores:

```cli
vmName: vmsstestvm1
vmSSName: vmsstest1
instanceCount: 5
adminUserName: vmadmin1
adminPassword: VMpass1
resourcePrefix: vmsstest
```

Deve levar cerca de 15 minutos para todos os recursos serem implantados com êxito.

> [!NOTE]
> Você também pode usar a capacidade do portal para implantar os recursos. Use este link: https://portal.azure.com/#create/Microsoft.Template/uri/<link to VM Scale Set JSON template>


## <a name="step-5-monitor-resources"></a>Etapa 5: monitorar recursos
Você pode obter informações sobre os conjuntos de dimensionamento de máquina virtual usando estes métodos:

* O portal do Azure - Atualmente você pode obter uma quantidade limitada de informações usando o portal.

* O [Gerenciador de Recursos do Azure](https://resources.azure.com/) – Esta é a melhor ferramenta para explorar o estado atual do conjunto de escala. Siga este caminho e você deverá ver a exibição da instância do conjunto de dimensionamento que você criou:
  
    ```cli
    subscriptions > {your subscription} > resourceGroups > vmsstestrg1 > providers > Microsoft.Compute > virtualMachineScaleSets > vmsstest1 > virtualMachines
    ```

* CLI do Azure - use esse comando para obter algumas informações:

    ```cli  
    azure resource show -n vmsstest1 -r Microsoft.Compute/virtualMachineScaleSets -o 2015-06-15 -g vmsstestrg1
    ```

* Conecte-se à máquina virtual de jumpbox exatamente como faria com qualquer outra máquina e, em seguida, você pode acessar remotamente as máquinas virtuais no conjunto de dimensionamento para monitorar os processos individuais.

> [!NOTE]
> Uma API REST completa para obter informações sobre conjuntos de escalas podem ser encontradas nos [Conjuntos de Escalas de Máquina Virtual](https://msdn.microsoft.com/library/mt589023.aspx).

## <a name="step-6-remove-the-resources"></a>Etapa 6: remover os recursos
Como você é cobrado pelos recursos usados no Azure, sempre é uma boa prática excluir os recursos que não são mais necessários. Você não precisa excluir cada recurso separadamente de um grupo de recursos. Você pode excluir o grupo de recursos e todos os seus recursos serão excluídos automaticamente.

```cli
azure group delete vmsstestrg1
```

## <a name="next-steps"></a>Próximas etapas
* Encontre exemplos de recursos de monitoramento do Azure Monitor nos [Exemplos de início rápido da CLI de plataforma cruzada do Azure Monitor](../monitoring-and-diagnostics/insights-cli-samples.md)
* Saiba sobre os recursos de notificação em [Usar ações de dimensionamento automático para enviar notificações de alerta por email e webhook no Azure Monitor](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md)
* Saiba como [Usar logs de auditoria para enviar notificações de alerta por email e webhook no Azure Monitor](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md)
* Confira o modelo de [aplicativo de demonstração de dimensionamento automático no Ubuntu 16.04](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale) que configura um aplicativo Python/Bottle para praticar a funcionalidade de dimensionamento automático de Conjuntos de dimensionamento de máquinas virtuais.

