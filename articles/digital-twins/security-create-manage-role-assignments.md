---
title: Entenda a conectividade e autenticação de dispositivos dos Gêmeos Digitais do Azure | Microsoft Docs
description: Use os Gêmeos Digitais do Azure para conectar e autenticar dispositivos
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: lyrana
ms.openlocfilehash: f032e3ebf6a10411057cd6d41df0cad6248f328b
ms.sourcegitcommit: 542964c196a08b83dd18efe2e0cbfb21a34558aa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51636230"
---
# <a name="create-and-manage-role-assignments"></a>Criar e gerenciar atribuições de função

Os Gêmeos Digitais do Azure usam [RBAC](./security-role-based-access-control.md) (controle de acesso baseado em função) para gerenciar o acesso aos recursos.

Cada atribuição de função inclui:

* **Identificador de objeto**: uma ID do Active Directory do Azure, ID de objeto de entidade de serviço ou nome de domínio
* **Tipo de identificador de objeto**
* **ID de definição de função**
* **Caminho de espaço**
* **ID do locatário**: na maioria dos casos, um Azure Active Directory ID do locatário

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

## <a name="role-definition-identifiers"></a>Identificadores de definição de função

A tabela a seguir mostra o que pode ser obtido consultando a API system / roles.

| **Função** | **Identificador** |
| --- | --- |
| Administrador de Espaço | 98e44ad7-28d4-4007-853b-b9968ad132d1 |
| Administrador de usuários| dfaac54c-f583-4dd2-b45d-8d4bbc0aa1ac |
| Administrador de Dispositivo | 3cdfde07-bc16-40d9-bed3-66d49a8f52ae |
| Administrador de Chave | 5a0b1afc-e118-4068-969f-b50efb8e5da6 |
| Administrador de Token | 38a3bb21-5424-43b4-b0bf-78ee228840c3 |
| Usuário | b1ffdb77-c635-4e7e-ad25-948237d85b30 |
| Especialista de Suporte | 6e46958b-dc62-4e7c-990c-c3da2e030969 |
| Instalador do Dispositivo | b16dd9fe-4efe-467b-8c8c-720e2ff8817c |
| Dispositivos de gateway | d4c69766-e9bd-4e61-bfc1-d8b6e686c7a8 |

## <a name="supported-objectidtypes"></a>ObjectIdTypes compatíveis

Com o suporte `ObjectIdTypes`:

* `UserId`
* `DeviceId`
* `DomainName`
* `TenantId`
* `ServicePrincipalId`
* `UserDefinedFunctionId`

## <a name="create-a-role-assignment"></a>Criar uma atribuição de função

```plaintext
HTTP POST YOUR_MANAGEMENT_API_URL/roleassignments
```

| **Nome** | **Obrigatório** | **Tipo** | **Descrição** |
| --- | --- | --- | --- |
| roleId| SIM |Cadeia de caracteres | O identificador de definição de função. Encontre definições de função e seus identificadores consultando a API do sistema. |
| ID do objeto | SIM |Cadeia de caracteres | A ID de objeto para a atribuição de função precisa ser formatada de acordo com seu tipo associado. Para o `DomainName` ObjectIdType, ObjectId precisa começar com o caractere `“@”`. |
| objectIdType | SIM |Cadeia de caracteres | O tipo da atribuição de função. Precisa ser uma das seguintes linhas nesta tabela. |
| tenantId | Varia | Cadeia de caracteres |O identificador de locatário. Não permitido para ObjectIdTypes `DeviceId` e `TenantId`. Obrigatório para ObjectIdTypes `UserId` e `ServicePrincipalId`. Opcional para o ObjectIdType DomainName. |
| path* | SIM | Cadeia de caracteres |O caminho de acesso completo para o objeto `Space`. Um exemplo é `/{Guid}/{Guid}`. Se um identificador precisar da atribuição de função para todo o gráfico, especifique `"/"`. Esse caractere designa a raiz, mas seu uso é desencorajado. Sempre siga o princípio de privilégios mínimos. |

## <a name="sample-configuration"></a>Exemplo de configuração

Neste exemplo, um usuário precisa de acesso administrativo a um andar de um espaço de locatário.

  ```JSON
    {
      "RoleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
      "ObjectId" : " 0fc863bb-eb51-4704-a312-7d635d70e599",
      "ObjectIdType" : "UserId",
      "TenantId": " a0c20ae6-e830-4c60-993d-a91ce6032724",
      "Path": "/ 091e349c-c0ea-43d4-93cf-6b57abd23a44/ d84e82e6-84d5-45a4-bd9d-006a118e3bab"
    }
  ```

Neste exemplo, um aplicativo executa cenários de teste que zombam de dispositivos e sensores.

  ```JSON
    {
      "RoleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
      "ObjectId" : "cabf7acd-af0b-41c5-959a-ce2f4c26565b",
      "ObjectIdType" : "ServicePrincipalId",
      "TenantId": " a0c20ae6-e830-4c60-993d-a91ce6032724",
      "Path": "/"
    }
  ```

Todos os usuários que fazem parte de um domínio recebem acesso de leitura para espaços, sensores e usuários. Esse acesso inclui seus objetos relacionados correspondentes.

  ```JSON
    {
      "RoleId": " b1ffdb77-c635-4e7e-ad25-948237d85b30",
      "ObjectId" : "@microsoft.com",
      "ObjectIdType" : "DomainName",
      "Path": "/091e349c-c0ea-43d4-93cf-6b57abd23a44"
    }
  ```

Use GET para obter uma atribuição de função.

```plaintext
HTTP GET YOUR_MANAGEMENT_API_URL/roleassignments?path=YOUR_PATH
```

| **Nome** | **In** | **Obrigatório** |    **Tipo** |  **Descrição** |
| --- | --- | --- | --- | --- |
| YOUR_PATH | Caminho | True | Cadeia de caracteres |    O caminho completo para o espaço |

Use DELETE para excluir uma atribuição de função.

```plaintext
HTTP DELETE YOUR_MANAGEMENT_API_URL/roleassignments/YOUR_ROLE_ID
```

| **Nome** | **In** | **Obrigatório** | **Tipo** | **Descrição** |
| --- | --- | --- | --- | --- |
| YOUR_ROLE_ID | Caminho | True | Cadeia de caracteres | ID de atribuição de função |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a segurança de Gêmeos Digitais do Azure, leia [Autenticação de API](./security-authenticating-apis.md).
