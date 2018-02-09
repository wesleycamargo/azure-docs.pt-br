---
title: "Como usar uma Identidade de Serviço Gerenciado atribuída pelo usuário para adquirir um token de acesso em uma VM."
description: "Instruções e exemplos passo a passo para usar uma MSI atribuída pelo usuário de uma VM do Azure para adquirir um token de acesso OAuth."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: a9513a59ec4540c6d63236519873c6e1e177b65a
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2018
---
# <a name="acquire-an-access-token-for-a-vm-user-assigned-managed-service-identity-msi"></a>Adquirir um token de acesso para uma MSI (Identidade de Serviço Gerenciado) de VM atribuída pelo usuário

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]
Este artigo fornece vários exemplos de código e de script para aquisição de token, assim como diretrizes sobre tópicos importantes como a manipulação de expiração de token e erros HTTP.

## <a name="prerequisites"></a>pré-requisitos

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

Se você planeja usar os exemplos do Azure PowerShell neste artigo, instale a versão mais recente do [Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM).

> [!IMPORTANT]
> - Todo código/script de exemplo neste artigo supõe que o cliente esteja sendo executado em uma Máquina Virtual habilitada para MSI. Use o recurso "Conectar" da VM no Portal do Azure para conectar-se remotamente à sua VM. Para obter detalhes sobre como habilitar a MSI em uma VM, consulte [Configurar uma MSI (Identidade de Serviço Gerenciado) atribuída pelo usuário para uma VM usando a CLI do Azure](msi-qs-configure-cli-windows-vm.md) ou um dos artigos variantes (usando o Portal, o PowerShell, um modelo ou um SDK do Azure). 

## <a name="overview"></a>Visão geral

