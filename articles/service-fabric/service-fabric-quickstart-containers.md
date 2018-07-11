---
title: Criar um aplicativo de contêiner Windows no Service Fabric no Azure | Microsoft Docs
description: Neste início rápido, você cria seu primeiro aplicativo de contêiner do Windows no Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: vturecek
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/30/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 95877f8b81641dd70434fc167003cfcce07d9e84
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37110803"
---
# <a name="quickstart-deploy-windows-containers-to-service-fabric"></a>Início Rápido: Implantar contêineres do Windows no Service Fabric

O Azure Service Fabric é uma plataforma de sistemas distribuídos para implantação e gerenciamento de contêineres e microsserviços escalonáveis e confiáveis.

Executar um aplicativo existente em um contêiner do Windows em um cluster do Service Fabric não requer alterações no seu aplicativo. Este guia de início rápido mostra como implantar uma imagem de contêiner do Docker predefinida em um aplicativo do Service Fabric. Quando você terminar, terá um contêiner do Windows Server 2016 Nano Server e o IIS em execução. Este guia de início rápido descreve a implantação de um contêiner do Windows; leia [este guia de início rápido](service-fabric-quickstart-containers-linux.md) para implantar um contêiner do Linux.

![Página de Web do IIS padrão][iis-default]

Neste guia de início rápido, você aprende a:

* Empacotar um contêiner de imagem do Docker
* Configurar a comunicação
* Compilar e empacotar o aplicativo do Service Fabric
* Implantar o aplicativo de contêiner no Azure

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure (você pode criar um [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)).
* Um computador de desenvolvimento executando:
  * Visual Studio 2015 ou Visual Studio 2017.
  * [Ferramentas e SDK do Service Fabric](service-fabric-get-started.md).

## <a name="package-a-docker-image-container-with-visual-studio"></a>Empacotar um recipiente de imagem do Docker com o Visual Studio

As ferramentas e o SDK do Service Fabric oferecem um modelo de serviço para ajudar você a implantar um contêiner em um cluster do Service Fabric.

Inicie o Visual Studio como “Administrador”.  Selecione **Arquivo** > **Novo** > **Projeto**.

Selecione **Aplicativo do Service Fabric**, nomeie-o como "MyFirstContainer" e clique em **OK**.

Selecione **Contêiner** nos modelos **Aplicativos e Contêineres Hospedados**.

