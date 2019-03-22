---
title: Use o API do Azure Stack | Microsoft Docs
description: Saiba como recuperar uma autenticação do Azure para fazer solicitações de API para o Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 1773083a5d02f3bb988ac3e5cef6528a5d49b94e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58100387"
---
<!--  cblackuk and charliejllewellyn. This is a community contribution by cblackuk-->

# <a name="use-the-azure-stack-api"></a>Use o API do Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Você pode usar a Interface de programação de aplicativo (API) para automatizar as operações como adicionar uma máquina virtual à nuvem do Azure Stack.

A API requer que seu cliente autenticar para o Microsoft Azure entrar no ponto de extremidade. O ponto de extremidade retorna um token a ser usado no cabeçalho de cada solicitação enviada para a API do Azure Stack. O Microsoft Azure usa Oauth 2.0.

Este artigo fornece exemplos que usam o **cURL** utilitário para criar solicitações de pilha do Azure. O aplicativo, cURL, é uma ferramenta de linha de comando com uma biblioteca para a transferência de dados. Esses exemplos percorrer o processo de recuperar um token para acessar a API do Azure Stack. A maioria das linguagens de programação fornecem bibliotecas de Oauth 2.0, que têm robustas tarefas de gerenciamento e o identificador de token tal uma atualização do token.

Examinar todo o processo de usar a API REST do Azure Stack com um cliente REST genérico, como **cURL**, para ajudar você a entender subjacente solicita e mostra o que você pode esperar receber em uma carga de resposta.

Este artigo não explorar todas as opções disponíveis para recuperar tokens como entrada interativa ou criação de IDs de aplicativo dedicado. Para obter informações sobre esses tópicos, consulte [referência da API REST do Azure](https://docs.microsoft.com/rest/api/).

## <a name="get-a-token-from-azure"></a>Obter um token do Azure

Crie um corpo de solicitação formatado usando o tipo de conteúdo x-www-form-urlencoded para obter um token de acesso. POSTE sua solicitação para o ponto de extremidade de autenticação de REST do Azure e faça logon.

### <a name="uri"></a>URI

```bash  
POST https://login.microsoftonline.com/{tenant id}/oauth2/token
```

**ID do locatário** é:

 - Seu domínio de locatário, como `fabrikam.onmicrosoft.com`
 - ID de locatário, como `8eaed023-2b34-4da1-9baa-8bc8c9d6a491`
 - Valor padrão para chaves independente de locatário: `common`

### <a name="post-body"></a>Corpo do POST

```bash  
grant_type=password
&client_id=1950a258-227b-4e31-a9cf-717495945fc2
&resource=https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155
&username=admin@fabrikam.onmicrosoft.com
&password=Password123
&scope=openid
```

Para cada valor:

- **grant_type**  
   O tipo de esquema de autenticação, você irá usar. Neste exemplo, o valor é `password`

- **recurso**  
   O recurso em que o token acessa. Você pode encontrar o recurso consultando o ponto de extremidade de metadados de gerenciamento do Azure Stack. Examine os **audiências** seção

- **O ponto de extremidade do Azure Stack gerenciamento**  
   ```
   https://management.{region}.{Azure Stack domain}/metadata/endpoints?api-version=2015-01-01
   ```

  > [!NOTE]  
  > Se você for um administrador tentar acessar a API de locatário, em seguida, certifique-se de usar o ponto de extremidade de locatário, por exemplo: `https://adminmanagement.{region}.{Azure Stack domain}/metadata/endpoints?api-version=2015-01-011`  

  Por exemplo, com o Kit de desenvolvimento do Azure Stack como um ponto de extremidade:

   ```bash
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

  Por exemplo, a conta do AAD do Azure Stack:

  ```
  azurestackadmin@fabrikam.onmicrosoft.com
  ```

  **password**

  A senha de administrador do AAD de pilha do Azure.

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

Depois de obter seu token de acesso, você precisará adicioná-lo como um cabeçalho para cada uma das suas solicitações de API. Para fazer isso, você precisa criar um cabeçalho **autorização** com valor: `Bearer <access token>`. Por exemplo: 

Solicitação:

```bash  
curl -H "Authorization: Bearer eyJ0eXAiOi...truncated for readability..." 'https://adminmanagement.local.azurestack.external/subscriptions?api-version=2016-05-01'
```

Resposta:

```bash  
offerId : /delegatedProviders/default/offers/92F30E5D-F163-4C58-8F02-F31CFE66C21B
id : /subscriptions/800c4168-3eb1-406b-a4ca-919fe7ee42e8
subscriptionId : 800c4168-3eb1-406b-a4ca-919fe7ee42e8
tenantId : 9fea4606-7c07-4518-9f3f-8de9c52ab628
displayName : Default Provider Subscription
state : Enabled
subscriptionPolicies : @{locationPlacementId=AzureStack}
```

### <a name="url-structure-and-query-syntax"></a>Sintaxe de estrutura e a consulta de URL

URI de solicitação genérico consiste em: {esquema de URI} :// {host URI} / {resource-path}? {query-string}

- **Esquema de URI**:  
O URI indica o protocolo usado para enviar a solicitação. Por exemplo, `http` ou `https`.
- **Host de URI**:  
O host Especifica o nome de domínio ou endereço IP do servidor onde o ponto de extremidade de serviço REST está hospedado, como `graph.microsoft.com` ou `adminmanagement.local.azurestack.external`.
- **Caminho do recurso**:  
O caminho Especifica o recurso ou coleção de recursos, que pode incluir vários segmentos usados pelo serviço para determinar a seleção desses recursos. Por exemplo: `beta/applications/00003f25-7e1f-4278-9488-efc7bac53c4a/owners` pode ser usado para consultar a lista de proprietários de um aplicativo específico dentro da coleção de aplicativos.
- **Cadeia de caracteres de consulta**:  
A cadeia de caracteres fornece parâmetros adicionais simples, como os critérios de seleção de recurso ou a versão de API.

## <a name="azure-stack-request-uri-construct"></a>Construção URI de solicitação de pilha do Azure

```
{URI-scheme} :// {URI-host} / {subscription id} / {resource group} / {provider} / {resource-path} ? {OPTIONAL: filter-expression} {MANDATORY: api-version}
```

### <a name="uri-syntax"></a>Sintaxe URI

```
https://adminmanagement.local.azurestack.external/{subscription id}/resourcegroups/{resource group}/providers/{provider}/{resource-path}?{api-version}
```

### <a name="query-uri-example"></a>Exemplo de URI de consulta

```
https://adminmanagement.local.azurestack.external/subscriptions/800c4168-3eb1-406b-a4ca-919fe7ee42e8/resourcegroups/system.local/providers/microsoft.infrastructureinsights.admin/regionhealths/local/Alerts?$filter=(Properties/State eq 'Active') and (Properties/Severity eq 'Critical')&$orderby=Properties/CreatedTimestamp desc&api-version=2016-05-01"
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como usar os pontos de extremidade RESTful do Azure, consulte [referência da API REST do Azure](https://docs.microsoft.com/rest/api/).
