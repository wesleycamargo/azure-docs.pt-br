<properties
	pageTitle="Gerenciar VMs em um Conjunto de Escala de Máquina Virtual | Microsoft Azure"
	description="Gerenciar máquinas virtuais em um conjunto de escala de máquina virtual usando o Azure PowerShell."
	services="virtual-machine-scale-sets"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machine-scale-sets"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/22/2016"
	ms.author="davidmu"/>

# Gerenciar máquinas virtuais em um conjunto de escala de máquina virtual

O Azure PowerShell fornece muita potência e flexibilidade ao gerenciar recursos no Microsoft Azure. Use as tarefas neste artigo para gerenciar os recursos de máquina virtual no seu conjunto de escala de máquina virtual.

- [Exibir informações sobre um conjunto de escala de máquina virtual](#displayvm)
- [Iniciar uma máquina virtual em um conjunto de escala](#start)
- [Parar uma máquina virtual em um conjunto de escala](#stop)
- [Reiniciar uma máquina virtual em um conjunto de escala](#restart)
- [Excluir uma máquina virtual de um conjunto de escala](#delete)

Todas as tarefas que envolvem o gerenciamento de uma máquina virtual em um conjunto de escala exigem que você saiba a ID da instância da máquina que você deseja gerenciar. Você pode usar o [Gerenciador de Recursos do Azure](https://resources.azure.com) para localizar a ID da instância de uma máquina virtual em um conjunto de escala. Você também pode usar o Gerenciador de Recursos para verificar o status das tarefas que você concluir.

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

## <a id="displayvm"></a>Exibir informações sobre um conjunto de escala de máquina virtual

Você pode obter informações gerais sobre um conjunto de escala, que também é conhecido como a exibição de instância. Ou então, você pode obter informações mais específicas, tais como informações sobre os recursos no conjunto.

Neste comando, substitua *nome do grupo de recursos* pelo nome do grupo de recursos que contém o conjunto de escala de máquina virtual e *nome do conjunto de escala* pelo nome do conjunto de escala da máquina virtual e execute-o:

    Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"

Ele retorna algo semelhante a:

    Sku                      :  {
                                  "name": "Standard_A0",
                                  "tier": "Standard",
                                  "capacity": 4
                                }
    UpgradePolicy            :  {
                                  "mode": "Manual"
                                }
    VirtualMachineProfile    :  {
                                  "osProfile": {
                                    "computerNamePrefix": "myvmss1",
                                    "adminUserName": "user1",
                                    "adminPassword": null,
                                    "customData": null,
                                    "windowsConfiguration": {
                                      "provisionVMAgent": true,
                                      "enableAutomaticUpdates": true,
                                      "timeZone": null,
                                      "additionalUnattendContent": null,
                                      "winRM": null
                                    }
                                    "linuxConfiguration": null,
                                    "secrets": []
                                  },
                                  "storageProfile": {
                                    "imageReference": {
                                      "publisher": "MicrosoftWindowsServer",
                                      "offer": "WindowsServer",
                                      "sku": "2012-R2-Datacenter",
                                      "version": "latest"
                                    },
                                    "osDisk": {
                                      "name": "vmssosdisk",
                                      "caching": "ReadOnly",
                                      "createOption": "FromImage",
                                      "osType": null,
                                      "image": null,
                                      "vhdContainers": [
                                        "https://amyst1.blob.core.windows.net/vmss",
                                        "https://gmyst1.blob.core.windows.net/vmss",
                                        "https://mmyst1.blob.core.windows.net/vmss",
                                        "https://smyst1.blob.core.windows.net/vmss",
                                        "https://ymyst1.blob.core.windows.net/vmss"
                                      ]
                                    }
                                  },
                                  "networkProfile": {
                                    "networkInterfaceConfigurations": [
                                      {
                                        "name": "myresnc2",
                                        "properties.primary": true,
                                        "properties.ipConfigurations": [
                                          {
                                            "name": "ip1",
                                            "properties.subnet": {
                                              "id": "/subscriptions/{subscription-id}/resourceGroups/rg1/providers/Microsoft.Network/virtualNetworks/myresvn1/subnets/myressn1"
                                            },
                                            "properties.loadBalancerBackendAddressPools": [
                                              {
                                                "id": "/subscriptions/{subscription-id}/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/myreslb1/backendAddressPools/bepool1"
                                              }
                                            ],
                                            "properties.loadBalancerInboundNatPools": [
                                              {
                                                "id": "/subscriptions/{subscription-id}/resourceGroups/rg1/providers/Microsoft.Network/loadBalancers/myreslb1/inboundNatPools/natpool1"
                                              }
                                            ],
                                            "id": null
                                          }
                                        ],
                                        "id": null
                                      }
                                    ]
                                  },
                                  "extensionProfile": {
                                    "extensions": [
                                      {
                                        "name": "Microsoft.Insights.VMDiagnosticsSettings",
                                        "properties.publisher": "Microsoft.Azure.Diagnostics",
                                        "properties.type": "IaaSDiagnostics",
                                        "properties.typeHandlerVersion": "1.5",
                                        "properties.autoUpgradeMinorVersion": true,
                                        "properties.settings": {
                                          "xmlCfg": "{encoded configuration}",
                                          "storageAccount": "amyst1"
                                        },
                                        "properties.protectedSettings": null,
                                        "properties.provisioningState": null,
                                        "id": null
                                      }
                                    ]
                                  }
                                }
    ProvisioningState           : Succeeded
    Id                          : /subscriptions/{subscription-id}/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/myvmss1
    Name                        : myvmss1
	Type                        : Microsoft.Compute/virtualMachineScaleSets
	Location                    : westus
	Tags.Count                  : 0
	Tags                        :

Para obter informações gerais, substitua *nome do grupo de recursos* pelo nome do grupo de recursos que contém o conjunto de escala de máquina virtual e *nome do conjunto de escala* pelo nome do conjunto de escala da máquina virtual e execute-o:

	Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceView

Ele retorna algo semelhante a:

    VirtualMachine   :  {
                          "statusesSummary": [
                            {
                              "code": "ProvisioningState/succeeded",
                              "count": 4
                            }
                          ]
                        }
    Extensions.Count :  1
    Extensions       :  {
                          "name": "Microsoft.Insights.VMDiagnosticsSettings",
                          "statusesSummary": [
                            {
                              "code": "ProvisioningState/succeeded",
                              "count": 4
                            }
                          ]
                        }
	Statuses.Count   :  1
	Statuses         :  {
                          "code": "ProvisioningState/succeeded",
                          "level": "Info",
                          "displayStatus": "Provisioning succeeded",
                          "message": null,
                          "time": "2016-03-14T20:29:37.170809Z"
                        }

## <a id="start"></a>Iniciar uma máquina virtual em um conjunto de escala

Neste comando, substitua *nome do grupo de recursos* pelo nome do grupo de recursos que contém o conjunto de escala de máquina virtual, substitua *nome do conjunto de escala de VM* pelo nome do conjunto de escala, substitua *id da instância* pelo identificador da máquina virtual que você deseja reiniciar e execute-o:

    Start-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId "instance id"

No Gerenciador de Recursos, podemos ver que o status da instância é **em execução**:

    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "level": "Info",
        "displayStatus": "Provisioning succeeded",
        "time": "2016-03-15T02:10:08.0730839+00:00"
      },
      {
        "code": "PowerState/running",
        "level": "Info",
        "displayStatus": "VM running"
      }
    ]

## <a id="stop"></a>Parar uma máquina virtual em um conjunto de escala

Neste comando, substitua *nome do grupo de recursos* pelo nome do grupo de recursos que contém o conjunto de escala de máquina virtual, substitua *nome do conjunto de escala* pelo nome do conjunto de escala, substitua *id da instância* pelo identificador da máquina virtual que você deseja parar e execute-o:

	Stop-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId "instance id"

No Gerenciador de Recursos, podemos ver que o status da instância é **desalocada**:

	"statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "level": "Info",
        "displayStatus": "Provisioning succeeded",
        "time": "2016-03-15T01:25:17.8792929+00:00"
      },
      {
        "code": "PowerState/deallocated",
        "level": "Info",
        "displayStatus": "VM deallocated"
      }
    ]

## <a id="restart"></a>Reiniciar uma máquina virtual em um conjunto de escala

Neste comando, substitua *nome do grupo de recursos* pelo nome do grupo de recursos que contém o conjunto de escala de máquina virtual, substitua *nome do conjunto de escala* pelo nome do conjunto de escala, substitua *id da instância* pelo identificador da máquina virtual que você deseja reiniciar e execute-o:

	Restart-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name" -InstanceId "instance id"

## <a id="delete"></a>Remover uma máquina virtual de um conjunto de escala

Neste comando, substitua *nome do grupo de recursos* pelo nome do grupo de recursos que contém o conjunto de escala de máquina virtual, substitua *nome do conjunto de escala* pelo nome do conjunto de escala, substitua *id da instância* pelo identificador da máquina virtual que você deseja remover do conjunto de escala e execute-o:

	Remove-AzureRmVmssVM -ResourceGroupName "resource group name" –VMScaleSetName "scale set name" -InstanceId "instance id"

<!---HONumber=AcomDC_0427_2016-->