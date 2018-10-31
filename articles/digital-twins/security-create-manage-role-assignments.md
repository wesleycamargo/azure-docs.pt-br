---
title: Noções básicas sobre a autenticação e conectividade de dispositivos Gêmeos Digitais do Azure | Microsoft Docs
description: Usando os Gêmeos Digitais do Azure para se conectar e autenticar dispositivos
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: lyrana
ms.openlocfilehash: adfb4c369ea1b324da8562a5b0b245ebdecff602
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323658"
---
# <a name="create-and-manage-role-assignments"></a>Criar e gerenciar atribuições de função

Os Gêmeos Digitais do Azure usam [RBAC](./security-role-based-access-control.md) (controle de acesso baseado em função) para gerenciar o acesso aos recursos.

Cada atribuição de função inclui:

* Um **identificador de objeto** (uma ID do Azure Active Directory, ID de objeto de entidade de serviço ou nome de domínio).
* Um **tipo de identificador de objeto**.
* Uma **ID de definição de função**.
* Um **caminho de espaço**.
* (Na maioria dos casos) uma **ID de locatário** do Azure Active Directory.

## <a name="role-definition-identifiers"></a>Identificadores de definição de função

A tabela a seguir mostra o que pode ser obtido consultando a API do Sistema/Funções:

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
| GatewayDevice | d4c69766-e9bd-4e61-bfc1-d8b6e686c7a8 |

## <a name="supported-objectidtypes"></a>ObjectIdTypes compatíveis

Os `ObjectIdTypes` compatíveis são:

* `UserId`
* `DeviceId`
* `DomainName`
* `TenantId`
* `ServicePrincipalId`
* `UserDefinedFunctionId`

## <a name="create-a-role-assignment"></a>Criar uma atribuição de função

```plaintext
HTTP POST /api/v1.0/roleassignments
```

| **Nome** | **Obrigatório** | **Tipo** | **Descrição** |
| --- | --- | --- | --- |
| roleId| SIM |string | O identificador de definição de função. Definições de função e seus identificadores podem ser encontradas ao consultar a API do sistema. |
| ID do objeto | SIM |string | A ID de objeto para a atribuição de função precisa ser formatada de acordo com seu tipo associado. Para o `DomainName` ObjectIdType, ObjectId precisa começar com o caractere `“@”`. |
| objectIdType | SIM |string | O tipo da atribuição de função. Precisa ser uma das seguintes linhas nesta tabela. |
| tenantId | Varia | string |O identificador de locatário. Não permitido para ObjectIdTypes `DeviceId` e `TenantId`. Obrigatório para ObjectIdTypes `UserId` e `ServicePrincipalId`. Opcional para o ObjectIdType DomainName. |
| path* | SIM | string |O caminho de acesso completo para o objeto `Space`. Por exemplo: `/{Guid}/{Guid}` se um identificador precisar de uma atribuição de função para todo o gráfico, especifique `"/"` (que designa a raiz). No entanto, não é recomendável usar isso, e **você sempre precisa seguir o princípio de privilégios mínimos**. |

## <a name="sample-configuration"></a>Exemplo de configuração

Um usuário precisa de acesso administrativo a um andar de um espaço de locatário:

  ```JSON
    {
      "RoleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
      "ObjectId" : " 0fc863bb-eb51-4704-a312-7d635d70e599",
      "ObjectIdType" : "UserId",
      "TenantId": " a0c20ae6-e830-4c60-993d-a91ce6032724",
      "Path": "/ 091e349c-c0ea-43d4-93cf-6b57abd23a44/ d84e82e6-84d5-45a4-bd9d-006a118e3bab"
    }
  ```

Um aplicativo que executa cenários de teste simula dispositivos e sensores:

  ```JSON
    {
      "RoleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
      "ObjectId" : "cabf7acd-af0b-41c5-959a-ce2f4c26565b",
      "ObjectIdType" : "ServicePrincipalId",
      "TenantId": " a0c20ae6-e830-4c60-993d-a91ce6032724",
      "Path": "/"
    }
  ```

Todos os usuários que fazem parte de um domínio receberão acesso de leitura aos espaços, sensores e usuários, incluindo seus objetos relacionados correspondentes:

  ```JSON
    {
      "RoleId": " b1ffdb77-c635-4e7e-ad25-948237d85b30",
      "ObjectId" : "@microsoft.com",
      "ObjectIdType" : "DomainName",
      "Path": "/091e349c-c0ea-43d4-93cf-6b57abd23a44"
    }
  ```

Para realizar GET em uma atribuição de função:

```plaintext
HTTP GET /api/v1/roleassignments?path={path}
```

| **Nome** | **In** | **Obrigatório** |    **Tipo** |  **Descrição** |
| --- | --- | --- | --- | --- |
| Caminho | Caminho | True | Cadeia de caracteres | O caminho completo para o espaço |

Para EXCLUIR uma atribuição de função:

```plaintext
HTTP DELETE /api/v1/roleassignments/{id}
```

| **Nome** | **In** | **Obrigatório** | **Tipo** | **Descrição** |
| --- | --- | --- | --- | --- |
| ID | Caminho | True | Cadeia de caracteres |   ID de atribuição de função |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a segurança de Gêmeos Digitais do Azure, leia [Autenticação de API](./security-authenticating-apis.md).
