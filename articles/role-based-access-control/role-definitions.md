---
title: Compreender as definições de função no RBAC do Azure | Microsoft Docs
description: Saiba mais sobre as definições RBAC (controle de acesso baseado em função) e como definir funções personalizadas para o gerenciamento de acesso refinado de recursos no Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/18/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: 9bb7808f2b483fe9cd7d22c6df3fe80d4a98f1f4
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35266849"
---
# <a name="understand-role-definitions"></a>Compreender as definições de função

Se você estiver tentando compreender o funcionamento de uma função ou se estiver criando sua própria [função personalizada](custom-roles.md), é aconselhável entender como as funções são definidas. Este artigo descreve os detalhes das definições de função e fornece alguns exemplos.

## <a name="role-definition-structure"></a>Estrutura da definição da função

Uma *definição de função* é um conjunto de permissões. Às vezes, é chamada apenas de *função*. Uma definição de função lista as operações que podem ser executadas, como leitura, gravação e exclusão. Ele também pode listar as operações que não podem ser executadas ou operações relacionadas a dados subjacentes. Uma definição de função tem a seguinte estrutura:

```
assignableScopes []
description
id
name
permissions []
  actions []
  dataActions []
  notActions []
  notDataActions []
roleName
roleType
type
```

Operações são especificadas com cadeias de caracteres que têm o seguinte formato:

- `Microsoft.{ProviderName}/{ChildResourceType}/{action}`

A parte `{action}` de uma cadeia de caracteres de operação especifica o tipo de operações que você pode executar em um tipo de recurso. Por exemplo, você verá as seguintes subcadeias de caracteres em `{action}`:

| Subcadeia de caracteres de ação    | DESCRIÇÃO         |
| ------------------- | ------------------- |
| `*` | O caractere curinga concede acesso a todas as operações que correspondem à cadeia de caracteres. |
| `read` | Habilita operações de leitura (GET). |
| `write` | Habilita operações de gravação (PUT, POST e PATCH). |
| `delete` | Habilita operações de exclusão (DELETE). |

