---
title: Use a API da pilha do Azure | Microsoft Docs
description: Saiba como recuperar uma autenticação do Azure para fazer solicitações de API para a pilha do Azure.
services: azure-stack
documentationcenter: ''
author: cblackuk
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: 2bbfe4f829ad5c42a5742fdf08f2d185af627f42
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/11/2018
---
<!--  cblackuk and charliejllewellyn -->

# <a name="use-the-azure-stack-api"></a>Use a API da pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Você pode usar a API de pilha do Azure para automatizar operações como distribuir itens do marketplace.

Usando a API requer que seu cliente autenticar o ponto de extremidade de logon do Microsoft Azure. O ponto de extremidade retorna um token a ser usado no cabeçalho de cada solicitação enviada para a API de pilha do Azure. (Microsoft Azure usa Oauth 2.0.)

Este artigo fornece exemplos que usam o utilitário de rotação para criar solicitações de pilha do Azure. Esses exemplos percorrer o processo de recuperar um token para acessar a API de pilha do Azure. A maioria das linguagens de programação fornecem bibliotecas Oauth 2.0, que têm robustas tarefas de gerenciamento e o identificador de token tais o token de atualização.

Examinar todo o processo de usar a API de REST de pilha do Azure com um cliente REST genérico como rotação pode ajudá-lo a entender subjacente solicitações e mostra o que você pode esperar receber em uma carga de resposta.

