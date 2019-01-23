---
title: Usando perfis de versão de API com o GO no Azure Stack | Microsoft Docs
description: Saiba mais sobre como usar perfis de versão de API com GO no Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2019
ms.author: sethm
ms.reviewer: sijuman
ms.openlocfilehash: 36496dd80111689b510bbbd27edc35a454d230a3
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54448285"
---
# <a name="use-api-version-profiles-with-go-in-azure-stack"></a>Usar perfis de versão de API com o Go no Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

## <a name="go-and-version-profiles"></a>Perfis de ir e versão

Um perfil é uma combinação de diferentes tipos de recursos com diferentes versões de serviços diferentes. Usando um perfil de ajuda a você misturar e combinar entre diferentes tipos de recursos. Perfis podem fornecer os seguintes benefícios:

- Estabilidade do aplicativo, bloqueando a versões específicas de API.
- Compatibilidade do aplicativo com o Azure Stack e regionais datacenters do Azure.

No SDK Go, perfis estão disponíveis sob o caminho de perfis, com a versão na **AAAA-MM-DD** formato. Agora, é a versão mais recente do perfil de API do Azure Stack **2017-03-09**. Para importar um determinado serviço de um perfil, importe o módulo correspondente do perfil. Por exemplo, para importar **Compute** partir **2017-03-09** perfil, use o seguinte código:

````go
import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/compute/mgmt/compute"
````

## <a name="install-azure-sdk-for-go"></a>Instale o SDK do Azure para linguagem Go

