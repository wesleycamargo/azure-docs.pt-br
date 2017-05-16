---
title: "Criar um aplicativo de contêiner do Azure Service Fabric | Microsoft Docs"
description: "Criar seu primeiro aplicativo de contêiner no Azure Service Fabric.  Criar uma imagem do Docker com o seu aplicativo, forçar a imagem para um registro de contêiner, criar e implantar um aplicativo de contêiner do Service Fabric."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/08/2017
ms.author: ryanwi
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: df5832478bf7049a7bb883eacac4be961ef65cf9
ms.contentlocale: pt-br
ms.lasthandoff: 05/09/2017


---

# <a name="create-your-first-service-fabric-container-app"></a>Criar seu primeiro aplicativo de contêiner do Service Fabric
Executar um aplicativo existente em um contêiner do Windows em um cluster do Service Fabric não requer alterações no seu aplicativo. Este guia rápido orienta a criação de uma imagem de Docker, que contém um aplicativo Web, enviar a nova imagem do Registro de Contêiner do Azure, criando um aplicativo de contêiner do Service Fabric e implantando do aplicativo de contêiner para um cluster do Service Fabric.  Este artigo pressupõe uma compreensão básica sobre o Docker. Saiba mais sobre o Docker lendo a [Visão geral de Docker](https://docs.docker.com/engine/understanding-docker/).

## <a name="prerequisites"></a>Pré-requisitos
Um computador de desenvolvimento executando:
* Visual Studio 2015 ou Visual Studio 2017.
* [Ferramentas e SDK do Service Fabric](service-fabric-get-started.md).
*  Docker para Windows.  [Obter Docker CE para o Windows (estável)](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description). Depois de instalar e iniciar o Docker, clique no ícone de bandeja e selecione **Alternar para contêineres do Windows**. Isso é necessário para executar imagens do Docker com base no Windows.

Um cluster do Windows com três ou mais nós em execução no Windows Server 2016 com contêineres - [Criar um cluster](service-fabric-get-started-azure-cluster.md) ou [experimente o Service Fabric gratuitamente](http://tryazureservicefabrictest.westus.cloudapp.azure.com/). 

Um registro no Registro de Contêiner do Azure - [Crie um registro de contêiner](../container-registry/container-registry-get-started-portal.md) em sua assinatura do Azure. 

## <a name="create-a-simple-web-app"></a>Criar um aplicativo Web simples
Colete todos os ativos que você precisa carregar em uma imagem de Docker em um único local. Para esse início rápido, crie um aplicativo Web "Olá, Mundo" no computador de desenvolvimento.

1. Crie um diretório, como *c:\temp\helloworldapp*.
2. Criar um subdiretório *c:\temp\helloworldapp\content*.
3. Crie um arquivo *index.html* em *c:\temp\helloworldapp\content*.
4. Edite *index.html* e adicione a seguinte linha:
    ```
    <h1>Hello World!</h1>
    ```
5. Salve suas alterações em *index.html*.

## <a name="build-the-docker-image"></a>Criar a imagem de Docker
Crie uma imagem com base na [imagem do microsoft/iis](https://hub.docker.com/r/microsoft/iis/) localizada no Hub do Docker. A imagem do microsoft/iis deriva da imagem do sistema operacional básico do Windows Server Core e contém Serviços de Informações da Internet (IIS).  Executar essa imagem em seu contêiner automaticamente inicia o IIS e os sites instalados.

Defina a imagem do Docker em um Dockerfile. O Dockerfile contém instruções para criar a imagem e carregar o aplicativo que você deseja executar. O Dockerfile é a entrada para o comando ```docker build```, que cria a imagem. 

1. Crie um arquivo *Dockerfile* (sem nenhuma extensão de arquivo) em *c:\temp\helloworldapp* e adicione o seguinte:

    ```
    # The `FROM` instruction specifies the base image. You are
    # extending the `microsoft/iis` image.
    FROM microsoft/iis

    # Create a directory to hold the web app in the container.
    RUN mkdir C:\site

    # Create a new IIS site.
    RUN powershell -NoProfile -Command \
        Import-module IISAdministration; \
        New-IISSite -Name "Site" -PhysicalPath C:\site -BindingInformation "*:8000:"

    # Opens port 8000 on the container.
    EXPOSE 8000

    # The final instruction copies the web app you created earlier into the container.
    ADD content/ /site
    ```

    Não há nenhum comando ```ENTRYPOINT``` neste Dockerfile. Você não precisa de um. Ao executar o Windows Server com IIS, o processo do IIS é o ponto de entrada, que é configurado para iniciar a imagem base.

    Leia a [referência do Dockerfile](https://docs.docker.com/engine/reference/builder/) para saber mais informações.

2. Execute o comando ```docker build``` para criar a imagem que executa seu aplicativo Web. Abra uma janela do PowerShell e navegue até *c:\temp\helloworldapp*. Execute o comando a seguir:

    ```
    docker build -t helloworldapp .
    ```
    Esse comando cria a nova imagem usando as instruções no seu Dockerfile de nomeando (-t marcação) a imagem "helloworldapp". Criar uma imagem puxa a imagem base do Hub do Docker e cria uma nova imagem que adiciona seu aplicativo sobre a imagem base.  A [imagem do microsft/iis](https://hub.docker.com/r/microsoft/iis/) e as imagens base do sistema operacional possuem 10.5 GB e demoram para serem baixadas e extraídas para o seu computador de desenvolvimento.  Talvez seja melhor você sair para almoçar ou tomar um café.  O download leva menos tempo se colocada anteriormente recebeu a imagem base do sistema operacional em seu computador de desenvolvimento.

3. Depois de concluir o comando de compilação, execute o comando `docker images` para ver informações sobre a nova imagem:

    ```
    docker images
    
    REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
    helloworldapp              latest              86838648aab6        2 minutes ago       10.1 GB
    ```

## <a name="verify-the-image-runs-locally"></a>Verifique se a imagem é executada localmente
Verifique a imagem localmente antes de enviá-la ao registro de contêiner.  

1. Iniciar o contêiner com ```docker run```:

    ```
    docker run -d -p 8000:8000 --name my-web-site helloworldapp
    ```

    *name* fornece um nome para o contêiner em execução (em vez da ID do contêiner).

2. Depois que o contêiner iniciar, localize seu endereço IP para que você pode se conectar ao seu contêiner em execução em um navegador:
    ```
    docker inspect -f "{{ .NetworkSettings.Networks.nat.IPAddress }}" my-web-site
    ```

3. Conectar-se ao contêiner em execução.  Abra um navegador da Web apontando para o endereço IP retornado na porta 8000, por exemplo "http://172.31.194.61:8000". Você deve ver o cabeçalho "Olá, Mundo!" ser exibido no navegador.

4. Para interromper o contêiner, execute:

    ```
    docker stop my-web-site
    ```

5. Exclua o contêiner do seu computador de desenvolvimento:

    ```
    docker rm my-web-site
    ```

## <a name="push-the-image-to-the-container-registry"></a>Enviar a imagem para o registro de contêiner
Depois de verificar que o contêiner é executado na máquina de desenvolvimento, envie a imagem para seu registro no Registro de Contêiner do Azure.

1. Execute ``docker login`` para fazer logon em seu registro de contêiner as [credenciais de registro](../container-registry/container-registry-authentication.md).

    O seguinte exemplo passa a ID e senha de uma [entidade de serviço](../active-directory/active-directory-application-objects.md) do Azure Active Directory. Por exemplo, você pode atribuir uma entidade de serviço ao registro para um cenário de automação.

    ```
    docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
    ```

2. O comando a seguir cria uma marca ou alias imagem, com um caminho totalmente qualificado para o registro. Este exemplo coloca a imagem no namespace ```samples``` para evitar confusão na raiz do registro.

    ```
    docker tag helloworldapp myregistry.azurecr.io/samples/helloworldapp
    ```

3.  Enviar a imagem para o eu registro de contêiner:

    ```
    docker push myregistry.azurecr.io/samples/helloworldapp
    ```

## <a name="create-and-package-the-containerized-service-in-visual-studio"></a>Criar e empacotar o serviço em contêineres no Visual Studio
As ferramentas e o SDK do Service Fabric oferecem um modelo de serviço para ajudar você a implantar um contêiner em um cluster do Service Fabric.

1. Inicie o Visual Studio.  Selecione **Arquivo** > **Novo** > **Projeto**.
2. Selecione **Aplicativo do Service Fabric**, nomeie-o como "MyFirstContainer" e clique em **OK**.
3. Selecione **Contêiner Convidado** na lista de **modelos de serviço**.
4. Em **Nome da imagem** insira "myregistry.azurecr.io/samples/helloworldapp", a imagem é enviada para o seu repositório de contêiner. 
5. Dê um nome ao seu serviço e clique em **OK**.
6. Se o seu serviço em contêiner precisar de um ponto de extremidade para comunicação, você poderá adicionar o protocolo, a porta e o tipo a um ```Endpoint``` no arquivo ServiceManifest.xml. Para esse início rápido, o serviço em contêineres escuta na porta 80: 

    ```xml
    <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
    ```
    Ao fornecer ```UriScheme```, o ponto de extremidade do contêiner é registrado automaticamente no serviço de Nomenclatura do Service Fabric para capacidade de descoberta. Um arquivo de exemplo servicemanifest. XML completo é fornecido no final deste artigo. 
7. Configure o mapeamento de porta, da porta para o host, do contêiner especificando uma política ```PortBinding``` no ```ContainerHostPolicies``` do arquivo ApplicationManifest.xml.  Para esse início rápido, ```ContainerPort``` é 8000 (o contêiner expõe a porta 8000, conforme especificado no Dockerfile) e ```EndpointRef``` é "Guest1TypeEndpoint" (o ponto de extremidade definido no manifesto do serviço).  As solicitações de entrada para o serviço na porta 80 são mapeadas para a porta 8000 no contêiner.  Se o seu contêiner precisar autenticar com um repositório privado, adicione ```RepositoryCredentials```.  Para esse início rápido, adicione o nome da conta e a senha para o registro de contêiner de myregistry.azurecr.io. 

    ```xml
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
            <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
            <PortBinding ContainerPort="8000" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ```

    Um arquivo de exemplo ApplicationManifest. XML completo é fornecido no final deste artigo.
8. Configure o ponto de extremidade de conexão do cluster para que você possa publicar o aplicativo para o seu cluster.  Você pode encontrar o ponto de extremidade de conexão do cliente na folha de Visão geral para o cluster no [portal do Azure](https://portal.azure.com). No Gerenciador de Soluções, abra *Cloud.xml* em **MyFirstContainer**->**PublishProfiles**.  Adicione o nome do cluster e a porta de conexão para **ClusterConnectionParameters**.  Por exemplo:
    ```xml
    <ClusterConnectionParameters ConnectionEndpoint="containercluster.westus2.cloudapp.azure.com:19000" />
    ```
    
9. Salve todos os arquivos e crie seu projeto.  

10. Para empacotar seu aplicativo, clique com o botão direito do mouse em **MyFirstContainer** no Gerenciador de Soluções e selecione **Empacotar**. 

## <a name="deploy-the-container-app"></a>Implantar o aplicativo de contêiner
1. Para publicar o seu aplicativo, clique com o botão direito do mouse em **MyFirstContainer** no Gerenciador de Soluções e selecione **Publicar**.

2. O [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) é uma ferramenta baseada na Web para inspecionar e gerenciar aplicativos e nós em um cluster do Service Fabric. Abra um navegador e navegue até http://containercluster.westus2.cloudapp.azure.com:19080/Explorer/ e siga a implantação do aplicativo.  O aplicativo é implantado, mas fica em estado de erro até que a imagem seja baixada nos nós de cluster (o que pode levar algum tempo, dependendo do tamanho da imagem):  ![Erro][1]

3. O aplicativo estará pronto quando ele estiver em no estado ```Ready``` :  ![Pronto][2]

4. Abra um navegador e navegue até http://containercluster.westus2.cloudapp.azure.com. Você deve ver o cabeçalho "Olá, Mundo!" ser exibido no navegador.

## <a name="clean-up"></a>Limpar
Você continua a incorrer em encargos enquanto o cluster estiver em execução, considere [excluir o cluster](service-fabric-get-started-azure-cluster.md#remove-the-cluster).  [Clusters de terceiros](http://tryazureservicefabrictest.westus.cloudapp.azure.com/) são excluídos automaticamente após algumas horas.

Depois que você enviar a imagem para o registro de contêiner, você pode excluir a imagem local do seu computador de desenvolvimento:

```
docker rmi helloworldapp
docker rmi myregistry.azurecr.io/samples/helloworldapp
```

## <a name="complete-example-service-fabric-application-and-service-manifests"></a>Exemplo completo de manifestos de serviço e aplicativo do Service Fabric
Aqui estão os manifestos de aplicativo e serviço completos usados neste guia rápido.

### <a name="servicemanifestxml"></a>ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Guest1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="Guest1Type" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <!-- Follow this link for more information about deploying Windows containers to Service Fabric: https://aka.ms/sfguestcontainers -->
      <ContainerHost>
        <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
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
      <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
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
    <Parameter Name="Guest1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
        <PortBinding ContainerPort="8000" EndpointRef="Guest1TypeEndpoint"/>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Guest1">
      <StatelessService ServiceTypeName="Guest1Type" InstanceCount="[Guest1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre como executar [contêineres no Service Fabric](service-fabric-containers-overview.md).
* Leia mais sobre o [ciclo de vida do aplicativo](service-fabric-application-lifecycle.md) do Service Fabric.
* Confira os [exemplos de código do Service Fabric](https://github.com/Azure-Samples/service-fabric-dotnet-containers) no GitHub.

[1]: ./media/service-fabric-get-started-containers/MyFirstContainerError.png
[2]: ./media/service-fabric-get-started-containers/MyFirstContainerReady.png

