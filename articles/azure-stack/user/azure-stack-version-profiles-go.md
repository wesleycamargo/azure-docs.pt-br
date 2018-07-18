---
title: Usando perfis de versão de API com o GO na pilha do Azure | Microsoft Docs
description: Saiba mais sobre como usar perfis de versão de API com GO na pilha do Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: dd2d0c46c0829a73d32c96b506b9f2111eda3c84
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
ms.locfileid: "34010057"
---
# <a name="use-api-version-profiles-with-go-in-azure-stack"></a>Use perfis de versão de API com Go na pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

## <a name="go-and-version-profiles"></a>Perfis go e versão

Um perfil é uma combinação de diferentes tipos de recursos com versões diferentes de serviços diferentes. Usando um perfil o ajudará a combinação de entre diferentes tipos de recursos. Podem fornecer perfis:

 - Estabilidade de seu aplicativo por meio do bloqueio para versões específicas de API.
 - Compatibilidade do seu aplicativo com a pilha do Azure e datacenters do Azure regional.

No SDK vá perfis estão disponíveis nos perfis / caminho, com a versão no **AAAA-MM-DD** formato. Agora, a pilha do Azure mais recente versão do perfil é **2017-03-09**. Para importar um determinado serviço de um perfil, você precisa importar o módulo correspondente do perfil. Por exemplo, para importar **de computação** serviço **2017-03-09** perfil:

````go
import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/compute/mgmt/compute" 
````

