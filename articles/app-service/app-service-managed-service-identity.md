---
title: "Managed Service Identity no Serviço de Aplicativo e no Azure Functions | Microsoft Docs"
description: "Guia de configuração e referência conceitual para suporte do Managed Service Identity no Serviço de Aplicativo do Azure e no Azure Functions"
services: app-service
author: mattchenderson
manager: cfowler
editor: 
ms.service: app-service
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 09/13/2017
ms.author: mahender
ms.openlocfilehash: 6b2dcaa4b0e0f59bf8a632b48813ba6a24202ec5
ms.sourcegitcommit: 7f1ce8be5367d492f4c8bb889ad50a99d85d9a89
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2017
---
# <a name="how-to-use-azure-managed-service-identity-public-preview-in-app-service-and-azure-functions"></a>Como usar o Azure Managed Service Identity (visualização pública) no Serviço de Aplicativo e no Azure Functions

> [!NOTE] 
> Atualmente, o Managed Service Identity para o Serviço de Aplicativo e o Azure Functions está em versão prévia.

Este tópico mostra como criar uma identidade de aplicativo gerenciado para aplicativos do Serviço de Aplicativo e do Azure Functions e como usá-la para acessar outros recursos. Uma identidade de serviço gerenciado do Azure Active Directory permite que o aplicativo acesse facilmente os outros recursos protegidos pelo AAD, como o Azure Key Vault. A identidade é gerenciada pela plataforma do Azure e não exige provisionamento ou giro de nenhum segredo. Para obter mais informações sobre o Managed Service Identity, consulte a [Visão Geral do Managed Service Identity](../active-directory/msi-overview.md).

## <a name="creating-an-app-with-an-identity"></a>Criar um aplicativo com uma identidade

Criar um aplicativo com uma identidade exige que uma propriedade adicional seja definida no aplicativo.

> [!NOTE] 
> Apenas o slot principal de um site receberá a identidade. Ainda não há suporte para identidades de serviço gerenciadas para slots de implantação.


### <a name="using-the-azure-portal"></a>Usando o portal do Azure

Para configurar uma identidade de serviço gerenciado no portal, primeiro, crie um aplicativo como normal e, em seguida, habilite o recurso.

1. Crie um aplicativo no portal, como você faria normalmente. Navegue até ele no portal.

2. Se você estiver usando um aplicativo de funções, navegue até os **recursos da Plataforma**. Para outros tipos de aplicativo, role para baixo até o grupo **Configurações** no painel de navegação à esquerda.

3. Selecione **Managed Service Identity**.

4. Defina **Registrar com o Azure Active Directory** como **Ativado**. Clique em **Salvar**.

![Managed Service Identity no Serviço de Aplicativo](media/app-service-managed-service-identity/msi-blade.png)

### <a name="using-the-azure-cli"></a>Usando a CLI do Azure

Para configurar uma identidade do serviço gerenciado usando a CLI do Azure, será preciso usar o comando `az webapp assign-identity` em um aplicativo existente. Você tem três opções para executar os exemplos nesta seção:

- Usar o [Azure Cloud Shell](../cloud-shell/overview.md) do portal do Azure.
- Usar o Azure Cloud Shell inserido por meio do botão "Experimentar", localizado no canto superior direito de cada bloco de código abaixo.
- [Instale a versão mais recente da CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.21 ou posterior) se preferir usar um console local da CLI. 

As etapas a seguir o guiarão na criação de um aplicativo Web e na atribuição de uma identidade a ele usando a CLI:

