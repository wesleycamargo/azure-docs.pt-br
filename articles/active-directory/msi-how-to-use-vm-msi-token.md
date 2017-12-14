---
title: "Como usar uma Identidade de Serviço Gerenciado da VM do Azure para adquiri um token de acesso"
description: "Instruções e exemplos passo a passo para usar uma MSI de VM do Azure para adquirir um token de acesso OAuth."
services: active-directory
documentationcenter: 
author: bryanla
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: bryanla
ms.openlocfilehash: 9d80e0e4dbaa010aabd0e7aad91ac79cf2d433d5
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2017
---
# <a name="how-to-use-an-azure-vm-managed-service-identity-msi-for-token-acquisition"></a>Como usar uma MSI (Identidade de Serviço Gerenciado) da VM do Azure para aquisição de token 

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)] Este artigo fornece vários exemplos de código e de script para aquisição de token, assim como diretrizes sobre tópicos importantes como a manipulação de expiração de token e erros HTTP.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

Se você planeja usar os exemplos do Azure PowerShell neste artigo, instale a versão mais recente do [Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM).


> [!IMPORTANT]
> - Todo código/script de exemplo neste artigo supõe que o cliente esteja sendo executado em uma Máquina Virtual habilitada para MSI. Use o recurso "Conectar" da VM no Portal do Azure para conectar-se remotamente à sua VM. Para obter detalhes sobre como habilitar a MSI em uma VM, consulte [Configurar uma MSI (Identidade de Serviço Gerenciado) da VM usando o portal do Azure](msi-qs-configure-portal-windows-vm.md) ou um dos artigos variantes (o PowerShell, CLI, modelo ou SDK do Azure). 

## <a name="overview"></a>Visão geral

