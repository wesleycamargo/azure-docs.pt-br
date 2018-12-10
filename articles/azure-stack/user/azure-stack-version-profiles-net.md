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
ms.openlocfilehash: cfebbdb9b88a1de6a05f06e6ed72ebc9cddddcf6
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53074444"
---
# <a name="use-api-version-profiles-with-net-in-azure-stack"></a>Usar perfis de versão da API com .NET no Azure Stack

*Aplica-se a: integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

O SDK do .NET para o Azure Stack Resource Manager fornece ferramentas para ajudá-lo a criar e gerenciar sua infraestrutura. Provedores de recursos no SDK incluem computação, rede, armazenamento, serviços de aplicativos, e [KeyVault](../../key-vault/key-vault-whatis.md). O SDK do .NET inclui 14 pacotes do NuGet que devem ser baixados para a sua solução de projeto cada vez que incorporam as informações de perfil. No entanto, você pode baixar especificamente qual provedor de recursos serão usados para o 2018-03-01-hybrid ou 2017-03-09-perfil para otimizar a memória para seu aplicativo. Cada pacote consiste em um provedor de recursos, a respectiva versão da API e o perfil de API à qual ele pertence. Os perfis de API no SDK do .NET permitem o desenvolvimento em nuvem híbrida, ajudando você a alternar entre os recursos globais do Azure e recursos no Azure Stack.

## <a name="net-and-api-version-profiles"></a>Perfis de versão do .NET e API

Um perfil de API é uma combinação de provedores de recursos e as versões de API. Você pode usar um perfil de API para obter a versão mais recente e mais estável de cada tipo de recurso em um pacote de provedor de recursos.

-   Para usar as versões mais recentes de todos os serviços, use o **mais recente** perfil dos pacotes. Este perfil é parte do **Microsoft** pacote do NuGet.

-   Para usar os serviços compatíveis com o Azure Stack, use o **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01. *ResourceProvider*. 0.9.0-preview.nupkg** ou **Microsoft.Azure.Management.Profiles.hybrid\_2017\_03\_09. *ResourceProvider*. 0.9.0-preview.nupkg** pacotes.

    -   Há dois pacotes para cada provedor de recursos para cada perfil.

    -   Certifique-se de que o **ResourceProvider** parte do pacote do NuGet acima é alterado para o provedor correto.

-   Para usar a versão de API mais recente de um serviço, use o **mais recente** perfil do pacote NuGet específico. Por exemplo, se você quiser usar o **API mais recente** versão do serviço de computação usado sozinho, use o **mais recente** perfil dos **computação** pacote. O **mais recente** perfil faz parte da **Microsoft** pacote do NuGet.

-   Para usar versões de API específicas para um tipo de recurso em um provedor de recursos específico, use as versões de API específicas definidas dentro do pacote.

Observe que você pode combinar todas as opções no mesmo aplicativo.

## <a name="install-the-azure-net-sdk"></a>Instalar o SDK .NET do Azure

1.  Instale o Git. Para obter instruções, consulte [Guia de Introdução - instalação do Git][].

2.  Para instalar os pacotes do NuGet corretos, consulte [Localizando e instalando um pacote][].

3.  Os pacotes que precisam ser instaladas depende da versão de perfil que você deseja usar. O nome do pacote para as versões de perfil são:

    1.  **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01. *ResourceProvider*. 0.9.0-preview.nupkg**

    2.  **Microsoft.Azure.Management.Profiles.hybrid\_2017\_03\_09. *ResourceProvider*. 0.9.0-preview.nupkg**

4.  Para instalar os pacotes do NuGet corretos para o Visual Studio Code, consulte o seguinte link para baixar o [instruções do Gerenciador de pacotes NuGet][].

5.  Se não estiver disponível, crie uma assinatura e salve a ID da assinatura a ser usado posteriormente. Para obter instruções criar uma assinatura, consulte [criar assinaturas de ofertas no Azure Stack][].

6.  Crie uma entidade de serviço e salve a ID do cliente e o segredo do cliente. Para obter instruções sobre como criar uma entidade de serviço para o Azure Stack, consulte [fornecer acesso a aplicativos para o Azure Stack][]. Observe que a ID do cliente é também conhecido como a ID do aplicativo durante a criação de uma entidade de serviço.

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

Para localizar a ID do locatário para o Azure Stack, siga as instruções encontradas [aqui](../azure-stack-csp-ref-operations.md). Para definir as variáveis de ambiente, faça o seguinte:

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

