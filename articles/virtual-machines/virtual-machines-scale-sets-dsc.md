<properties
   pageTitle="Uso de Configuração de Estado Desejado com Conjuntos de Escala de Máquina Virtual | Microsoft Azure"
   description="Uso de Conjuntos de Escala de Máquina Virtual com a Extensão de DSC do Azure"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="zjalexander"
   manager="timlt"
   editor=""
   tags="azure-service-management,azure-resource-manager"
   keywords=""/>

<tags
   ms.service="virtual-machines-scale-sets"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="08/29/2016"
   ms.author="zachal"/>

# Uso de Conjuntos de Escala de Máquina Virtual com a Extensão de DSC do Azure

[VMSS (Conjuntos de Escala de Máquina Virtual)](virtual-machines-windows-vmss-powershell-creating.md) podem ser usado com o manipulador de extensão [DSC (Configuração de Estado Desejado) do Azure](virtual-machines-windows-extensions-dsc-overview.md). O VMSS é usado para configurar a implantação de máquinas virtuais para atender à carga. O DSC é usado para configurar as VMs quando elas ficam online, para que executem o software de produção.

## Diferenças entre implantar em VM e VMSS

A estrutura do modelo subjacente para VMSS é ligeiramente diferente de uma única VM. Especificamente, uma única VM implanta extensões sob o nó "virtualMachines". Há uma entrada do tipo "extensions" em que o DSC é adicionado ao modelo

```
"resources": [
          {
              "name": "Microsoft.Powershell.DSC",
              "type": "extensions",
              "location": "[resourceGroup().location]",
              "apiVersion": "2015-06-15",
              "dependsOn": [
                  "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
              ],
              "tags": {
                  "displayName": "dscExtension"
              },
              "properties": {
                  "publisher": "Microsoft.Powershell",
                  "type": "DSC",
                  "typeHandlerVersion": "2.20",
                  "autoUpgradeMinorVersion": false,
                  "forceUpdateTag": "[parameters('dscExtensionUpdateTagVersion')]",
                  "settings": {
                      "configuration": {
                          "url": "[concat(parameters('_artifactsLocation'), '/', variables('dscExtensionArchiveFolder'), '/', variables('dscExtensionArchiveFileName'))]",
                          "script": "dscExtension.ps1",
                          "function": "Main"
                      },
                      "configurationArguments": {
                          "nodeName": "[variables('vmName')]"
                      }
                  },
                  "protectedSettings": {
                      "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                  }
              }
          }
      ]
```

Um nó VMSS tem uma seção "properties" com o atributo "VirtualMachineProfile", "extensionProfile". O DSC é adicionado sob "extensões"

```
"extensionProfile": {
            "extensions": [
                {
                    "name": "Microsoft.Powershell.DSC",
                    "properties": {
                        "publisher": "Microsoft.Powershell",
                        "type": "DSC",
                        "typeHandlerVersion": "2.9",
                        "autoUpgradeMinorVersion": true,
                        "forceUpdateTag": "[parameters('DscExtensionUpdateTagVersion')]",
                        "settings": {
                            "configuration": {
                                "url": "[concat(parameters('_artifactsLocation'), '/', variables('DscExtensionArchiveFolder'), '/', variables('DscExtensionArchiveFileName'))]",
                                "script": "DscExtension.ps1",
                                "function": "Main"
                            },
                            "configurationArguments": {
                                "nodeName": "localhost"
                            }
                        },
                        "protectedSettings": {
                            "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                        }
                    }
                }
            ]
```

## Comportamento de VMSS

O comportamento de VMSS é idêntico ao comportamento de uma única VM. Quando uma nova VM é criada, ela é provisionada automaticamente com a extensão de DSC. Se uma versão mais recente do WMF for necessária para a extensão, a VM será reiniciada antes de ser colocada online. Quando ela estiver online, baixará o arquivo .zip de configuração de DSC e o provisionará na VM. Mais detalhes podem ser encontrados em [Visão geral da Extensão DSC do Azure](virtual-machines-windows-extensions-dsc-overview.md).

<!---HONumber=AcomDC_0914_2016-->