---
title: "Service Fabric e implantação de contêineres | Microsoft Docs"
description: "Service Fabric e o uso de contêineres para implantar aplicativos de microsserviço. Este artigo descreve os recursos que o Service Fabric fornece para contêineres e como implantar uma imagem de contêiner do Windows em um cluster."
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: 
ms.assetid: 799cc9ad-32fd-486e-a6b6-efff6b13622d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 5/16/2017
ms.author: msfussell
ms.translationtype: Human Translation
ms.sourcegitcommit: 8be2bcb9179e9af0957fcee69680ac803fd3d918
ms.openlocfilehash: 25d6b056421e71fa70ed20a39589f77dbbc25c69
ms.contentlocale: pt-br
ms.lasthandoff: 06/23/2017


---
# <a name="deploy-a-windows-container-to-service-fabric"></a>Implantar um contêiner do Windows no Service Fabric
> [!div class="op_single_selector"]
> * [Implantar contêiner do Windows](service-fabric-deploy-container.md)
> * [Implantar contêiner do Docker](service-fabric-deploy-container-linux.md)
> 
> 

Este artigo o orienta pelo processo de compilação de serviços contidos em contêineres do Windows.

O Service Fabric tem vários recursos que ajudam na criação de aplicativos compostos de microsserviços em execução em contêineres. 

As funcionalidade s incluem:

* Implantação e ativação de imagens de contêiner
* Governança de recursos
* Autenticação do repositório
* Mapeamento de porta de contêiner para porta de host
* Descoberta e comunicação de contêiner para contêiner
* Capacidade de configurar e definir as variáveis de ambiente

Vamos ver como cada um dos recursos funciona quando você está empacotando um serviço em contêiner a ser incluído em seu aplicativo.

