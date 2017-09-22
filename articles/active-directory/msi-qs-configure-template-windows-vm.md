---
title: Como configurar o MSI em uma VM do Azure usando um modelo
description: "Instruções passo a passo para configurar um MSI (Identidade de Serviço Gerenciada) em uma VM do Azure usando um modelo do Azure Resource Manager."
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: bryanla
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: 266458323ca54d9805aea12108faed79e69d30b0
ms.contentlocale: pt-br
ms.lasthandoff: 09/14/2017

---

# <a name="configure-a-vm-managed-service-identity-msi-using-a-template"></a>Configurar o MSI (Identidade de Serviço Gerenciada) de um VM usando um modelo

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

A Identidade de Serviço Gerenciado fornece aos serviços do Azure uma identidade gerenciada automaticamente no Active Directory do Azure. Você pode usar essa identidade para autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter as credenciais no seu código. 

Neste artigo, você aprenderá a habilitar e a remover o MSI de uma VM do Windows Azure usando um modelo de implantação do Azure Resource Manager.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-qs-configure-prereqs](../../includes/msi-qs-configure-prereqs.md)]

## <a name="enable-msi-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>Habilitar o MSI durante a criação de uma VM do Azure ou em uma VM existente

Assim como com o Portal do Azure e o script, os modelos do Azure Resource Manager permitem a implantação de recursos novos/modificados definidos por um grupo de recursos do Azure. Há várias opções disponíveis para a edição e implantação do modelo, incluindo localmente e com base no portal/Web. Entre elas estão:

   - Usar um [modelo personalizado do Azure marketplace](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), que permite a criação de um modelo a partir do zero, ou baseá-lo em um modelo comum existente ou de [Início rápido](https://azure.microsoft.com/documentation/templates/).
   - Derivar de um grupo de recursos existente, exportando um modelo da [implantação original](../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history) ou do [estado atual da implantação](../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
   - Usar um [editor de JSON local (por exemplo, o VS Code)](../azure-resource-manager/resource-manager-create-first-template.md), depois carregar/implantar usando o PowerShell ou a CLI.
   - Usar o [projeto de Grupo de Recursos do Azure](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) do Visual Studio para criar e implantar o modelo.  

Independentemente do caminho escolhido, a sintaxe do modelo é a mesma durante a implantação inicial e a reimplantação, portanto a habilitação do MSI em uma máquina virtual nova ou existente é feita da mesma maneira. Além disso, por padrão, o Azure Resource Manager faz uma [atualização incremental](../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) para implantações:

1. Depois de carregar o modelo em um editor, localize o recurso `Microsoft.Compute/virtualMachines` de interesse na seção `resources`. O seu pode parecer um pouco diferente do mostrado nesta captura de tela dependendo do editor usado, e se você está editando um modelo para uma implantação nova ou existente:

   >[!NOTE] 
   > A Etapa 2 também pressupõe que as variáveis `vmName`, `storageAccountName` e `nicName` estão definidas no modelo.
   >

   ![Modelo antes da captura de tela - localizar VM](./media/msi-qs-configure-template-windows-vm/template-file-before.png) 

2. Adicione a propriedade `"identity"` ao mesmo nível que a propriedade `"type": "Microsoft.Compute/virtualMachines"` usando a seguinte sintaxe:

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

3. Depois, adicione a extensão do MSI da VM como um elemento `resources` usando a seguinte sintaxe:

   >[!NOTE] 
   > O exemplo a seguir pressupõe que uma extensão de VM do Windows (`ManagedIdentityExtensionForWindows`) está sendo implantada. Você também pode configurar isso para Linux usando `ManagedIdentityExtensionForLinux` no lugar.
   >

   ```JSON
   { 
       "type": "Microsoft.Compute/virtualMachines/extensions",
       "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
       "apiVersion": "2016-03-30",
       "location": "[resourceGroup().location]",
       "dependsOn": [
           "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
       ],
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

4. Quando terminar, seu modelo deverá ser semelhante à imagem a seguir:

   ![Modelo após a captura](./media/msi-qs-configure-template-windows-vm/template-file-after.png) 

## <a name="remove-msi-from-an-azure-vm"></a>Remover o MSI de uma VM do Azure

Se você tiver uma máquina virtual que não precisa mais de um MSI, basta remover os dois elementos adicionados no exemplo anterior: a propriedade `"identity"` e o recurso `"Microsoft.Compute/virtualMachines/extensions"` da VM.

## <a name="related-content"></a>Conteúdo relacionado

- [Visão geral da Identidade de Serviço Gerenciada](msi-overview.md)

Use a seção de comentários a seguir para fornecer seus comentários e nos ajudar a aprimorar e adaptar nosso conteúdo.


