---
title: Criar um aplicativo Spring Boot no Service Fabric no Azure | Microsoft Docs
description: Neste guia de início rápido, você implanta um aplicativo Spring Boot para o Azure Service Fabric usando um aplicativo Spring Boot de exemplo.
services: service-fabric
documentationcenter: java
author: suhuruli
manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/23/2017
ms.author: suhuruli
ms.custom: mvc, devcenter
ms.openlocfilehash: 612c397900d496e7f2b92e70f1a1521741ba84e9
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37112228"
---
# <a name="quickstart-deploy-a-java-spring-boot-application-to-service-fabric"></a>Início Rápido: implantar um aplicativo Spring Boot em Java no Service Fabric

O Azure Service Fabric é uma plataforma de sistemas distribuídos para implantação e gerenciamento de contêineres e microsserviços.

Este início rápido mostra como implantar um aplicativo Spring Boot no Service Fabric. Este guia de início rápido usa a [Introdução](https://spring.io/guides/gs/spring-boot/) de exemplo do site do Spring. Usando ferramentas de linha de comando, este guia de início rápido conduz você pela implantação do Spring Boot de exemplo como um aplicativo Service Fabric. Quando terminar, o Guia de Introdução do Spring Boot de exemplo estará funcionando no Service Fabric.

![Captura de tela do aplicativo](./media/service-fabric-quickstart-java-spring-boot/springbootsflocalhost.png)

Neste guia de início rápido, você aprende a:

* Implantar um aplicativo Spring Boot no Service Fabric
* Implantar o aplicativo no cluster local
* Implantar o aplicativo em um cluster no Azure
* Expandir o aplicativo para vários nós
* Executar failover de seu serviço sem nenhuma ocorrência de disponibilidade

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido:

1. Instalar o SDK do Service Fabric e a CLI (interface de linha de comando) do Service Fabric

    a. [Mac](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cli#cli-mac)
    
    b. [Linux](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#installation-methods)

2. [Instalar o Git](https://git-scm.com/)
3. Instalar o Yeoman

    a. [Mac](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-get-started-mac#create-your-application-on-your-mac-by-using-yeoman)

    b. [Linux](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-yeoman-generators-for-containers-and-guest-executables)
4. Configurar um ambiente Java

    a. [Mac](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-get-started-mac#create-your-application-on-your-mac-by-using-yeoman)
    
    b.  [Linux](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-java-development)

## <a name="download-the-sample"></a>Baixar o exemplo

Em uma janela de terminal, execute o comando a seguir para clonar o aplicativo Guia de Introdução do Spring Boot de exemplo em seu computador local.

```bash
git clone https://github.com/spring-guides/gs-spring-boot.git
```

## <a name="build-the-spring-boot-application"></a>Criar o aplicativo Spring Boot 
1. No diretório `gs-spring-boot/complete`, execute o comando a seguir para criar o aplicativo 

    ```bash
    ./gradlew build
    ``` 

## <a name="package-the-spring-boot-application"></a>Empacotar o aplicativo Spring Boot 
1. Dentro do diretório `gs-spring-boot` em seu clone, execute o comando `yo azuresfguest`. 

2. Insira os seguintes detalhes para cada aviso.

    ![Entradas do Yeoman](./media/service-fabric-quickstart-java-spring-boot/yeomanspringboot.png)

3. Na pasta `SpringServiceFabric/SpringServiceFabric/SpringGettingStartedPkg/code`, crie um arquivo chamado `entryPoint.sh`. Adicione o seguinte ao arquivo `entryPoint.sh`. 

    ```bash
    #!/bin/bash
    BASEDIR=$(dirname $0)
    cd $BASEDIR
    java -jar gs-spring-boot-0.1.0.jar
    ```

4. Adicione o recurso **Pontos de Extremidade** ao arquivo `gs-spring-boot/SpringServiceFabric/SpringServiceFabric/SpringGettingStartedPkg/ServiceManifest.xml`

    ```xml 
        <Resources>
          <Endpoints>
            <Endpoint Name="WebEndpoint" Protocol="http" Port="8080" />
          </Endpoints>
       </Resources>
    ```

    O **ServiceManifest.xml** agora tem esta aparência: 

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ServiceManifest Name="SpringGettingStartedPkg" Version="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" >

       <ServiceTypes>
          <StatelessServiceType ServiceTypeName="SpringGettingStartedType" UseImplicitHost="true">
       </StatelessServiceType>
       </ServiceTypes>

       <CodePackage Name="code" Version="1.0.0">
          <EntryPoint>
             <ExeHost>
                <Program>entryPoint.sh</Program>
                <Arguments></Arguments>
                <WorkingFolder>CodePackage</WorkingFolder>
             </ExeHost>
          </EntryPoint>
       </CodePackage>
        <Resources>
          <Endpoints>
            <Endpoint Name="WebEndpoint" Protocol="http" Port="8080" />
          </Endpoints>
       </Resources>
     </ServiceManifest>
    ```

Neste estágio, você criou um aplicativo Service Fabric para o Guia de Introdução do Spring Boot de exemplo que pode ser implantado no Service Fabric.

## <a name="run-the-application-locally"></a>Executar o aplicativo localmente

1. Inicie o cluster local nos computadores Ubuntu executando o seguinte comando:

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

    Se estiver usando um Mac, inicie o cluster local na imagem do Docker (assumindo que você seguiu os [pré-requisitos](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-get-started-mac#create-a-local-container-and-set-up-service-fabric) a fim de configurar o cluster local para Mac). 

    ```bash
    docker run --name sftestcluster -d -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 -p 8080:8080 mysfcluster
    ```

    A inicialização do cluster local leva algum tempo. Para confirmar que o cluster está totalmente funcional, acesse o Service Fabric Explorer em **http://localhost:19080**. Os cinco nós íntegros indicam que o cluster local está em execução. 
    
    ![Integridade do cluster local](./media/service-fabric-quickstart-java-spring-boot/sfxlocalhost.png)

2. Navegue até a pasta `gs-spring-boot/SpringServiceFabric`.
3. Execute o seguinte comando para se conectar ao cluster local.

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```
4. Execute o script `install.sh`.

    ```bash
    ./install.sh
    ```

5. Abra seu navegador da Web favorito e acesse o aplicativo em **http://localhost:8080**.

    ![Front-end do aplicativo local](./media/service-fabric-quickstart-java-spring-boot/springbootsflocalhost.png)

Agora é possível acessar o aplicativo Spring Boot que foi implantado em um cluster do Service Fabric.

## <a name="deploy-the-application-to-azure"></a>Implantar o aplicativo no Azure

### <a name="set-up-your-azure-service-fabric-cluster"></a>Configurar o cluster do Azure Service Fabric

Para implantar o aplicativo em um cluster no Azure, crie seu próprio cluster.

Clusters de entidade são clusters do Service Fabric gratuitos e com tempo limitado hospedados no Azure e executados pela equipe do Service Fabric. Você pode usar party clusters para implantar aplicativos e saber mais sobre a plataforma. O cluster usa um certificado único e autoassinado para segurança entre nós e entre cliente e nó.

Entre e ingresse em um [cluster do Linux](http://aka.ms/tryservicefabric). Baixe o certificado PFX em seu computador clicando no link **PFX**. Clique no link **Leiame** para localizar a senha do certificado e as instruções sobre como configurar vários ambientes para usar o certificado. Mantenha ambas as páginas **Bem-vindo** e **Leiame** abertas. Você usará algumas das instruções nas etapas a seguir.

> [!Note]
> Há um número limitado de party clusters disponíveis por hora. Se você receber um erro ao tentar se inscrever para um cluster de entidade, poderá aguardar um período e tentar novamente, ou pode seguir estas etapas em [Criar um cluster do Service Fabric no Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md) para criar um cluster em sua assinatura.
>
> O serviço Spring Boot está configurado para escutar o tráfego de entrada na porta 8080. Verifique se a porta está aberta no cluster. Se você estiver usando o Cluster de Entidade, essa porta estará aberta.
>

O Service Fabric fornece várias ferramentas que você pode usar para gerenciar um cluster e seus aplicativos:

* Service Fabric Explorer, uma ferramenta baseada no navegador.
* CLI (Interface de Linha de Comando) do Service Fabric, que é executada sobre a CLI 2.0 do Azure.
* Comandos do PowerShell.

Neste início rápido você usa a CLI do Service Fabric e o Service Fabric Explorer.

Para usar a CLI, você precisa criar um arquivo PEM com base no arquivo PFX que você baixou. Para converter o arquivo, use o comando a seguir. (Para clusters de entidade, você pode copiar um comando específico para o arquivo PFX a partir das instruções na página **Leiame**.)

```bash
openssl pkcs12 -in party-cluster-1486790479-client-cert.pfx -out party-cluster-1486790479-client-cert.pem -nodes -passin pass:1486790479
``` 

Para usar o Service Fabric Explorer, você precisa importar o arquivo PFX do certificado que você baixou do site do Cluster de Entidade para o repositório de certificados (Windows ou Mac) ou para o navegador propriamente dito (Ubuntu). Você precisa da senha de chave privada do PFX, que pode ser obtida na página **Leiame**.

Use qualquer método com o qual você está mais familiarizado para importar o certificado em seu sistema. Por exemplo: 

* No Windows: clique duas vezes no arquivo PFX e siga os prompts para instalar o certificado em seu armazenamento pessoal, `Certificates - Current User\Personal\Certificates`. Como alternativa, você pode usar o comando do PowerShell nas instruções **Leiame**.
* No Mac: clique duas vezes no arquivo PFX e siga os prompts para instalar o certificado no conjunto de chaves.
* No Ubuntu: o Mozilla Firefox é o navegador padrão no Ubuntu 16.04. Para importar o certificado para o Firefox, clique no botão de menu no canto superior direito do seu navegador e clique em **Opções**. Na página **Preferências**, use a caixa de pesquisa para procurar por "certificados". Clique em **Exibir Certificados**, selecione a guia **Seus Certificados**, clique em **Importar** e siga os prompts para importar o certificado.

   ![Instalar certificado no Firefox](./media/service-fabric-quickstart-java-spring-boot/install-cert-firefox.png)

### <a name="deploy-the-application-using-cli"></a>Implantar o aplicativo usando a CLI

Agora que o aplicativo e o cluster estão prontos, é possível implantá-los no cluster diretamente da linha de comando.

1. Navegue até a pasta `gs-spring-boot/SpringServiceFabric`.
2. Execute o seguinte comando para se conectar ao cluster do Azure.

    ```bash
    sfctl cluster select --endpoint https://<ConnectionIPOrURL>:19080 --pem <path_to_certificate> --no-verify
    ```
3. Execute o script `install.sh`.

    ```bash
    ./install.sh
    ```

4. Abra seu navegador da Web e acesse o aplicativo em: **http://\<ConnectionIPOrUrl>:8080**.

    ![Front-end do aplicativo local](./media/service-fabric-quickstart-java-spring-boot/springbootsfazure.png)

Agora é possível acessar o aplicativo Spring Boot em execução em um cluster do Service Fabric no Azure.

## <a name="scale-applications-and-services-in-a-cluster"></a>Dimensionar aplicativos e serviços em um cluster

Os serviços do Service Fabric podem ser colocados em escala em um cluster para acomodar uma alteração na carga dos serviços. Dimensione um serviço alterando o número de instâncias em execução no cluster. Existem várias maneiras de colocar seus serviços em escala, por exemplo, usando scripts ou comandos da CLI do Service Fabric (sfctl). As etapas a seguir usam o Service Fabric Explorer.

O Service Fabric Explorer é executado em todos os clusters do Service Fabric e pode ser acessado em um navegador, navegando para a porta de gerenciamento HTTP do cluster (19080), por exemplo, `http://localhost:19080`.

Para dimensionar o serviço de front-end da Web, faça o seguinte:

1. Abra o Service Fabric Explorer no cluster – por exemplo, `http://localhost:19080`.
2. Clique nas reticências (três pontos) ao lado do nó **fabric:/SpringServiceFabric/SpringGettingStarted** no modo de exibição de árvore e escolha **Dimensionar Serviço**.

    ![Dimensionar Serviço do Service Fabric Explorer](./media/service-fabric-quickstart-java-spring-boot/sfxscaleservicehowto.png)

    Agora é possível optar por dimensionar o número de instâncias do serviço.

3. Altere o número para **3** e clique em **Dimensionar Serviço**.

    Uma maneira alternativa de dimensionar o serviço usando a linha de comando é a seguinte.

    ```bash
    # Connect to your local cluster
    sfctl cluster select --endpoint https://<ConnectionIPOrURL>:19080 --pem <path_to_certificate> --no-verify

    # Run Bash command to scale instance count for your service
    sfctl service update --service-id 'SpringServiceFabric~SpringGettingStarted' --instance-count 3 --stateless 
    ``` 

4. Clique no nó **fabric:/SpringServiceFabric/SpringGettingStarted** do modo de exibição de árvore e expanda o nó de partição (representado por um GUID).

    ![Serviço de colocação em escala do Service Fabric Explorer concluído](./media/service-fabric-quickstart-java-spring-boot/sfxscaledservice.png)

    O serviço tem três instâncias e o modo de exibição de árvore mostra em quais nós as instâncias são executadas.

Com essa tarefa de gerenciamento simples, você dobrou o número de recursos disponíveis para o serviço de front-end processar a carga do usuário. É importante entender que você não precisa de várias instâncias de um serviço para que ele seja executado de forma confiável. Se um serviço falhar, o Service Fabric fará com que uma nova instância de serviço seja executada no cluster.

## <a name="fail-over-services-in-a-cluster"></a>Fazer failover de serviços em um cluster

Para demonstrar o failover do serviço, é possível simular uma reinicialização do nó usando o Service Fabric Explorer. Verifique se apenas uma instância do seu serviço está em execução.

1. Abra o Service Fabric Explorer no cluster – por exemplo, `http://localhost:19080`.
2. Clique nas reticências (três pontos) ao lado do nó que está executando a instância do serviço e reinicie o nó.

    ![Reiniciar o nó do Service Fabric Explorer](./media/service-fabric-quickstart-java-spring-boot/sfxhowtofailover.png)
3. Agora a instância do serviço é movida para um nó diferente, e seu aplicativo não passa por nenhum tempo de inatividade.

    ![Reinicialização do nó do Service Fabric Explorer bem-sucedida](./media/service-fabric-quickstart-java-spring-boot/sfxfailedover.png)

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você aprendeu a:

* Implantar um aplicativo Spring Boot no Service Fabric
* Implantar o aplicativo no cluster local
* Implantar o aplicativo em um cluster no Azure
* Expandir o aplicativo para vários nós
* Executar failover de seu serviço sem nenhuma ocorrência de disponibilidade

Para saber mais sobre como trabalhar com aplicativos Java no Service Fabric, continue o tutorial para os aplicativos Java.

> [!div class="nextstepaction"]
> [Implantar um aplicativo Java](./service-fabric-tutorial-create-java-app.md)