Em **Nome da Imagem**, insira "microsoft/iis:nanoserver", a [imagem de base do Windows Server Nano Server e IIS](https://hub.docker.com/r/microsoft/iis/).

Configure o mapeamento de portas porta a host do contêiner para que as solicitações de entrada para o serviço na porta 80 sejam mapeadas para a porta 80 no contêiner.  Defina a **Porta do Contêiner** como "80" e defina a **Porta do Host** como "80".  

Chame o serviço de "MyContainerService" e clique em **OK**.

<<<<<<< Upstream atualizado ![Caixa de diálogo Novo serviço][new-service]
=======
## <a name="configure-communication-and-container-port-to-host-port-mapping"></a>Configurar o mapeamento de porta para porta do host de comunicação e do contêiner

O serviço precisa de um ponto de extremidade para comunicação.  Neste Guia de Início Rápido, o serviço em contêineres escuta na porta 80.  No Gerenciador de Soluções, abra *MyFirstContainer/ApplicationPackageRoot/MyContainerServicePkg/ServiceManifest.xml*.  Atualize o `Endpoint` existente no arquivo ServiceManifest.xml e adicione o esquema de URI, protocolo e porta:

```xml
<Resources>
    <Endpoints>
        <Endpoint Name="MyContainerServiceTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
   </Endpoints>
</Resources>
```

Ao fornecer `UriScheme`, o ponto de extremidade do contêiner é registrado automaticamente no serviço de Nomenclatura do Service Fabric para capacidade de descoberta. Um arquivo de exemplo servicemanifest. XML completo é fornecido no final deste artigo.

Configure o mapeamento de portas porta a host do contêiner para que as solicitações de entrada para o serviço na porta 80 sejam mapeadas para a porta 80 no contêiner.  No Gerenciador de Soluções, abra *MyFirstContainer/ApplicationPackageRoot/ApplicationManifest.xml* e especifique uma política `PortBinding` em `ContainerHostPolicies`.  Para este guia de início rápido, `ContainerPort` é 80 e `EndpointRef` é "MyContainerServiceTypeEndpoint" (o ponto de extremidade definido no manifesto do serviço).

```xml
<ServiceManifestImport>
...
  <ConfigOverrides />
  <Policies>
    <ContainerHostPolicies CodePackageRef="Code">
      <PortBinding ContainerPort="80" EndpointRef="MyContainerServiceTypeEndpoint"/>
    </ContainerHostPolicies>
  </Policies>
</ServiceManifestImport>
```

Um arquivo de exemplo ApplicationManifest. XML completo é fornecido no final deste artigo.
>>>>>>> Alterações armazenadas

## <a name="specify-the-os-build-for-your-container-image"></a>Especifique o build do sistema operacional para a imagem de contêiner
Contêineres criados com uma versão específica do Windows Server podem não ser executados em um host executando uma versão diferente do Windows Server. Por exemplo, os contêineres do Windows Server criados usando o Windows Server 1709 não funcionam em hosts que estão executando o Windows Server 2016. Para obter mais informações, consulte [Compatibilidade do sistema operacional contêiner e do sistema operacional do host do Windows Server](service-fabric-get-started-containers.md#windows-server-container-os-and-host-os-compatibility). 

Com a versão 6.1 do tempo de execução do Service Fabric e mais recente, você pode especificar várias imagens do sistema operacional por contêiner e marcar cada um com a versão do build do sistema operacional na que ele deve ser implantado. Isso ajuda a garantir que seu aplicativo será executado em hosts que executam diferentes versões do sistema operacional Windows. Para saber mais, consulte [Especifique a compilação do sistema operacional das imagens de contêiner específicas](service-fabric-get-started-containers.md#specify-os-build-specific-container-images). 

A Microsoft publica imagens diferentes para versões do IIS criadas em diferentes versões do Windows Server. Para certificar-se de que o Service Fabric implanta um contêiner compatível com a versão do Windows Server em execução em nós de cluster onde ele implanta o aplicativo, adicione as seguintes linhas ao arquivo *ApplicationManifest.xml*. A versão de compilação para o Windows Server 2016 é 14393 e a versão de compilação para a versão 1709 do Windows Server é 16299. 

```xml
    <ContainerHostPolicies CodePackageRef="Code"> 
      <ImageOverrides> 
        ...
          <Image Name="microsoft/iis:nanoserverDefault" /> 
          <Image Name= "microsoft/iis:nanoserver" Os="14393" /> 
          <Image Name="microsoft/iis:windowsservercore-1709" Os="16299" /> 
      </ImageOverrides> 
    </ContainerHostPolicies> 
```

O manifesto do serviço continua a especificar apenas uma imagem para o Nano Server, `microsoft/iis:nanoserver`. 

## <a name="create-a-cluster"></a>Criar um cluster

Para implantar o aplicativo em um cluster no Azure, você pode ingressar em um cluster de terceiros. Os clusters Party são clusters gratuitos de duração limitada do Service Fabric, hospedados no Azure e executados pela equipe do Service Fabric, nos quais qualquer pessoa pode implantar aplicativos e aprender mais sobre a plataforma.  O cluster usa um único certificado autoassinado para nó-a-nó, bem como segurança de cliente para nó. Clusters de equipe dão suporte a contêineres. Se optar por configurar e usar seu próprio cluster, ele deve estar sendo executado em um SKU que ofereça suporte a contêineres (como Windows Server 2016 Datacenter com Contêineres).

Entre e [ingresse em um cluster do Windows](http://aka.ms/tryservicefabric). Baixe o certificado PFX em seu computador clicando no link **PFX**. Clique no link **Como se conectar a um cluster de equipe seguro?** e copie a senha do certificado. O certificado, senha do certificado e os valores de **Ponto de extremidade de conexão** são utilizados nas etapas a seguir.

![PFX e ponto de extremidade de conexão](./media/service-fabric-quickstart-containers/party-cluster-cert.png)

> [!Note]
> Há um número limitado de clusters de equipe disponíveis por hora. Se você receber um erro ao tentar se inscrever para um cluster de equipe, você poderá aguardar um período e tentar novamente, ou você pode seguir estas etapas no tutorial [Implantar um aplicativo .NET](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-deploy-app-to-party-cluster#deploy-the-sample-application) para criar um cluster do Service Fabric em sua assinatura do Azure e implantar o aplicativo nele. O cluster criado pelo Visual Studio oferece suporte a contêineres. Depois de implantar e verificar o aplicativo em seu cluster, é possível pular para a seção [Completar aplicativo de exemplo e manifestos de serviço do Service Fabric](#complete-example-service-fabric-application-and-service-manifests) neste de início rápido.
>

Em um computador com Windows, instale o PFX no repositório de certificados *CurrentUser\My*.

```powershell
PS C:\mycertificates> Import-PfxCertificate -FilePath .\party-cluster-873689604-client-cert.pfx -CertStoreLocation Cert:\CurrentUser\My -Password (ConvertTo-SecureString 873689604 -AsPlainText -Force)


  PSParentPath: Microsoft.PowerShell.Security\Certificate::CurrentUser\My

Thumbprint                                Subject
----------                                -------
3B138D84C077C292579BA35E4410634E164075CD  CN=zwin7fh14scd.westus.cloudapp.azure.com
<<<<<<< Updated upstream
``` 
=======
```

Remember the thumbprint for the following step.
>>>>>>> Stashed changes

## Deploy the application to Azure using Visual Studio

Now that the application is ready, you can deploy it to a cluster directly from Visual Studio.

Right-click **MyFirstContainer** in the Solution Explorer and choose **Publish**. The Publish dialog appears.

<<<<<<< Updated upstream
Copy the **Connection Endpoint** from the Party cluster page into the **Connection Endpoint** field. For example, `zwin7fh14scd.westus.cloudapp.azure.com:19000`. 
=======
Copy the **Connection Endpoint** from the Party cluster page into the **Connection Endpoint** field. For example, `zwin7fh14scd.westus.cloudapp.azure.com:19000`. Click **Advanced Connection Parameters** and verify the connection parameter information.  *FindValue* and *ServerCertThumbprint* values must match the thumbprint of the certificate installed in the previous step.

![Publish Dialog](./media/service-fabric-quickstart-containers/publish-app.png)
>>>>>>> Stashed changes

Click **Publish**.

Each application in the cluster must have a unique name.  Party clusters are a public, shared environment however and there may be a conflict with an existing application.  If there is a name conflict, rename the Visual Studio project and deploy again.

Open a browser and navigate to the **Connection endpoint** specified in the Party cluster page. You can optionally prepend the scheme identifier, `http://`, and append the port, `:80`, to the URL. For example, http://zwin7fh14scd.westus.cloudapp.azure.com:80. You should see the IIS default web page:
![IIS default web page][iis-default]

<<<<<<< Updated upstream
=======
## Complete example Service Fabric application and service manifests

Here are the complete service and application manifests used in this quickstart.

### ServiceManifest.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="MyContainerServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="MyContainerServiceType" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <!-- Follow this link for more information about deploying Windows containers to Service Fabric: https://aka.ms/sfguestcontainers -->
      <ContainerHost>
        <ImageName>microsoft/iis:nanoserver</ImageName>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container: -->
    <!--
    <EnvironmentVariables>
      <EnvironmentVariable Name="VariableName" Value="VariableValue"/>
    </EnvironmentVariables>
    -->
  </CodePackage>

  <!-- Config package is the contents of the Config directoy under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="MyContainerServiceTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```

### <a name="applicationmanifestxml"></a>ApplicationManifest.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="MyFirstContainerType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="MyContainerService_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyContainerServicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <PortBinding ContainerPort="80" EndpointRef="MyContainerServiceTypeEndpoint"/>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="MyContainerService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="MyContainerServiceType" InstanceCount="[MyContainerService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

>>>>>>> Alterações armazenadas
## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprendeu a:

* Empacotar um contêiner de imagem do Docker
* Configurar a comunicação
* Compilar e empacotar o aplicativo do Service Fabric
* Implantar o aplicativo de contêiner no Azure

Para saber mais sobre como trabalhar com os contêineres do Windows no Service Fabric, prossiga com o tutorial sobre aplicativos de contêiner do Windows.

> [!div class="nextstepaction"]
> [Criar um aplicativo de contêiner do Windows](./service-fabric-host-app-in-a-container.md)

[iis-default]: ./media/service-fabric-quickstart-containers/iis-default.png
[publish-dialog]: ./media/service-fabric-quickstart-containers/publish-dialog.png
[new-service]: ./media/service-fabric-quickstart-containers/NewService.png