- O **ResourceManagerUrl** em sistemas integrados é: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/` para recuperar os metadados necessários: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`

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

1.  **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01. *ResourceProvider*. 0.9.0-preview.nupkg**: perfil mais recente criado para o Azure Stack. Use esse perfil para os serviços sejam mais compatíveis com o Azure Stack como são 1808 carimbo ou adicional.

2.  **Microsoft.Azure.Management.Profiles.hybrid\_2017\_03\_09. *ResourceProvider*. 0.9.0-preview.nupkg**: se você estiver usando um carimbo de menor do que a compilação 1808, usar esse perfil.

3.  **Mais recente**: consiste nas versões mais recentes de todos os serviços de perfil. Use as versões mais recentes de todos os serviços. Este perfil é parte do **Microsoft** pacote do NuGet.

Para obter mais informações sobre perfis de API e o Azure Stack, consulte um [Resumo dos perfis de API][].

## <a name="azure-net-sdk-api-profile-usage"></a>Uso de perfil de API do SDK .NET do Azure

O código a seguir deve ser usado para instanciar um cliente de perfil. Esse parâmetro só é necessário para o Azure Stack ou em outras nuvens privadas. Global Azure já tem essas configurações por padrão.

O código a seguir é necessária para autenticar a entidade de serviço no Azure Stack. Ele cria um token pelo locatário ID e a base de autenticação, que é específica para o Azure Stack.

```csharp
public class CustomLoginCredentials : ServiceClientCredentials
{
    private string clientId;
    private string clientSecret;
    private string resourceId;
    private string tenantId;

    private const string authenticationBase = "https://login.windows.net/{0}";

    public CustomLoginCredentials(string servicePrincipalId, string servicePrincipalSecret, string azureEnvironmentResourceId, string azureEnvironmentTenandId)
    {
        clientId = servicePrincipalId;
        clientSecret = servicePrincipalSecret;
        resourceId = azureEnvironmentResourceId;
        tenantId = azureEnvironmentTenandId;
    }
```

Isso permitirá que você use os pacotes do NuGet do perfil de API para implantar seu aplicativo com êxito para o Azure Stack.

## <a name="define-azure-stack-environment-setting-functions"></a>Definir funções de configuração do ambiente do Azure Stack

Para autenticar a entidade de serviço para o ambiente do Azure Stack, use o seguinte código:

```csharp
private string AuthenticationToken { get; set; }
public override void InitializeServiceClient<T>(ServiceClient<T> client)
{
    var authenticationContext = new AuthenticationContext(String.Format(authenticationBase, tenantId));
    var credential = new ClientCredential(clientId, clientSecret);
    var result = authenticationContext.AcquireTokenAsync(resource: resourceId,
    clientCredential: credential).Result;
    if (result == null)
    {
        throw new InvalidOperationException("Failed to obtain the JWT token");
    }
    AuthenticationToken = result.AccessToken;
}
```

Isso substitui o cliente do serviço de inicialização para autenticar no Azure Stack.

## <a name="samples-using-api-profiles"></a>Exemplos usando perfis de API

Você pode usar os exemplos a seguir encontrados em repositórios do GitHub como uma referência para a criação de soluções com perfis do .NET e a API de pilha do Azure.

-   [Projeto de teste para a máquina Virtual, rede virtual, grupos de recursos e conta de armazenamento][]
-   Gerenciar máquinas virtuais usando o .NET

### <a name="sample-unit-test-project"></a>Projeto de teste de unidade de exemplo 

1.  Clone o repositório usando o seguinte comando:

    ```shell
    git clone https://github.com/Azure-Samples/hybrid-compute-dotnet-manage-vm.git
    ```

2.  Criar uma entidade de serviço e atribuir uma função para acessar a assinatura. Para obter instruções sobre como criar uma entidade de serviço, consulte [usar o Azure PowerShell para criar uma entidade de serviço com um certificado][].

3.  Recupere os valores necessários a seguir:

    1.  ID do locatário
    2.  ID do cliente
    3.  Segredo do cliente
    4.  ID da assinatura
    5.  Ponto de extremidade do Gerenciador de recursos

4.  Defina as seguintes variáveis de ambiente usando as informações recuperadas do serviço principal que você criou usando o prompt de comando:

    1.  Exportar AZURE_TENANT_ID = {sua id de locatário}
    2.  Exportar AZURE_CLIENT_ID = {sua id de cliente}
    3.  Exportar AZURE_CLIENT_SECRET = {seu segredo de cliente}
    4.  Exportar AZURE_SUBSCRIPTION_ID = {sua id da assinatura}
    5.  Exportar ARM_ENDPOINT = {sua URL do Gerenciador de recursos do Azure Stack}

   No Windows, use **definir** em vez de **exportar**.

