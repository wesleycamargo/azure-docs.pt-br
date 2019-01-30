---
title: Usando perfis de versão de API com o SDK do .NET no Azure Stack | Microsoft Docs
description: Saiba mais sobre como usar perfis de versão da API com .NET no Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2018
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 12/07/2018
ms.openlocfilehash: e335f4cb112c0f029768ccf050f888e00a6efdc1
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55250600"
---
# <a name="use-api-version-profiles-with-net-in-azure-stack"></a>Usar perfis de versão da API com .NET no Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

O SDK do .NET para o Azure Stack Resource Manager fornece ferramentas para ajudá-lo a criar e gerenciar sua infraestrutura. Provedores de recursos no SDK incluem computação, rede, armazenamento, serviços de aplicativos, e [KeyVault](../../key-vault/key-vault-whatis.md). O SDK do .NET inclui 14 pacotes do NuGet. Esses pacotes devem ser baixados para a sua solução de projeto cada vez que incorpora as informações de perfil. No entanto, você pode baixar especificamente qual provedor de recursos serão usados para o 2018-03-01-hybrid ou 2017-03-09-perfil para otimizar a memória para seu aplicativo. Cada pacote consiste em um provedor de recursos, a respectiva versão da API e o perfil de API à qual ele pertence. Os perfis de API no SDK do .NET permitem o desenvolvimento em nuvem híbrida, ajudando você a alternar entre os recursos globais do Azure e recursos no Azure Stack.

## <a name="net-and-api-version-profiles"></a>Perfis de versão do .NET e API

Um perfil de API é uma combinação de provedores de recursos e as versões de API. Você pode usar um perfil de API para obter a versão mais recente e mais estável de cada tipo de recurso em um pacote de provedor de recursos.

-   Para usar as versões mais recentes de todos os serviços, use o **mais recente** perfil dos pacotes. Este perfil é parte do **Microsoft** pacote do NuGet.

-   Para usar os serviços compatíveis com o Azure Stack, use o **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01. *ResourceProvider*. 0.9.0-preview.nupkg** ou **Microsoft.Azure.Management.Profiles.hybrid\_2017\_03\_09. *ResourceProvider*. 0.9.0-preview.nupkg** pacotes.

    -   Há dois pacotes para cada provedor de recursos para cada perfil.

    -   Certifique-se de que o **ResourceProvider** parte do pacote do NuGet acima é alterado para o provedor correto.

-   Para usar a versão de API mais recente de um serviço, use o **mais recente** perfil do pacote NuGet específico. Por exemplo, se você quiser usar o **API mais recente** versão do serviço de computação usado sozinho, use o **mais recente** perfil dos **computação** pacote. O **mais recente** perfil faz parte da **Microsoft** pacote do NuGet.

-   Para usar versões de API específicas para um tipo de recurso em um provedor de recursos específico, use as versões de API específicas definidas dentro do pacote.

Você pode combinar todas as opções no mesmo aplicativo.

## <a name="install-the-azure-net-sdk"></a>Instalar o SDK .NET do Azure

1.  Instale o Git. Para obter instruções, consulte [Guia de Introdução - instalação do Git][].

2.  Para instalar os pacotes do NuGet corretos, consulte [Localizando e instalando um pacote][].

3.  Os pacotes que precisam ser instaladas depende da versão de perfil que você deseja usar. Os nomes de pacote para as versões de perfil são:

    1.  **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01. *ResourceProvider*. 0.9.0-preview.nupkg**

    2.  **Microsoft.Azure.Management.Profiles.hybrid\_2017\_03\_09. *ResourceProvider*. 0.9.0-preview.nupkg**

4.  Para instalar os pacotes do NuGet corretos para o Visual Studio Code, consulte o seguinte link para baixar o [instruções do Gerenciador de pacotes NuGet][].

5.  Se não estiver disponível, crie uma assinatura e salve a ID da assinatura a ser usado posteriormente. Para obter instruções criar uma assinatura, consulte [criar assinaturas de ofertas no Azure Stack][].

6.  Crie uma entidade de serviço e salve a ID do cliente e o segredo do cliente. Para obter instruções sobre como criar uma entidade de serviço para o Azure Stack, consulte [fornecer acesso a aplicativos para o Azure Stack][]. A ID do cliente é também conhecido como a ID do aplicativo durante a criação de um serviço principal.

7.  Verifique se que a entidade de serviço tem a função de proprietário/Colaborador em sua assinatura. Para obter instruções sobre como atribuir uma função à entidade de serviço, consulte [fornecer acesso a aplicativos para o Azure Stack][].

## <a name="prerequisites"></a>Pré-requisitos

