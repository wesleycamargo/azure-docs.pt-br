---
title: Service Fabric e Implantação de Contêineres | Microsoft Docs
description: Service Fabric e o uso de contêineres para implantar aplicativos de microsserviço. Este artigo apresenta os recursos que o Service Fabric fornece para contêineres e como implantar uma imagem de contêiner em um cluster
services: service-fabric
documentationcenter: .net
author: msfussell
manager: ''
editor: ''

ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/25/2016
ms.author: msfussell

---
# <a name="preview:-deploy-a-container-to-service-fabric"></a>Preview: implantar um contêiner no Service Fabric
> [!NOTE]
> Este recurso está em preview para o Linux e não está disponível no Windows Server no momento. Ele estará em preview para o Windows Server na próxima versão do Service Fabric após o lançamento do Windows Server 2016 GA e com suporte na versão subsequente depois disso.
> 
> 

O Service Fabric tem vários recursos de contêiner que ajudam na compilação de aplicativos que são compostos por microsserviços que estão em contêineres. Eles são chamados de serviços em contêineres. Os recursos incluem;

* Implantação e ativação de imagens de contêiner
* Governança de recursos
* Autenticação do repositório
* Porta do contêiner para o mapeamento da porta de host
* Descoberta e comunicação de contêiner para contêiner
* Capacidade de configurar e definir as variáveis de ambiente

Vamos analisar cada um dos recursos sucessivamente durante o empacotamento de um serviço em contêiner a ser incluído em seu aplicativo.

