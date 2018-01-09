---
title: Implantar um aplicativo Spring Boot no Azure Service Fabric | Microsoft Docs
description: "Implante um aplicativo Spring Boot para o Azure Service Fabric usando o Guia de Introdução ao Spring Boot."
services: service-fabric
documentationcenter: java
author: suhuruli
manager: msfussell
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/23/2017
ms.author: suhuruli
ms.custom: mvc, devcenter
ms.openlocfilehash: 544f189e79733c6476bf71e9ce39ab5f35e3d032
ms.sourcegitcommit: 901a3ad293669093e3964ed3e717227946f0af96
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/21/2017
---
# <a name="deploy-a-spring-boot-application"></a>Implantar um aplicativo Spring Boot
O Azure Service Fabric é uma plataforma de sistemas distribuídos para implantação e gerenciamento de contêineres e microsserviços. 

Este guia de início rápido demonstra como implantar um aplicativo Spring Boot no Service Fabric. Este guia de início rápido usa a [Introdução](https://spring.io/guides/gs/spring-boot/) de exemplo do site do Spring. Usando ferramentas de linha de comando, este guia de início rápido conduz você pela implantação do Spring Boot de exemplo como um aplicativo Service Fabric. Quando terminar, o Guia de Introdução do Spring Boot de exemplo estará funcionando no Service Fabric. 

![Captura de tela do aplicativo](./media/service-fabric-quickstart-java-spring-boot/springbootsflocalhost.png)

Neste guia de início rápido, você aprende a:

> [!div class="checklist"]
> * Implantar um aplicativo Spring Boot no Service Fabric
> * Implantar o aplicativo no cluster local 
> * Implantar o aplicativo em um cluster no Azure
> * Expandir o aplicativo para vários nós
> * Executar failover de seu serviço sem nenhuma ocorrência de disponibilidade

## <a name="prerequisites"></a>pré-requisitos
Para concluir este guia de início rápido:
1. [Instalar o SDK do Service Fabric e a CLI (interface de linha de comando) do Service Fabric (CLI)](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#installation-methods)
2. [Instalar o Git](https://git-scm.com/)
3. [Instalar o Yeoman](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-yeoman-generators-for-containers-and-guest-executables)
4. [Configurar um ambiente Java](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started-linux#set-up-java-development)

## <a name="download-the-sample"></a>Baixar o exemplo
Em uma janela de comando, execute o comando a seguir para clonar o aplicativo Guia de Introdução do Spring Boot de exemplo em seu computador local.
```
git clone https://github.com/spring-guides/gs-spring-boot.git
```

## <a name="package-the-spring-boot-application"></a>Empacotar o aplicativo Spring Boot 
1. Dentro do diretório `gs-spring-boot` que foi clonado, execute o comando `yo azuresfguest`. 

2. Insira os seguintes detalhes para cada aviso. 

    ![Entradas do Yeoman](./media/service-fabric-quickstart-java-spring-boot/yeomanspringboot.png)

3. Na pasta `SpringServiceFabric/SpringServiceFabric/SpringGettingStartedPkg/code`, crie um arquivo chamado `entryPoint.sh`. Adicione o seguinte ao arquivo. 

    ```bash
    #!/bin/bash
    BASEDIR=$(dirname $0)
    cd $BASEDIR
    java -jar gs-spring-boot-0.1.0.jar
    ```

Neste estágio, você criou um aplicativo Service Fabric para o Guia de Introdução do Spring Boot de exemplo que pode ser implantado no Service Fabric.

## <a name="run-the-application-locally"></a>Executar o aplicativo localmente
1. Inicie seu cluster local executando o seguinte comando:

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
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

5. Abra seu navegador da Web favorito e acessar o aplicativo acessando **http://localhost:8080**. 

    ![Front-end do aplicativo local](./media/service-fabric-quickstart-java-spring-boot/springbootsflocalhost.png)
    
Agora é possível acessar o aplicativo Spring Boot que foi implantado em um cluster do Service Fabric.  

## <a name="deploy-the-application-to-azure"></a>Implantar o aplicativo no Azure

### <a name="set-up-your-azure-service-fabric-cluster"></a>Configurar o cluster do Azure Service Fabric
Para implantar o aplicativo em um cluster no Azure, crie seu próprio cluster.

Clusters de entidade são clusters do Service Fabric gratuitos e com tempo limitado hospedados no Azure. Eles são executados pela equipe do Service Fabric, em que qualquer pessoa pode implantar aplicativos e conhecer a plataforma. Para obter acesso a um Cluster de Terceiros, [siga as instruções](http://aka.ms/tryservicefabric). 

Para obter informações sobre como criar seu próprio cluster, consulte [Criar um cluster do Service Fabric no Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md).

> [!Note]
> O serviço Spring Boot está configurado para escutar o tráfego de entrada na porta 8080. Verifique se a porta está aberta no cluster. Se você estiver usando o Cluster de Entidade, essa porta estará aberta.
>

### <a name="deploy-the-application-using-cli"></a>Implantar o aplicativo usando a CLI
Agora que o aplicativo e o cluster estão prontos, é possível implantá-los no cluster diretamente da linha de comando.

1. Navegue até a pasta `gs-spring-boot/SpringServiceFabric`.
2. Execute o seguinte comando para se conectar ao cluster do Azure. 

    ```bash
    sfctl cluster select --endpoint http://<ConnectionIPOrURL>:19080
    ```
    
    Se o cluster estiver protegido com um certificado autoassinado, o comando executado será: 

    ```bash
    sfctl cluster select --endpoint https://<ConnectionIPOrURL>:19080 --pem <path_to_certificate> --no-verify
    ```
3. Execute o script `install.sh`. 

    ```bash
    ./install.sh
    ```

4. Abra seu navegador da Web favorito e acesse o aplicativo acessando **http://\<IPOuUrlDeConexão>:8080**. 

    ![Front-end do aplicativo local](./media/service-fabric-quickstart-java-spring-boot/springbootsfazure.png)
    
Agora é possível acessar o aplicativo Spring Boot que foi implantado em um cluster do Service Fabric.  
    
## <a name="scale-applications-and-services-in-a-cluster"></a>Dimensionar aplicativos e serviços em um cluster
Os serviços do Service Fabric podem ser facilmente colocados em escala em um cluster para acomodar uma alteração na carga dos serviços. Dimensione um serviço alterando o número de instâncias em execução no cluster. Existem várias maneiras de colocar seus serviços em escala usando scripts ou comandos da CLI do Service Fabric (sfctl). Neste exemplo, o Service Fabric Explorer é usado.

O Service Fabric Explorer é executado em todos os clusters do Service Fabric e pode ser acessado em um navegador, navegando para a porta de gerenciamento HTTP dos de clusters (19080), por exemplo, `http://localhost:19080`.

Para dimensionar o serviço de front-end da Web, realize as seguintes etapas:

1. Abra o Service Fabric Explorer no cluster – por exemplo, `http://localhost:19080`.
2. Clique nas reticências (três pontos) ao lado do nó **fabric:/SpringServiceFabric/SpringGettingStarted** no modo de exibição de árvore e escolha **Dimensionar Serviço**.

    ![Dimensionar Serviço do Service Fabric Explorer](./media/service-fabric-quickstart-java-spring-boot/sfxscaleservicehowto.png)

    Agora é possível optar por dimensionar o número de instâncias do serviço.

3. Altere o número para **3** e clique em **Dimensionar Serviço**.

    Uma maneira alternativa de dimensionar o serviço usando a linha de comando é a seguinte.

    ```bash 
    # Connect to your local cluster
    sfctl cluster select --endpoint http://localhost:19080

    # Run Bash command to scale instance count for your service
    sfctl service update --service-id 'SpringServiceFabric~SpringGettingStarted` --instance-count 3 --stateless 
    ``` 

4. Clique no nó **fabric:/SpringServiceFabric/SpringGettingStarted** do modo de exibição de árvore e expanda o nó de partição (representado por um GUID).

    ![Serviço de colocação em escala do Service Fabric Explorer concluído](./media/service-fabric-quickstart-java-spring-boot/sfxscaledservice.png)

    O serviço tem três instâncias e o modo de exibição de árvore mostra em quais nós as instâncias são executadas.

Com essa tarefa de gerenciamento simples, os recursos disponíveis para o serviço Spring processar o carregamento do usuário foram aumentados. É importante entender que você não precisa de várias instâncias de um serviço para que ele seja executado de forma confiável. Se um serviço falhar, o Service Fabric garantirá que uma nova instância de serviço seja executada no cluster.

## <a name="fail-over-services-in-a-cluster"></a>Fazer failover de serviços em um cluster 
Para demonstrar o failover do serviço, é possível simular uma reinicialização do nó usando o Service Fabric Explorer. Verifique se apenas uma instância do seu serviço está em execução. 

1. Abra o Service Fabric Explorer no cluster – por exemplo, `http://localhost:19080`.
2. Clique nas reticências (três pontos) ao lado do nó que está executando a instância do serviço e reinicie o nó. 

    ![Reiniciar o nó do Service Fabric Explorer](./media/service-fabric-quickstart-java-spring-boot/sfxhowtofailover.png)
3. Agora a instância do serviço é movida para um nó diferente, e seu aplicativo não passa por nenhum tempo de inatividade. 

    ![Reinicialização do nó do Service Fabric Explorer bem-sucedida](./media/service-fabric-quickstart-java-spring-boot/sfxfailedover.png)

## <a name="next-steps"></a>Próximas etapas
Neste guia de início rápido, você aprendeu a:

> [!div class="checklist"]
> * Implantar um aplicativo Spring Boot no Service Fabric
> * Implantar o aplicativo no cluster local 
> * Implantar o aplicativo em um cluster no Azure
> * Expandir o aplicativo para vários nós
> * Executar failover de seu serviço sem nenhuma ocorrência de disponibilidade

* Saiba mais sobre [como criar microsserviços Java usando Modelos de Programação do Service Fabric](service-fabric-quickstart-java-reliable-services.md)
* Saiba mais sobre [configurar sua integração e implantação contínuas usando Jenkins](service-fabric-cicd-your-linux-applications-with-jenkins.md)
* Confira outros [Exemplos de Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)