---
title: Procedimento para atualizar/migrar o SKU para PrimaryNodeType para SKUs superiores | Microsoft Docs
description: Saiba como atualizar/migrar o SKU para PrimaryNodeType para SKUs superiores usando comandos do PowerShell e comandos CLI.
services: service-fabric
documentationcenter: .net
author: v-rachiw
manager: navya
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/08/2018
ms.author: v-rachiw
ms.openlocfilehash: 8d5b560068a9e0bc0169bfdb98c5e939e34a3b8c
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/17/2018
ms.locfileid: "34274018"
---
# <a name="upgrademigrate-the-sku-for-primary-node-type-to-higher-sku"></a>Atualizar/migrar a SKU para o tipo de nó primário para o SKU superior

Este artigo descreve como atualizar/migrar o SKU do Tipo de Nó Primário do cluster do Service Fabric para SKU superior usando o Microsoft Azure PowerShell

## <a name="add-a-new-virtual-machine-scale-set"></a>Adicione um novo conjunto de dimensionamento de máquina virtual 

Implante uma nova escola de máquina virtual com um Balanceador de Carga. A configuração da extensão do Service Fabric (especialmente o tipo de nó) do novo conjunto de escala de máquina virtual deve ser a mesma que o conjunto de escala antiga que você está tentando atualizar. Verifique no explorador SF se seus novos nós estão disponíveis. 

### <a name="azure-powershell"></a>Azure PowerShell
O exemplo a seguir usa o Microsoft Azure PowerShell para implantar o modelo do Gerenciador de Recursos atualizados *template.json* usando o grupo de recursos denominado *myResourceGroup*:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName myResourceGroup -TemplateFile \template\template.json 
```

Consulte o seguinte exemplo para modificar o modelo json para adicionar o novo recurso de conjunto de escala de máquina virtual com o tipo de nó principal no cluster existente

```json
        {
            "apiVersion": "[variables('vmssApiVersion')]",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[parameters('vmNodeType2Name')]",
            "location": "[parameters('computeLocation')]",
            "dependsOn": [
                "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]",
                "[concat('Microsoft.Network/loadBalancers/', concat('LB','-', parameters('clusterName'),'-',parameters('vmNodeType2Name')))]",
                "[concat('Microsoft.Storage/storageAccounts/', parameters('supportLogStorageAccountName'))]",
                "[concat('Microsoft.Storage/storageAccounts/', parameters('applicationDiagnosticsStorageAccountName'))]"
            ],
            "properties": {
                "overprovision": "[parameters('overProvision')]",
                "upgradePolicy": {
                    "mode": "Automatic"
                },
                "virtualMachineProfile": {
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "[concat(parameters('vmNodeType2Name'),'_ServiceFabricNode')]",
                                "properties": {
                                    "type": "ServiceFabricNode",
                                    "autoUpgradeMinorVersion": true,
                                    "protectedSettings": {
                                        "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
                                        "StorageAccountKey2": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('supportLogStorageAccountName')),'2015-05-01-preview').key2]"
                                    },
                                    "publisher": "Microsoft.Azure.ServiceFabric",
                                    "settings": {
                                        "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                                        "nodeTypeRef": "[parameters('vmNodeType0Name')]",
                                        "dataPath": "D:\\\\SvcFab",
                                        "durabilityLevel": "Bronze",
                                        "enableParallelJobs": true,
                                        "nicPrefixOverride": "[parameters('subnet0Prefix')]",
                                        "certificate": {
                                            "thumbprint": "[parameters('certificateThumbprint')]",
                                            "x509StoreName": "[parameters('certificateStoreValue')]"
                                        }
                                    },
                                    "typeHandlerVersion": "1.0"
                                }
                            },
                            {
                                "name": "[concat('VMDiagnosticsVmExt','_vmNodeType2Name')]",
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
                        ]
                    },
                    "networkProfile": {
                        "networkInterfaceConfigurations": [
                            {
                                "name": "[concat(parameters('nicName'), '-2')]",
                                "properties": {
                                    "ipConfigurations": [
                                        {
                                            "name": "[concat(parameters('nicName'),'-',2)]",
                                            "properties": {
                                                "loadBalancerBackendAddressPools": [
                                                    {
                                                        "id": "[variables('lbPoolID2')]"
                                                    }
                                                ],
                                                "loadBalancerInboundNatPools": [
                                                    {
                                                        "id": "[variables('lbNatPoolID2')]"
                                                    }
                                                ],
                                                "subnet": {
                                                    "id": "[variables('subnet0Ref')]"
                                                }
                                            }
                                        }
                                    ],
                                    "primary": true
                                }
                            }
                        ]
                    },
                    "osProfile": {
                        "adminPassword": "[parameters('adminPassword')]",
                        "adminUsername": "[parameters('adminUsername')]",
                        "computernamePrefix": "[parameters('vmNodeType2Name')]",
                        "secrets": [
                            {
                                "sourceVault": {
                                    "id": "[parameters('sourceVaultValue')]"
                                },
                                "vaultCertificates": [
                                    {
                                        "certificateStore": "[parameters('certificateStoreValue')]",
                                        "certificateUrl": "[parameters('certificateUrlValue')]"
                                    }
                                ]
                            }
                        ]
                    },
                    "storageProfile": {
                        "imageReference": {
                            "publisher": "[parameters('vmImagePublisher')]",
                            "offer": "[parameters('vmImageOffer')]",
                            "sku": "[parameters('vmImageSku')]",
                            "version": "[parameters('vmImageVersion')]"
                        },
                        "osDisk": {
                            "caching": "ReadOnly",
                            "createOption": "FromImage",
                            "managedDisk": {
                                "storageAccountType": "[parameters('storageAccountType')]"
                            }
                        }
                    }
                }
            },
            "sku": {
                "name": "[parameters('vmNodeType2Size')]",
                "capacity": "[parameters('nt2InstanceCount')]",
                "tier": "Standard"
            },
            "tags": {
                "resourceType": "Service Fabric",
                "clusterName": "[parameters('clusterName')]"
            }
        },