Para usar o SDK do .NET do Azure com o Azure Stack, você deve fornecer os seguintes valores e, em seguida, definir valores com variáveis de ambiente. Para definir as variáveis de ambiente, consulte as instruções seguintes à tabela para seu sistema operacional.

| Valor                     | Variáveis de ambiente   | DESCRIÇÃO                                                                                                             |
|---------------------------|-------------------------|-------------------------------------------------------------------------------------------------------------------------|
| ID do locatário                 | AZURE_TENANT_ID       | O valor do Azure Stack [ *ID do locatário*][].                                                                          |
| ID do cliente                 | AZURE_CLIENT_ID       | O serviço de ID do aplicativo principal salvo quando a entidade de serviço foi criada na seção anterior deste artigo. |
| ID da assinatura           | AZURE_SUBSCRIPTION_ID | O [ *ID da assinatura* ][] é como você pode acessar ofertas no Azure Stack.                                                      |
| Segredo do cliente             | AZURE_CLIENT_SECRET   | O segredo do aplicativo principal do serviço salvo quando a entidade de serviço foi criada.                                      |
| Ponto de extremidade do Gerenciador de recursos | ARM_ENDPOINT           | Ver [ *o ponto de extremidade do Gerenciador de recursos do Azure Stack*][].                                                                    |
| Local padrão                  | RESOURCE_LOCATION     | Local para o Azure Stack.

Para localizar a ID do locatário para o Azure Stack, siga as instruções encontradas [aqui](../azure-stack-csp-ref-operations.md). Para definir as variáveis de ambiente, siga as etapas a seguir:

### <a name="microsoft-windows"></a>Microsoft Windows

Para definir as variáveis de ambiente em um prompt de comando do Windows, use o seguinte formato:

```shell
Set Azure_Tenant_ID=Your_Tenant_ID
```

### <a name="macos-linux-and-unix-based-systems"></a>sistemas baseados em Unix, Linux e macOS

Em sistemas baseados em Unix, você pode usar o comando a seguir:

```shell
Export Azure_Tenant_ID=Your_Tenant_ID
```

### <a name="the-azure-stack-resource-manager-endpoint"></a>O ponto de extremidade do Gerenciador de recursos do Azure Stack

O Gerenciador de recursos do Microsoft Azure é uma estrutura de gerenciamento que permite aos administradores implantar, gerenciar e monitorar recursos do Azure. O Azure Resource Manager pode lidar com essas tarefas, como um grupo, em vez de individualmente, em uma única operação.

Você pode obter as informações de metadados do ponto de extremidade do Gerenciador de recursos. O ponto de extremidade retorna um arquivo JSON com as informações necessárias para executar seu código.

Observe as seguintes considerações:

- O **ResourceManagerUrl** no Azure Stack desenvolvimento ASDK (Kit) é: https://management.local.azurestack.external/

- O **ResourceManagerUrl** em sistemas integrados é: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/` Para recuperar os metadados necessários: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`

Arquivo JSON de exemplo:

```json
{ 
   "galleryEndpoint": "https://portal.local.azurestack.external:30015/",
   "graphEndpoint": "https://graph.windows.net/",
   "portal Endpoint": "https://portal.local.azurestack.external/",
   "authentication": 
      {
      "loginEndpoint": "https://login.windows.net/",
      "audiences": ["https://management.yourtenant.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
      }
}
```

## <a name="existing-api-profiles"></a>Perfis de API existentes

1.  **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01.*ResourceProvider*.0.9.0-preview.nupkg**: Perfil mais recente criado para o Azure Stack. Use esse perfil para os serviços sejam mais compatíveis com o Azure Stack como são 1808 carimbo ou adicional.

2.  **Microsoft.Azure.Management.Profiles.hybrid\_2017\_03\_09.*ResourceProvider*.0.9.0-preview.nupkg**: Se você estiver usando um carimbo de menor do que a compilação 1808, use esse perfil.

3.  **Mais recente**: Consiste nas versões mais recentes de todos os serviços de perfil. Use as versões mais recentes de todos os serviços. Este perfil é parte do **Microsoft** pacote do NuGet.

Para obter mais informações sobre perfis de API e o Azure Stack, consulte um [Resumo dos perfis de API][].

## <a name="azure-net-sdk-api-profile-usage"></a>Uso de perfil de API do SDK .NET do Azure

O código a seguir deve ser usado para instanciar um cliente de gerenciamento de recursos. Um código semelhante pode ser usado para criar uma instância de outro provedor de recursos (por exemplo, computação, rede e armazenamento) clientes. 

