---
title: Configurar uma Identidade de Serviço Gerenciada em um conjunto de dimensionamento de máquinas virtuais do Azure usando um modelo
description: Instruções passo a passo para configurar uma Identidade de Serviço Gerenciada em uma VMSS do Azure, usando um modelo do Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2018
ms.author: daveba
ms.openlocfilehash: 69b88b5aabbadadea3b04f96d9a21f266c85a52f
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2018
ms.locfileid: "43125377"
---
# <a name="configure-managed-service-identity-on-virtual-machine-scale-using-a-template"></a>Configurar uma Identidade de Serviço Gerenciada em um conjunto de dimensionamento de máquinas virtuais usando um modelo

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

A Identidade de Serviço Gerenciado fornece aos serviços do Azure uma identidade gerenciada automaticamente no Active Directory do Azure. Você pode usar essa identidade para autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter as credenciais no seu código. 

Neste artigo, você aprenderá a executar as seguintes operações de Identidade de Serviço Gerenciada em um conjunto de dimensionamento de máquinas virtuais do Azure usando o modelo de implantação do Azure Resource Manager:
- Habilitar e desabilitar a identidade atribuída pelo sistema em um conjunto de dimensionamento de máquinas virtuais do Azure
- Adicionar e remover uma identidade atribuída pelo usuário em um conjunto de dimensionamento de máquinas virtuais do Azure

## <a name="prerequisites"></a>Pré-requisitos