Este artigo não explorar todas as opções disponíveis para recuperar tokens, como o logon interativo ou criação de IDs de aplicativo dedicado. Para obter mais informações, consulte [referência da API REST do Azure](https://docs.microsoft.com/rest/api/).

## <a name="get-a-token-from-azure"></a>Obter um token do Azure

Criar uma solicitação de *corpo* formatado usando o tipo de conteúdo x-www-form-urlencoded para obter um token de acesso. POSTE sua solicitação para o ponto de extremidade de autenticação de REST do Azure e de logon.

```
POST https://login.microsoftonline.com/{tenant id}/oauth2/token
```

**ID do locatário** é:

* Seu domínio de locatário, como fabrikam.onmicrosoft.com
* Sua ID de locatário, como 8eaed023-2b34-4da1-9baa-8bc8c9d6a491
* Valor padrão para chaves independente de locatário: comuns

### <a name="post-body"></a>Corpo do POST

```
grant_type=password
&client_id=1950a258-227b-4e31-a9cf-717495945fc2
&resource=https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155
&username=admin@fabrikam.onmicrosoft.com
&password=Password123
&scope=openid
```

Para cada valor:

  **grant_type**

  O tipo de esquema de autenticação que você irá usar. Neste exemplo, o valor é:

  ```
  password
  ```

  **recurso**

  O recurso acesse o token. Você pode encontrar o recurso consultando o ponto de extremidade de metadados de gerenciamento de pilha do Azure. Examine o **audiências** seção

  O ponto de extremidade de gerenciamento da pilha do Azure:

  ```
  https://management.{region}.{Azure Stack domain}/metadata/endpoints?api-version=2015-01-01
  ```

 > [!NOTE]
 > Se você for um administrador tentar acessar a API de locatário, você deve certificar-se de usar o ponto de extremidade de locatário, por exemplo: 'https://adminmanagement.{region}.{Azure domínio pilha} metadados/pontos de extremidade? api-version = 2015-01-011

  Por exemplo, com o Kit de desenvolvimento de pilha do Azure como um ponto de extremidade:

  ```
  curl 'https://management.local.azurestack.external/metadata/endpoints?api-version=2015-01-01'
  ```

  Resposta:

  ```
  {
  "galleryEndpoint":"https://adminportal.local.azurestack.external:30015/",
  "graphEndpoint":"https://graph.windows.net/",
  "portalEndpoint":"https://adminportal.local.azurestack.external/",
  "authentication":{
      "loginEndpoint":"https://login.windows.net/",
      "audiences":["https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155"]
      }
  }
  ```

### <a name="example"></a>Exemplo

  ```
  https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155
  ```

  **client_id**

  Esse valor é codificado como um valor padrão:

  ```
  1950a258-227b-4e31-a9cf-717495945fc2
  ```

  Opções alternativas estão disponíveis para cenários específicos:

  
  | Aplicativo | ApplicationID |
  | --------------------------------------- |:-------------------------------------------------------------:|
  | LegacyPowerShell | 0a7bdc5c-7b57-40be-9939-d4c5fc7cd417 |
  | PowerShell | 1950a258-227b-4e31-a9cf-717495945fc2 |
  | WindowsAzureActiveDirectory | 00000002-0000-0000-c000-000000000000 |
  | VisualStudio | 872cd9fa-d31f-45e0-9eab-6e460a02d1f1 |
  | AzureCLI | 04b07795-8ddb-461a-bbee-02f9e1bf7b46 |

  **username**

  Por exemplo, a conta do Azure AAD de pilha:

  ```
  azurestackadmin@fabrikam.onmicrosoft.com
  ```

  **password**

  A senha do administrador AAD de pilha do Azure.

### <a name="example"></a>Exemplo

Solicitação:

```
curl -X "POST" "https://login.windows.net/fabrikam.onmicrosoft.com/oauth2/token" \
-H "Content-Type: application/x-www-form-urlencoded" \
--data-urlencode "client_id=1950a258-227b-4e31-a9cf-717495945fc2" \
--data-urlencode "grant_type=password" \
--data-urlencode "username=admin@fabrikam.onmicrosoft.com" \
--data-urlencode 'password=Password12345' \
--data-urlencode "resource=https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155"
```

Resposta:

```
{
  "token_type": "Bearer",
  "scope": "user_impersonation",
  "expires_in": "3599",
  "ext_expires_in": "0",
  "expires_on": "1512574780",
  "not_before": "1512570880",
  "resource": "https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155",
  "access_token": "eyJ0eXAiOi...truncated for readability..."
}
```

## <a name="api-queries"></a>Consultas de API

Depois de obter o token de acesso, você precisa adicioná-lo como um cabeçalho para cada uma das suas solicitações de API. Para fazer isso, você precisa criar um cabeçalho **autorização** com valor: `Bearer <access token>`. Por exemplo: 

Solicitação:

```
curl -H "Authorization: Bearer eyJ0eXAiOi...truncated for readability..." 'https://adminmanagement.local.azurestack.external/subscriptions?api-version=2016-05-01'
```

Resposta:

```
offerId : /delegatedProviders/default/offers/92F30E5D-F163-4C58-8F02-F31CFE66C21B
id : /subscriptions/800c4168-3eb1-406b-a4ca-919fe7ee42e8
subscriptionId : 800c4168-3eb1-406b-a4ca-919fe7ee42e8
tenantId : 9fea4606-7c07-4518-9f3f-8de9c52ab628
displayName : Default Provider Subscription
state : Enabled
subscriptionPolicies : @{locationPlacementId=AzureStack}
```

### <a name="url-structure-and-query-syntax"></a>Sintaxe de estrutura e a consulta de URL

URI de solicitação genérico consiste em: {esquema URI} :// {URI-host} / {resource-path}? {query-string}

- **Esquema de URI**:  
O URI indica o protocolo usado para enviar a solicitação. Por exemplo, `http` ou `https`.
- **Host URI**:  
O host Especifica o nome de domínio ou endereço IP do servidor onde o ponto de extremidade do serviço REST está hospedado, como `graph.microsoft.com` ou `adminmanagement.local.azurestack.external`.
- **Caminho do recurso**:  
O caminho Especifica o recurso ou a coleção de recursos, que pode incluir vários segmentos usados pelo serviço para determinar a seleção desses recursos. Por exemplo: `beta/applications/00003f25-7e1f-4278-9488-efc7bac53c4a/owners` pode ser usado para consultar a lista de proprietários de um aplicativo específico dentro da coleção de aplicativos.
- **Cadeia de caracteres de consulta**:  
A cadeia de caracteres fornece parâmetros adicionais de simples, como os critérios de seleção de recursos ou de versão de API.

## <a name="azure-stack-request-uri-construct"></a>Construção URI de solicitação de pilha do Azure

```
{URI-scheme} :// {URI-host} / {subscription id} / {resource group} / {provider} / {resource-path} ? {OPTIONAL: filter-expression} {MANDATORY: api-version}
```

### <a name="uri-syntax"></a>Sintaxe URI

```
https://adminmanagement.local.azurestack.external/{subscription id}/resourcegroups/{resource group}/providers/{provider}/{resource-path}?{api-version}
```

### <a name="query-uri-example"></a>Exemplo de consulta de URI

```
https://adminmanagement.local.azurestack.external/subscriptions/800c4168-3eb1-406b-a4ca-919fe7ee42e8/resourcegroups/system.local/providers/microsoft.infrastructureinsights.admin/regionhealths/local/Alerts?$filter=(Properties/State eq 'Active') and (Properties/Severity eq 'Critical')&$orderby=Properties/CreatedTimestamp desc&api-version=2016-05-01"
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como usar os pontos de extremidade RESTful do Azure, consulte [referência da API REST do Azure](https://docs.microsoft.com/rest/api/).
