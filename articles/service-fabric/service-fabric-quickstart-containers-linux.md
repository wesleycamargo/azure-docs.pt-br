---
title: "Criar um aplicativo de contêiner do Azure Service Fabric no Linux | Microsoft Docs"
description: "Crie seu primeiro aplicativo de contêiner do Linux no Azure Service Fabric.  Crie uma imagem do Docker com o seu aplicativo, envie a imagem para um registro de contêiner por push, crie e implante um aplicativo de contêiner do Service Fabric."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/05/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: eeed445631885093a8e1799a8a5e1bcc69214fe6
ms.openlocfilehash: 78306672e812745fd1902ae264c2adea196ab721
ms.contentlocale: pt-br
ms.lasthandoff: 09/07/2017

---

# <a name="deploy-a-service-fabric-linux-container-application-on-azure"></a>Implantar um aplicativo de contêiner Linux do Service Fabric no Azure
O Azure Service Fabric é uma plataforma de sistemas distribuídos para implantação e gerenciamento de contêineres e microsserviços escalonáveis e confiáveis. 

A execução de um aplicativo existente em um contêiner do Linux em um cluster do Service Fabric não requer alterações no seu aplicativo. Este guia de início rápido mostra como implantar uma imagem de contêiner do Docker predefinida em um aplicativo do Service Fabric. Quando você terminar, terá um contêiner nginx em execução.  Este guia de início rápido descreve a implantação de um contêiner do Linux; leia [este guia de início rápido](service-fabric-quickstart-containers.md) para implantar um contêiner do Windows.

![Nginx][nginx]

Neste guia de início rápido, você aprende a:
> [!div class="checklist"]
> * Empacotar um contêiner de imagem do Docker
> * Configurar a comunicação
> * Compilar e empacotar o aplicativo do Service Fabric
> * Implantar o aplicativo de contêiner no Azure

## <a name="prerequisites"></a>Pré-requisitos
Instale o [SDK do Service Fabric, a CLI do Service Fabric e os geradores de modelo yeoman do Service Fabric](service-fabric-get-started-linux.md).
  
