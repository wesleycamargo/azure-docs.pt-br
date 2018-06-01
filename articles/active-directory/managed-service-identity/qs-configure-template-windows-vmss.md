---
title: Configurar MSI em um conjunto de dimensionamento de máquinas virtuais do Azure usando um modelo
description: Instruções passo a passo para configurar uma MSI (Identidade de Serviço Gerenciada) em uma VMSS do Azure, usando um modelo do Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2018
ms.author: daveba
ms.openlocfilehash: f7c5d063bfb287de9afe808395b951ecb161da69
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
ms.locfileid: "33930605"
---
# <a name="configure-a-vmss-managed-service-identity-by-using-a-template"></a>Configurar a Identidade de Serviço Gerenciada de uma VMSS usando um modelo

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

A Identidade de Serviço Gerenciado fornece aos serviços do Azure uma identidade gerenciada automaticamente no Active Directory do Azure. Você pode usar essa identidade para autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter as credenciais no seu código. 

Neste artigo, você aprenderá a executar as seguintes operações de Identidade de Serviço Gerenciada em uma VMSS do Azure usando o modelo de implantação do Azure Resource Manager:
- Habilitar e desabilitar a identidade atribuída pelo sistema em uma VMSS do Azure
- Adicionar e remover uma identidade atribuída pelo sistema em uma VMSS do Azure

## <a name="prerequisites"></a>pré-requisitos

- Se você não estiver familiarizado com a Identidade de Serviço Gerenciada, consulte a [seção de visão geral](overview.md). **Verifique se examinou a [diferença entre uma identidade atribuída pelo sistema e uma atribuída pelo usuário](overview.md#how-does-it-work)**.
- Se você ainda não tiver uma conta do Azure, [inscreva-se em uma conta gratuita](https://azure.microsoft.com/free/) antes de continuar.

## <a name="azure-resource-manager-templates"></a>Modelos do Gerenciador de Recursos do Azure

Assim como com o Portal do Azure e o script, os modelos do [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) permitem implantar recursos novos ou modificados definidos por um grupo de recursos do Azure. Há várias opções disponíveis para a edição e a implantação do modelo, tanto locais quanto baseadas em portal, incluindo:

   - Use um [modelo personalizado do Azure Marketplace](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), que permite a criação de um modelo do zero ou use como base um modelo comum existente ou de [Início Rápido](https://azure.microsoft.com/documentation/templates/).
   - Derivar de um grupo de recursos existente, exportando um modelo da [implantação original](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history) ou do [estado atual da implantação](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
   - Usar um [editor JSON local (por exemplo, VS Code)](../../azure-resource-manager/resource-manager-create-first-template.md), depois carregar e implantar usando o PowerShell ou a CLI.
   - Usar o [projeto do Grupo de Recursos do Azure](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) do Visual Studio para criar e implantar um modelo.  

Independentemente da opção escolhido, a sintaxe do modelo será a mesma durante a implantação inicial e a reimplantação. Habilitar a MSI em uma VM nova ou existente é feito da mesma maneira. Além disso, por padrão, o Azure Resource Manager faz uma [atualização incremental](../../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) para implantações.

## <a name="system-assigned-identity"></a>Identidade atribuída pelo sistema

Nesta seção, você habilitará e desabilitará a identidade atribuída pelo sistema usando um modelo do Azure Resource Manager.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vmss-or-an-existing-azure-vmss"></a>Habilitar a identidade atribuída pelo sistema durante a criação de uma VMSS do Azure ou de uma VMSS do Azure existente

1. Carregue o modelo em um editor, localize o recurso `Microsoft.Compute/virtualMachineScaleSets` de interesse na seção `resources`. O seu pode parecer um pouco diferente do mostrado captura de tela a seguir, dependendo do editor usado e de se você está editando um modelo para uma implantação nova ou existente.
   
   ![Captura de tela do modelo – localizar VM](../media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-before-vmss.png) 

2. Para habilitar a identidade atribuída pelo sistema, adicione a propriedade `"identity"` no mesmo nível que a propriedade `"type": "Microsoft.Compute/virtualMachineScaleSets"`. Use a seguinte sintaxe:

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

3. (Opcional) Adicione a extensão de MSI do conjunto de dimensionamento de máquinas virtuais configurada como um elemento `extensionsProfile`. Essa etapa é opcional, pois você pode usar a identidade do Serviço de Metadados da Instância do Azure (IMDS) para recuperar também os tokens.  Use a seguinte sintaxe:

   >[!NOTE] 
   > O exemplo a seguir assume que uma extensão do conjunto de dimensionamento de máquinas virtuais do Windows (`ManagedIdentityExtensionForWindows`) está sendo implantada. Você também pode configurar para Linux usando `ManagedIdentityExtensionForLinux` em vez disso, para os elementos `"name"` e `"type"`.
   >

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

4. Quando terminar, seu modelo deverá ser semelhante ao seguinte:

   ![Captura de tela do modelo após a atualização](../media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-after-vmss.png) 

### <a name="disable-a-system-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Desabilitar uma identidade atribuída pelo sistema de um conjunto de dimensionamento de máquinas virtuais

> [!NOTE]
> Atualmente, não há suporte para desabilitar a Identidade de Serviço Gerenciada de uma de máquina virtual. Enquanto isso, você pode alternar entre usar identidades atribuídas pelo sistema ou pelo usuário.

Caso tenha um conjunto de dimensionamento de máquinas virtuais que não precisa mais da identidade atribuída pelo sistema, mas ainda precisa de identidades atribuídas pelo usuário:

- Carregar o modelo em um editor e alterar o tipo de identidade para `'UserAssigned'`

## <a name="user-assigned-identity"></a>Identidade atribuída pelo usuário

Nesta seção você atribui uma identidade atribuída pelo usuário a uma VMSS do Azure usando um modelo do Azure Resource Manager.

> [!Note]
> Para criar uma identidade atribuída pelo usuário usando um Modelo do Azure Resource Manager, consulte [Criar uma identidade atribuída pelo usuário](how-to-manage-ua-identity-arm.md#create-a-user-assigned-identity).

### <a name="assign-a-user-assigned-identity-to-an-azure-vmss"></a>Atribuir uma identidade atribuída pelo usuário a uma VMSS do Azure

1. No elemento `resources`, adicione a seguinte entrada para atribuir uma identidade atribuída pelo usuário à VMSS.  Certifique-se de substituir `<USERASSIGNEDIDENTITY>` pelo nome da identidade atribuída pelo usuário que você criou.

    ```json
    {
        "name": "[variables('vmssName')]",
        "apiVersion": "2017-03-30",
        "location": "[parameters(Location')]",
        "identity": {
            "type": "userAssigned",
            "identityIds": [
                "[resourceID('Micrososft.ManagedIdentity/userAssignedIdentities/<USERASSIGNEDIDENTITY>)']"
            ]
        }

    }
    ```
2. (Opcional) Adicione a seguinte entrada no elemento `extensionProfile` para atribuir a extensão de identidade gerenciada para sua VMSS. Esta etapa é opcional, uma vez que você pode usar o ponto de extremidade de identidade do serviço de metadados na instância (IMDS) do Azure para recuperar tokens também. Use a seguinte sintaxe:
   
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
3.  Quando terminar, seu modelo deverá ser semelhante ao seguinte:
   
      ![Captura de tela de identidade atribuída pelo usuário](./media/qs-configure-template-windows-vmss/qs-configure-template-windows-final.PNG)

## <a name="next-steps"></a>Próximas etapas

- Para obter uma perspectiva mais ampla sobre MSI, leia a [visão geral da Identidade de Serviço Gerenciado](overview.md).