```csharp
var client = new ResourceManagementClient(armEndpoint, credentials)
{
    SubscriptionId = subscriptionId
};
```

O `credentials` parâmetro no código acima é necessário para instanciar um cliente. O código a seguir gera um token de autenticação, a ID do locatário e a entidade de serviço.

```csharp
var azureStackSettings = getActiveDirectoryServiceSettings(armEndpoint);
var credentials = ApplicationTokenProvider.LoginSilentAsync(tenantId, servicePrincipalId, servicePrincipalSecret, azureStackSettings).GetAwaiter().GetResult();
```
O `getActiveDirectoryServiceSettings` chamada no código recupera os pontos de extremidade do Azure Stack do ponto de extremidade de metadados. Ele declara as variáveis de ambiente da chamada feita: 

```csharp
public static ActiveDirectoryServiceSettings getActiveDirectoryServiceSettings(string armEndpoint)
{
    var settings = new ActiveDirectoryServiceSettings();
    try
    {
        var request = (HttpWebRequest)HttpWebRequest.Create(string.Format("{0}/metadata/endpoints?api-version=1.0", armEndpoint));
        request.Method = "GET";
        request.UserAgent = ComponentName;
        request.Accept = "application/xml";
        using (HttpWebResponse response = (HttpWebResponse)request.GetResponse())
        {
            using (StreamReader sr = new StreamReader(response.GetResponseStream()))
            {
                var rawResponse = sr.ReadToEnd();
                var deserialized = JObject.Parse(rawResponse);
                var authenticationObj = deserialized.GetValue("authentication").Value<JObject>();
                var loginEndpoint = authenticationObj.GetValue("loginEndpoint").Value<string>();
                var audiencesObj = authenticationObj.GetValue("audiences").Value<JArray>();
                settings.AuthenticationEndpoint = new Uri(loginEndpoint);
                settings.TokenAudience = new Uri(audiencesObj[0].Value<string>());
                settings.ValidateAuthority = loginEndpoint.TrimEnd('/').EndsWith("/adfs", StringComparison.OrdinalIgnoreCase) ? false : true;
            }
        }
    }
    catch (Exception ex)
    {
        Console.WriteLine(String.Format("Could not get AD service settings. Exception: {0}", ex.Message));
    }
    return settings;
}
```
Isso permitirá que você use os pacotes do NuGet do perfil de API para implantar seu aplicativo com êxito para o Azure Stack.

## <a name="samples-using-api-profiles"></a>Exemplos usando perfis de API

Os exemplos a seguir podem ser usados como uma referência para a criação de soluções com perfis do .NET e a API de pilha do Azure.
- [Gerenciar grupos de recursos](https://github.com/Azure-Samples/hybrid-resources-dotnet-manage-resource-group)
- [Gerenciar contas de armazenamento](https://github.com/Azure-Samples/hybird-storage-dotnet-manage-storage-accounts)
- [Gerenciar uma máquina Virtual](https://github.com/Azure-Samples/hybrid-compute-dotnet-manage-vm)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre perfis de API, consulte:

- [Gerenciar perfis de versão de API no Azure Stack](azure-stack-version-profiles.md)
- [Versões de API do provedor de recursos com suporte por perfis](azure-stack-profiles-azure-resource-manager-versions.md)

  [Guia de Introdução - instalação do Git]: https://git-scm.com/book/en/v2/Getting-Started-Installing-Git
  [Localizando e instalando um pacote]: /nuget/tools/package-manager-ui
  [Instruções do Gerenciador de pacotes NuGet]: https://marketplace.visualstudio.com/items?itemName=jmrog.vscode-nuget-package-manager
  [Criar assinaturas de ofertas no Azure Stack]: ../azure-stack-subscribe-plan-provision-vm.md
  [Fornecer acesso a aplicativos para o Azure Stack]: ../azure-stack-create-service-principals.md
  [* locatário ID *]: ../azure-stack-identity-overview.md
  [* ID do * assinatura do]: ../azure-stack-plan-offer-quota-overview.md#subscriptions
  [* o Azure ponto de extremidade de Gerenciador de recursos de pilha *]: ../user/azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint
  [Resumo dos perfis de API]: ../user/azure-stack-version-profiles.md#summary-of-api-profiles
  [Test Project to Virtual Machine, vNet, resource groups, and storage account]: https://github.com/seyadava/azure-sdk-for-net-samples/tree/master/TestProject
  [Use Azure PowerShell to create a service principal with a certificate]: ../azure-stack-create-service-principals.md
  [Run unit tests with Test Explorer.]: /visualstudio/test/run-unit-tests-with-test-explorer?view=vs-2017