- Se você não estiver familiarizado com a Identidade de Serviço Gerenciada, consulte a [seção de visão geral](overview.md). **Verifique se examinou a [diferença entre uma identidade atribuída pelo sistema e uma atribuída pelo usuário](overview.md#how-does-it-work)**.
- Se você ainda não tiver uma conta do Azure, [inscreva-se em uma conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Para realizar as operações de gerenciamento deste artigo, a conta precisará das seguintes atribuições de função:
    - [Colaborador da Máquina Virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) para criar um conjunto de dimensionamento de máquinas virtuais e habilitar e remover identidade gerenciada atribuída ao usuário e/ou sistema de um conjunto de dimensionamento de máquinas virtuais.
    - Função de [Colaborador de Identidade Gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) para criar uma identidade atribuída ao usuário.
    - Função de [Operador de Identidade Gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-operator) para atribuir e remover uma identidade atribuída ao usuário de e para um conjunto de dimensionamento de máquinas virtuais.

## <a name="azure-resource-manager-templates"></a>Modelos do Gerenciador de Recursos do Azure

Assim como com o Portal do Azure e o script, os modelos do [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) permitem implantar recursos novos ou modificados definidos por um grupo de recursos do Azure. Há várias opções disponíveis para a edição e a implantação do modelo, tanto locais quanto baseadas em portal, incluindo:

   - Use um [modelo personalizado do Azure Marketplace](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), que permite a criação de um modelo do zero ou use como base um modelo comum existente ou de [Início Rápido](https://azure.microsoft.com/documentation/templates/).
   - Derivar de um grupo de recursos existente, exportando um modelo da [implantação original](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history) ou do [estado atual da implantação](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
   - Usar um [editor JSON local (por exemplo, VS Code)](../../azure-resource-manager/resource-manager-create-first-template.md), depois carregar e implantar usando o PowerShell ou a CLI.
   - Usar o [projeto do Grupo de Recursos do Azure](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) do Visual Studio para criar e implantar um modelo.  

Independentemente da opção escolhido, a sintaxe do modelo será a mesma durante a implantação inicial e a reimplantação. A habilitação da Identidade de Serviço Gerenciada em uma VM nova ou existente é feita da mesma maneira. Além disso, por padrão, o Azure Resource Manager faz uma [atualização incremental](../../azure-resource-manager/deployment-modes.md) para implantações.

## <a name="system-assigned-identity"></a>Identidade atribuída pelo sistema

Nesta seção, você habilitará e desabilitará a identidade atribuída pelo sistema usando um modelo do Azure Resource Manager.

### <a name="enable-system-assigned-identity-during-creation-the-creation-of-a-virtual-machines-scale-set-or-a-existing-virtual-machine-scale-set"></a>Habilitar identidade atribuída ao sistema durante a criação de um conjunto de dimensionamento de máquinas virtuais ou um conjunto de dimensionamento de máquinas virtuais existente

1. Se você entrar no Azure localmente ou por meio do Portal do Azure, use uma conta que esteja associada à assinatura do Azure que contém o conjunto de dimensionamento de máquinas virtuais.
   
2. Para habilitar a identidade atribuída ao sistema, carregue o modelo em um editor, localize o recurso `Microsoft.Compute/virtualMachinesScaleSets` de interesse dentro da seção recursos e adicione a propriedade `identity` no mesmo nível que a propriedade `"type": "Microsoft.Compute/virtualMachines"`. Use a seguinte sintaxe:

   ```JSON
   "identity": { 
       "type": "SystemAssigned"
   }
   ```

3. (Opcional) Adicione a extensão de Identidade de Serviço Gerenciada do conjunto de dimensionamento de máquinas virtuais como um elemento `extensionsProfile`. Essa etapa é opcional, pois você pode usar a identidade do Serviço de Metadados da Instância do Azure (IMDS) para recuperar também os tokens.  Use a seguinte sintaxe:

   >[!NOTE] 
   > O exemplo a seguir assume que uma extensão do conjunto de dimensionamento de máquinas virtuais do Windows (`ManagedIdentityExtensionForWindows`) está sendo implantada. Você também pode configurar para Linux usando `ManagedIdentityExtensionForLinux` em vez disso, para os elementos `"name"` e `"type"`.
   >

   ```json
   "extensionProfile": {
        "extensions": [
            {
                "name": "MSIWindowsExtension",
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
            }
   ```

4. Quando tiver concluído, as seções a seguir deverão ser adicionadas à seção recurso do modelo e serem semelhantes ao seguinte:

   ```json
    "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[variables('vmssName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "SystemAssigned",
            },
           "properties": {
                //other resource provider properties...
                "virtualMachineProfile": {
                    //other virtual machine profile properties...
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "ManagedIdentityWindowsExtension",
                                "properties": {
                                  "publisher": "Microsoft.ManagedIdentity",
                                  "type": "ManagedIdentityExtensionForWindows",
                                  "typeHandlerVersion": "1.0",
                                  "autoUpgradeMinorVersion": true,
                                  "settings": {
                                      "port": 50342
                                  }
                                }
                            } 
                        ]
                    }
                }
            }
        }
    ]
   ``` 

### <a name="disable-a-system-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Desabilitar uma identidade atribuída pelo sistema de um conjunto de dimensionamento de máquinas virtuais

Se você tiver um conjunto de dimensionamento de máquinas virtuais que não precise mais de uma identidade de serviço gerenciada:

1. Se você entrar no Azure localmente ou por meio do Portal do Azure, use uma conta que esteja associada à assinatura do Azure que contém o conjunto de dimensionamento de máquinas virtuais.

2. Carregue o modelo em um [editor](#azure-resource-manager-templates) e localize o recurso `Microsoft.Compute/virtualMachineScaleSets` de interesse na seção `resources`. Caso tenha uma VM com apenas a identidade atribuída ao sistema, desabilite-a alterando o tipo de identidade para `None`.

   **Microsoft.Compute/virtualMachineScaleSets API versão 01-06-2018**

   Se apiVersion for `2018-06-01` e a VM tiver as identidades atribuídas de usuário e sistema, remova `SystemAssigned` do tipo de identidade e mantenha `UserAssigned` com os valores de dicionário userAssignedIdentities.

   **Microsoft.Compute/virtualMachineScaleSets API versão 01-06-2018 e anteriores**

   Se apiVersion for `2017-12-01` e seu conjunto de dimensionamento de máquinas virtuais tiver identidades atribuídas de sistema e usuário, remova `SystemAssigned` do tipo de identidade e mantenha `UserAssigned` juntamente com a matriz `identityIds` das identidades atribuídas ao usuário. 
   
    

   O exemplo a seguir mostra como remover uma identidade atribuída ao sistema de um conjunto de dimensionamento de máquinas virtuais sem identidades atribuídas ao usuário:
   
   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2018-06-01",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "None"
        }

   }
   ```

## <a name="user-assigned-identity"></a>Identidade atribuída pelo usuário

Nesta seção, você atribui uma identidade atribuída pelo usuário a um conjunto de dimensionamento de máquinas virtuais usando um modelo do Azure Resource Manager.

> [!Note]
> Para criar uma identidade atribuída pelo usuário usando um Modelo do Azure Resource Manager, consulte [Criar uma identidade atribuída pelo usuário](how-to-manage-ua-identity-arm.md#create-a-user-assigned-identity).

### <a name="assign-a-user-assigned-identity-to-an-azure-vmss"></a>Atribuir uma identidade atribuída pelo usuário a uma VMSS do Azure

1. No elemento `resources`, adicione a seguinte entrada para atribuir uma identidade atribuída pelo usuário ao conjunto de dimensionamento de máquinas virtuais.  Certifique-se de substituir `<USERASSIGNEDIDENTITY>` pelo nome da identidade atribuída pelo usuário que você criou.
   
   **Microsoft.Compute/virtualMachineScaleSets API versão 01-06-2018**

   Se apiVersion for `2018-06-01`, as identidades atribuídas ao usuário serão armazenadas no formato de dicionário `userAssignedIdentities` e o valor `<USERASSIGNEDIDENTITYNAME>` deverá ser armazenado em uma variável definida na seção `variables` do modelo.

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2018-06-01",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "userAssigned",
           "userAssignedIdentities": {
               "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
           }
       }
    
   }
   ```   

   **Microsoft.Compute/virtualMachineScaleSets API versão 01-12-2017**
    
   Se `apiVersion` for `2017-12-01` ou anterior, as identidades atribuídas ao usuário serão armazenadas na matriz `identityIds` e o valor `<USERASSIGNEDIDENTITYNAME>` deverá ser armazenado em uma seção de variáveis definida na seção do modelo.

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2017-03-30",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "userAssigned",
           "identityIds": [
               "[resourceID('Micrososft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITY>'))]"
           ]
       }

   }
   ``` 

2. (Opcional) Adicione a seguinte entrada no elemento `extensionProfile` para atribuir a extensão de identidade gerenciada para sua VMSS. Essa etapa é opcional, pois você pode usar o ponto de extremidade de identidade do Serviço de Metadados da Instância do Azure (IMDS) para recuperar também os tokens. Use a seguinte sintaxe:
   
    ```JSON
       "extensionProfile": {
            "extensions": [
                {
                    "name": "MSIWindowsExtension",
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
                }
    ```

3. Quando terminar, seu modelo deverá ser semelhante ao seguinte:
   
   **Microsoft.Compute/virtualMachineScaleSets API versão 01-06-2018**   

   ```json
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[variables('vmssName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "UserAssigned",
                "userAssignedIdentities": {
                    "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
                }
            },
           "properties": {
                //other virtual machine properties...
                "virtualMachineProfile": {
                    //other virtual machine profile properties...
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "ManagedIdentityWindowsExtension",
                                "properties": {
                                  "publisher": "Microsoft.ManagedIdentity",
                                  "type": "ManagedIdentityExtensionForWindows",
                                  "typeHandlerVersion": "1.0",
                                  "autoUpgradeMinorVersion": true,
                                  "settings": {
                                      "port": 50342
                                  }
                                }
                            } 
                        ]
                    }
                }
            }
        }
    ]
   ```

   **Microsoft.Compute/virtualMachines API versão 01-12-2017 e anteriores**

   ```json
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2017-12-01",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[variables('vmssName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "UserAssigned",
                "identityIds": [
                    "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]"
                ]
            },
           "properties": {
                //other virtual machine properties...
                "virtualMachineProfile": {
                    //other virtual machine profile properties...
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "ManagedIdentityWindowsExtension",
                                "properties": {
                                  "publisher": "Microsoft.ManagedIdentity",
                                  "type": "ManagedIdentityExtensionForWindows",
                                  "typeHandlerVersion": "1.0",
                                  "autoUpgradeMinorVersion": true,
                                  "settings": {
                                      "port": 50342
                                  }
                                }
                            } 
                        ]
                    }
                }
            }
        }
    ]
   ```
### <a name="remove-user-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Remover uma identidade atribuída ao usuário de um conjunto de dimensionamento de máquinas virtuais do Azure

Se você tiver um conjunto de dimensionamento de máquinas virtuais que não precise mais de uma identidade de serviço gerenciada:

1. Se você entrar no Azure localmente ou por meio do Portal do Azure, use uma conta que esteja associada à assinatura do Azure que contém o conjunto de dimensionamento de máquinas virtuais.

2. Carregue o modelo em um [editor](#azure-resource-manager-templates) e localize o recurso `Microsoft.Compute/virtualMachineScaleSets` de interesse na seção `resources`. Se você tiver um conjunto de dimensionamento de máquinas virtuais que tem apenas a identidade atribuída ao usuário, você pode desabilitá-la alterando o tipo de identidade para `None`.

   O exemplo a seguir mostra como remover todas as identidades atribuídas pelo usuário de uma VM sem identidades atribuídas pelo sistema:

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2018-06-01",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "None"
        }
   }
   ```
   
   **Microsoft.Compute/virtualMachineScaleSets API versão 01-06-2018**
    
   Para remover uma identidade de usuário único atribuída de um conjunto de dimensionamento de máquinas virtuais, remova-a do dicionário `userAssignedIdentities`.

   Se você tiver uma identidade atribuída ao sistema, mantenha-a no valor `type` no valor `identity`.

   **Microsoft.Compute/virtualMachineScaleSets API versão 01-12-2017**

   Para remover um uma identidade de usuário único atribuído de um conjunto de dimensionamento de máquinas virtuais, remova-a da matriz `identityIds`.

   Se você tiver uma identidade atribuída ao sistema, mantenha-a no valor `type` no valor `identity`.
   
## <a name="next-steps"></a>Próximas etapas

- Para obter uma perspectiva mais ampla sobre Identidade de Serviço Gerenciada, leia a [visão geral de Identidade de Serviço Gerenciada](overview.md).