```


## <a name="remove-old-virtual-machine-scale-set"></a>Remover o conjunto de dimensionamento de máquinas virtuais

Desabilitar as instâncias da VM do conjunto de dimensionamento de máquina virtual com a intenção de remover o nó. Essa operação pode demorar muito para ser concluída. Você pode desabilitar todas de uma vez e elas serão enfileiradas. Aguarde até que todos os nós estejam desabilitados. 

### <a name="azure-powershell"></a>Azure PowerShell
O exemplo a seguir usam o Powershell do Microsoft Azure Service Fabric para desabilitar a instância de nó denominada *NTvm1_0* do conjunto de dimensionamento da máquina virtual denominada *NTvm1*:

```powershell
Disable-ServiceFabricNode -NodeName NTvm1_0 -Intent RemoveNode 
```

Remova o conjunto completo de dimensionamento. Aguarde até que o estado de provisionamento do conjunto de escala seja bem-sucedido 

### <a name="azure-powershell"></a>Azure PowerShell
O exemplo a seguir usa o Microsoft Azure PowerShell para remover o conjunto de dimensionamento completo nomeado *NTvm1* do grupo de recursos denominado *myResourceGroup*:

```powershell
Remove-AzureRmVmss -ResourceGroupName myResourceGroup -VMScaleSetName NTvm1
```

## <a name="remove-load-balancer-related-to-old-scale-set"></a>Remover o Load Balancer relacionado ao antigo conjunto de dimensionamento

Remova o Load Balancer relacionado ao antigo conjunto de dimensionamento. Esta etapa causará um breve período de tempo de inativade para o cluster

### <a name="azure-powershell"></a>Azure PowerShell
O exemplo a seguir usa o Microsoft Azure PowerShell para remover o balanceador de carga nomeado *LB-myCluster-NTvm1* relacionado ao conjunto de escala antigo do grupo de recursos nomeado *myResourceGroup*:

```powershell
Remove-AzureRmLoadBalancer -Name LB-myCluster-NTvm1 -ResourceGroupName myResourceGroup
```

## <a name="remove-public-ip-related-to-old-scale-set"></a>Remova o IP público relacionado ao antigo conjunto de escala

Armazenar as configurações de DNS do endereço IP público relacionadas à antiga escala definida na variável, em seguida, remova o endereço IP público

### <a name="azure-powershell"></a>Azure PowerShell
O exemplo a seguir usa o Microsoft Azure PowerShell para armazenar configurações de DNS do endereço IP público denominado *LBIP-myCluster-NTvm1* na variável e remove o endereço IP:

```powershell
$oldprimaryPublicIP = Get-AzureRmPublicIpAddress -Name LBIP-myCluster-NTvm1  -ResourceGroupName myResourceGroup
$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel
$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn
Remove-AzureRmPublicIpAddress -Name LBIP-myCluster-NTvm1 -ResourceGroupName myResourceGroup
```

## <a name="update-public-ip-address-related-to-new-scale-set"></a>Atualizar o endereço IP público relacionado ao novo conjunto de escala

Atualizar as configurações de DNS do endereço IP público relacionadas a nova escala definida com as configurações de DNS do endereço IP público relacionadas ao antigo conjunto de escala

### <a name="azure-powershell"></a>Azure PowerShell
O exemplo a seguir usa o Microsoft Azure PowerShell para armazenar configurações de DNS do endereço IP público denominado *LBIP-myCluster-NTvm3* com as configurações DNS armazenadas em variáveis em uma etapa anterior:

```powershell
$PublicIP = Get-AzureRmPublicIpAddress -Name LBIP-myCluster-NTvm1  -ResourceGroupName myResourceGroup
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzureRmPublicIpAddress -PublicIpAddress $PublicIP 
```

## <a name="remove-knowledge-of-service-fabric-node-from-fm"></a>Remover o conhecimento do nó de malha de serviço de FM

Notificação do Service Fabric que os nós, que estão inativos foram removidos do cluster. (execute esse comando para VM de todas as instâncias do antigo conjunto de escalas da máquina virtual) (se o nível de durabilidade do conjunto de escala de máquina virtual antigo era prata ou ouro, esta etapa pode não ser necessária. Desde que essa etapa seja feita pelo sistema automaticamente.)

### <a name="azure-powershell"></a>Azure PowerShell
O exemplo a seguir usa o PowerShell do Microsoft Azure Service Fabric para notificar a Service Fabric para que o nó nomeado *NTvm1_0* foi removido:

```powershell
Remove-ServiceFabricNodeState -NodeName NTvm1_0
```