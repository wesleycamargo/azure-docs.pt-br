---
title: Uso de Configuração de Estado Desejado com Conjuntos de Escala de Máquina Virtual | Microsoft Docs
description: Uso de Conjuntos de Escala de Máquina Virtual com a Extensão de DSC do Azure
services: virtual-machine-scale-sets
documentationcenter: ''
author: zjalexander
manager: timlt
editor: ''
tags: azure-service-management,azure-resource-manager
keywords: ''

ms.service: virtual-machine-scale-sets
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 09/15/2016
ms.author: zachal

---
# <a name="using-virtual-machine-scale-sets-with-the-azure-dsc-extension"></a>Uso de Conjuntos de Escala de Máquina Virtual com a Extensão de DSC do Azure
[VMSS (Conjuntos de Dimensionamento de Máquina Virtual)](virtual-machine-scale-sets-overview.md) pode ser usado com o manipulador de extensão [DSC (Configuração de Estado Desejado) do Azure](../virtual-machines/virtual-machines-windows-extensions-dsc-overview.md). VMSS fornece uma maneira de implantar e gerenciar uma grande quantidade de máquinas virtuais, e pode ser dimensionado de forma elástica em resposta ao carregamento. O DSC é usado para configurar as VMs quando elas ficam online, para que executem o software de produção.

## <a name="differences-between-deploying-to-vm-and-vmss"></a>Diferenças entre implantar em VM e VMSS
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
                          "script": "DscExtension.ps1",
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
                        "typeHandlerVersion": "2.20",
                        "autoUpgradeMinorVersion": false,
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

## <a name="behavior-for-vmss"></a>Comportamento de VMSS
O comportamento de VMSS é idêntico ao comportamento de uma única VM. Quando uma nova VM é criada, ela é provisionada automaticamente com a extensão de DSC. Se uma versão mais recente do WMF for necessária para a extensão, a VM será reiniciada antes de ser colocada online. Quando ela estiver online, baixará o arquivo .zip de configuração de DSC e o provisionará na VM. Mais detalhes podem ser encontrados em [Visão geral da Extensão DSC do Azure](../virtual-machines/virtual-machines-windows-extensions-dsc-overview.md).

## <a name="next-steps"></a>Próximas etapas
Examine o [modelo do Azure Resource Manager para a extensão de DSC](../virtual-machines/virtual-machines-windows-extensions-dsc-template.md).

Saiba como a [extensão DSC manipula com segurança as credenciais](../virtual-machines/virtual-machines-windows-extensions-dsc-credentials.md). 

Para saber mais sobre o manipulador de extensões DSC do Azure, confira [Introduction to the Azure Desired State Configuration extension handler (Introdução ao manipulador de extensões Configuração de Estado Desejado do Azure)](../virtual-machines/virtual-machines-windows-extensions-dsc-overview.md). 

Para saber mais sobre a DSC do PowerShell, [visite o centro de documentação do PowerShell](https://msdn.microsoft.com/powershell/dsc/overview). 

<!--HONumber=Oct16_HO2-->


