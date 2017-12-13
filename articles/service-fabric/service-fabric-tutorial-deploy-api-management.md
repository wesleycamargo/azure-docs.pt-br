---
title: Integrar o Azure Service Fabric ao Gerenciamento de API | Microsoft Docs
description: Saiba como iniciar rapidamente com o Gerenciamento de API do Azure e o Service Fabric.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/10/2017
ms.author: ryanwi
ms.openlocfilehash: da1f2c3170aba9dc13d77a8729a98e7b655edea8
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2017
---
# <a name="deploy-api-management-with-service-fabric"></a>Implantar o Gerenciamento de API com o Service Fabric
Este tutorial é a parte quatro de uma série.  A implantação do Gerenciamento de API do Azure com o Service Fabric é um cenário avançado.  O Gerenciamento de API é útil quando você precisa publicar APIs com um conjunto avançado de regras de roteamento para os serviços do Service Fabric de back-end. Os aplicativos em nuvem geralmente precisam de um gateway front-end para fornecer um ponto de entrada único para usuários, dispositivos ou outros aplicativos. No Service Fabric, um gateway pode ser qualquer serviço sem estado projetado para entrada de tráfego, como um aplicativo APP.NET Core, Hubs de Eventos, Hub IoT ou Gerenciamento de API do Azure. 

Este tutorial mostra como configurar o [Gerenciamento de API do Azure](../api-management/api-management-key-concepts.md) com o Service Fabric para rotear o tráfego para um serviço de back-end no Service Fabric.  Quando terminar, você terá implantado o Gerenciamento de API para uma VNET e configurado uma operação de API para enviar tráfego para serviços sem monitoração de estado de back-end. Para saber mais sobre os cenários do Gerenciamento de API do Azure com Service Fabric, consulte o artigo [visão geral](service-fabric-api-management-overview.md).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Implantar o Gerenciamento de API
> * Configurar o Gerenciamento de API
> * Criar uma operação de API
> * Configure uma política de back-end
> * Adicionar a API a um produto

