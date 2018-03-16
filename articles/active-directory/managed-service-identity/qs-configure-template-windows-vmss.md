---
title: "Configurar MSI em um conjunto de dimensionamento de máquinas virtuais do Azure usando um modelo"
description: "Instruções passo a passo para configurar uma MSI (Identidade de Serviço Gerenciada) em uma VMSS do Azure, usando um modelo do Azure Resource Manager."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2018
ms.author: daveba
ms.openlocfilehash: 7f65c2ce53e30aa8682a9ee798af9001b4f210dc
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2018
---
# <a name="configure-a-vm-managed-service-identity-by-using-a-template"></a>Configurar a Identidade de Serviço Gerenciado de um VM usando um modelo

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

A MSI (Identidade de Serviço Gerenciado) fornece aos serviços do Azure uma identidade gerenciada automaticamente no Azure AD (Azure Active Directory). Você pode usar essa identidade para autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter as credenciais no seu código. 

Neste artigo, você aprenderá como habilitar e remover MSI para um conjunto de dimensionamento de máquinas virtuais do Azure, usando um modelo de implantação do Azure Resource Manager.

## <a name="prerequisites"></a>pré-requisitos

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="enable-msi-during-creation-of-an-azure-virtual-machine-scale-set-or-an-existing-azure-virtual-machine-scale-set"></a>Habilitar MSI durante a criação de um conjunto de dimensionamento de máquinas virtuais do Azure, ou um conjunto de dimensionamento de máquinas virtuais do Azure existente

Assim como com o portal do Azure e o script, os modelos do Azure Resource Manager permitem implantar recursos novos ou modificados definidos por um grupo de recursos do Azure. Há várias opções disponíveis para a edição e a implantação do modelo, tanto locais quanto baseadas em portal, incluindo:

   - Use um [modelo personalizado do Azure Marketplace](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), que permite a criação de um modelo do zero ou use como base um modelo comum existente ou de [Início Rápido](https://azure.microsoft.com/documentation/templates/).
   - Derivar de um grupo de recursos existente, exportando um modelo da [implantação original](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history) ou do [estado atual da implantação](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
   - Usar um [editor JSON local (por exemplo, VS Code)](../../azure-resource-manager/resource-manager-create-first-template.md), depois carregar e implantar usando o PowerShell ou a CLI.
   - Usar o [projeto do Grupo de Recursos do Azure](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) do Visual Studio para criar e implantar um modelo.  

Independentemente da opção escolhido, a sintaxe do modelo será a mesma durante a implantação inicial e a reimplantação. Habilitar MSI em um conjunto de dimensionamento de máquinas virtuais do Azure novo ou existente é feito da mesma maneira. Além disso, por padrão, o Azure Resource Manager faz uma [atualização incremental](../../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) para implantações:

1. Se você entrar no Azure localmente ou por meio do Portal do Azure, use uma conta que esteja associada à assinatura do Azure que contém o conjunto de dimensionamento de máquinas virtuais.

2. Depois de carregar o modelo em um editor, localize o recurso `Microsoft.Compute/virtualMachineScaleSets` de interesse na seção `resources`. O seu pode parecer um pouco diferente do mostrado captura de tela a seguir, dependendo do editor usado e de se você está editando um modelo para uma implantação nova ou existente.
   
   ![Captura de tela do modelo – localizar VM](../media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-before-vmss.png) 

3. Adicione a propriedade `"identity"` no mesmo nível que a propriedade `"type": "Microsoft.Compute/virtualMachineScaleSets"`. Use a seguinte sintaxe:

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

4. Em seguida, adicione a extensão de MSI do conjunto de dimensionamento de máquinas virtuais configurada como um elemento `extensionsProfile`. Use a seguinte sintaxe:

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

5. Quando terminar, seu modelo deverá ser semelhante ao seguinte:

   ![Captura de tela do modelo após a atualização](../media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-after-vmss.png) 

## <a name="remove-msi-from-an-azure-virtual-machine-scale-set"></a>Remover MSI de um conjunto de dimensionamento de máquinas virtuais do Azure

Se você tiver um conjunto de dimensionamento de máquinas virtuais que não precisa mais de uma MSI:

1. Se você entrar no Azure localmente ou por meio do Portal do Azure, use uma conta que esteja associada à assinatura do Azure que contém o conjunto de dimensionamento de máquinas virtuais.

2. Remova os dois elementos que foram adicionados na seção anterior: o conjunto de dimensionamento de máquinas virtuais `"identity"` e as `"extensionsProfile"` propriedades.

## <a name="next-steps"></a>Próximas etapas

- Para obter uma perspectiva mais ampla sobre MSI, leia a [visão geral da Identidade de Serviço Gerenciado](overview.md).

