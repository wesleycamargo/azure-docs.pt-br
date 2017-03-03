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
ms.date: 2/17/2017
ms.author: msfussell
translationtype: Human Translation
ms.sourcegitcommit: 47b3fffb2d5c24b7473884e490be19ff17b61b61
ms.openlocfilehash: 97b0cb7a5f04f2c5c547cb4b70d87273aa8f2383
ms.lasthandoff: 02/21/2017


---
# <a name="preview-deploy-a-windows-container-to-service-fabric"></a>Versão prévia: implantar um contêiner do Windows no Service Fabric
> [!div class="op_single_selector"]
> * [Implantar contêiner do Windows](service-fabric-deploy-container.md)
> * [Implantar contêiner do Docker](service-fabric-deploy-container-linux.md)
> 
> 

Este artigo o orienta pelo processo de compilação de serviços contidos em contêineres do Windows.

> [!NOTE]
> Este recurso está em versão prévia para o Windows Server 2016.
>  

O Service Fabric tem vários recursos de contêiner que ajudam na compilação de aplicativos que são compostos por microsserviços que estão em contêineres. 

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
3. Escolha **Nome da Imagem** e forneça o caminho para a imagem no seu repositório de contêineres, como em https://hub.docker.com/, por exemplo, myrepo/myimage:v1 
4. Dê um nome ao seu serviço e clique em **OK**.
5. Se seu serviço em contêiner precisar de um ponto de extremidade para comunicação, você poderá adicionar o protocolo, a porta e o tipo ao arquivo ServiceManifest.xml. Por exemplo: 
     
    `<Endpoint Name="MyContainerServiceEndpoint" Protocol="http" Port="80" UriScheme="http" PathSuffix="myapp/" Type="Input" />`
    
    Ao fornecer `UriScheme`, o ponto de extremidade do contêiner é registrado automaticamente no serviço de Nomenclatura do Service Fabric para capacidade de descoberta. A porta pode ser fixa (como mostrado no exemplo anterior) ou alocada dinamicamente (deixada em branco e uma porta é alocada do intervalo de portas de aplicativo designado), assim como você faria com qualquer serviço.
    Você também precisa configurar o mapeamento de porta, da porta para o host, do contêiner especificando uma política `PortBinding` no manifesto do aplicativo, conforme descrito abaixo.
6. Se seu contêiner precisar de governança de recursos, adicione `ResourceGovernancePolicy`.
8. Se seu contêiner precisar autenticar com um repositório privado, adicione `RepositoryCredentials`.
7. Agora você pode usar o pacote e a ação de publicação no seu cluster local no caso do Windows Server 2016 com suporte ao contêiner ativado. 
8. Quando estiver pronto, você poderá publicar o aplicativo em um cluster remoto ou fazer check-in da solução para o controle do código-fonte. 

Para obter um aplicativo de exemplo, [confira os códigos de exemplo de contêiner do Service Fabric no GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-containers)

## <a name="creating-a-windows-server-2016-cluster"></a>Criar um cluster do Windows Server 2016
Para implantar aplicativos contidos, é necessário criar um cluster que execute o Windows Server 2016 com o suporte para contêineres habilitado. Tal implantação pode ser realizada no seu computador de desenvolvimento local ou por meio do Azure Resource Manager (ARM), no Azure. 

Para implantar um cluster usando o ARM, escolha a opção de imagem do **Windows Server 2016 com Contêineres** no Azure. Consulte o artigo [Criar um cluster do Service Fabric usando o Azure Resource Manager](service-fabric-cluster-creation-via-arm.md). Verifique se você está usando as seguintes configurações do ARM:

```xml
"vmImageOffer": { "type": "string","defaultValue": "WindowsServer"     },
"vmImageSku": { "defaultValue": "2016-Datacenter-with-Containers","type": "string"     },
"vmImageVersion": { "defaultValue": "latest","type": "string"     },  
```
Também é possível usar o [modelo de&5; Nós do ARM](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) para criar um cluster. Como alternativa, confira a [postagem no blog do Leok](https://loekd.blogspot.com/2017/01/running-windows-containers-on-azure.html) sobre como usar o Service Fabric e os contêineres do Windows.

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

Você pode fornecer comandos de entrada especificando o elemento opcional `Commands` com uma vírgula delimitada por conjunto de comandos a serem executados dentro do contêiner.

## <a name="understand-resource-governance"></a>Compreender a governança de recursos
A governança de recursos é uma funcionalidade do contêiner e restringe os recursos que o contêiner pode usar no host. O `ResourceGovernancePolicy`, especificado no manifesto do aplicativo, é usado para declarar os limites de recurso para um pacote de códigos de serviço. Limites de recursos podem ser definidos para os seguintes recursos:

* Memória
* MemorySwap
* CpuShares (peso relativo da CPU)
* MemoryReservationInMB  
* BlkioWeight (peso relativo do BlockIO).

> [!NOTE]
> Em uma versão futura, será incluído suporte para especificar limites de E/S de um bloco específico, como IOPs, leitura/gravação de BPS e outros.
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

## <a name="configure-container-port-to-host-port-mapping"></a>Configurar mapeamento de porta do contêiner para o porta de host
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
Usando a política `PortBinding`, você pode mapear uma porta de contêiner para um `Endpoint` no manifesto do serviço conforme mostrado no exemplo a seguir. O ponto de extremidade `Endpoint1` pode especificar uma porta fixa (por exemplo, porta 80). Também pode especificar nenhuma porta, quando uma porta aleatória no intervalo de portas do aplicativo do cluster será escolhida para você.

Se você especificar um ponto de extremidade, usando a marca `Endpoint` no manifesto do serviço de um contêiner de convidado, o Service Fabric poderá publicar automaticamente este ponto de extremidade no serviço de Nomenclatura. Portanto, outros serviços que são executados no cluster podem descobrir esse contêiner usando as consultas REST para resolver.

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

Ao se registrar com o serviço de Nomenclatura, você poderá fazer a comunicação de contêiner para contêiner facilmente no código dentro do contêiner usando o [proxy reverso](service-fabric-reverseproxy.md). A comunicação é realizada fornecendo a porta de escuta de proxy reverso http e o nome dos serviços com os quais você deseja se comunicar como variáveis de ambiente. Para obter mais informações, confira a próxima seção. 

## <a name="configure-and-set-environment-variables"></a>Configurar e definir as variáveis de ambiente
Variáveis de ambiente podem ser especificadas para cada pacote de código no manifesto de serviço, tanto para serviços implantados em contêineres quanto para serviços implantados como processos/executáveis convidados. Esses valores de variável de ambiente podem ser substituídos especialmente no manifesto do aplicativo ou especificados durante a implantação como parâmetros do aplicativo.

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
* Para obter um aplicativo de exemplo, [confira os códigos de exemplo de contêiner do Service Fabric no GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-containers)

