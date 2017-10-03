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
ms.sourcegitcommit: 4f77c7a615aaf5f87c0b260321f45a4e7129f339
ms.openlocfilehash: 8b599c3e0e7d4fa3ae5bdb156191bff0553249ee
ms.contentlocale: pt-br
ms.lasthandoff: 09/22/2017

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

1. Se você entrar no Azure localmente ou por meio do portal do Azure, use uma conta que esteja associada com a assinatura do Azure que contenha a máquina virtual. Verifique também se sua conta pertence a uma função que fornece permissões de gravação na VM, como "Colaborador da Máquina Virtual".

2. Depois de carregar o modelo em um editor, localize o recurso `Microsoft.Compute/virtualMachines` de interesse na seção `resources`. O seu pode parecer um pouco diferente do mostrado nesta captura de tela dependendo do editor usado, e se você está editando um modelo para uma implantação nova ou existente:

   >[!NOTE] 
   > Este exemplo supõe que variáveis como `vmName`, `storageAccountName`, e `nicName` foram definidas no modelo.
   >

   ![Modelo antes da captura de tela - localizar VM](./media/msi-qs-configure-template-windows-vm/template-file-before.png) 

3. Adicione a propriedade `"identity"` ao mesmo nível que a propriedade `"type": "Microsoft.Compute/virtualMachines"` usando a seguinte sintaxe:

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

4. Depois, adicione a extensão do MSI da VM como um elemento `resources` usando a seguinte sintaxe:

   >[!NOTE] 
   > O exemplo a seguir pressupõe que uma extensão de VM do Windows (`ManagedIdentityExtensionForWindows`) está sendo implantada. Você também pode configurar para Linux usando `ManagedIdentityExtensionForLinux` no lugar, para os elementos `"name"` e `"type"`.
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

5. Quando terminar, seu modelo deverá ser semelhante à imagem a seguir:

   ![Modelo após a captura](./media/msi-qs-configure-template-windows-vm/template-file-after.png) 

## <a name="remove-msi-from-an-azure-vm"></a>Remover o MSI de uma VM do Azure

Se você tiver uma Máquina Virtual que não precisa mais de um MSI:

1. Se você entrar no Azure localmente ou por meio do portal do Azure, use uma conta que esteja associada com a assinatura do Azure que contenha a máquina virtual. Verifique também se sua conta pertence a uma função que fornece permissões de gravação na VM, como "Colaborador da Máquina Virtual".

2. Remova os dois elementos que foram adicionados na seção anterior: a propriedade da VM `"identity"` e o `"Microsoft.Compute/virtualMachines/extensions"` recurso.

## <a name="related-content"></a>Conteúdo relacionado

- [Visão geral da Identidade do Serviço Gerenciado](msi-overview.md)

Use a seção de comentários a seguir para fornecer seus comentários e nos ajudar a aprimorar e adaptar nosso conteúdo.


