---
title: Elevar o acesso de um Administrador global no Azure Active Directory | Microsoft Docs
description: Descreve como elevar o acesso de um Administrador global no Azure Active Directory usando o portal do Azure ou a API REST.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: bagovind
ms.assetid: b547c5a5-2da2-4372-9938-481cb962d2d6
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/29/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 0abf0a5971435fc3842a93e79d39468cba5c74da
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2018
ms.locfileid: "37445204"
---
# <a name="elevate-access-for-a-global-administrator-in-azure-active-directory"></a>Elevar o acesso de um Administrador global no Azure Active Directory

Caso você seja um [Administrador global](../active-directory/users-groups-roles/directory-assign-admin-roles.md#company-administrator) no Azure Active Directory (Azure AD), pode haver momentos em que você deseje fazer o seguinte:

- Recuperar o acesso a uma assinatura do Azure quando um usuário tiver perdido o acesso
- Conceder que acesso a uma assinatura do Azure a outro usuário ou a si mesmo
- Ver todas as assinaturas do Azure em uma organização
- Permitir o acesso de um aplicativo de automação (como um aplicativo de faturamento ou de auditoria) a todas as assinaturas do Azure

Por padrão, as funções de administrador do Azure AD e as funções de RBAC (controle de acesso baseado em função) do Azure não se estendem ao Azure AD e Azure. No entanto, caso você seja um Administrador global no Azure AD, pode elevar seu acesso para gerenciar assinaturas do Azure e grupos de gerenciamento. Ao elevar o acesso, você receberá a função de [Administrador de Acesso do Usuário](built-in-roles.md#user-access-administrator) (uma função de RBAC) em todas as assinaturas de um locatário específico. A função de Administrador de Acesso do Usuário permite que você conceda a outros usuários o acesso aos recursos do Azure no escopo raiz (`/`).

Essa elevação deve ser temporária e só deve ser feita quando for necessário.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="elevate-access-for-a-global-administrator-using-the-azure-portal"></a>Elevar o acesso de um Administrador global usando o portal do Azure

1. Entre no [portal do Azure](https://portal.azure.com) ou no [Centro de administração do Azure Active Directory](https://aad.portal.azure.com).

1. Na lista de navegação, clique em **Azure Active Directory**, depois clique em **Propriedades**.

   ![Azure Active Directory – captura de tela](./media/elevate-access-global-admin/aad-properties.png)

1. Em **Administrador global pode gerenciar Assinaturas do Azure e Grupos de Gerenciamento**, selecione a opção **Sim**.

   ![O Administrador global pode gerenciar Assinaturas do Azure e Grupos de Gerenciamento – captura de tela](./media/elevate-access-global-admin/aad-properties-global-admin-setting.png)

   Ao selecionar a opção **Sim**, sua conta de Administrador global (conectada no usuário no momento) é adicionada à função de Administrador de Acesso do Usuário no Azure RBAC no escopo raiz (`/`), o que lhe concede acesso de visualização e relatório em todas as assinaturas do Azure associadas ao seu locatário do Azure AD.

   Ao selecionar a opção **Não**, sua conta de Administrador global (conectada no usuário no momento) é removida da função de Administrador de Acesso do Usuário no Azure RBAC. Não é possível ver todas as assinaturas do Azure associadas ao locatário do Azure AD, sendo possível visualizar e gerenciar apenas as assinaturas do Azure às quais você recebeu acesso.

1. Clique em **Salvar**, para salvar suas configurações.

   Essa configuração não é uma propriedade global, aplicando-se somente ao usuário conectado no momento.

1. Execute as tarefas que você precisa fazer com o acesso elevado. Ao terminar, retorne a opção para **Não**.

## <a name="list-role-assignment-at-the-root-scope--using-powershell"></a>Listar a atribuição de função no escopo raiz (/) usando o PowerShell

Para listar a atribuição de função de Administrador de Acesso do Usuário para um usuário no escopo raiz (`/`), use o comando [Get-AzureRmRoleAssignment](/powershell/module/azurerm.resources/get-azurermroleassignment).

```azurepowershell
Get-AzureRmRoleAssignment | where {$_.RoleDefinitionName -eq "User Access Administrator" `
  -and $_.SignInName -eq "<username@example.com>" -and $_.Scope -eq "/"}
```

```Example
RoleAssignmentId   : /providers/Microsoft.Authorization/roleAssignments/098d572e-c1e5-43ee-84ce-8dc459c7e1f0
Scope              : /
DisplayName        : username
SignInName         : username@example.com
RoleDefinitionName : User Access Administrator
RoleDefinitionId   : 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9
ObjectId           : d65fd0e9-c185-472c-8f26-1dafa01f72cc
ObjectType         : User
```

## <a name="remove-a-role-assignment-at-the-root-scope--using-powershell"></a>Remover a atribuição de função no escopo raiz (/) usando o PowerShell

Para remover a atribuição de função de Administrador de Acesso do Usuário para um usuário no escopo raiz (`/`), use o comando [Remove-AzureRmRoleAssignment](/powershell/module/azurerm.resources/remove-azurermroleassignment).

```azurepowershell
Remove-AzureRmRoleAssignment -SignInName <username@example.com> `
  -RoleDefinitionName "User Access Administrator" -Scope "/"
```

## <a name="elevate-access-for-a-global-administrator-using-the-rest-api"></a>Elevar o acesso de um Administrador global usando a API REST

Use as etapas básicas a seguir para elevar o acesso de um Administrador global usando a API REST.

1. Usando a REST, chame `elevateAccess`, que lhe concede a função de Administrador de Acesso do Usuário no escopo raiz (`/`).

   ```http
   POST https://management.azure.com/providers/Microsoft.Authorization/elevateAccess?api-version=2016-07-01
   ```

1. Crie uma [atribuição de função](/rest/api/authorization/roleassignments) para atribuir qualquer função em qualquer escopo. O exemplo a seguir mostra as propriedades para atribuir a função {roleDefinitionID} no escopo raiz (`/`):

   ```json
   { 
     "properties": {
       "roleDefinitionId": "providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionID}",
       "principalId": "{objectID}",
       "scope": "/"
     },
     "id": "providers/Microsoft.Authorization/roleAssignments/64736CA0-56D7-4A94-A551-973C2FE7888B",
     "type": "Microsoft.Authorization/roleAssignments",
     "name": "64736CA0-56D7-4A94-A551-973C2FE7888B"
   }
   ```

1. Enquanto Administrador de Acesso do Usuário, você também pode remover atribuições de função no escopo raiz (`/`).

1. Remova seus privilégios de Administrador de Acesso do Usuário até que sejam necessários novamente.

## <a name="list-role-assignments-at-the-root-scope--using-the-rest-api"></a>Listar a atribuição de função no escopo raiz (/) usando a API REST

Você pode listar todas as atribuições de função para um usuário no escopo raiz (`/`).

- Chame [GET roleAssignments](/rest/api/authorization/roleassignments/listforscope), em que `{objectIdOfUser}` é a ID de objeto do usuário cujas atribuições de função que você deseja recuperar.

   ```http
   GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectIdOfUser}'
   ```

## <a name="remove-elevated-access-using-the-rest-api"></a>Remover acesso com privilégios elevados usando a API REST

Ao chamar `elevateAccess`, você cria uma atribuição de função para si mesmo, de modo que, para revogar esses privilégios, é preciso remover a atribuição.

1. Chame [GET roleDefinitions](/rest/api/authorization/roledefinitions/get), em que `roleName` é igual a Administrador de Acesso do Usuário, para determinar a ID do nome da função de Administrador de Acesso do Usuário.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter=roleName+eq+'User Access Administrator'
    ```

    ```json
    {
      "value": [
        {
          "properties": {
        "roleName": "User Access Administrator",
        "type": "BuiltInRole",
        "description": "Lets you manage user access to Azure resources.",
        "assignableScopes": [
          "/"
        ],
        "permissions": [
          {
            "actions": [
              "*/read",
              "Microsoft.Authorization/*",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "createdOn": "0001-01-01T08:00:00.0000000Z",
        "updatedOn": "2016-05-31T23:14:04.6964687Z",
        "createdBy": null,
        "updatedBy": null
          },
          "id": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
          "type": "Microsoft.Authorization/roleDefinitions",
          "name": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9"
        }
      ],
      "nextLink": null
    }
    ```

    Salve a ID do parâmetro `name`, nesse caso: `18d7d88d-d35e-4fb5-a5c3-7773c20a72d9`.

2. Também é preciso listar a atribuição de função de administrador de locatário no escopo de locatário. Liste todas as atribuições do escopo de locatário de `principalId` do administrador de locatário que fez a chamada de elevação de acesso. Isso listará todas as atribuições do objectid no locatário.

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=principalId+eq+'{objectid}'
    ```
    
    >[!NOTE] 
    >Um administrador de locatário não deve ter muitas atribuições. Se a consulta anterior retornar atribuições demais, você também poderá consultar todas as atribuições apenas no nível de escopo de locatário e, em seguida, filtrar os resultados: `GET https://management.azure.com/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter=atScope()`
        
    2. As chamadas anteriores retornam uma lista de atribuições de função. Localize a atribuição de função em que o escopo é `"/"` e o `roleDefinitionId` termina com a ID do nome da função encontrada na etapa 1 e `principalId` corresponde à objectId do administrador de locatário. 
    
    Atribuição de função de amostra:

        ```json
        {
          "value": [
            {
              "properties": {
                "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
                "principalId": "{objectID}",
                "scope": "/",
                "createdOn": "2016-08-17T19:21:16.3422480Z",
                "updatedOn": "2016-08-17T19:21:16.3422480Z",
                "createdBy": "93ce6722-3638-4222-b582-78b75c5c6d65",
                "updatedBy": "93ce6722-3638-4222-b582-78b75c5c6d65"
              },
              "id": "/providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099",
              "type": "Microsoft.Authorization/roleAssignments",
              "name": "e7dd75bc-06f6-4e71-9014-ee96a929d099"
            }
          ],
          "nextLink": null
        }
        ```
        
    Novamente, salve a ID do parâmetro `name`, nesse caso: e7dd75bc-06f6-4e71-9014-ee96a929d099.

    3. Por fim, use a ID da atribuição de função para remover a atribuição adicionada por `elevateAccess`:

    ```http
    DELETE https://management.azure.com/providers/Microsoft.Authorization/roleAssignments/e7dd75bc-06f6-4e71-9014-ee96a929d099?api-version=2015-07-01
    ```

## <a name="next-steps"></a>Próximas etapas

- [Controle de acesso baseado em função com REST](role-assignments-rest.md)
- [Gerenciar acesso aos recursos do Azure com Privileged Identity Management](pim-azure-resource.md)
- [Gerenciar o acesso ao gerenciamento do Azure com acesso condicional](conditional-access-azure-management.md)