5.  Certifique-se de que a variável local é definida para o local do Azure Stack. Por exemplo, LOCAL = "local".

6.  Defina as credenciais de logon personalizada que permitirá que você se autenticar no Azure Stack. Observe que essa parte do código é incluído nesse exemplo na pasta de autorização.

   ```csharp
   public class CustomLoginCredentials : ServiceClientCredentials
   {
       private string clientId;
       private string clientSecret;
       private string resourceId;
       private string tenantId;
       private const string authenticationBase = "https://login.windows.net/{0}";
       public CustomLoginCredentials(string servicePrincipalId, string servicePrincipalSecret, string azureEnvironmentResourceId, string azureEnvironmentTenandId)
       {
           clientId = servicePrincipalId;
           clientSecret = servicePrincipalSecret;
           resourceId = azureEnvironmentResourceId;
           tenantId = azureEnvironmentTenandId;
       }
   private string AuthenticationToken { get; set; }
   ```

7.  Adicione o código a seguir se você estiver usando o Azure Stack para substituir o cliente do serviço de inicialização para autenticar no Azure Stack. Observe que uma parte do código já está incluída nesse exemplo na pasta de autorização.

   ```csharp
   public override void InitializeServiceClient<T>(ServiceClient<T> client)
   {
      var authenticationContext = new AuthenticationContext(String.Format(authenticationBase, tenantId));
      var credential = new ClientCredential(clientId, clientSecret);
      var result = authenticationContext.AcquireTokenAsync(resource: resourceId,
                clientCredential: credential).Result;
      if (result == null)
      {
          throw new InvalidOperationException("Failed to obtain the JWT token");
      }
      AuthenticationToken = result.AccessToken;
   }
   ```
 
8.  Usando o Gerenciador de pacotes do NuGet, pesquise por "2018-03-01-hybrid" e instale os pacotes associados a esse perfil para os provedores de recursos de computação, rede, armazenamento, serviços de aplicativo e KeyVault.

2.  Dentro de cada tarefa no arquivo. cs, defina os parâmetros que são necessárias para trabalhar com o Azure Stack. Um exemplo é mostrado a seguir para a tarefa `CreateResourceGroupTest`:

   ```csharp
   var location = Environment.GetEnvironmentVariable("AZURE_LOCATION");
   var baseUriString = Environment.GetEnvironmentVariable("AZURE_BASE_URL");
   var resourceGroupName = Environment.GetEnvironmentVariable("AZURE_RESOURCEGROUP");
   var servicePrincipalId = Environment.GetEnvironmentVariable("AZURE_CLIENT_ID");
   var servicePrincipalSecret = Environment.GetEnvironmentVariable("AZURE_CLIENT_SECRET");
   var azureResourceId = Environment.GetEnvironmentVariable("AZURE_RESOURCE_ID");
   var tenantId = Environment.GetEnvironmentVariable("AZURE_TENANT_ID");
   var subscriptionId = Environment.GetEnvironmentVariable("AZURE_SUBSCRIPTION_ID");
   var credentials = new CustomLoginCredentials(servicePrincipalId, servicePrincipalSecret, azureResourceId, tenantId);
   ```

1.  Clique com botão direito em cada tarefa e selecione **executar teste**.

    1.  As marcas de seleção verdes na janela do painel do lado alertarão-lo de que cada tarefa foi criada com êxito com base nos parâmetros fornecidos. Verifique se sua assinatura do Azure Stack para garantir que os recursos foram criados com êxito.

    2.  Para obter mais informações sobre como executar testes de unidade, consulte [Execute testes de unidade com Gerenciador de testes.][]

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
  [* a pilha do Azure resource manager ponto de extremidade *]: ../user/azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint
  [Resumo dos perfis de API]: ../user/azure-stack-version-profiles.md#summary-of-api-profiles
  [Projeto de teste para a máquina Virtual, rede virtual, grupos de recursos e conta de armazenamento]: https://github.com/seyadava/azure-sdk-for-net-samples/tree/master/TestProject
  [Usar o Azure PowerShell para criar uma entidade de serviço com um certificado]: ../azure-stack-create-service-principals.md
  [Execute testes de unidade com Gerenciador de testes.]: /visualstudio/test/run-unit-tests-with-test-explorer?view=vs-2017