## <a name="package-a-docker-image-container-with-yeoman"></a>Empacotar um recipiente de imagem do Docker com o Yeoman
O SDK do Service Fabric para Linux inclui um gerador [Yeoman](http://yeoman.io/) que facilita a criação de seu aplicativo e a adição de uma imagem de contêiner. 

Para criar um aplicativo de contêiner do Service Fabric, abra uma janela do terminal e execute `yo azuresfcontainer`.  

Chame o aplicativo de "MyFirstContainer" e chame o serviço de aplicativo de "MyContainerService".

Forneça o nome para a imagem de contêiner “nginx:latest" (a [imagem de contêiner nginx](https://hub.docker.com/r/_/nginx/) no Hub do Docker). 

Esta imagem tem um ponto de entrada de carga de trabalho definido e, portanto, você precisa especificar explicitamente os comandos de entrada. 

Especifique a contagem de instâncias como "1".

![Gerador de Yeoman do Service Fabric para contêineres][sf-yeoman]

## <a name="configure-communication-and-container-port-to-host-port-mapping"></a>Configurar o mapeamento de porta para porta do host de comunicação e do contêiner
Configure um ponto de extremidade HTTP para que os clientes possam se comunicar com o serviço.  Abra o arquivo *./MyFirstContainer/MyContainerServicePkg/ServiceManifest.xml* e declare um recurso de ponto de extremidade no elemento **ServiceManifest**.  Adicione protocolo, porta e nome. Para este guia de início rápido, o serviço escuta na porta 80: 

```xml
<Resources>
  <Endpoints>
    <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
    <Endpoint Name="myserviceTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
  </Endpoints>
</Resources>

```
Ao fornecer `UriScheme`, o ponto de extremidade do contêiner é registrado automaticamente no serviço de Nomenclatura do Service Fabric para capacidade de descoberta. Um arquivo de exemplo servicemanifest. XML completo é fornecido no final deste artigo. 

Mapeie uma porta de contêiner para um `Endpoint` do serviço usando uma política `PortBinding` em `ContainerHostPolicies` do arquivo ApplicationManifest.xml.  Para este guia de início rápido, `ContainerPort` é 80 (o contêiner expõe a porta 80) e `EndpointRef` é "myserviceTypeEndpoint" (o ponto de extremidade definido anteriormente no manifesto do serviço).  As solicitações de entrada para o serviço na porta 80 são mapeadas para a porta 80 no contêiner.  

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="80" EndpointRef="myserviceTypeEndpoint"/>
  </ContainerHostPolicies>
</Policies>
```

## <a name="build-and-package-the-service-fabric-application"></a>Compilar e empacotar o aplicativo do Service Fabric
Os modelos Yeoman do Service Fabric incluem um script de compilação para [Gradle](https://gradle.org/), que pode ser usado para compilar o aplicativo no terminal. Salve todas as alterações.  Para compilar e empacotar o aplicativo, execute o seguinte:

```bash
cd MyFirstContainer
gradle
```
## <a name="create-a-cluster"></a>Criar um cluster
Para implantar o aplicativo em um cluster do Azure, você pode optar por criar seu próprio cluster ou usar um cluster de entidade.

Os clusters Party são clusters gratuitos de duração limitada do Service Fabric, hospedados no Azure e executados pela equipe do Service Fabric, nos quais qualquer pessoa pode implantar aplicativos e aprender mais sobre a plataforma. Para obter acesso a um cluster de entidade, [siga as instruções](http://aka.ms/tryservicefabric).  

Para obter informações sobre como criar seu próprio cluster, consulte [Criar seu primeiro cluster do Service Fabric no Azure](service-fabric-get-started-azure-cluster.md).

Observe o ponto de extremidade de conexão, que você usará na etapa a seguir.

## <a name="deploy-the-application-to-azure"></a>Implantar o aplicativo no Azure
Após a compilação do aplicativo, você pode implantá-lo no cluster do Azure usando a CLI do Service Fabric.

Conectar-se ao cluster do Service Fabric no Azure.

```bash
sfctl cluster select --endpoint http://lnxt10vkfz6.westus.cloudapp.azure.com:19080
```

Use o script de instalação fornecido no modelo para copiar o pacote de aplicativo no repositório de imagens do cluster, registrar o tipo de aplicativo e criar uma instância do aplicativo.

```bash
./install.sh
```

Abra um navegador e navegue até o Service Fabric Explorer em http://lnxt10vkfz6.westus.cloudapp.azure.com:19080/Explorer. Expanda o nó Aplicativos e observe que agora há uma entrada para o seu tipo de aplicativo e outra para a primeira instância desse tipo.

![Service Fabric Explorer][sfx]

Conectar-se ao contêiner em execução.  Abra um navegador da Web apontando para o endereço IP retornado na porta 80, por exemplo "lnxt10vkfz6.westus.cloudapp.azure.com:80". Você deve ver a página de boas-vindas do nginx exibida no navegador.

![Nginx][nginx]

## <a name="clean-up"></a>Limpar
Use o script de desinstalação fornecido com o modelo para excluir a instância do aplicativo no cluster e cancelar o registro do tipo de aplicativo.

```bash
./uninstall.sh
```

## <a name="complete-example-service-fabric-application-and-service-manifests"></a>Exemplo completo de manifestos de serviço e aplicativo do Service Fabric
Aqui estão os manifestos de aplicativo e serviço completos usados neste guia rápido.

### <a name="servicemanifestxml"></a>ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="MyContainerServicePkg" Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" >

   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="MyContainerServiceType" UseImplicitHost="true">
   </StatelessServiceType>
   </ServiceTypes>
   
   <CodePackage Name="code" Version="1.0.0">
      <EntryPoint>
         <ContainerHost>
            <ImageName>nginx:latest</ImageName>
            <Commands></Commands>
         </ContainerHost>
      </EntryPoint>
      <EnvironmentVariables> 
      </EnvironmentVariables> 
   </CodePackage>
<Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="myserviceTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
    </Endpoints>
  </Resources>
 </ServiceManifest>

```
### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest  ApplicationTypeName="MyFirstContainerType" ApplicationTypeVersion="1.0.0"
                      xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
   
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyContainerServicePkg" ServiceManifestVersion="1.0.0" />
   <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <PortBinding ContainerPort="80" EndpointRef="myserviceTypeEndpoint"/>
      </ContainerHostPolicies>
    </Policies>
</ServiceManifestImport>
   
   <DefaultServices>
      <Service Name="MyContainerService">
        <!-- On a local development cluster, set InstanceCount to 1.  On a multi-node production 
        cluster, set InstanceCount to -1 for the container service to run on every node in 
        the cluster.
        -->
        <StatelessService ServiceTypeName="MyContainerServiceType" InstanceCount="1">
            <SingletonPartition />
        </StatelessService>
      </Service>
   </DefaultServices>
   
</ApplicationManifest>

```

## <a name="next-steps"></a>Próximas etapas
Neste guia de início rápido, você aprende a:
> [!div class="checklist"]
> * Empacotar um contêiner de imagem do Docker
> * Configurar a comunicação
> * Compilar e empacotar o aplicativo do Service Fabric
> * Implantar o aplicativo de contêiner no Azure

* Saiba mais sobre como executar [contêineres no Service Fabric](service-fabric-containers-overview.md).
* Leia o tutorial [Como implantar um aplicativo .NET em um contêiner](service-fabric-host-app-in-a-container.md).
* Leia mais sobre o [ciclo de vida do aplicativo](service-fabric-application-lifecycle.md) do Service Fabric.
* Confira os [exemplos de código do Service Fabric](https://github.com/Azure-Samples/service-fabric-dotnet-containers) no GitHub.

[sfx]: ./media/service-fabric-quickstart-containers-linux/SFX.png
[nginx]: ./media/service-fabric-quickstart-containers-linux/nginx.png
[sf-yeoman]: ./media/service-fabric-quickstart-containers-linux/YoSF.png