Um aplicativo cliente pode solicitar um [token de acesso somente de aplicativo](develop/active-directory-dev-glossary.md#access-token) da MSI para acessar um determinado recurso. O token é [com base na entidade de serviço da MSI](msi-overview.md#how-does-it-work). Sendo assim, o cliente não precisa se registrar para obter um token de acesso em sua própria entidade de serviço. O token é adequado para uso como um token de portador em [chamadas de serviço a serviço que exigem credenciais de cliente](active-directory-protocols-oauth-service-to-service.md).

|  |  |
| -------------- | -------------------- |
| [Obter um token usando HTTP](#get-a-token-using-http) | Detalhes de protocolo para o ponto de extremidade do token da MSI |
| [Obter um token usando C#](#get-a-token-using-c) | Exemplo de como usar o ponto de extremidade REST da MSI de um cliente C# |
| [Obter um token usando Go](#get-a-token-using-go) | Exemplo de como usar o ponto de extremidade REST da MSI de um cliente Go |
| [Obter um token usando o Azure PowerShell](#get-a-token-using-azure-powershell) | Exemplo de como usar o ponto de extremidade REST da MSI de um cliente PowerShell |
| [Obter um token usando CURL](#get-a-token-using-curl) | Exemplo de como usar o ponto de extremidade REST da MSI de um cliente Bash/CURL |
| [Manipulando a expiração do token](#handling-token-expiration) | Diretrizes para manipular tokens de acesso expirados |
| [Tratamento de erros](#error-handling) | Diretrizes para tratamento de erros HTTP retornados do ponto de extremidade do token da MSI |
| [IDs de recurso para serviços do Azure](#resource-ids-for-azure-services) | Onde obter IDs de recurso para os serviços do Azure compatíveis |

## <a name="get-a-token-using-http"></a>Obter um token usando HTTP 

A interface fundamental para adquirir um token de acesso é baseada em REST, tornando-a acessível para qualquer aplicativo cliente em execução na VM que pode fazer chamadas REST HTTP. Isso é semelhante ao modelo de programação do Azure AD, exceto que o cliente usa um ponto de extremidade de localhost na máquina virtual (vs. um ponto de extremidade do Azure AD).

Solicitação de exemplo:

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| Elemento | Descrição |
| ------- | ----------- |
| `GET` | O verbo HTTP, indicando que você deseja recuperar os dados do ponto de extremidade. Neste caso, um token de acesso OAuth. | 
| `http://localhost:50342/oauth2/token` | O ponto de extremidade da MSI em que 50342 é a porta padrão e é configurável. |
| `resource` | Um parâmetro de cadeia de caracteres de consulta que indica o URI da ID do aplicativo do recurso de destino. Ele também aparece na declaração `aud` (público) do token emitido. Este exemplo solicita um token para acessar o Azure Resource Manager, que tem um URI de ID do aplicativo de https://management.azure.com/. |
| `Metadata` | Um campo de cabeçalho de solicitação HTTP, exigido pela MSI como uma mitigação contra ataques de SSRF (Falsificação de Solicitação no Lado de Servidor). Esse valor deve ser definido como "true", com todas as letras minúsculas.

Exemplo de resposta:

```
HTTP/1.1 200 OK
Content-Type: application/json
{
  "access_token": "eyJ0eXAi...",
  "refresh_token": "",
  "expires_in": "3599",
  "expires_on": "1506484173",
  "not_before": "1506480273",
  "resource": "https://management.azure.com/",
  "token_type": "Bearer"
}
```

| Elemento | Descrição |
| ------- | ----------- |
| `access_token` | O token de acesso solicitado. Ao chamar uma API REST protegida, o token é inserido no campo de cabeçalho de solicitação `Authorization` como um token "portador", permitindo que a API autentique o chamador. | 
| `refresh_token` | Não é usado pela MSI. |
| `expires_in` | O número de segundos que o token de acesso continua sendo válido antes da expiração desde o momento da emissão. A hora da emissão pode ser encontrada na declaração `iat` do token. |
| `expires_on` | O período de expiração do token de acesso. A data é representada como o número de segundos de “1970-01-01T0:0:0Z UTC” (corresponde à declaração `exp` do token). |
| `not_before` | O período para o token de acesso entrar em vigor e poder ser aceito. A data é representada como o número de segundos de “1970-01-01T0:0:0Z UTC” (corresponde à declaração `nbf` do token). |
| `resource` | O recurso para o qual o token de acesso foi solicitado, que corresponde ao parâmetro de cadeia de consulta `resource` da solicitação. |
| `token_type` | O tipo de token, que é um token de acesso "Portador", o que significa que o recurso pode conceder acesso ao portador desse token. |

## <a name="get-a-token-using-c"></a>Obter um token usando C#

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net;
using System.Web.Script.Serialization; 

// Build request to acquire MSI token
HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://localhost:50342/oauth2/token?resource=https://management.azure.com/");
request.Headers["Metadata"] = "true";
request.Method = "GET";

try
{
    // Call /token endpoint
    HttpWebResponse response = (HttpWebResponse)request.GetResponse();

    // Pipe response Stream to a StreamReader, and extract access token
    StreamReader streamResponse = new StreamReader(response.GetResponseStream()); 
    string stringResponse = streamResponse.ReadToEnd();
    JavaScriptSerializer j = new JavaScriptSerializer();
    Dictionary<string, string> list = (Dictionary<string, string>) j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
    string accessToken = list["access_token"];
}
catch (Exception e)
{
    string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
}

```

## <a name="get-a-token-using-go"></a>Obter um token usando Go

```
package main

import (
  "fmt"
  "io/ioutil"
  "net/http"
  "net/url"
  "encoding/json"
)

type responseJson struct {
  AccessToken string `json:"access_token"`
  RefreshToken string `json:"refresh_token"`
  ExpiresIn string `json:"expires_in"`
  ExpiresOn string `json:"expires_on"`
  NotBefore string `json:"not_before"`
  Resource string `json:"resource"`
  TokenType string `json:"token_type"`
}

func main() {
    
    // Create HTTP request for MSI token to access Azure Resource Manager
    var msi_endpoint *url.URL
    msi_endpoint, err := url.Parse("http://localhost:50342/oauth2/token")
    if err != nil {
      fmt.Println("Error creating URL: ", err)
      return 
    }
    msi_parameters := url.Values{}
    msi_parameters.Add("resource", "https://management.azure.com/")
    msi_endpoint.RawQuery = msi_parameters.Encode()
    req, err := http.NewRequest("GET", msi_endpoint.String(), nil)
    if err != nil {
      fmt.Println("Error creating HTTP request: ", err)
      return 
    }
    req.Header.Add("Metadata", "true")

    // Call MSI /token endpoint
    client := &http.Client{}
    resp, err := client.Do(req) 
    if err != nil{
      fmt.Println("Error calling token endpoint: ", err)
      return
    }

    // Pull out response body
    responseBytes,err := ioutil.ReadAll(resp.Body)
    defer resp.Body.Close()
    if err != nil {
      fmt.Println("Error reading response body : ", err)
      return
    }

    // Unmarshall response body into struct
    var r responseJson
    err = json.Unmarshal(responseBytes, &r)
    if err != nil {
      fmt.Println("Error unmarshalling the response:", err)
      return
    }

    // Print HTTP response and marshalled response body elements to console
    fmt.Println("Response status:", resp.Status)
    fmt.Println("access_token: ", r.AccessToken)
    fmt.Println("refresh_token: ", r.RefreshToken)
    fmt.Println("expires_in: ", r.ExpiresIn)
    fmt.Println("expires_on: ", r.ExpiresOn)
    fmt.Println("not_before: ", r.NotBefore)
    fmt.Println("resource: ", r.Resource)
    fmt.Println("token_type: ", r.TokenType)
}
```

## <a name="get-a-token-using-azure-powershell"></a>Obter um token usando o Azure PowerShell

O exemplo a seguir demonstra como usar o ponto de extremidade REST da MSI de um cliente do PowerShell para:

1. Adquirir um token de acesso.
2. Use o token de acesso para chamar uma API REST do Azure Resource Manager e obter informações sobre a VM. Substitua sua ID de assinatura, o nome do grupo de recursos e o nome da máquina virtual para `<SUBSCRIPTION-ID>`, `<RESOURCE-GROUP>` e `<VM-NAME>`, respectivamente.

```azurepowershell
# Get an access token for the MSI
$response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token `
                              -Method GET -Body @{resource="https://management.azure.com/"} -Headers @{Metadata="true"}
$content =$response.Content | ConvertFrom-Json
$access_token = $content.access_token
echo "The MSI access token is $access_token"

# Use the access token to get resource information for the VM
$vmInfoRest = (Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Compute/virtualMachines/<VM-NAME>?api-version=2017-12-01 -Method GET -ContentType "application/json" -Headers @{ Authorization ="Bearer $access_token"}).content
echo "JSON returned from call to get VM info:"
echo $vmInfoRest

```

## <a name="get-a-token-using-curl"></a>Obter um token usando CURL

```bash
response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The MSI access token is $access_token
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

| Elemento | Descrição |
| ------- | ----------- |
| error   | Identificador do erro. |
| error_description | Descrição detalhada do erro. **Descrições de erro podem ser alteradas a qualquer momento. Não escreva código que se ramifique com base nos valores na descrição do erro.**|

### <a name="http-response-reference"></a>Referência de resposta HTTP

Esta seção documenta as possíveis respostas de erro. Um status "200 OK" é uma resposta bem-sucedida, e o token de acesso está contido no JSON do corpo de resposta, no elemento access_token.

| Código de status | Erro | Descrição do erro | Solução |
| ----------- | ----- | ----------------- | -------- |
| 400 Solicitação Inválida | invalid_resource | AADSTS50001: o aplicativo chamado *\<URI\>* não foi encontrado no locatário chamado *\<TENANT-ID\>*. Isso poderá acontecer se o aplicativo não tiver sido instalado pelo administrador do locatário ou aceito por qualquer usuário no locatário. Talvez você tenha enviado a solicitação de autenticação ao locatário errado.\ | (Apenas Linux) |
| 400 Solicitação Inválida | bad_request_102 | Cabeçalho de metadados necessário não especificado | O campo de cabeçalho de solicitação `Metadata` está ausente da solicitação ou está formatado incorretamente. O valor deve ser especificado como `true`, com todas as letras minúsculas. Consulte a "Amostra de solicitação" na [seção REST anterior](#rest) para obter um exemplo.|
| 401 Não Autorizado | unknown_source | *\<URI\>* de origem desconhecida | Verifique se o URI da solicitação HTTP GET está formatado corretamente. A parte `scheme:host/resource-path` deve ser especificada como `http://localhost:50342/oauth2/token`. Consulte a "Amostra de solicitação" na [seção REST anterior](#rest) para obter um exemplo.|
|           | invalid_request | A solicitação não tem um parâmetro obrigatório, inclui um valor de parâmetro inválido, inclui um parâmetro mais de uma vez ou está malformada. |  |
|           | unauthorized_client | O cliente não está autorizado a solicitar um token de acesso usando este método. | Causado por uma solicitação que não usou o loopback local para chamar a extensão ou em uma VM que não tem uma MSI configurada corretamente. Consulte [Configure a VM Managed Service Identity (MSI) using the Azure portal](msi-qs-configure-portal-windows-vm.md) (Configurar uma MSI [Identidade de Serviço Gerenciado] da VM usando o Portal do Azure) se precisar de ajuda com a configuração da VM. |
|           | access_denied | O proprietário do recurso ou o servidor de autorização negou a solicitação. |  |
|           | unsupported_response_type | O servidor de autorização não dá suporte à obtenção de um token de acesso usando este método. |  |
|           | invalid_scope | O escopo solicitado é inválido, desconhecido ou malformado. |  |
| Erro interno do servidor 500 | unknown | Falha ao recuperar o token do Active Directory. Para obter detalhes, consulte os logs no *\<caminho do arquivo\>* | Verifique se a MSI foi habilitada na VM. Consulte [Configure a VM Managed Service Identity (MSI) using the Azure portal](msi-qs-configure-portal-windows-vm.md) (Configurar uma MSI [Identidade de Serviço Gerenciado] da VM usando o Portal do Azure) se precisar de ajuda com a configuração da VM.<br><br>Verifique também se seu URI de solicitação GET HTTP foi formatado corretamente, principalmente o URI do recurso especificado na cadeia de caracteres de consulta. Consulte a "Solicitação de amostra" na [seção REST anterior](#rest) para obter um exemplo ou [Serviços do Azure que dão suporte para autenticação do Azure AD](msi-overview.md#azure-services-that-support-azure-ad-authentication) para obter uma lista de serviços e seus respectivos IDs de recurso.

## <a name="resource-ids-for-azure-services"></a>IDs de recurso para serviços do Azure

Consulte [Azure services that support Azure AD authentication](msi-overview.md#azure-services-that-support-azure-ad-authentication) (Serviços do Azure que dão suporte à autenticação do Azure AD) para obter uma lista dos recursos compatíveis com o Azure AD e que foram testados com a MSI e as respectivas IDs do recurso.


## <a name="related-content"></a>Conteúdo relacionado

- Para habilitar a MSI em uma VM do Azure, consulte [Configure a VM Managed Service Identity (MSI) using the Azure portal](msi-qs-configure-portal-windows-vm.md) (Configurar uma MSI (Identidade de Serviço Gerenciado) usando o portal do Azure).

Use a seção de comentários a seguir para fornecer seus comentários e nos ajudar a aprimorar e adaptar nosso conteúdo.