Um aplicativo cliente pode solicitar um [token de acesso somente de aplicativo](~/articles/active-directory/develop/active-directory-dev-glossary.md#access-token) da MSI para acessar um determinado recurso. O token é [com base na entidade de serviço da MSI](msi-overview.md#how-does-it-work). Sendo assim, o cliente não precisa se registrar para obter um token de acesso em sua própria entidade de serviço. O token é adequado para uso como um token de portador em [chamadas de serviço a serviço que exigem credenciais de cliente](~/articles/active-directory/active-directory-protocols-oauth-service-to-service.md).

|  |  |
| -------------- | -------------------- |
| [Obter um token usando HTTP](#get-a-token-using-http) | Detalhes de protocolo para o ponto de extremidade do token da MSI |
| [Obter um token usando CURL](#get-a-token-using-curl) | Exemplo de como usar o ponto de extremidade REST da MSI de um cliente Bash/CURL |
| [Manipulando a expiração do token](#handling-token-expiration) | Diretrizes para manipular tokens de acesso expirados |
| [Tratamento de erros](#error-handling) | Diretrizes para tratamento de erros HTTP retornados do ponto de extremidade do token da MSI |
| [IDs de recurso para serviços do Azure](#resource-ids-for-azure-services) | Onde obter IDs de recurso para os serviços do Azure compatíveis |

## <a name="get-a-token-using-http"></a>Obter um token usando HTTP 

A interface fundamental para adquirir um token de acesso é baseada em REST, tornando-a acessível para qualquer aplicativo cliente em execução na VM que pode fazer chamadas REST HTTP. Isso é semelhante ao modelo de programação do Azure AD, exceto que o cliente usa um ponto de extremidade de localhost na máquina virtual (vs. um ponto de extremidade do Azure AD).

Solicitação de exemplo:

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F&client_id=712eac09-e943-418c-9be6-9fd5c91078bl HTTP/1.1
Metadata: true
```

| Elemento | DESCRIÇÃO |
| ------- | ----------- |
| `GET` | O verbo HTTP, indicando que você deseja recuperar os dados do ponto de extremidade. Neste caso, um token de acesso OAuth. | 
| `http://localhost:50342/oauth2/token` | O ponto de extremidade da MSI em que 50342 é a porta padrão e é configurável. |
| `resource` | Um parâmetro de cadeia de caracteres de consulta que indica o URI da ID do aplicativo do recurso de destino. Ele também aparece na declaração `aud` (público) do token emitido. Este exemplo solicita um token para acessar o Azure Resource Manager, que tem um URI de ID do aplicativo de https://management.azure.com/. |
| `client_id` | Um parâmetro de cadeia de caracteres de consulta, que indica a ID de cliente (também conhecida como ID do aplicativo) da entidade de serviço que representa a MSI atribuída pelo usuário. Esse valor é retornado na propriedade `clientId` durante a criação de uma MSI atribuída pelo usuário. Este exemplo solicita um token para a ID de cliente "712eac09-e943-418c-9be6-9fd5c91078bl". |
| `Metadata` | Um campo de cabeçalho de solicitação HTTP, exigido pela MSI como uma mitigação contra ataques de SSRF (Falsificação de Solicitação no Lado de Servidor). Esse valor deve ser definido como "true", com todas as letras minúsculas.

Exemplo de resposta:

```
HTTP/1.1 200 OK
Content-Type: application/json
{
  "access_token": "eyJ0eXAi...",
  "expires_in": "3599",
  "expires_on": "1506484173",
  "not_before": "1506480273",
  "resource": "https://management.azure.com/",
  "token_type": "Bearer"
  "client_id":"712eac09-e943-418c-9be6-9fd5c91078bl"
}
```

| Elemento | DESCRIÇÃO |
| ------- | ----------- |
| `access_token` | O token de acesso solicitado. Ao chamar uma API REST protegida, o token é inserido no campo de cabeçalho de solicitação `Authorization` como um token "portador", permitindo que a API autentique o chamador. | 
| `expires_in` | O número de segundos que o token de acesso continua sendo válido antes da expiração desde o momento da emissão. A hora da emissão pode ser encontrada na declaração `iat` do token. |
| `expires_on` | O período de expiração do token de acesso. A data é representada como o número de segundos de “1970-01-01T0:0:0Z UTC” (corresponde à declaração `exp` do token). |
| `not_before` | O período para o token de acesso entrar em vigor e poder ser aceito. A data é representada como o número de segundos de “1970-01-01T0:0:0Z UTC” (corresponde à declaração `nbf` do token). |
| `resource` | O recurso para o qual o token de acesso foi solicitado, que corresponde ao parâmetro de cadeia de consulta `resource` da solicitação. |
| `token_type` | O tipo de token, que é um token de acesso "Portador", o que significa que o recurso pode conceder acesso ao portador desse token. |
| `client_id` | A ID de cliente (também conhecida como ID do aplicativo) da entidade de serviço que representa a MSI atribuída pelo usuário, para a qual o token foi solicitado. |

## <a name="get-a-token-using-curl"></a>Obter um token usando CURL

Substitua a ID de cliente (também conhecida como ID do aplicativo) da entidade de serviço da MSI atribuída pelo usuário, pelo valor <MSI CLIENT ID> do parâmetro `client_id`. Esse valor é retornado na propriedade `clientId` durante a criação de uma MSI atribuída pelo usuário.

   ```bash
   response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/&client_id=<MSI CLIENT ID>" -H Metadata:true -s)
   access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
   echo The MSI access token is $access_token
   ```

   Respostas de exemplo:

   ```bash
   user@vmLinux:~$ response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/&client_id=9d484c98-b99d-420e-939c-z585174b63bl" -H Metadata:true -s)
   user@vmLinux:~$ access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
   user@vmLinux:~$ echo The MSI access token is $access_token
   The MSI access token is eyJ0eXAiOiJKV1QiLCJhbGciO...
   ```

## <a name="handling-token-expiration"></a>Manipulando a expiração do token

O subsistema de MSI local armazena em cache os tokens. Portanto, você pode chamá-lo sempre que desejar e uma chamada durante a transmissão para resultados do Azure AD somente se:
- ocorrer um cache ignorado devido a não haver token no cache
- o token está expirado

Se você armazenar em cache o token em seu código, deverá estar preparado para lidar com cenários em que o recurso indique que o token expirou.

## <a name="error-handling"></a>Tratamento de erros 

O ponto de extremidade da MSI sinaliza os erros por meio do campo de código de status do cabeçalho da mensagem de resposta HTTP, como erros 4xx ou 5xx:

| Código de status | Motivo do erro | Como tratar |
| ----------- | ------------ | ------------- |
| o erro 4xx na solicitação. | Um ou mais parâmetros de solicitação estava incorreto. | Não tente novamente.  Examine os detalhes do erro para obter mais informações.  Os erros 4xx são erros de tempo de design.|
| Erro 5xx transitório do serviço. | O subsistema da MSI ou o Azure Active Directory retornou um erro transitório. | É seguro tentar novamente após aguardar pelo menos 1 segundo.  Se tentar novamente muito rápido ou com muita frequência, o Azure AD poderá retornar um erro de limite de taxa (429).|

Se ocorrer um erro, o corpo da resposta HTTP correspondente conterá o JSON com os detalhes do erro:

| Elemento | DESCRIÇÃO |
| ------- | ----------- |
| error   | Identificador do erro. |
| error_description | Descrição detalhada do erro. **Descrições de erro podem ser alteradas a qualquer momento. Não escreva código que se ramifique com base nos valores na descrição do erro.**|

### <a name="http-response-reference"></a>Referência de resposta HTTP

Esta seção documenta as possíveis respostas de erro. Um status "200 OK" é uma resposta bem-sucedida, e o token de acesso está contido no JSON do corpo de resposta, no elemento access_token.

| Código de status | Erro | Descrição do erro | Solução |
| ----------- | ----- | ----------------- | -------- |
| 400 Solicitação Inválida | invalid_resource | AADSTS50001: o aplicativo chamado *\<URI\>* não foi encontrado no locatário chamado *\<TENANT-ID\>*. Isso poderá acontecer se o aplicativo não tiver sido instalado pelo administrador do locatário ou aceito por qualquer usuário no locatário. Talvez você tenha enviado a solicitação de autenticação ao locatário errado.\ | (Apenas Linux) |
| 400 Solicitação Inválida | bad_request_102 | Cabeçalho de metadados necessário não especificado | O campo de cabeçalho de solicitação `Metadata` está ausente da solicitação ou está formatado incorretamente. O valor deve ser especificado como `true`, com todas as letras minúsculas. Consulte o "Exemplo de solicitação" na seção [Obter um token usando HTTP](#get-a-token-using-http) para obter um exemplo.|
| 401 Não Autorizado | unknown_source | *\<URI\>* de origem desconhecida | Verifique se o URI da solicitação HTTP GET está formatado corretamente. A parte `scheme:host/resource-path` deve ser especificada como `http://localhost:50342/oauth2/token`. Consulte o "Exemplo de solicitação" na seção [Obter um token usando HTTP](#get-a-token-using-http) para obter um exemplo.|
|           | invalid_request | A solicitação não tem um parâmetro obrigatório, inclui um valor de parâmetro inválido, inclui um parâmetro mais de uma vez ou está malformada. |  |
|           | unauthorized_client | O cliente não está autorizado a solicitar um token de acesso usando este método. | Causado por uma solicitação que não usou o loopback local para chamar a extensão ou em uma VM que não tem uma MSI configurada corretamente. Consulte [Configure a VM Managed Service Identity (MSI) using the Azure portal](msi-qs-configure-portal-windows-vm.md) (Configurar uma MSI [Identidade de Serviço Gerenciado] da VM usando o Portal do Azure) se precisar de ajuda com a configuração da VM. |
|           | access_denied | O proprietário do recurso ou o servidor de autorização negou a solicitação. |  |
|           | unsupported_response_type | O servidor de autorização não dá suporte à obtenção de um token de acesso usando este método. |  |
|           | invalid_scope | O escopo solicitado é inválido, desconhecido ou malformado. |  |
| Erro interno do servidor 500 | unknown | Falha ao recuperar o token do Active Directory. Para obter detalhes, consulte os logs no *\<caminho do arquivo\>* | Verifique se a MSI foi habilitada na VM. Consulte [Configure a VM Managed Service Identity (MSI) using the Azure portal](msi-qs-configure-portal-windows-vm.md) (Configurar uma MSI [Identidade de Serviço Gerenciado] da VM usando o Portal do Azure) se precisar de ajuda com a configuração da VM.<br><br>Verifique também se seu URI de solicitação GET HTTP foi formatado corretamente, principalmente o URI do recurso especificado na cadeia de caracteres de consulta. Consulte a "Solicitação de exemplo" na seção [Obter um token usando HTTP](#get-a-token-using-http) para obter um exemplo ou [Serviços do Azure que são compatíveis com a autenticação do Azure AD](msi-overview.md#azure-services-that-support-azure-ad-authentication) para obter uma lista de serviços e suas respectivas IDs de recurso.

## <a name="resource-ids-for-azure-services"></a>IDs de recurso para serviços do Azure

Consulte [Azure services that support Azure AD authentication](msi-overview.md#azure-services-that-support-azure-ad-authentication) (Serviços do Azure que dão suporte à autenticação do Azure AD) para obter uma lista dos recursos compatíveis com o Azure AD e que foram testados com a MSI e as respectivas IDs do recurso.


## <a name="next-steps"></a>Próximas etapas

- Para habilitar a MSI em uma VM do Azure, confira [Configurar uma MSI (Identidade de Serviço Gerenciado) atribuída pelo usuário para uma VM usando a CLI do Azure](msi-qs-configure-cli-windows-vm.md).

Use a seção de comentários a seguir para fornecer seus comentários e nos ajudar a aprimorar e adaptar nosso conteúdo.