1. Se você estiver usando a CLI do Azure em um console local, primeiro entre no Azure usando o [logon az](/cli/azure/#login). Use uma conta que esteja associada à assinatura do Azure sob a qual você deseja implantar o aplicativo:

    ```azurecli-interactive
    az login
    ```
2. Crie um aplicativo Web usando a CLI. Para ver mais exemplos de como usar a CLI com o Serviço de Aplicativo, consulte [Exemplos de CLI do Serviço de Aplicativo](../app-service/app-service-cli-samples.md):

    ```azurecli-interactive
    az group create --name myResourceGroup --location westus
    az appservice plan create --name myplan --resource-group myResourceGroup --sku S1
    az webapp create --name myapp --resource-group myResourceGroup --plan myplan
    ```

3. Execute o comando `assign-identity` para criar a identidade para este aplicativo:

    ```azurecli-interactive
    az webapp assign-identity --name myApp --resource-group myResourceGroup
    ```

### <a name="using-an-azure-resource-manager-template"></a>Usando um modelo do Azure Resource Manager

Um modelo do Azure Resource Manager pode ser usado para automatizar a implantação de recursos do Azure. Para saber mais sobre a implantação do Serviço de Aplicativo e do Azure Functions, consulte [Automatizar a implantação de recursos no Serviço de Aplicativo](../app-service/app-service-deploy-complex-application-predictably.md) e [Automatizar a implantação de recursos no Azure Functions](../azure-functions/functions-infrastructure-as-code.md).

Qualquer recurso do tipo `Microsoft.Web/sites` pode ser criado com uma identidade, incluindo a propriedade a seguir na definição de recurso:
```json
"identity": {
    "type": "SystemAssigned"
}    
```

Isso faz com que o Azure crie e gerencie a identidade do aplicativo.

Por exemplo, um aplicativo Web pode ser semelhante ao seguinte:
```json
{
    "apiVersion": "2016-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('appName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "SystemAssigned"
    },
    "properties": {
        "name": "[variables('appName')]",
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "hostingEnvironment": "",
        "clientAffinityEnabled": false,
        "alwaysOn": true
    },
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
    ]
}
```

Quando o site é criado, ele tem as seguintes propriedades adicionais:
```json
"identity": {
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

No qual `<TENANTID>` e `<PRINCIPALID>` são substituídos por GUIDs. A propriedade tenantId identifica a qual locatário do AAD o aplicativo pertence. O principalId é um identificador exclusivo para a nova identidade do aplicativo. No AAD, o aplicativo tem o mesmo nome dado à instância do Serviço de Aplicativo ou do Azure Functions.

## <a name="obtaining-tokens-for-azure-resources"></a>Obter tokens para recursos do Azure

Um aplicativo pode usar sua identidade para obter tokens para outros recursos protegidos pelo AAD, como o Azure Key Vault. Esses tokens representam o acesso do aplicativo ao recurso e não um usuário específico do aplicativo. 

> [!IMPORTANT]
> Talvez seja necessário configurar o recurso de destino para permitir o acesso do aplicativo. Por exemplo, se você solicitar um token para o Key Vault, será necessário se certificar de que uma política de acesso que inclui a identidade do aplicativo foi adicionada. Caso contrário, as chamadas para o Key Vault serão rejeitadas, mesmo se elas incluírem o token. Para saber mais sobre os recursos que oferecem suporte a tokens do Managed Service Identity, consulte [Serviços do Azure que oferecem suporte à autenticação do Azure AD](../active-directory/msi-overview.md#which-azure-services-support-managed-service-identity).

Há um protocolo REST simples para obter um token no Serviço de Aplicativo e no Azure Functions. Para aplicativos .NET, a biblioteca Microsoft.Azure.Services.AppAuthentication fornece uma abstração sobre esse protocolo e dá suporte a uma experiência de desenvolvimento local.

### <a name="asal"></a>Usar a biblioteca de Microsoft.Azure.Services.AppAuthentication do .NET

Para aplicativos e funções .NET, a maneira mais simples de trabalhar com uma identidade de serviço gerenciado é por meio do pacote Microsoft.Azure.Services.AppAuthentication. Essa biblioteca também permitirá testar o código localmente no computador de desenvolvimento, usando a conta de usuário da Visual Studio, o [CLI do Azure 2.0](https://docs.microsoft.com/cli/azure/overview?view=azure-cli-latest) ou Autenticação Integrada do Active Directory. Para obter mais informações sobre as opções de desenvolvimento local com essa biblioteca, consulte a [Referência Microsoft.Azure.Services.AppAuthentication]. Esta seção mostra a você como começar a usar a biblioteca no seu código.

1. Adicione uma referência aos pacotes NuGet [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) e [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) no aplicativo.

2.  Adicione o código a seguir à sua página:

```csharp
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.KeyVault;
// ...
var azureServiceTokenProvider = new AzureServiceTokenProvider();
string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");
// OR
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
```

Para saber mais sobre o Microsoft.Azure.Services.AppAuthentication e as operações que ele expõe, consulte a [Referência Microsoft.Azure.Services.AppAuthentication] e [Serviço de Aplicativo e KeyVault com a amostra MSI .NET](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet).

### <a name="using-the-rest-protocol"></a>Usar o protocolo REST

Um aplicativo com uma identidade de serviço gerenciado tem duas variáveis de ambiente definidas:
- MSI_ENDPOINT
- MSI_SECRET

A **MSI_ENDPOINT** é uma URL local da qual o aplicativo pode solicitar tokens. Para obter um token para um recurso, solicite uma HTTP GET para esse ponto de extremidade, incluindo os seguintes parâmetros:

> [!div class="mx-tdBreakAll"]
> |Nome do parâmetro|Nesse|Descrição|
> |-----|-----|-----|
> |recurso|Consultar|O URI do recurso do AAD do recurso para o qual um token deve ser obtido.|
> |api-version|Consultar|A versão da API do token a ser usada. Atualmente, a única versão com suporte é 2017-09-01.|
> |segredo|Cabeçalho|O valor da variável de ambiente MSI_SECRET.|


Uma resposta bem-sucedida de 200 OK inclui um corpo JSON com as seguintes propriedades:

> [!div class="mx-tdBreakAll"]
> |Nome da propriedade|Descrição|
> |-------------|----------|
> |access_token|O token de acesso solicitado. O serviço Web de chamada pode usar esse token para se autenticar no serviço Web de recebimento.|
> |expires_on|A hora de expiração do token de acesso. A data é representada como o número de segundos de 1970-01-01T0:0:0Z UTC até a hora de expiração. Esse valor é usado para determinar o tempo de vida de tokens em cache.|
> |recurso|O URI da ID de aplicativo do serviço Web de recebimento.|
> |token_type|Indica o valor do tipo de token. O único tipo com suporte do Azure AD é Portador Para saber mais sobre os tokens de portador, consulte [Estrutura de Autorização do OAuth 2.0: Uso do Token de Portador (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt).|


Essa resposta é igual a [resposta à solicitação do AAD de token de acesso de serviço para serviço](../active-directory/develop/active-directory-protocols-oauth-service-to-service.md#service-to-service-access-token-response).

> [!NOTE] 
> Variáveis de ambiente são configuradas quando o processo é iniciado pela primeira vez, então após habilitar a identidade de serviço gerenciado para o seu aplicativo, você pode precisar reiniciar o aplicativo ou reimplantar o código dele antes que `MSI_ENDPOINT` e `MSI_SECRET` estejam disponíveis para seu código.

### <a name="rest-protocol-examples"></a>Exemplos de protocolo REST
Uma solicitação de exemplo pode ser semelhante ao seguinte:
```
GET /MSI/token?resource=https://vault.azure.net&api-version=2017-09-01 HTTP/1.1
Host: localhost:4141
Secret: 853b9a84-5bfa-4b22-a3f3-0b9a43d9ad8a
```
Uma resposta de exemplo pode ser semelhante ao seguinte:
```
HTTP/1.1 200 OK
Content-Type: application/json
 
{
    "access_token": "eyJ0eXAi…",
    "expires_on": "09/14/2017 00:00:00 PM +00:00",
    "resource": "https://vault.azure.net",
    "token_type": "Bearer"
}
```

### <a name="code-examples"></a>Exemplos de código
Para fazer essa solicitação no C#:
```csharp
public static async Task<HttpResponseMessage> GetToken(string resource, string apiversion)  {
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Add("Secret", Environment.GetEnvironmentVariable("MSI_SECRET"));
    return await client.GetAsync(String.Format("{0}/?resource={1}&api-version={2}", Environment.GetEnvironmentVariable("MSI_ENDPOINT"), resource, apiversion));
}
```
> [!TIP]
> Para as linguagens .NET, também é possível usar [Microsoft.Azure.Services.AppAuthentication](#asal) em vez de criar essa solicitação por conta própria.

No Node.JS:
```javascript
const rp = require('request-promise');
const getToken = function(resource, apiver, cb) {
    var options = {
        uri: `${process.env["MSI_ENDPOINT"]}/?resource=${resource}&api-version=${apiver}`,
        headers: {
            'Secret': process.env["MSI_SECRET"]
        }
    };
    rp(options)
        .then(cb);
}
```

No PowerShell:
```powershell
$apiVersion = "2017-09-01"
$resourceURI = "https://<AAD-resource-URI-for-resource-to-obtain-token>"
$tokenAuthURI = $env:MSI_ENDPOINT + "?resource=$resourceURI&api-version=$apiVersion"
$tokenResponse = Invoke-RestMethod -Method Get -Headers @{"Secret"="$env:MSI_SECRET"} -Uri $tokenAuthURI
$accessToken = $tokenResponse.access_token
```


[Referência Microsoft.Azure.Services.AppAuthentication]: https://go.microsoft.com/fwlink/p/?linkid=862452