## <a name="install-azure-sdk-for-go"></a>Instalar o SDK do Azure para ir

  1. Instale o Git. Para obter instruções, consulte [Introdução - Instalando Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
  2. Instalar o [ir a linguagem de programação](https://golang.org/dl).  
  Perfis de API do Azure exige ir versão 1,9 ou mais recente.
  3. Instale o SDK do Azure vá e suas dependências, executando o seguinte comando bash:
  ```
    go get -u -d github.com/Azure/azure-sdk-for-go/...
  ```

### <a name="the-go-sdk"></a>O SDK VÁ

Você pode encontrar mais informações sobre o SDK do Azure VÁ em:
- O Azure vá SDK em [instalar o SDK do Azure para ir](https://docs.microsoft.com/go/azure/azure-sdk-go-install).
- O SDK do Azure ir está disponível publicamente no GitHub em [azure sdk para ir](https://github.com/Azure/azure-sdk-for-go).

### <a name="go-autorest-dependencies"></a>Dependências de go AutoRest

O SDK VÁ depende dos módulos do Azure Go-AutoRest para enviar solicitações REST para pontos de extremidade do Gerenciador de recursos do Azure. Você precisará importar as dependências do módulo Azure Go-AutoRest de [Azure Go-AutoRest no GitHub](https://github.com/Azure/go-autorest). Você pode localizar os comandos bash instalar o **instalar** seção.

## <a name="how-to-use-go-sdk-profiles-on-azure-stack"></a>Como usar perfis de SDK VÁ na pilha do Azure

Para executar um exemplo de código Go na pilha do Azure:
  1. Instale o SDK do Azure para ir e suas dependências. Para obter instruções, consulte a seção anterior, [instalar o SDK do Azure para ir](#install-azure-sdk-for-go).
  2. Obtenha as informações de metadados do ponto de extremidade do Gerenciador de recursos. O ponto de extremidade retorna um arquivo JSON com as informações necessárias para executar seu código Go.

  > [!Note]  
  > O **ResourceManagerUrl** no Kit de desenvolvimento de pilha para Azure (ASDK) é: `https://management.local.azurestack.external/`  
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

  3. Se não estiver disponível, crie uma assinatura e salve a ID de assinatura a ser usado posteriormente. Para obter informações sobre como criar uma assinatura, consulte [criar assinaturas para ofertas na pilha do Azure](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm). 
  4. Criar uma entidade de serviço com o escopo de "Assinatura" e **proprietário** função. Salve as entidades de serviço segredo e ID. Para obter informações sobre como criar uma entidade de serviço para a pilha do Azure, consulte [criar entidade de serviço](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#create-service-principal-for-azure-ad). O ambiente de pilha do Azure está configurado.
  5. Importe um módulo de serviço de perfil SDK vá em seu código. A versão atual do perfil de pilha do Azure é **2017-03-09**. Por exemplo, para importar o módulo de rede de **2017-03-09** tipo de perfil: 

  ````go
    package main 
    import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"
  ````
  
  6. Em sua função, criar e autenticar um cliente com um **novo** chamada de função do cliente. Para criar um cliente de rede virtual, você pode usar o código a seguir:  

  ````go
  package main 

  import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network" 

  func main() { 
      vnetClient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
      vnetClient.Authorizer = autorest.NewBearerAuthorizer(token)
  ````

  Definir `<baseURI>` para o **ResourceManagerUrl** valor usado na etapa 2.
  Definir `<subscriptionID>` para o **SubscriptionID** valor salvou da etapa três.
  Para criar um token, consulte autenticação seção abaixo.  

  7. Invocar métodos de API usando o cliente que você criou na etapa anterior. Por exemplo, para criar uma rede virtual por meio de nosso cliente da etapa anterior: 
  
````go
package main

import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network" 
func main() { 
  vnetC1ient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
  vnetClient .Authorizer = autorest.NewBearerAuthorizer(token)

  vnetClient .CreateOrUpdate( ) 
````
  
  Para obter um exemplo completo de criação de uma rede virtual na pilha do Azure usando o perfil vá SDK, consulte [exemplo](#example).

## <a name="authentication"></a>Autenticação

Para obter a propriedade autorizador do Active Directory do Azure usando o SDK vá, instale os módulos de AutoRest Go. Esses módulos já devem ter sido instalados com a instalação "SDK ir"; Se não estiver, instale o [pacote de autenticação no GitHub](https://github.com/Azure/go-autorest/tree/master/autorest/adal).

O autorizador deve ser definido como o autorizador para o cliente do recurso. Há métodos diferentes para obter um autorizador; para uma lista completa, consulte aqui.

Esta seção apresenta uma forma comum de obter tokens autorizador na pilha do Azure usando as credenciais do cliente:

  1. Se uma entidade de serviço com a função de proprietário na assinatura estiver disponível, ignore esta etapa. Caso contrário, crie uma entidade de serviço [instruções]( https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals) e atribuí-la uma função de "proprietário" no escopo à sua assinatura [instruções]( https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#assign-role-to-service-principal). Salve o segredo e ID de aplicativo principal do serviço. 

  2. Importação **adal** pacote de AutoRest ir em seu código. 
  
  ````go
  package main
  import "github.com/Azure/go-autorest/autorest/adal" 
  ````

  3. Criar um oauthConfig usando o método NewOAuthConfig de **adal** módulo. 
  
  ````go
  package main 

  import "github.com/Azure/go-autorest/autorest/ada1" 

  func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
  ````
   
  Definir `<activeDirectoryEndpoint>` como o valor de "loginEndpoint" propriedade dos metadados ResourceManagerUrl recuperado na seção anterior deste documento.
  Definir `<tenantID>` valor para o ID de locatário de pilha do Azure. 

  4. Finalmente, crie um token da entidade de serviço usando o método NewServicePrincipalToken do módulo adal. 

  ````go
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
  ````
  
  Definir `<activeDirectoryResourceID>` para um dos valores no "público" lista dos metadados ResourceManagerUrl recuperado na seção anterior deste documento.  
  Defina `<clientID>` para o aplicativo de serviço principal ID salvo quando a entidade de serviço foi criada na seção anterior deste documento.  
  Defina `<clientSecret>` para o aplicativo de serviço principal segredo salvo quando a entidade de serviço foi criada na seção anterior deste documento.  

## <a name="example"></a>Exemplo

Esta seção mostra um exemplo de código de ir para criar rede virtual na pilha do Azure. Para obter exemplos de conclusão do SDK vá consulte [repositório de exemplos do SDk do Azure vá](https://github.com/Azure-Samples/azure-sdk-for-go-samples). Exemplos de pilha do Azure estão disponíveis em híbrida / caminho dentro das pastas de serviço do repositório.

> [!Note]  
> Para executar o código neste exemplo, verifique se a assinatura usada tem **rede** provedor de recursos listado como **registrado**. Para verificar se ela, procure a assinatura no portal do Azure pilha e clique em **provedores de recursos.**

1. Importe os pacotes necessários em seu código. Você deve usar o perfil mais recente na pilha do Azure para importar o módulo de rede. 
  
  ````go
  package main

  import (
      "context"
      "fmt"
      "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"
      "github.com/Azure/go-autorest/autorest"
      "github.com/Azure/go-autorest/autorest/adal"
      "github.com/Azure/go-autorest/autorest/to"
  )
  ````

2. Defina as variáveis de ambiente. Para criar uma rede virtual, você precisa ter um grupo de recursos. 

  ````go
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
  ````

3. Agora que você tiver definido as variáveis de ambiente, adicione um método para criar o token de autenticação usando **adal** pacote. Consulte os detalhes sobre a autenticação na seção anterior.
  
  ````go
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
  ````

4. Adicione o método principal. O método principal primeiro obtém um token usando o método que é definido na etapa anterior. Em seguida, ele cria um cliente usando o módulo de rede do perfil. Por fim, ele cria uma rede virtual. 
  
````go
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

````

## <a name="next-steps"></a>Próximas etapas
* [Instalar o PowerShell para o Azure Stack](azure-stack-powershell-install.md)
* [Configurar o ambiente do PowerShell do usuário a pilha do Azure](azure-stack-powershell-configure-user.md)  
