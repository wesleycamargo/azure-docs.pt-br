---
title: Como criar e excluir uma Identidade de Serviço Gerenciada atribuída pelo usuário usando o Azure Resource Manager
description: Instruções passo a passo sobre como criar e excluir uma Identidade de Serviço Gerenciada atribuída pelo usuário usando o Azure Resource.
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
ms.date: 04/16/2018
ms.author: daveba
ms.openlocfilehash: 42d5b55e0bddf2d027810bfdf146de9bfee8a0fb
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2018
ms.locfileid: "39188124"
---
# <a name="create-list-and-delete-a-user-assigned-identity-using-azure-resource-manager"></a>Criar, listar e excluir uma identidade atribuída pelo usuário usando o Azure Resource Manager

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

A Identidade de Serviço Gerenciado fornece aos serviços do Azure uma identidade gerenciada no Azure Active Directory. Você pode usar essa identidade para autenticar em qualquer serviço que seja compatível com a autenticação do Azure Active Directory sem precisar ter as credenciais no seu código. 

Nesse artigo, você criará uma identidade gerenciada atribuída pelo usuário usando um Azure Resource Manager.

Não for possível listar e excluir uma identidade atribuída pelo usuário usando um  modelo de Azure Resource Manager.  Consulte os artigos a seguir para criar e listar uma identidade atribuída pelo usuário:

- [Listar identidade atribuída pelo usuário](how-to-manage-ua-identity-cli.md#list-user-assigned-identities)
- [Excluir identidade atribuída pelo usuário](how-to-manage-ua-identity-cli.md#delete-a-user-assigned-identity)
## <a name="prerequisites"></a>Pré-requisitos

- Se você não estiver familiarizado com a Identidade de Serviço Gerenciada, consulte a [seção de visão geral](overview.md). **Verifique se examinou a [diferença entre uma identidade atribuída pelo sistema e uma atribuída pelo usuário](overview.md#how-does-it-work)**.
- Se você ainda não tiver uma conta do Azure, [inscreva-se em uma conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Para realizar as operações neste artigo, sua conta precisará da seguinte atribuição de função:
    - [Colaborador de Identidade Gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) função para criar, ler (listar), atualizar e excluir uma identidade atribuída ao usuário.

## <a name="template-creation-and-editing"></a>Criação e edição de modelo

Assim como com o portal do Azure e o script, os modelos do Azure Resource Manager permitem implantar recursos novos ou modificados definidos por um grupo de recursos do Azure. Há várias opções disponíveis para a edição e a implantação do modelo, tanto locais quanto baseadas em portal, incluindo:

- Use um [modelo personalizado do Azure Marketplace](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), que permite a criação de um modelo do zero ou use como base um modelo comum existente ou de [Início Rápido](https://azure.microsoft.com/documentation/templates/).
- Derivar de um grupo de recursos existente, exportando um modelo da [implantação original](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history) ou do [estado atual da implantação](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
- Usar um [editor JSON local (por exemplo, VS Code)](../../azure-resource-manager/resource-manager-create-first-template.md), depois carregar e implantar usando o PowerShell ou a CLI.
- Usar o [projeto do Grupo de Recursos do Azure](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) do Visual Studio para criar e implantar um modelo. 

## <a name="create-a-user-assigned-identity"></a>Criar uma identidade atribuída pelo usuário 

Para criar uma identidade atribuída pelo usuário, use o modelo a seguir. Substitua o valor `<USER ASSIGNED IDENTITY NAME>` por seus próprios valores:

[!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "resourceName": {
          "type": "string",
          "metadata": {
            "description": "<USER ASSIGNED IDENTITY NAME>"
          }
        }
  },
  "resources": [
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "name": "[parameters('resourceName')]",
      "apiVersion": "2015-08-31-PREVIEW",
      "location": "[resourceGroup().location]"
    }
  ],
  "outputs": {
      "identityName": {
          "type": "string",
          "value": "[parameters('resourceName')]"
      }
  }
}
```
## <a name="related-content"></a>Conteúdo relacionado

Para obter informações sobre como atribuir uma identidade atribuída pelo usuário para uma VM do Azure usando um modelo de Azure Resource Manager, consulte [Configurar Identidade de Serviço Gerenciada (MSI) de VM usando um modelo](qs-configure-template-windows-vm.md)


 
