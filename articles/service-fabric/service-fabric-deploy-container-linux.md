---
title: "Service Fabric e implantação de contêineres no Linux| Microsoft Docs"
description: "Service Fabric e o uso de contêineres do Linux para implantar aplicativos de microsserviço. Este artigo descreve os recursos que o Service Fabric fornece para contêineres e como implantar uma imagem de contêiner do Linux em um cluster"
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: 
ms.assetid: 4ba99103-6064-429d-ba17-82861b6ddb11
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/29/2017
ms.author: msfussell
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 9dcec753e5f999a1bac07276373c0c25f89ec58d
ms.contentlocale: pt-br
ms.lasthandoff: 07/01/2017


---
# <a name="deploy-a-linux-container-to-service-fabric"></a>Implantar um contêiner do Linux no Service Fabric
> [!div class="op_single_selector"]
> * [Implantar contêiner do Windows](service-fabric-deploy-container.md)
> * [Implantar contêiner do Linux](service-fabric-deploy-container-linux.md)
>
>

Este artigo orienta a criação de serviços contentorizados em contêineres do Docker no Linux.

O Service Fabric tem vários recursos de contêiner que ajudam na compilação de aplicativos que são compostos por microsserviços que estão em contêineres. Esses serviços são chamados de serviços em contêineres.

Os recursos incluem;

* Implantação e ativação de imagens de contêiner
* Governança de recursos
* Autenticação do repositório
* Porta do contêiner para o mapeamento da porta de host
* Descoberta e comunicação de contêiner para contêiner
* Capacidade de configurar e definir as variáveis de ambiente

## <a name="packaging-a-docker-container-with-yeoman"></a>Empacotando um contêiner do Docker com yeoman
Ao empacotar um contêiner no Linux, você pode optar por usar um modelo yeoman ou por [criar o pacote de aplicativos manualmente](#manually).

Um aplicativo do Service Fabric pode conter um ou mais contêineres, cada um com uma função específica no fornecimento de funcionalidade do aplicativo. O SDK do Service Fabric para Linux inclui um gerador [Yeoman](http://yeoman.io/) que facilita a criação de seu aplicativo e a adição de uma imagem de contêiner. Vamos usar o Yeoman para criar um aplicativo, com um único contêiner do Docker, chamado de *SimpleContainerApp*. Você pode adicionar mais serviços mais tarde editando os arquivos de manifesto gerados.

## <a name="install-docker-on-your-development-box"></a>Instalar o Docker em sua caixa de desenvolvimento

Execute os seguintes comandos para instalar o docker em sua caixa de desenvolvimento do Linux (se você estiver usando a imagem vagrant no OSX, o docker já estará instalado):

```bash
    sudo apt-get install wget
    wget -qO- https://get.docker.io/ | sh
```

## <a name="create-the-application"></a>Criar o aplicativo
1. Em um terminal, digite `yo azuresfcontainer`.
2. Dê um nome para o seu aplicativo, por exemplo, mycontainerap
3. Forneça a URL da imagem do contêiner de um repositório DockerHub. O parâmetro da imagem assume a forma [repositório]/[nome da imagem]
4. Se a imagem não tiver um ponto de entrada de carga de trabalho definido, em seguida, você precisa especificar explicitamente os comandos de entrada com um conjunto delimitado por vírgulas de comandos para serem executados dentro do contêiner, que manterá o contêiner em execução após a inicialização.

![Gerador de Yeoman do Service Fabric para contêineres][sf-yeoman]

## <a name="deploy-the-application"></a>Implantar o aplicativo

### <a name="using-xplat-cli"></a>Usando a CLI XPlat
Após a compilação do aplicativo, você pode implantá-lo no cluster local usando a CLI do Azure.

1. Conectar-se ao cluster local do Service Fabric.

    ```bash
    azure servicefabric cluster connect
    ```

2. Use o script de instalação fornecido no modelo para copiar o pacote de aplicativo no repositório de imagens do cluster, registrar o tipo de aplicativo e criar uma instância do aplicativo.

    ```bash
    ./install.sh
    ```

3. Abra um navegador e navegue até o Service Fabric Explorer em http://localhost:19080/Explorer (substitua localhost pelo IP privado da VM se estiver usando Vagrant no Mac OS X).
4. Expanda o nó Aplicativos e observe que agora há uma entrada para o seu tipo de aplicativo e outra para a primeira instância desse tipo.
5. Use o script de desinstalação fornecido com o modelo para excluir a instância do aplicativo e cancelar o registro do tipo de aplicativo.

    ```bash
    ./uninstall.sh
    ```

### <a name="using-azure-cli-20"></a>Usando a CLI do Azure 2.0

Confira o documento de referência sobre como gerenciar um [ciclo de vida do aplicativo usando a CLI do Azure 2.0](service-fabric-application-lifecycle-azure-cli-2-0.md).

Para obter um aplicativo de exemplo, [confira os códigos de exemplo de contêiner do Service Fabric no GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-containers)

## <a name="adding-more-services-to-an-existing-application"></a>Adicionando mais serviços a um aplicativo existente

Para adicionar outro serviço de contêiner a um aplicativo já criado usando `yo`, execute as seguintes etapas:

1. Altere o diretório para a raiz do aplicativo existente.  Por exemplo, `cd ~/YeomanSamples/MyApplication`, se `MyApplication` é o aplicativo criado pelo Yeoman.
2. Execute o `yo azuresfcontainer:AddService`

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

> [!NOTE]
> Se a imagem não tiver um ponto de entrada de carga de trabalho definido, em seguida, você precisa especificar explicitamente os comandos de entrada dentro do elemento `Commands` com um conjunto delimitado por vírgulas de comandos para serem executados dentro do contêiner, que manterá o contêiner em execução após a inicialização.

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
Usando a política `PortBinding`, você pode mapear uma porta de contêiner para um `Endpoint` no manifesto do serviço. O ponto de extremidade `Endpoint1` pode especificar uma porta fixa (por exemplo, porta 80). Também pode especificar nenhuma porta, quando uma porta aleatória no intervalo de portas do aplicativo do cluster será escolhida para você.

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
* [Interagindo com clusters do Service Fabric usando a CLI do Azure](service-fabric-azure-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-deploy-container-linux/sf-container-yeoman1.png

## <a name="related-articles"></a>Artigos relacionados

* [Introdução ao Service Fabric e à CLI 2.0 do Azure](service-fabric-azure-cli-2-0.md)
* [Introdução à CLI XPlat do Service Fabric](service-fabric-azure-cli.md)