Aqui está a definição da função [Colaborador](built-in-roles.md#contributor) no formato JSON. A operação curinga (`*`) em `actions` indica que a entidade de segurança atribuída a essa função pode executar todas as ações ou, em outras palavras, pode gerenciar tudo. Isso inclui ações definidas no futuro, conforme o Azure adiciona novos tipos de recurso. As operações em `notActions` são subtraídas de `actions`. No caso da função [Contribuidor](built-in-roles.md#contributor), `notActions` remove a capacidade de essa função gerenciar o acesso a recursos e também atribuir acesso aos recursos.

```json
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you manage everything except access to resources.",
    "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
    "permissions": [
      {
        "actions": [
          "*"
        ],
        "additionalProperties": {},
        "dataActions": [],
        "notActions": [
          "Microsoft.Authorization/*/Delete",
          "Microsoft.Authorization/*/Write",
          "Microsoft.Authorization/elevateAccess/Action"
        ],
        "notDataActions": []
      }
    ],
    "roleName": "Contributor",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

## <a name="management-and-data-operations-preview"></a>Gerenciamento e operações de dados (visualização)

O controle de acesso baseado em função para operações de gerenciamento é especificado nas seções `actions` e `notActions` de uma definição de função. Aqui estão alguns exemplos de operações de gerenciamento no Azure:

- Gerenciar o acesso a uma conta de armazenamento
- Criar, atualizar ou excluir um contêiner de blob
- Excluir um grupo de recursos e todos os seus recursos

O acesso de gerenciamento não é herdado para seus dados. Essa separação impede que funções com curingas (`*`) tenham acesso irrestrito aos seus dados. Por exemplo, se um usuário tiver uma [leitor](built-in-roles.md#reader) função em uma assinatura, em seguida, eles podem exibir a conta de armazenamento, mas não podem exibir os dados subjacentes por padrão.

Anteriormente, o controle de acesso baseado em função não foi usado para operações de dados. Autorização para operações de dados variadas em provedores de recursos. O mesmo modelo de autorização de controle de acesso baseado em função usado para operações de gerenciamento foi estendido para operações de dados (atualmente na visualização).

Para dar suporte a operações de dados, novas seções de dados foram adicionadas à estrutura de definição de função. Operações de dados são especificadas no `dataActions` e `notDataActions` seções. Adicionando essas seções de dados, a separação entre o gerenciamento e de dados é mantida. Isso impede que as atribuições de função atual com curingas (`*`) de repente ter acesso a dados. Aqui estão algumas operações de dados que podem ser especificadas em `dataActions` e `notDataActions`:

- Leia uma lista de blobs em um contêiner
- Gravar um blob de armazenamento em um contêiner
- Deletar uma mensagem em uma fila

Aqui está a [ definição de função do Leitor de Dados de Blob de Armazenamento (Visualização) ](built-in-roles.md#storage-blob-data-reader-preview), que inclui operações nas seções `actions` e `dataActions`. Essa função permite que você leia o contêiner de blob e também os dados blob subjacentes.

```json
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Allows for read access to Azure Storage blob containers and data.",
    "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
    "name": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
    "permissions": [
      {
        "actions": [
          "Microsoft.Storage/storageAccounts/blobServices/containers/read"
        ],
        "additionalProperties": {},
        "dataActions": [
          "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
        ],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "Storage Blob Data Reader (Preview)",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

Apenas operações de dados podem ser adicionadas para o `dataActions` e `notDataActions` seções. Provedores de recursos identificam quais operações são operações de dados, definindo o `isDataAction` propriedade `true`. Para ver uma lista das operações em que `isDataAction` é `true`, consulte [ Operações do provedor de recursos ](resource-provider-operations.md). Funções que não têm as operações de dados não precisam ter `dataActions` e `notDataActions` seções dentro da definição de função.

Autorização para todas as chamadas de API de operação de gerenciamento é tratada pelo Gerenciador de recursos do Azure. A autorização para chamadas de API de operação de dados é tratada por um provedor de recursos ou o Gerenciador de recursos do Azure.

### <a name="data-operations-example"></a>Exemplo de operações de dados

Para entender melhor como funcionam as operações de gerenciamento e de dados, vamos considerar um exemplo específico. Alice foi atribuída a [proprietário](built-in-roles.md#owner) função no escopo da assinatura. Bob foi atribuído a [Colaborador de dados de Blob de armazenamento (visualização)](built-in-roles.md#storage-blob-data-contributor-preview) função em um escopo de conta de armazenamento. O diagrama a seguir mostra este exemplo.

![Controle de acesso baseado em função foi estendido para dar suporte a operações de dados e de gerenciamento](./media/role-definitions/rbac-management-data.png)

O [proprietário](built-in-roles.md#owner) função para Alice e [Colaborador de dados de Blob de armazenamento (visualização)](built-in-roles.md#storage-blob-data-contributor-preview) função para Bob tem as seguintes ações:

Proprietário

&nbsp;&nbsp;&nbsp;&nbsp;Ações<br>
&nbsp;&nbsp;&nbsp;&nbsp;`*`

Colaborador de dados de blob de armazenamento (pré-visualização)

&nbsp;&nbsp;&nbsp;&nbsp;Ações<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/write`<br>
&nbsp;&nbsp;&nbsp;&nbsp;DataActions<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write`

Como Alice tem uma ação curinga (`*`) em um escopo de assinatura, suas permissões são herdadas para permitir que ela execute todas as ações de gerenciamento. No entanto, Alice não pode executar operações de dados. Por exemplo, por padrão, Alice não pode ler os blobs dentro de um contêiner, mas ela pode ler, gravar e exclua os contêineres.

Permissões de Bob são restritas a apenas o `actions` e `dataActions` especificado no [Colaborador de dados de Blob de armazenamento (visualização)](built-in-roles.md#storage-blob-data-contributor-preview) função. Com base na função, Bob pode executar operações de dados e de gerenciamento. Por exemplo, Bob pode ler, gravar e excluir contêineres na conta de armazenamento especificado e ele também pode ler, gravar e excluir os blobs.

### <a name="what-tools-support-using-rbac-for-data-operations"></a>Quais ferramentas de suportam usando o RBAC para operações de dados?

Para visualizar e trabalhar com operações de dados, você deve ter as versões corretas das ferramentas ou SDKs:

| Ferramenta  | Versão  |
|---------|---------|
| [PowerShell do Azure](/powershell/azure/install-azurerm-ps) | 5.6.0 ou posterior |
| [CLI do Azure](/cli/azure/install-azure-cli) | 2.0.30 ou posterior |
| [Azure para .NET](/dotnet/azure/) | 2.8.0-versão prévia ou posterior |
| [SDK do Azure para ir](/go/azure/azure-sdk-go-install) | 15.0.0 ou posterior |
| [Azure para Java](/java/azure/) | 1.9.0 ou posterior |
| [Azure para Python](/python/azure) | 0.40.0 ou posterior |
| [SDK do Azure para Ruby](https://rubygems.org/gems/azure_sdk) | 0.17.1 ou posterior |

## <a name="actions"></a>Ações

A permissão `actions` especifica as operações de gerenciamento às quais a função concede acesso. É uma coleção de cadeias de operação que identificam as operações protegíveis dos provedores de recursos do Azure. Aqui estão alguns exemplos de operações de gerenciamento que podem ser usadas no `actions`.

| Cadeia de caracteres da operação    | DESCRIÇÃO         |
| ------------------- | ------------------- |
| `*/read` | Concede acesso a operações de leitura a todos os tipos de recursos de todos os provedores de recursos do Azure.|
| `Microsoft.Compute/*` | Concede acesso a todas as operações a todos os tipos de recursos no provedor de recursos Microsoft.Compute.|
| `Microsoft.Network/*/read` | Concede acesso a operações de leitura a todos os tipos de recursos no provedor de recursos Microsoft.Network do Azure.|
| `Microsoft.Compute/virtualMachines/*` | Concede acesso a todas as operações de máquinas virtuais e seus tipos de recursos filho.|
| `microsoft.web/sites/restart/Action` | Concede acesso para reinicialização de um aplicativo Web.|

## <a name="notactions"></a>notActions

A permissão `notActions` especifica as operações de gerenciamento que são excluídas do `actions` permitido. Use a permissão `notActions` se o conjunto de operações que você deseja permitir fica definido mais facilmente excluindo as operações restritas. O acesso concedido por uma função (permissões efetivas) é calculado subtraindo as operações `notActions` das operações `actions`.

> [!NOTE]
> Se um usuário for atribuído a uma função que exclui uma operação em `notActions` e for atribuído a uma segunda função que concede acesso à mesma operação, ele terá permissão para executar essa operação. `notActions` não é uma regra de negação, é simplesmente uma maneira conveniente de criar um conjunto de operações permitidas quando for necessário excluir operações específicas.
>

## <a name="dataactions-preview"></a>dataActions (visualização)

O `dataActions` permissão especifica as operações de dados ao qual a função concede acesso aos seus dados dentro desse objeto. Por exemplo, se um usuário tem acesso de leitura blob dados para uma conta de armazenamento, eles podem ler blobs dentro dessa conta de armazenamento. Aqui estão alguns exemplos de operações de dados que podem ser usados em `dataActions`.

| Cadeia de caracteres da operação    | DESCRIÇÃO         |
| ------------------- | ------------------- |
| `Microsoft.Storage/storageAccounts/ blobServices/containers/blobs/read` | Retorna um blob ou uma lista de blobs. |
| `Microsoft.Storage/storageAccounts/ blobServices/containers/blobs/write` | Retorna o resultado de escrever um blob. |
| `Microsoft.Storage/storageAccounts/ queueServices/queues/messages/read` | Retorna uma mensagem. |
| `Microsoft.Storage/storageAccounts/ queueServices/queues/messages/*` | Retorna uma mensagem ou o resultado de gravar ou excluir uma mensagem. |

## <a name="notdataactions-preview"></a>notDataActions (visualização)

O `notDataActions` permissão especifica as operações de dados que são excluídas do permitidos `dataActions`. O acesso concedido por uma função (permissões efetivas) é calculado subtraindo as operações `notDataActions` das operações `dataActions`. Cada provedor de recursos fornece seu respectivo conjunto de APIs para atender as operações de dados.

> [!NOTE]
> Se um usuário é atribuído a uma função que exclui uma operação de dados em `notDataActions`e é atribuído a uma segunda função que concede acesso à mesma operação de dados, o usuário tem permissão para executar essa operação de dados. `notDataActions` não é uma negação de regra – é simplesmente uma maneira conveniente de criar um conjunto de operações de dados permitidos quando precisam de operações de dados específicos a serem excluídos.
>

## <a name="assignablescopes"></a>assignableScopes

A seção `assignableScopes` especifica os escopos (grupos de gerenciamento (atualmente na versão prévia), assinaturas, grupos de recursos ou recursos) em que a função está disponível para atribuição. Você pode disponibilizar a função para atribuição apenas nas assinaturas ou grupos de recursos que a exijam e não sobrecarregar a experiência do usuário nas assinaturas ou grupos de recursos restantes. Você deve usar pelo menos um grupo de gerenciamento, uma assinatura, um grupo de recursos ou uma ID de recurso.

As funções internas têm `assignableScopes` definido como o escopo raiz (`"/"`). O escopo raiz indica que a função está disponível para atribuição em todos os escopos. Você não pode usar o escopo raiz em suas próprias funções personalizadas. Se você tentar, receberá um erro de autorização.

Exemplos de escopos válidos que podem ser atribuídos incluem:

| Cenário | Exemplo |
|----------|---------|
| A função está disponível para atribuição em uma assinatura única | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"` |
| A função está disponível para atribuição em duas assinaturas | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e", "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"` |
| A função está disponível para atribuição apenas no grupo de recursos de rede | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network"` |
| A função está disponível para atribuição em todos os escopos | `"/"` |

## <a name="assignablescopes-and-custom-roles"></a>assignableScopes e funções personalizadas

A seção `assignableScopes` de uma função personalizada também controla quem pode criar, excluir, modificar ou exibir a função personalizada.

| Tarefa | Operação | DESCRIÇÃO |
| --- | --- | --- |
| Criar/excluir uma função personalizada | `Microsoft.Authorization/ roleDefinition/write` | Os usuários que recebem essa operação em todos os `assignableScopes` da função personalizada podem criar (ou excluir) funções personalizadas para uso nesses escopos. Por exemplo, os [Proprietários](built-in-roles.md#owner) e os [Administradores de Acesso do Usuário](built-in-roles.md#user-access-administrator) das assinaturas, grupos de recursos e recursos. |
| Modificar uma função personalizada | `Microsoft.Authorization/ roleDefinition/write` | Os usuários que recebem essa operação em todos os `assignableScopes` da função personalizada podem modificar as funções personalizadas nesses escopos. Por exemplo, os [Proprietários](built-in-roles.md#owner) e os [Administradores de Acesso do Usuário](built-in-roles.md#user-access-administrator) das assinaturas, grupos de recursos e recursos. |
| Exibir uma função personalizada | `Microsoft.Authorization/ roleDefinition/read` | Os usuários que recebem essa operação em um escopo podem exibir as funções personalizadas que estão disponíveis para atribuição nesse escopo. Todas as funções internas permitem que funções personalizadas estejam disponíveis para atribuição. |

## <a name="role-definition-examples"></a>Exemplos de definição de função

A exemplo a seguir mostra a definição da função [Leitor](built-in-roles.md#reader) conforme exibido usando a CLI do Azure:

```json
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you view everything, but not make any changes.",
    "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "name": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "permissions": [
      {
        "actions": [
          "*/read"
        ],
        "additionalProperties": {},
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "Reader",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

O exemplo a seguir mostra uma função personalizada para monitoramento e reinicialização de máquinas virtuais conforme mostrado usando o Azure PowerShell:

```json
{
  "Name":  "Virtual Machine Operator",
  "Id":  "88888888-8888-8888-8888-888888888888",
  "IsCustom":  true,
  "Description":  "Can monitor and restart virtual machines.",
  "Actions":  [
                  "Microsoft.Storage/*/read",
                  "Microsoft.Network/*/read",
                  "Microsoft.Compute/*/read",
                  "Microsoft.Compute/virtualMachines/start/action",
                  "Microsoft.Compute/virtualMachines/restart/action",
                  "Microsoft.Authorization/*/read",
                  "Microsoft.Resources/subscriptions/resourceGroups/read",
                  "Microsoft.Insights/alertRules/*",
                  "Microsoft.Insights/diagnosticSettings/*",
                  "Microsoft.Support/*"
  ],
  "NotActions":  [

                 ],
  "DataActions":  [

                  ],
  "NotDataActions":  [

                     ],
  "AssignableScopes":  [
                           "/subscriptions/{subscriptionId1}",
                           "/subscriptions/{subscriptionId2}",
                           "/subscriptions/{subscriptionId3}"
                       ]
}
```

## <a name="see-also"></a>Consulte também

* [Funções internas](built-in-roles.md)
* [Funções personalizadas](custom-roles.md)
* [Operações do provedor de recursos do Azure Resource Manager](resource-provider-operations.md)