Nesta série de tutoriais, você aprenderá a:
> [!div class="checklist"]
> * Criar um [cluster do Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) ou [cluster do Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md) seguro no Azure usando um modelo
> * [Reduzir ou escalar um cluster horizontalmente](/service-fabric-tutorial-scale-cluster.md)
> * [Atualizar o tempo de execução de um cluster](service-fabric-tutorial-upgrade-cluster.md)
> * Implantar o Gerenciamento de API com o Service Fabric

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial:
- Se você não tem uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Instale o [módulo do Azure PowerShell versão 4.1 ou superior](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) ou [CLI 2.0 do Azure](/cli/azure/install-azure-cli).
- Criar um [cluster do Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) ou [cluster do Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md) seguro no Azure
- Se você implantar um cluster do Windows, configure um ambiente de desenvolvimento do Windows. Instale as cargas de trabalho do [Visual Studio 2017](http://www.visualstudio.com) e o **Desenvolvimento do Azure**, de **desenvolvimento ASP.NET e Web** e de **desenvolvimento multiplataforma do .NET Core**.  Em seguida, configure um [ambiente de desenvolvimento .NET](service-fabric-get-started.md).
- Se você implantar um cluster do Linux, configure um ambiente de desenvolvimento Java em [Linux](service-fabric-get-started-linux.md) ou [MacOS](service-fabric-get-started-mac.md).  Instale a [CLI do Service Fabric](service-fabric-cli.md). 

## <a name="network-topology"></a>Topologia de rede
Agora que você tem um [cluster do Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) ou um [cluster do Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md) seguro no Azure, implante o Gerenciamento de API na VNET (rede virtual) na sub-rede e no NSG designados para o Gerenciamento de API. Para este tutorial, o modelo do Resource Manager do Gerenciamento de API é pré-configurado para usar os nomes da VNET, da sub-rede e do NSG configurados no [tutorial do cluster do Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) ou no [tutorial do cluster do Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md) anterior. Este tutorial implanta a seguinte topologia para o Azure, na qual o Gerenciamento de API e o Service Fabric estão em sub-redes da mesma Rede Virtual:

 ![Legenda de imagem][sf-apim-topology-overview]

## <a name="sign-in-to-azure-and-select-your-subscription"></a>Entre no Azure e selecione sua assinatura
Entre na sua conta do Azure e selecione sua assinatura antes de executar comandos do Azure.

```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

```azurecli
az login
az account set --subscription <guid>
```

## <a name="deploy-a-service-fabric-back-end-service"></a>Implantar um serviço de back-end do Service Fabric

Antes de configurar o gerenciamento de API para rotear o tráfego para um serviço de back-end do Service Fabric, primeiro é necessário um serviço em execução para aceitar solicitações.  Se você já tiver criado um [cluster do Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md), implante um serviço do Service Fabric .NET.  Se você já tiver criado um [cluster do Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md), implante um serviço do Service Fabric Java.

### <a name="deploy-a-net-service-fabric-service"></a>Implantar um serviço do Service Fabric .NET

Para este tutorial, criamos um Serviço Confiável do ASP.NET Core básico sem monitoração de estado utilizando o modelo de projeto de API Web padrão. Isso cria um ponto de extremidade HTTP para o seu serviço, que será exposto por meio do Gerenciamento de API do Azure.

Inicie o Visual Studio como administrador e crie um serviço ASP.NET Core:

 1. No Visual Studio, selecione Arquivo -> Novo projeto.
 2. Selecione o modelo de Aplicativo do Service Fabric em Nuvem e nomeie-o **"ApiApplication"**.
 3. Selecione o modelo de serviço do ASP.NET Core e nomeie o projeto **"WebApiService"**.
 4. Selecione o modelo de projeto ASP.NET Core 1.1 da API Web.
 5. Uma vez que o projeto é criado, abra `PackageRoot\ServiceManifest.xml` e remova o atributo `Port` da configuração do recurso do ponto de extremidade:
 
    ```xml
    <Resources>
      <Endpoints>
        <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" />
      </Endpoints>
    </Resources>
    ```

    A remoção da porta permite que o Service Fabric especifique uma porta dinamicamente do intervalo de portas do aplicativo, abertas através do Grupo de Segurança de Rede no modelo do Resource Manager do cluster, permitindo que o tráfego flua para o Gerenciamento de API.
 
 6. Pressione F5 no Visual Studio para verificar se a API Web está disponível localmente. 

    Abra o Service Fabric Explorer e faça busca detalhada de uma instância específica do serviço ASP.NET Core para visualizar o endereço básico no qual o serviço está ouvindo. Adicione `/api/values` ao endereço de base e abra-o em um navegador, o que invoca o método Get em ValuesController no modelo de API Web. Ele retorna a resposta padrão fornecida pelo modelo, uma matriz JSON que contém duas cadeias de caracteres:

    ```json
    ["value1", "value2"]`
    ```

    Este é o ponto de extremidade que você expõe pelo Gerenciamento de API no Azure.

 7. Finalmente, implante o aplicativo em seu cluster no Azure. No Visual Studio, clique com o botão direito do mouse no projeto de Aplicativo e selecione **Publicar**. Forneça o seu ponto de extremidade de cluster (por exemplo, `mycluster.southcentralus.cloudapp.azure.com:19000`) para implantar o aplicativo em seu cluster do Service Fabric no Azure.

Um serviço sem estado ASP.NET Core nomeado `fabric:/ApiApplication/WebApiService` agora deve estar em execução no seu cluster do Service Fabric no Azure.

### <a name="create-a-java-service-fabric-service"></a>Criar um serviço do Service Fabric Java
Para este tutorial, implantamos um servidor Web básico que exiba mensagens de volta para o usuário. O aplicativo de exemplo do servidor de eco contém um ponto de extremidade HTTP para o seu serviço, que é exposto por meio do Gerenciamento de API do Azure.

1. Clone os exemplos de introdução a Java.

   ```bash
   git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git
   cd service-fabric-java-getting-started
   ```

2. Edite *Services/EchoServer/EchoServer1.0/EchoServerApplication/EchoServerPkg/ServiceManifest.xml*. Atualize o ponto de extremidade para que o serviço escute na porta 8081.

   ```xml
   <Endpoint Name="WebEndpoint" Protocol="http" Port="8081" />
   ```

3. Salve *ServiceManifest.xml*, então crie o aplicativo EchoServer1.0.

   ```bash
   cd Services/EchoServer/EchoServer1.0/
   gradle
   ```

4. Implante o aplicativo no cluster.

   ```bash
   cd Scripts
   sfctl cluster select --endpoint http://mycluster.southcentralus.cloudapp.azure.com:19080
   ./install.sh
   ```

   Um serviço sem estado Java chamado `fabric:/EchoServerApplication/EchoServerService` agora deve estar em execução no seu cluster do Service Fabric no Azure.

5. Abra um navegador e digite no http://mycluster.southcentralus.cloudapp.azure.com:8081/getMessage, você deverá ver "[versão 1.0]Olá, Mundo!!!" exibido.

## <a name="download-and-understand-the-resource-manager-template"></a>Baixar e compreender o modelo do Resource Manager
Baixe e salve o seguinte modelo do Resource Manager e arquivo de parâmetros:
 
- [apim.json][apim-arm]
- [apim.parameters.json][apim-parameters-arm]

As seções a seguir descrevem os recursos que estão sendo definidos pelo modelo *apim.json*. Para saber mais, siga os links da documentação de referência de modelo em cada seção. Os parâmetros configuráveis definidos no arquivo de parâmetros *apim.parameters.json* são definidos mais adiante neste artigo.

### <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service
O [Microsoft.ApiManagement/service](/azure/templates/microsoft.apimanagement/service) descreve a instância do serviço de gerenciamento de API: nome, SKU ou camada, local do grupo de recursos, informações do editor e rede virtual.

### <a name="microsoftapimanagementservicecertificates"></a>Microsoft.ApiManagement/service/certificates
[Microsoft.ApiManagement/service/certificates](/azure/templates/microsoft.apimanagement/service/certificates) configura a segurança do gerenciamento de API. O Gerenciamento de API deve autenticar com seu cluster do Service Fabric para descoberta de serviço utilizando um certificado do cliente que tenha acesso ao seu cluster. Este tutorial usa o mesmo certificado especificado anteriormente ao criar o [cluster do Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md#createvaultandcert_anchor) ou o [cluster do Linux](service-fabric-tutorial-create-vnet-and-linux-cluster.md#createvaultandcert_anchor), que, por padrão, pode ser usado para acessar seu cluster. 

Este tutorial usa o mesmo certificado para autenticação de cliente e segurança de nó a nó de cluster. É possível utilizar um certificado do cliente separado se você tiver um configurado para acessar seu cluster do Service Fabric. Forneça o **nome**, a **senha** e os **dados** (cadeia de caracteres codificada em base 64) do arquivo de chave privada (.pfx) do certificado de cluster que você especificou ao criar o cluster do Service Fabric.

### <a name="microsoftapimanagementservicebackends"></a>Microsoft.ApiManagement/service/backends
[Microsoft.ApiManagement/service/backends](/azure/templates/microsoft.apimanagement/service/backends) descreve o serviço de back-end para o qual o tráfego é encaminhado. 

Para back-ends do Service Fabric, o cluster do Service Fabric é o back-end, em vez de um serviço do Service Fabric específico. Isso permite que uma única política encaminhe para mais de um serviço no cluster. O campo **url** neste exemplo é um nome de serviço totalmente qualificado de um serviço em seu cluster ao qual todas as solicitações serão roteadas por padrão se nenhum nome de serviço for especificado em uma política de back-end. É possível utilizar um nome de serviço falso, como "fabric:/serviço/falso" se você não pretende ter um serviço de fallback. **resourceId** especifica o ponto de extremidade de gerenciamento de cluster.  **clientCertificateThumbprint** e **serverCertificateThumbprints** identificam certificados usados para autenticação com o cluster.

### <a name="microsoftapimanagementserviceproducts"></a>Microsoft.ApiManagement/service/products
[Microsoft.ApiManagement/service/products](/azure/templates/microsoft.apimanagement/service/products) cria um produto. No Gerenciamento de API, um produto contém uma ou mais APIs, bem como uma quota de uso e os termos de uso. Uma vez publicado o produto, os desenvolvedores podem assinar o produto e começar a usar as APIs dele. 

Insira um **displayName** descritivo e uma **descrição** para o produto. Para este tutorial, é necessária uma assinatura, mas não a aprovação por um administrador de assinatura.  Este **estado** de produto é "publicado" e é visível para os assinantes. 

### <a name="microsoftapimanagementserviceapis"></a>Microsoft.ApiManagement/service/apis
[Microsoft.ApiManagement/service/apis](/azure/templates/microsoft.apimanagement/service/apis) cria uma API. Uma API no Gerenciamento de API representa um conjunto de operações que pode ser invocado pelas aplicações clientes. Após as operações serem adicionadas, a API é adicionada a um produto e pode ser publicada. Após uma API ser publicada, ela pode ser usada e assinada por desenvolvedores.

- **displayName** pode ser qualquer nome para a API. Para este tutorial, use "Aplicativo do Service Fabric".
- **name** fornece um nome exclusivo e descritivo para a API, como "service-fabric-app". Esse nome será exibido nos portais do desenvolvedor e do editor. 
- **serviceUrl** faz referência ao serviço HTTP que está implementando a API. O gerenciamento de API envia as solicitações para esse endereço. Para back-ends do Service Fabric, esse valor de URL não é usado. Aqui, você pode colocar qualquer valor. Para este tutorial, por exemplo, "http://servicefabric". 
- **path** é acrescentado à URL base para o serviço de gerenciamento de API. A URL base é comum para todas as APIs hospedadas por uma instância de um serviço de Gerenciamento de API. O Gerenciamento de API diferencia as APIs pelo sufixo e, portanto, o sufixo deve ser único para cada API para um editor específico. 
- **protocols** determina quais protocolos podem ser usados para acessar a API. Para este tutorial, liste **http** e **https**.
- **path** é um sufixo para a API. Para este tutorial, use “myapp”.

### <a name="microsoftapimanagementserviceapisoperations"></a>Microsoft.ApiManagement/service/apis/operations
[Microsoft.ApiManagement/service/apis/operations](/azure/templates/microsoft.apimanagement/service/apis/operations) para que uma API de gerenciamento de API possa ser usada, as operações devem ser adicionadas à API.  Clientes externos usam uma operação para se comunicar com o serviço sem monitoração de estado do ASP.NET Core em execução no cluster do Service Fabric.

Para adicionar uma operação de API de front-end, preencha os valores:

- **displayName** e **description** descrevem a operação. Para este tutorial, use “Values”.
- **method** especifica o verbo HTTP.  Para este tutorial, especifique **GET**.
- **urlTemplate** é acrescentado à URL base da API e identifica uma única operação HTTP.  Para este tutorial, use `/api/values` se você tiver adicionado o serviço de back-end .NET ou `getMessage` se você tiver adicionado o serviço de back-end Java.  Por padrão, o caminho URL especificado aqui é o caminho URL enviado ao serviço do Service Fabric de back-end. Se você usar o mesmo caminho URL aqui que seu serviço usa, como "/api/values", a operação funcionará sem modificações adicionais. Também é possível especificar um caminho de URL diferente do caminho de URL utilizado pelo serviço do Service Fabric de back-end, caso em que também será necessário especificar uma reescrita do caminho na sua política de operação mais tarde.

### <a name="microsoftapimanagementserviceapispolicies"></a>Microsoft.ApiManagement/service/apis/policies
[Microsoft.ApiManagement/service/apis/policies](/azure/templates/microsoft.apimanagement/service/apis/policies) cria uma política de back-end, que reúne tudo. Dessa forma, o serviço do Service Fabric de back-end é configurado de acordo com as solicitações roteadas. É possível aplicar essa política a qualquer operação de API.  Para saber mais, confira [Visão geral das políticas](/azure/api-management/api-management-howto-policies). 

A [configuração de back-end para o Service Fabric](/azure/api-management/api-management-transformation-policies#SetBackendService) fornece os seguintes controles de roteamento de solicitação: 
 - Seleção de instância de serviço, especificando um nome de instância de serviço do Service Fabric, codificado (por exemplo, `"fabric:/myapp/myservice"`) ou gerado a partir da solicitação HTTP (por exemplo, `"fabric:/myapp/users/" + context.Request.MatchedParameters["name"]`).
 - Resolução de partição gerando uma chave de partição utilizando qualquer esquema de particionamento do Service Fabric.
 - Seleção de réplica para serviços com estado.
 - Condições de repetição de resolução que permitem especificar as condições para resolver novamente um local de serviço e reenviar uma solicitação.

**policyContent** é o conteúdo XML Json com escape da política.  Para este tutorial, crie uma política de back-end para rotear solicitações diretamente para o serviço sem monitoração de estado .NET ou Java implantado anteriormente. Adicione um política `set-backend-service` em políticas de entrada.  Substitua "service-name" por `fabric:/ApiApplication/WebApiService` se você tiver implantado o serviço de back-end .NET ou `fabric:/EchoServerApplication/EchoServerService` se tiver implantado o serviço Java.
    
```xml
<policies>
  <inbound>
    <base/>
    <set-backend-service 
        backend-id="servicefabric"
        sf-service-instance-name="service-name"
        sf-resolve-condition="@((int)context.Response.StatusCode != 200)" />
  </inbound>
  <backend>
    <base/>
  </backend>
  <outbound>
    <base/>
  </outbound>
</policies>
```

Para obter um conjunto completo de atributos de política de back-end do Service Fabric, consulte a [documentação de back-end do Gerenciamento de API](https://docs.microsoft.com/azure/api-management/api-management-transformation-policies#SetBackendService)

## <a name="set-parameters-and-deploy-api-management"></a>Definir parâmetros e implantar o gerenciamento de API
Preencha os seguintes parâmetros vazios em *apim.parameters.json* para a implantação. 

|Parâmetro|Valor|
|---|---|
|apimInstanceName|sf-apim|
|apimPublisherEmail|myemail@contosos.com|
|apimSku|Desenvolvedores|
|serviceFabricCertificateName|sfclustertutorialgroup320171031144217|
|certificatePassword|q6D7nN%6ck@6| 
|serviceFabricCertificateThumbprint|C4C1E541AD512B8065280292A8BA6079C3F26F10 |
|serviceFabricCertificate|&lt;base-64 encoded string&gt;|
|url_path|/api/values|
|clusterHttpManagementEndpoint|https://mysfcluster.southcentralus.cloudapp.azure.com:19080|
|inbound_policy|&lt;Cadeia de caracteres XML&gt;|

*certificatePassword* e *serviceFabricCertificateThumbprint* devem coincidir com o certificado de cluster usado para configurar o cluster.  

*serviceFabricCertificate* é o certificado como uma cadeia de caracteres codificada em base 64, que pode ser gerada usando o seguinte script:

```powershell
$bytes = [System.IO.File]::ReadAllBytes("C:\mycertificates\sfclustertutorialgroup220171109113527.pfx");
$b64 = [System.Convert]::ToBase64String($bytes);
[System.Io.File]::WriteAllText("C:\mycertificates\sfclustertutorialgroup220171109113527.txt", $b64);
```

Em *inbound_policy*, substitua "service-name" por `fabric:/ApiApplication/WebApiService` se você tiver implantado o serviço de back-end .NET ou por `fabric:/EchoServerApplication/EchoServerService` se você tiver implantado o serviço Java.

```xml
<policies>
  <inbound>
    <base/>
    <set-backend-service 
        backend-id="servicefabric"
        sf-service-instance-name="service-name"
        sf-resolve-condition="@((int)context.Response.StatusCode != 200)" />
  </inbound>
  <backend>
    <base/>
  </backend>
  <outbound>
    <base/>
  </outbound>
</policies>
```

Use o seguinte script para implantar o modelo do Resource Manager e os arquivos de parâmetro para o Gerenciamento de API:

```powershell
$ResourceGroupName = "sfclustertutorialgroup"
New-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile .\apim.json -TemplateParameterFile .\apim.parameters.json -Verbose
```

```azurecli
ResourceGroupName="sfclustertutorialgroup"
az group deployment create --name ApiMgmtDeployment --resource-group $ResourceGroupName --template-file apim.json --parameters @apim.parameters.json 
```

## <a name="test-it"></a>Testá-lo

Agora é possível tentar enviar uma solicitação para seu serviço de back-end no Service Fabric através do Gerenciamento de API diretamente do [portal do Azure](https://portal.azure.com).

 1. No serviço de Gerenciamento de API, selecione **API**.
 2. Na API do **aplicativo do Service Fabric** criada nas etapas anteriores, selecione a guia **Testar** e, em seguida, a operação **Valores**.
 3. Clique no botão **Enviar** para enviar uma solicitação de teste para o serviço de back-end.  Você deve ver uma resposta HTTP semelhante a:

    ```http
    HTTP/1.1 200 OK

    Transfer-Encoding: chunked

    Content-Type: application/json; charset=utf-8

    Vary: Origin

    Access-Control-Allow-Origin: https://apimanagement.hosting.portal.azure.net

    Access-Control-Allow-Credentials: true

    Access-Control-Expose-Headers: Transfer-Encoding,Date,Server,Vary,Ocp-Apim-Trace-Location

    Ocp-Apim-Trace-Location: https://apimgmtstuvyx3wa3oqhdbwy.blob.core.windows.net/apiinspectorcontainer/RaVVuJBQ9yxtdyH55BAsjQ2-1?sv=2015-07-08&sr=b&sig=Ab6dPyLpTGAU6TdmlEVu32DMfdCXTiKAASUlwSq3jcY%3D&se=2017-09-15T05%3A49%3A53Z&sp=r&traceId=ed9f1f4332e34883a774c34aa899b832

    Date: Thu, 14 Sep 2017 05:49:56 GMT


    [
    "value1",
    "value2"
    ]
    ```

## <a name="clean-up-resources"></a>Limpar recursos

Um cluster é composto por outros recursos do Azure, além do próprio recurso do cluster. A maneira mais simples de excluir o cluster e todos os recursos que ele consume é excluir o grupo de recursos.

Faça logon no Azure e selecione a ID de assinatura com a qual você deseja remover o cluster.  Você pode encontrar sua ID de assinatura fazendo logon no [Portal do Azure](http://portal.azure.com). Exclua o grupo de recursos e todos os recursos de cluster usando o [cmdlet Remove-AzureRMResourceGroup](/en-us/powershell/module/azurerm.resources/remove-azurermresourcegroup).

```powershell
$ResourceGroupName = "sfclustertutorialgroup"
Remove-AzureRmResourceGroup -Name $ResourceGroupName -Force
```

```azurecli
ResourceGroupName="sfclustertutorialgroup"
az group delete --name $ResourceGroupName
```

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Implantar o Gerenciamento de API
> * Configurar o Gerenciamento de API
> * Criar uma operação de API
> * Configure uma política de back-end
> * Adicionar a API a um produto

[azure-powershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/

[apim-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/apim.json
[apim-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/apim.parameters.json

[network-arm]: https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.json
[network-parameters-arm]: https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.parameters.json

[cluster-arm]: https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.json
[cluster-parameters-arm]: https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.parameters.json

<!-- pics -->
[sf-apim-topology-overview]: ./media/service-fabric-tutorial-deploy-api-management/sf-apim-topology-overview.png
