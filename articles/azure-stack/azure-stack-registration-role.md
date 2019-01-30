---
title: Como criar uma função de registro para o Azure Stack
description: Como criar uma função personalizada para evitar o uso de administrador global para o registro.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2019
ms.author: patricka
ms.reviewer: rtiberiu
ms.lastreviewed: 01/10/2019
ms.openlocfilehash: 80caa470675a78a9c2e3d4c055333719f54fe64a
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55247877"
---
# <a name="create-a-registration-role-for-azure-stack"></a>Criar uma função de registro para o Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Para cenários em que você não deseja conceder permissões de proprietário na assinatura do Azure, você pode criar uma função personalizada para atribuir permissões a uma conta de usuário para registrar seu Azure Stack.

> [!WARNING]
> Isso não é um recurso de postura de segurança. Usá-lo em cenários onde você deseja que as restrições para impedir alterações acidentais à assinatura do Azure. Quando um usuário estiver direitos delegados para essa função personalizada, o usuário tem direitos para editar permissões e elevar os direitos. Atribua apenas usuários que confiáveis para a função personalizada.

Ao registrar o Azure Stack, a conta de registro requer as seguintes permissões do Active Directory do Azure e assinatura do Azure:

* **Permissões de registro de aplicativo em seu locatário do Azure Active Directory:** Os administradores têm permissões de registro do aplicativo. A permissão para usuários é uma configuração global para todos os usuários no locatário. Para exibir ou alterar a configuração, consulte [criar aplicativo e entidade de serviço que pode acessar recursos de um Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions).

    O *usuário pode registrar aplicativos* configuração deve ser definida como **Sim** para habilitar uma conta de usuário para registrar o Azure Stack. Se a configuração de registros de aplicativo for definido como **não**, não é possível usar uma conta de usuário e deve usar uma conta de administrador global para registrar o Azure Stack.

* **Um conjunto de permissões suficientes de assinatura do Azure:** Os usuários do grupo Proprietários tem permissões suficientes. Para outras contas, você pode atribuir a permissão definida atribuindo uma função personalizada, conforme descrito nas seções a seguir.

## <a name="create-a-custom-role-using-powershell"></a>Criar uma função personalizada usando o PowerShell

Para criar uma função personalizada, você precisa ter a permissão `Microsoft.Authorization/roleDefinitions/write` em todo `AssignableScopes`, como [Proprietário](../role-based-access-control/built-in-roles.md#owner) ou [Administrador de Acesso de Usuário](../role-based-access-control/built-in-roles.md#user-access-administrator). Use o modelo JSON a seguir para simplificar a definição de função personalizada. O modelo cria uma função personalizada que permite que o necessária acesso de leitura e gravação para o registro do Azure Stack.

1. Crie um arquivo JSON. Por exemplo, `C:\CustomRoles\registrationrole.json`
2. Adicione o seguinte JSON ao arquivo. Substitua <SubscriptionID> por sua ID da assinatura do Azure.

    ```json
    {
      "Name": "Azure Stack registration role",
      "Id": null,
      "IsCustom": true,
      "Description": "Allows access to register Azure Stack",
      "Actions": [
        "Microsoft.Resources/subscriptions/resourceGroups/write",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.AzureStack/registrations/*",
        "Microsoft.AzureStack/register/action",
        "Microsoft.Authorization/roleAssignments/read",
        "Microsoft.Authorization/roleAssignments/write",
        "Microsoft.Authorization/roleAssignments/delete",
        "Microsoft.Authorization/permissions/read"
      ],
      "NotActions": [
      ],
      "AssignableScopes": [
        "/subscriptions/<SubscriptionID>"
      ]
    }
    ```

3. No PowerShell, conecte-se ao Azure para usar o Azure Resource Manager. Quando solicitado, autentique usando uma conta com permissões suficientes, como [proprietário](../role-based-access-control/built-in-roles.md#owner) ou [administrador de acesso do usuário](../role-based-access-control/built-in-roles.md#user-access-administrator).

    ```azurepowershell
    Connect-AzureRmAccount
    ```

4. Para adicionar a função à assinatura, use **New-AzureRmRoleDefinition** especificando o arquivo de modelo JSON.

    ``` azurepowershell
    New-AzureRmRoleDefinition -InputFile "C:\CustomRoles\registrationrole.json"
    ```

## <a name="assign-a-user-to-registration-role"></a>Atribuir um usuário à função de registro

Depois que a função personalizada de registro é criada, atribua os usuários com função de registro do Azure Stack.

1. Entrar com a conta com permissões suficientes na assinatura do Azure para delegar direitos - como [proprietário](../role-based-access-control/built-in-roles.md#owner) ou [administrador de acesso do usuário](../role-based-access-control/built-in-roles.md#user-access-administrator) .
2. Na **inscrições**, selecione **controle de acesso (IAM) > Adicionar atribuição de função**.
3. Na **função**, escolha a função personalizada que você criou *função de registro do Azure Stack*.
4. Selecione os usuários que você deseja atribuir à função.
5. Selecione **salvar** para atribuir os usuários selecionados à função.

    ![Selecione os usuários para atribuir a função](media/azure-stack-registration-role/assign-role.png)

Para obter mais informações sobre como usar funções personalizadas, consulte [gerenciar o acesso usando o portal do Azure e o RBAC](../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Próximas etapas

[Registre-se a pilha do Azure com o Azure](azure-stack-registration.md)