## <a name="packaging-a-container"></a>Empacotamento de um contêiner
Ao empacotar um contêiner, você pode optar por usar um modelo de projeto do Visual Studio ou por [criar o pacote de aplicativos manualmente](#manually). Usando o Visual Studio, a estrutura do pacote de aplicativos e os arquivos de manifesto são criados pelo assistente de novo projeto para você.

## <a name="using-visual-studio-to-package-an-existing-executable"></a>Uso do Visual Studio para empacotar um aplicativo executável
> [!NOTE]
> Em uma versão futura do SDK de ferramentas do Visual Studio, você poderá adicionar um contêiner em um aplicativo de uma forma parecida a como você adiciona um convidado executável atualmente. Confira o tópico [Implantar um executável convidado no Service Fabric](service-fabric-deploy-existing-app.md) . Atualmente, você precisa fazer o empacotamento manual, conforme descrito abaixo.
> 
> 

<a id="manually"></a>

## <a name="manually-packaging-and-deploying-container"></a>Empacotamento e implantação manual de contêiner
O processo de empacotamento manual de um serviço em contêiner se baseia nas seguintes etapas:

1. Publicar os contêineres em seu repositório.
2. Criar a estrutura de diretórios do pacote.
3. Editar o arquivo de manifesto do serviço.
4. Editar o arquivo de manifesto do aplicativo.

## <a name="container-image-deployment-and-activation."></a>Implantação e ativação de imagem de contêiner.
No [modelo de aplicativo](service-fabric-application-model.md)do Service Fabric, um contêiner representa um host de aplicativo no qual várias réplicas de serviço são colocadas. Para implantar e ativar um contêiner, coloque o nome da imagem do contêiner em um elemento `ContainerHost` no manifesto do serviço.

No manifesto do serviço, adicione um `ContainerHost` no ponto de entrada e defina o `ImageName` para o nome do repositório de contêiner e imagem. O manifesto parcial a seguir mostra um exemplo de implantação do contêiner chamado *myimage:v1* de um repositório chamado *myrepo*

    <CodePackage Name="Code" Version="1.0">
        <EntryPoint>
          <ContainerHost>
            <ImageName>myrepo/myimagename:v1</ImageName>
            <Commands></Commands>
          </ContainerHost>
        </EntryPoint>
    </CodePackage>

Você pode fornecer comandos de entrada na imagem de contêiner, especificando o elemento opcional `Commands` com uma vírgula delimitada por conjunto de comandos a serem executados dentro do contêiner. 

## <a name="resource-governance"></a>Governança de recursos
A governança de recursos é uma funcionalidade do contêiner e restringe os recursos que o contêiner pode usar no host. O `ResourceGovernancePolicy`, especificado no manifesto do aplicativo, fornece a capacidade de declarar os limites de recurso para um pacote de códigos de serviço. Os limites de recurso podem ser definidos para;

* Memória
* MemorySwap
* CpuShares (peso relativo da CPU)
* MemoryReservationInMB  
* BlkioWeight (peso relativo do BlockIO). 

> [!NOTE]
> Em uma versão futura, será possível oferecer suporte para especificar limites de E/S de um bloco específico, como IOPs, leitura/gravação de BPS e outros.
> 
> 

    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ResourceGovernancePolicy CodePackageRef="FrontendService.Code" CpuShares="500" 
            MemoryInMB="1024" MemorySwapInMB="4084" MemoryReservationInMB="1024" />
        </Policies>
    </ServiceManifestImport>


## <a name="repository-authentication"></a>Autenticação do repositório
Para baixar um contêiner, você terá que fornecer as credenciais de logon para o repositório do contêiner. As credenciais de logon especificadas no manifesto do *aplicativo* são usadas para identificar as informações de logon, ou a chave de SSH, para baixar a imagem de contêiner do repositório de imagens.  O exemplo a seguir mostra uma conta chamada *TestUser* junto com a senha em texto não criptografado. Isso **não** é recomendado.

    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <RepositoryCredentials AccountName="TestUser" Password="12345" PasswordEncrypted="false"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>

A senha pode e deve ser criptografada usando um certificado implantado no computador.

O exemplo a seguir mostra uma conta chamada *TestUser* com a senha criptografada usando um certificado chamado *MyCert*. Você pode usar o comando `Invoke-ServiceFabricEncryptText` do Powershell para criar o texto cifrado secreto para a senha. Confira o artigo [Gerenciamento de segredos em aplicativos do Service Fabric](service-fabric-application-secret-management.md) para saber como. A chave privada do certificado para descriptografar a senha deve ser implantada no computador local em um método fora de banda (no Azure, isso ocorre por meio do Gerenciador de Recursos). Em seguida, quando o Service Fabric implantar o pacote de serviço no computador, ele será capaz de descriptografar o segredo e, juntamente com o nome da conta, autenticar com o repositório de contêiner usando essas credenciais.

    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <RepositoryCredentials AccountName="TestUser" Password="[Put encrypted password here using MyCert certificate ]" PasswordEncrypted="true"/>
            </ContainerHostPolicies>
            <SecurityAccessPolicies>
                <SecurityAccessPolicy ResourceRef="MyCert" PrincipalRef="TestUser" GrantRights="Full" ResourceType="Certificate" />
            </SecurityAccessPolicies>
        </Policies>
    </ServiceManifestImport>

## <a name="container-port-to-host-port-mapping"></a>Porta do contêiner para o mapeamento da porta de host
Você pode configurar uma porta de host usada para se comunicar com o contêiner ao especificar um `PortBinding` no manifesto do aplicativo. A associação de porta mapeia a porta que o serviço está escutando dentro do contêiner para uma porta no host.

    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <PortBinding ContainerPort="8905"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>


## <a name="container-to-container-discovery-and-communication"></a>Descoberta e comunicação de contêiner para contêiner
Usando a política `PortBinding`, você pode mapear uma porta de contêiner para um `Endpoint` no manifesto do serviço, conforme mostrado no exemplo a seguir. O ponto de extremidade `Endpoint1` pode especificar uma porta fixa, por exemplo, a porta 80, ou não especificar nenhuma porta, caso em que uma porta aleatória no intervalo de portas de aplicativo do cluster é escolhida para você.

Para contêineres de convidado, a especificação de um `Endpoint` como este no manifesto do serviço permite que o Service Fabric publique este ponto de extremidade automaticamente no Serviço de Cadastramento para que outros serviços em execução no cluster possam descobrir este contêiner usando as consultas de REST do serviço de resolução. 

    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>

Ao se registrar com o Serviço de Cadastramento, você poderá fazer a comunicação de contêiner para contêiner facilmente no código dentro de seu contêiner usando o [proxy reverso](service-fabric-reverseproxy.md). Tudo o que você precisa fazer é fornecer a porta de escuta de http de proxy reverso e o nome dos serviços com os quais você deseja se comunicar, os definindo como variáveis de ambiente. Confira a próxima seção sobre como fazer isso.  

## <a name="configure-and-set-environment-variables"></a>Configurar e definir as variáveis de ambiente
As variáveis de ambiente podem ser especificadas para cada pacote de códigos no manifesto do serviço para ambos os serviços implantados em contêineres ou como processos/executáveis de convidado. Esses valores de variável de ambiente podem ser substituídos especialmente no manifesto do aplicativo ou especificados durante a implantação como parâmetros do aplicativo.

O trecho XML do manifesto do serviço a seguir mostra um exemplo de como especificar variáveis de ambiente para um pacote de códigos. 

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

Essas variáveis de ambiente podem ser substituídas no nível do manifesto do aplicativo:

    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <EnvironmentOverrides CodePackageRef="FrontendService.Code">
            <EnvironmentVariable Name="BackendServiceName" Value="[BackendSvc]"/>
            <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
        </EnvironmentOverrides>
    </ServiceManifestImport>

No exemplo acima, especificamos um valor explícito para a variável de ambiente `HttpGateway` (19000) enquanto o valor do parâmetro `BackendServiceName` é definido por meio do parâmetro de aplicativo `[BackendSvc]`. Isso permite que você especifique o valor de `BackendServiceName`no momento da implantação do aplicativo em vez de ter um valor fixo no manifesto. 

## <a name="complete-examples-for-application-and-service-manifest"></a>Exemplos completos de aplicativo e manifesto do serviço
Veja a seguir um manifesto do aplicativo de exemplo que mostra os recursos do contêiner.

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


Veja a seguir um manifesto do serviço de exemplo (especificado no manifesto do aplicativo anterior) que mostra os recursos do contêiner

    <ServiceManifest Name="FrontendServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <Description> A service that implements a stateless frontend in a container</Description>
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
            <Eendpoints>
                <Endpoint Name="Endpoint1" Port="80"  UriScheme="http" />
            </Eendpoints>
        </Resources>
    </ServiceManifest>



<!--HONumber=Oct16_HO2-->