## <a name="package-a-windows-container"></a>Empacotar um contêiner do Windows
Ao empacotar um contêiner, você pode optar por usar um modelo de projeto do Visual Studio ou [criar o pacote de aplicativos manualmente](#manually).  Ao usar o Visual Studio, a estrutura do pacote de aplicativos e os arquivos de manifesto são criados para você pelo modelo Novo Projeto.

> [!TIP]
> A maneira mais fácil de empacotar uma imagem de contêiner existente em um serviço é usar o Visual Studio.

## <a name="use-visual-studio-to-package-an-existing-container-image"></a>Usar o Visual Studio para empacotar uma imagem de contêiner existente
O Visual Studio fornece um modelo de serviço do Service Fabric para ajudar você a implantar um contêiner em um cluster do Service Fabric.

1. Escolha **Arquivo** > **Novo Projeto** e crie um aplicativo de Service Fabric.
2. Escolha **Contêiner Convidado** como o modelo de serviço.
3. Escolha **Nome da Imagem** e forneça o caminho da imagem no repositório de contêiner. Por exemplo, `myrepo/myimage:v1` em https://hub.docker.com
4. Dê um nome ao seu serviço e clique em **OK**.
5. Se seu serviço em contêiner precisar de um ponto de extremidade para comunicação, você poderá adicionar o protocolo, a porta e o tipo ao arquivo ServiceManifest.xml. Por exemplo: 
     
    `<Endpoint Name="MyContainerServiceEndpoint" Protocol="http" Port="80" UriScheme="http" PathSuffix="myapp/" Type="Input" />`
    
    Fornecendo o `UriScheme`, o Service Fabric registra automaticamente o ponto de extremidade do contêiner no serviço de Nomenclatura para capacidade de descoberta. A porta pode ser fixa (conforme mostrado no exemplo anterior) ou alocada dinamicamente. Se você não especificar uma porta, ela será alocada dinamicamente do intervalo de portas do aplicativo (como acontece com qualquer serviço).
    Você também precisa configurar o contêiner para o mapeamento de porta de host, especificando uma política `PortBinding` no manifesto do aplicativo. Para obter mais informações, confira [Configurar contêiner para hospedar mapeamento de porta](#Portsection).
6. Se seu contêiner precisar de governança de recursos, adicione `ResourceGovernancePolicy`.
8. Se o seu contêiner precisar autenticar com um repositório privado, adicione `RepositoryCredentials`.
7. Se estiver realizando a execução em um computador Windows Server 2016 com o suporte de contêiner habilitado, você poderá usar o pacote e a ação de publicação para implantar no cluster local. 
8. Quando estiver pronto, você poderá publicar o aplicativo em um cluster remoto ou fazer check-in da solução para o controle do código-fonte. 

Para obter um exemplo, [confira os códigos de exemplo de contêiner do Service Fabric no GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-containers)

## <a name="creating-a-windows-server-2016-cluster"></a>Criar um cluster do Windows Server 2016
Para implantar aplicativos contidos, é necessário criar um cluster que execute o Windows Server 2016 com o suporte para contêineres habilitado. O cluster pode estar em execução localmente ou ser implantado por meio do Azure Resource Manager no Azure. 

Para implantar um cluster usando o Azure Resource Manager, escolha a opção de imagem **Windows Server 2016 com Contêineres** no Azure. Consulte o artigo [Criar um cluster do Service Fabric usando o Azure Resource Manager](service-fabric-cluster-creation-via-arm.md). Use as seguintes configurações do Azure Resource Manager:

```xml
"vmImageOffer": { "type": "string","defaultValue": "WindowsServer"     },
"vmImageSku": { "defaultValue": "2016-Datacenter-with-Containers","type": "string"     },
"vmImageVersion": { "defaultValue": "latest","type": "string"     },  
```
Você também pode usar o [modelo de cinco nós do Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) para criar um cluster. Como alternativa, leia uma [postagem de blog](https://loekd.blogspot.com/2017/01/running-windows-containers-on-azure.html) da comunidade sobre o uso de contêineres do Windows e do Service Fabric.

<a id="manually"></a>

## <a name="manually-package-and-deploy-a-container-image"></a>Empacotar e implantar manualmente uma imagem de contêiner
O processo de empacotamento manual de um serviço em contêiner se baseia nas seguintes etapas:

1. Publicar os contêineres em seu repositório.
2. Criar a estrutura de diretórios do pacote.
3. Editar o arquivo de manifesto do serviço.
4. Editar o arquivo de manifesto do aplicativo.

## <a name="deploy-and-activate-a-container-image"></a>Implantar e ativar uma imagem de contêiner
No [modelo de aplicativo](service-fabric-application-model.md)do Service Fabric, um contêiner representa um host de aplicativo no qual várias réplicas de serviço são colocadas. Para implantar e ativar um contêiner, coloque o nome da imagem do contêiner em um elemento `ContainerHost` no manifesto do serviço.

No manifesto do serviço, adicione um `ContainerHost` do ponto de entrada. Em seguida, defina o `ImageName` para o nome do repositório de contêiner e imagem. O manifesto parcial a seguir mostra um exemplo de como implantar o contêiner chamado `myimage:v1` de um repositório chamado `myrepo`:

```xml
    <CodePackage Name="Code" Version="1.0">
        <EntryPoint>
          <ContainerHost>
            <ImageName>myrepo/myimage:v1</ImageName>
            <Commands></Commands>
          </ContainerHost>
        </EntryPoint>
    </CodePackage>
```

Você pode especificar comandos opcionais para serem executados após a inicialização do contêiner sob o elemento `Commands`. Para vários comandos, delimite-os com vírgulas. 

## <a name="understand-resource-governance"></a>Compreender a governança de recursos
A governança de recursos é uma funcionalidade do contêiner e restringe os recursos que o contêiner pode usar no host. O `ResourceGovernancePolicy`, especificado no manifesto do aplicativo, é usado para declarar os limites de recurso para um pacote de códigos de serviço. Limites de recursos podem ser definidos para os seguintes recursos:

* Memória
* MemorySwap
* CpuShares (peso relativo da CPU)
* MemoryReservationInMB  
* BlkioWeight (peso relativo do BlockIO).

> [!NOTE]
> o Suporte para especificar os limites de e/s de bloco específicoS como IOPs, leitura/gravação BPS e outros estão planejados para uma versão futura.
> 
> 

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ResourceGovernancePolicy CodePackageRef="FrontendService.Code" CpuShares="500"
            MemoryInMB="1024" MemorySwapInMB="4084" MemoryReservationInMB="1024" />
        </Policies>
    </ServiceManifestImport>
```

## <a name="authenticate-a-repository"></a>Autenticar um repositório
Para baixar um contêiner, poderá ser preciso fornecer as credenciais de logon para o repositório do contêiner. As credenciais de logon especificadas no manifesto do aplicativo são usadas para especificar as informações de logon, ou a chave SSH, para baixar a imagem do contêiner do repositório de imagens. O exemplo a seguir mostra uma conta chamada *TestUser* junto com a senha em texto não criptografado (*não* recomendado):

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <RepositoryCredentials AccountName="TestUser" Password="12345" PasswordEncrypted="false"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>
```

É recomendável que você criptografe a senha usando um certificado implantado no computador.

O exemplo a seguir mostra uma conta chamada *TestUser*, em que a senha foi criptografada usando um certificado chamado *MyCert*. Você pode usar o comando `Invoke-ServiceFabricEncryptText` do PowerShell para criar o texto cifrado secreto para a senha. Para obter mais informações, consulte o artigo [Gerenciar segredos em aplicativos do Service Fabric](service-fabric-application-secret-management.md).

A chave privada do certificado usada para descriptografar a senha deve ser implantada no computador local em um método fora de banda. (No Azure, esse método é o Azure Resource Manager.) Em seguida, quando o Service Fabric implanta o pacote de serviço para o computador, ele poderá descriptografar o segredo. Usando o segredo junto com o nome da conta, ele então pode autenticar com o repositório do contêiner.

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <RepositoryCredentials AccountName="TestUser" Password="[Put encrypted password here using MyCert certificate ]" PasswordEncrypted="true"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>
```

## <a name ="Portsection"></a>Configurar o contêiner para o mapeamento de porta de host
Você pode configurar uma porta de host usada para se comunicar com o contêiner especificando um `PortBinding` no manifesto do aplicativo. A associação de porta mapeia a porta que o serviço está escutando dentro do contêiner para uma porta no host.

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <PortBinding ContainerPort="8905"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>
```

## <a name="configure-container-to-container-discovery-and-communication"></a>Configurar descoberta e comunicação de contêiner para contêiner
Você pode usar o elemento `PortBinding` para mapear uma porta de contêiner para um ponto de extremidade no manifesto do serviço. No exemplo a seguir, o ponto de extremidade `Endpoint1` especifica uma porta fixa, 8905. Também pode especificar nenhuma porta, quando uma porta aleatória no intervalo de portas do aplicativo do cluster será escolhida para você.


```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>
```
Se você especificar um ponto de extremidade, usando a marca `Endpoint` no manifesto do serviço de um contêiner de convidado, o Service Fabric poderá publicar automaticamente este ponto de extremidade no serviço de Nomenclatura. Portanto, outros serviços que são executados no cluster podem descobrir esse contêiner usando as consultas REST para resolver.

Registrando-se no serviço de Nomenclatura, você pode executar a comunicação de contêiner para contêiner no contêiner usando o [proxy reverso](service-fabric-reverseproxy.md). A comunicação é realizada fornecendo a porta de escuta de proxy reverso http e o nome dos serviços com os quais você deseja se comunicar como variáveis de ambiente. Para obter mais informações, confira a próxima seção. 

## <a name="configure-and-set-environment-variables"></a>Configurar e definir as variáveis de ambiente
Variáveis de ambiente podem ser especificadas para cada pacote de códigos no manifesto do serviço. Esse recurso está disponível para todos os serviços, independentemente de eles serem implantados como contêineres ou processos ou executáveis convidados. Você pode substituir valores de variáveis de ambiente no manifesto do aplicativo ou especificá-los durante a implantação como parâmetros de aplicativo.

O trecho XML do manifesto do serviço a seguir mostra um exemplo de como especificar variáveis de ambiente para um pacote de códigos:

```xml
    <ServiceManifest Name="FrontendServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <Description>a guest executable service in a container</Description>
        <ServiceTypes>
            <StatelessServiceType ServiceTypeName="StatelessFrontendService"  UseImplicitHost="true"/>
        </ServiceTypes>
        <CodePackage Name="FrontendService.Code" Version="1.0">
            <EntryPoint>
            <ContainerHost>
                <ImageName>myrepo/myimage:v1</ImageName>
                <Commands></Commands>
            </ContainerHost>
            </EntryPoint>
            <EnvironmentVariables>
                <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
                <EnvironmentVariable Name="BackendServiceName" Value=""/>
            </EnvironmentVariables>
        </CodePackage>
    </ServiceManifest>
```

Essas variáveis de ambiente podem ser substituídas no nível do manifesto do aplicativo:

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <EnvironmentOverrides CodePackageRef="FrontendService.Code">
            <EnvironmentVariable Name="BackendServiceName" Value="[BackendSvc]"/>
            <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
        </EnvironmentOverrides>
    </ServiceManifestImport>
```

No exemplo acima, especificamos um valor explícito para a variável de ambiente `HttpGateway` (19000) enquanto definimos o valor do parâmetro `BackendServiceName` é definido por meio do parâmetro de aplicativo `[BackendSvc]`. Essas configurações permitem especificar o valor para `BackendServiceName`valor quando você implanta o aplicativo e não tem um valor fixo no manifesto.

## <a name="configure-isolation-mode"></a>Configurar o modo de isolamento

L Windows dá suporte a dois modos de isolamento para contêineres: processo e Hyper-V.  Com o modo de isolamento de processo, todos os contêineres em execução no mesmo computador host compartilham o kernel com o host. Com o modo de isolamento do Hyper-V, os kernels são isolados entre cada contêiner do Hyper-V e o host do contêiner. O modo de isolamento é especificado na marca `ContainerHostPolicies` no arquivo de manifesto do aplicativo.  Os modos de isolamento que podem ser especificados são `process`, `hyperv` e `default`. O modo de isolamento `default` usa `process` por padrão em hosts do Windows Server e usa `hyperv` por padrão em hosts do Windows 10.  O trecho a seguir mostra como o modo de isolamento é especificado no arquivo de manifesto do aplicativo.

```xml
   <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv">
```


## <a name="complete-examples-for-application-and-service-manifest"></a>Exemplos completos de aplicativo e manifesto do serviço

Aqui está um exemplo de manifesto de aplicativo:

```xml
    <ApplicationManifest ApplicationTypeName="SimpleContainerApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <Description>A simple service container application</Description>
        <Parameters>
            <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
            <Parameter Name="BackEndSvcName" DefaultValue="bkend"></Parameter>
        </Parameters>
        <ServiceManifestImport>
            <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
            <EnvironmentOverrides CodePackageRef="FrontendService.Code">
                <EnvironmentVariable Name="BackendServiceName" Value="[BackendSvcName]"/>
                <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
            </EnvironmentOverrides>
            <Policies>
                <ResourceGovernancePolicy CodePackageRef="Code" CpuShares="500" MemoryInMB="1024" MemorySwapInMB="4084" MemoryReservationInMB="1024" />
                <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                    <RepositoryCredentials AccountName="username" Password="****" PasswordEncrypted="true"/>
                    <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
                </ContainerHostPolicies>
            </Policies>
        </ServiceManifestImport>
    </ApplicationManifest>
```

Aqui está um exemplo de manifesto de serviço (especificado no manifesto do aplicativo anterior):

```xml
    <ServiceManifest Name="FrontendServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <Description> A service that implements a stateless front end in a container</Description>
        <ServiceTypes>
            <StatelessServiceType ServiceTypeName="StatelessFrontendService"  UseImplicitHost="true"/>
        </ServiceTypes>
        <CodePackage Name="FrontendService.Code" Version="1.0">
            <EntryPoint>
            <ContainerHost>
                <ImageName>myrepo/myimage:v1</ImageName>
                <Commands></Commands>
            </ContainerHost>
            </EntryPoint>
            <EnvironmentVariables>
                <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
                <EnvironmentVariable Name="BackendServiceName" Value=""/>
            </EnvironmentVariables>
        </CodePackage>
        <ConfigPackage Name="FrontendService.Config" Version="1.0" />
        <DataPackage Name="FrontendService.Data" Version="1.0" />
        <Resources>
            <Endpoints>
                <Endpoint Name="Endpoint1" UriScheme="http" Port="80" Protocol="http"/>
            </Endpoints>
        </Resources>
    </ServiceManifest>
```

## <a name="next-steps"></a>Próximas etapas
Agora que você implantou um serviço em contêiner, saiba como gerenciar seu ciclo de vida lendo [ciclo de vida de aplicativos do Service Fabric](service-fabric-application-lifecycle.md).

* [Visão geral do Service Fabric e contêineres](service-fabric-containers-overview.md)
* Por exemplo, confira [Exemplos de código do contêiner do Service Fabric no GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-containers)