1. Instale o Git. Para obter instruções, consulte [Introdução - instalação do Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
2. Instalar o [linguagem de programação Go](https://golang.org/dl). Perfis de API do Azure exigem Go versão 1.9 ou mais recente.
3. Instale o SDK do ir do Azure e suas dependências, executando o seguinte comando do bash:

   ```bash
   go get -u -d github.com/Azure/azure-sdk-for-go/...
   ```

### <a name="the-go-sdk"></a>O SDK do Go

Você pode encontrar mais informações sobre o SDK do ir do Azure nos seguintes links:

- O Azure ir SDK na [instalar o SDK do Azure para linguagem Go](/azure/azure-sdk-go-install).
- O SDK do ir do Azure está disponível publicamente no GitHub na [sdk do azure para go](https://github.com/Azure/azure-sdk-for-go) repositório.

### <a name="go-autorest-dependencies"></a>Dependências do go-AutoRest

O SDK Go depende do Azure **AutoRest Go** módulos para enviar solicitações REST para pontos de extremidade do Azure Resource Manager. Você deve importar do Azure **Go AutoRest** as dependências do módulo do [ir Azure-AutoRest no GitHub](https://github.com/Azure/go-autorest). Você pode encontrar os comandos de bash de instalação nos **instalar** seção.

## <a name="how-to-use-go-sdk-profiles-on-azure-stack"></a>Como usar perfis de SDK Go no Azure Stack

Para executar um exemplo de código Go no Azure Stack, siga estas etapas:

1. Instale o SDK do Azure para Go e suas dependências. Para obter instruções, consulte a seção anterior, [instalar o SDK do Azure para Go](#install-azure-sdk-for-go).
2. Obtenha as informações de metadados do ponto de extremidade do Gerenciador de recursos. O ponto de extremidade retorna um arquivo JSON com as informações necessárias para executar seu código Go.

   > [!NOTE]  
   > O **ResourceManagerUrl** no Azure Stack desenvolvimento ASDK (Kit) é: `https://management.local.azurestack.external/`  
   > O **ResourceManagerUrl** em sistemas integrados é: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/`  
   > Para recuperar os metadados necessários: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`
  
   Arquivo JSON de exemplo:

   ```json
   { "galleryEndpoint": "https://portal.local.azurestack.external:30015/",  
     "graphEndpoint": "https://graph.windows.net/",  
     "portal Endpoint": "https://portal.local.azurestack.external/",
     "authentication": {
       "loginEndpoint": "https://login.windows.net/",
       "audiences": ["https://management.<yourtenant>.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
     }
   }
   ```

3. Se não estiver disponível, crie uma assinatura e salve a ID da assinatura a ser usado posteriormente. Para obter informações sobre como criar uma assinatura, consulte [criar assinaturas de ofertas no Azure Stack](../azure-stack-subscribe-plan-provision-vm.md).

4. Criar entidade com um tipo de serviço **assinatura** escopo e **proprietário** função. Salve a ID da entidade de serviço e o segredo. Para obter informações sobre como criar uma entidade de serviço para o Azure Stack, consulte [criar entidade de serviço](azure-stack-create-service-principals.md#create-service-principal-for-azure-ad). Seu ambiente do Azure Stack agora está configurado.

5. Importe um módulo de serviço do perfil do SDK Go em seu código. É a versão atual do perfil do Azure Stack **2017-03-09**. Por exemplo, para importar o módulo de rede a partir da **2017-03-09** tipo de perfil, use o seguinte código:

   ```go
   package main
    import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"
   ```

6. Em sua função, criar e autenticar um cliente com um **New** chamada de função do cliente. Para criar um cliente de rede virtual, você pode usar o código a seguir:  

   ```go
   package main

   import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"

   func main() {
      vnetClient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
      vnetClient.Authorizer = autorest.NewBearerAuthorizer(token)
   ```

   Definir `<baseURI>` para o **ResourceManagerUrl** valor usado na etapa 2. Definir `<subscriptionID>` para o **SubscriptionID** valor salvo na etapa 3.

   Para criar o token, consulte a seção a seguir.  

7. Invoca métodos de API usando o cliente que você criou na etapa anterior. Por exemplo, para criar uma rede virtual usando o cliente da etapa anterior, consulte o exemplo a seguir:

   ```go
   package main

   import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"
   func main() {
   vnetClient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
   vnetClient .Authorizer = autorest.NewBearerAuthorizer(token)

   vnetClient .CreateOrUpdate( )
   ```

Para obter um exemplo completo de criação de uma rede virtual no Azure Stack usando o perfil de SDK Go, consulte o [exemplo](#example).

## <a name="authentication"></a>Autenticação

Para obter o **autorizador** propriedade do Azure Active Directory usando o SDK do Go, instale o **Go AutoRest** módulos. Esses módulos já devem ter sido instalados com a instalação "SDK Go"; Se não estiver, instale o [pacote de autenticação do GitHub](https://github.com/Azure/go-autorest/tree/master/autorest/adal).

O autorizador de deve ser definido como o autorizador de para o cliente do recurso. Há diferentes maneiras de obter tokens de autorizador no Azure Stack, usando as credenciais do cliente:

1. Se uma entidade de serviço com a função de proprietário na assinatura estiver disponível, ignore esta etapa. Caso contrário, crie uma [entidade de serviço](azure-stack-create-service-principals.md) e atribua a ele uma função de "proprietário" [no escopo à sua assinatura](azure-stack-create-service-principals.md#assign-the-service-principal-to-a-role). Salve o segredo e ID de entidade de segurança de aplicativo de serviço.

2. Importar o **adal** pacote do Go-AutoRest em seu código.

   ```go
   package main
   import "github.com/Azure/go-autorest/autorest/adal"
   ```

3. Criar uma **oauthConfig** usando o método NewOAuthConfig **adal** módulo.

   ```go
   package main

   import "github.com/Azure/go-autorest/autorest/ada1"

   func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
   }
   ```

   Definir `<activeDirectoryEndpoint>` ao valor da `loginEndpoint` propriedade do `ResourceManagerUrl` metadados recuperados na seção anterior deste documento. Defina o `<tenantID>` valor à sua ID de locatário do Azure Stack.

4. Por fim, crie um token da entidade de serviço usando o `NewServicePrincipalToken` método a partir de **adal** módulo:

   ```go
   package main

   import "github.com/Azure/go-autorest/autorest/adal"

   func CreateToken() (adal.OAuthTokenProvider, error) {
       var token adal.OAuthTokenProvider
       oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
       token, err = adal.NewServicePrincipalToken(
           *oauthConfig,
           clientID,
           clientSecret,
           activeDirectoryResourceID)
       return token, err
   ```

    Definir `<activeDirectoryResourceID>` para um dos valores na lista de "audience" a **ResourceManagerUrl** metadados recuperados na seção anterior deste artigo.
    Definir `<clientID>` para o aplicativo de serviço principal ID salvo quando a entidade de serviço foi criada na seção anterior deste artigo.
    Definir `<clientSecret>` o segredo do aplicativo principal serviço salvo quando a entidade de serviço foi criada na seção anterior deste artigo.

## <a name="example"></a>Exemplo

Este exemplo mostra um exemplo de código Go que cria uma rede virtual no Azure Stack. Para obter exemplos completos de SDK do Go, consulte o [repositório de exemplos do SDK do Azure acesse](https://github.com/Azure-Samples/azure-sdk-for-go-samples). Exemplos de pilha do Azure estão disponíveis no caminho híbrido dentro das pastas de serviço do repositório.

> [!NOTE]  
> Para executar o código neste exemplo, verifique se a assinatura usada tem o **rede** provedor de recursos listado como **registrado**. Para verificar, procure a assinatura no portal do Azure Stack e selecione **provedores de recursos.**

1. Importe os pacotes necessários em seu código. Use o perfil mais recente disponível no Azure Stack para importar o módulo de rede:

   ```go
   package main

   import (
       "context"
       "fmt"
       "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"
       "github.com/Azure/go-autorest/autorest"
       "github.com/Azure/go-autorest/autorest/adal"
       "github.com/Azure/go-autorest/autorest/to"
   )
   ```

2. Defina as variáveis de ambiente. Para criar uma rede virtual, você deve ter um grupo de recursos.

   ```go
   var (
       activeDirectoryEndpoint = "yourLoginEndpointFromResourceManagerUrlMetadata"
       tenantID = "yourAzureStackTenantID"
       clientID = "yourServicePrincipalApplicationID"
       clientSecret = "yourServicePrincipalSecret"
       activeDirectoryResourceID = "yourAudienceFromResourceManagerUrlMetadata"
       subscriptionID = "yourSubscriptionID"
       baseURI = "yourResourceManagerURL"
       resourceGroupName = "existingResourceGroupName"
   )
   ```

3. Agora que você definiu as variáveis de ambiente, adicione um método para criar um token de autenticação usando o **adal** pacote. Para obter mais informações sobre autenticação, consulte a seção anterior.

   ```go
   //CreateToken creates a service principal token
   func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
      token, err = adal.NewServicePrincipalToken(
          *oauthConfig,
          clientID,
          clientSecret,
          activeDirectoryResourceID)
      return token, err
   }
   ```

4. Adicionar o `main` método. O `main` pela primeira vez, o método obtém um token usando o método que é definido na etapa anterior. Em seguida, ele cria um cliente usando um módulo de rede do perfil. Por fim, ele cria uma rede virtual.

   ```go
   package main

   import (
      "context"
      "fmt"
      "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"
      "github.com/Azure/go-autorest/autorest"
      "github.com/Azure/go-autorest/autorest/adal"
      "github.com/Azure/go-autorest/autorest/to"
   )

   var (
      activeDirectoryEndpoint = "yourLoginEndpointFromResourceManagerUrlMetadata"
      tenantID = "yourAzureStackTenantID"
      clientID = "yourServicePrincipalApplicationID"
      clientSecret = "yourServicePrincipalSecret"
      activeDirectoryResourceID = "yourAudienceFromResourceManagerUrlMetadata"
     subscriptionID = "yourSubscriptionID"
     baseURI = "yourResourceManagerURL"
     resourceGroupName = "existingResourceGroupName"
   )

   //CreateToken creates a service principal token
   func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
      token, err = adal.NewServicePrincipalToken(
          *oauthConfig,
          clientID,
          clientSecret,
          activeDirectoryResourceID)
      return token, err
   }

   func main() {
      token, _ := CreateToken()
      vnetClient := network.NewVirtualNetworksClientWithBaseURI(baseURI, subscriptionID)
      vnetClient.Authorizer = autorest.NewBearerAuthorizer(token)
      future, _ := vnetClient.CreateOrUpdate(
          context.Background(),
          resourceGroupName,
          "sampleVnetName",
          network.VirtualNetwork{
              Location: to.StringPtr("local"),
              VirtualNetworkPropertiesFormat: &network.VirtualNetworkPropertiesFormat{
                  AddressSpace: &network.AddressSpace{
                      AddressPrefixes: &[]string{"10.0.0.0/8"},
                  },
                  Subnets: &[]network.Subnet{
                      {
                          Name: to.StringPtr("subnetName"),
                          SubnetPropertiesFormat: &network.SubnetPropertiesFormat{
                              AddressPrefix: to.StringPtr("10.0.0.0/16"),
                          },
                      },
                  },
              },
          })
      err := future.WaitForCompletion(context.Background(), vnetClient.Client)
      if err != nil {
          fmt.Printf(err.Error())
          return
      }
   }
   ```

## <a name="next-steps"></a>Próximas etapas

- [Instalar o PowerShell para o Azure Stack](azure-stack-powershell-install.md)
- [Configurar o ambiente do PowerShell do usuário do Azure Stack](azure-stack-powershell-configure-user.md)  
