---
title: Configurar o ambiente de desenvolvimento no Mac OS X para funcionar com o Azure Service Fabric | Microsoft Docs
description: "Instale o tempo de execução, o SDK e as ferramentas e crie um cluster de desenvolvimento local. Depois de concluir a instalação, você estará pronto para criar aplicativos no Mac OS X."
services: service-fabric
documentationcenter: java
author: sayantancs
manager: timlt
editor: 
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/17/2017
ms.author: saysa
ms.openlocfilehash: 328b2778a68e32d95b666124bf7bba969a5f52a6
ms.sourcegitcommit: 7f1ce8be5367d492f4c8bb889ad50a99d85d9a89
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2017
---
# <a name="set-up-your-development-environment-on-mac-os-x"></a>Configurar seu ambiente de desenvolvimento no Mac OS X
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

Você pode criar aplicativos do Azure Service Fabric para serem executados nos clusters do Linux usando o Mac OS X. Este documento aborda como configurar seu Mac para o desenvolvimento.

## <a name="prerequisites"></a>Pré-requisitos
O Azure Service Fabric não é executado nativamente no Mac OS X. Para executar um cluster do Service Fabric local, é fornecida uma imagem de contêiner pré-configurada do Docker. Antes de começar, você precisa do:

* Pelo menos 4 GB de RAM.
* A versão mais recente do [Docker](https://www.docker.com/).
* Acesso à [imagem do contêiner onebox do Docker](https://hub.docker.com/r/servicefabricoss/service-fabric-onebox/) do Service Fabric.

>[!TIP]
>
>Para instalar o Docker no seu Mac, siga as etapas da [documentação do Docker](https://docs.docker.com/docker-for-mac/install/#what-to-know-before-you-install). Depois de instalar, [verifique sua instalação](https://docs.docker.com/docker-for-mac/#check-versions-of-docker-engine-compose-and-machine).
>

## <a name="create-a-local-container-and-set-up-service-fabric"></a>Criar um contêiner local e configuração do Service Fabric
Para configurar um contêiner de Docker local e ter um cluster do Service Fabric em execução, execute as seguintes etapas:

1. Baixe a imagem de contêiner onebox do Service Fabric do repositório de hub do Docker:

    ```bash
    docker pull servicefabricoss/service-fabric-onebox
    ```

2. Atualize a configuração do daemon de Docker em seu host com as configurações a seguir e reinicie o daemon de Docker: 

    ```json
    {
        "ipv6": true,
        "fixed-cidr-v6": "fd00::/64"
    }
    ```
    Você pode atualizar essas configurações diretamente no arquivo daemon.json em seu caminho de instalação do Docker.
    
    >[!NOTE]
    >
    >O local do arquivo daemon.json pode variar de computador para computador. Por exemplo, ~/Library/Containers/com.docker.docker/Data/database/com.docker.driver.amd64-linux/etc/docker/daemon.json.
    >
    >A abordagem recomendada é modificar diretamente as definições de configuração de daemon no Docker. Selecione o **ícone do Docker** e então selecione **Preferências** > **Daemon** > **Avançado**.
    >

3. Inicie uma instância de contêiner onebox do Service Fabric e use a imagem obtida por pull na primeira etapa:

    ```bash
    docker run -itd -p 19080:19080 --name sfonebox servicefabricoss/service-fabric-onebox
    ```
    >[!TIP]
    >Forneça um nome para sua instância de contêiner para que ela possa ser tratada de forma mais legível. 
    >
    >Se o seu aplicativo estiver escutando em determinadas portas, as portas deverão ser especificadas usando marcas `-p` adicionais. Por exemplo, se seu aplicativo estiver escutando na porta 8080, adicione a seguinte marca `-p`:
    >
    >`run docker run -itd -p 19080:19080 -p 8080:8080 --name sfonebox servicefabricoss/service-fabric-onebox`
    >

4. Faça logon no contêiner de Docker no modo SSH interativo:

    ```bash
    docker exec -it sfonebox bash
    ```

5. Execute o script de configuração que buscará as dependências necessárias e inicie o cluster no contêiner:

    ```bash
    ./setup.sh     # Fetches and installs the dependencies required for Service Fabric to run
    ./run.sh       # Starts the local cluster
    ```

6. Após a conclusão da etapa 5, navegue até `http://localhost:19080` do seu Mac. Você deve ver o gerenciador do Service Fabric.

## <a name="set-up-the-service-fabric-cli-sfctl-on-your-mac"></a>Configurar a CLI do Service Fabric (sfctl) no seu Mac

Siga as instruções em [CLI do Service Fabric](service-fabric-cli.md#cli-mac) para instalar a CLI do Service Fabric (`sfctl`) no seu Mac.
Os comandos da CLI dão suporte à interação com entidades do Service Fabric, incluindo clusters, aplicativos e serviços.

## <a name="create-your-application-on-your-mac-by-using-yeoman"></a>Criar seu aplicativo no Mac usando Yeoman

O Service Fabric fornece ferramentas de scaffolding que ajudarão a criar um aplicativo do Service Fabric no terminal usando gerador de modelos Yeoman. Use as etapas a seguir para garantir que o gerador de modelos Yeoman do Service Fabric esteja funcionando em seu computador:

1. O Node.js e o Gerenciador de Pacotes do Nó (NPM) devem estar instalados no seu Mac. O software pode ser instalado usando [HomeBrew](https://brew.sh/), da seguinte maneira:

    ```bash
    brew install node
    node -v
    npm -v
    ```
2. Instale o gerador de modelos [Yeoman](http://yeoman.io/) em seu computador com base no NPM:

    ```bash
    npm install -g yo
    ```
3. Instale o gerador do Yeoman de sua preferência ao executar as etapas na [documentação](service-fabric-get-started-linux.md) do guia de introdução. Para criar apps do Service Fabric usando o Yeoman, execute as etapas:

    ```bash
    npm install -g generator-azuresfjava       # for Service Fabric Java Applications
    npm install -g generator-azuresfguest      # for Service Fabric Guest executables
    npm install -g generator-azuresfcontainer  # for Service Fabric Container Applications
    ```
4. Para criar um aplicativo Java do Service Fabric no seu Mac, o JDK versão 1.8 e o Gradle devem ser instalados no computador host. O software pode ser instalado usando [HomeBrew](https://brew.sh/), da seguinte maneira: 

    ```bash
    brew update
    brew cask install java
    brew install gradle
    ```

## <a name="deploy-your-application-on-your-mac-from-the-terminal"></a>Implantar seu aplicativo no Mac desde o terminal

Depois de criar e compilar o aplicativo do Service Fabric, você pode implantar seu aplicativo usando a [CLI do Service Fabric](service-fabric-cli.md#cli-mac):

1. Conecte-se ao cluster do Service Fabric em execução na instância de contêiner no seu Mac:

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. Do diretório do projeto, execute o script de instalação:

    ```bash
    cd MyProject
    bash install.sh
    ```

## <a name="set-up-net-core-20-development"></a>Configurar o desenvolvimento do .NET Core 2.0

Instale o [SDK do .NET Core 2.0 para Mac](https://www.microsoft.com/net/core#macos) para iniciar a [criação de aplicativos do Service Fabric em C#](service-fabric-create-your-first-linux-application-with-csharp.md). Os pacotes de aplicativos do .NET Core 2.0 Service Fabric estão hospedados em NuGet.org, atualmente em versão prévia.

## <a name="install-the-service-fabric-plug-in-for-eclipse-neon-on-your-mac"></a>Instalar o plug-in do Service Fabric para o Eclipse Neon no Mac

O Azure Service Fabric fornece um plug-in do Eclipse Neon para o IDE de Java. O plug-in simplifica o processo de criação, compilação e implantação de serviços Java. Para instalar ou atualizar o plug-in do Service Fabric para Eclipse para a versão mais recente, execute [estas etapas](service-fabric-get-started-eclipse.md#install-or-update-the-service-fabric-plug-in-in-eclipse-neon). As outras etapas na [documentação do Service Fabric para Eclipse](service-fabric-get-started-eclipse.md) também são aplicáveis: criar um aplicativo, adicionar um serviço a um aplicativo, desinstalar um aplicativo e assim por diante.

A última etapa é instanciar o contêiner com um caminho que é compartilhado com o seu host. O plug-in requer que esse tipo de instanciação trabalhe com o contêiner do Docker em seu Mac. Por exemplo:

```bash
docker run -itd -p 19080:19080 -v /Users/sayantan/work/workspaces/mySFWorkspace:/tmp/mySFWorkspace --name sfonebox servicefabricoss/service-fabric-onebox
```

Os atributos são definidos da seguinte maneira:
* `/Users/sayantan/work/workspaces/mySFWorkspace` é o caminho totalmente qualificado do espaço de trabalho no seu Mac.
* `/tmp/mySFWorkspace` é o caminho no interior do contêiner para onde o espaço de trabalho deve ser mapeado.

>[!NOTE]
> 
>Se você tiver um caminho/nome diferente para seu espaço de trabalho, atualize esses valores no comando `docker run`.
> 
>Se você iniciar o contêiner com um nome diferente de `sfonebox`, atualize-o no arquivo testclient.sh em seu aplicativo Java do ator do Service Fabric.
>

## <a name="next-steps"></a>Próximas etapas
<!-- Links -->
* [Criar e implantar seu primeiro aplicativo Java de do Service Fabric no Linux usando Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* [Criar e implantar seu primeiro aplicativo Java do Service Fabric no Linux usando o plug-in do Service Fabric para o Eclipse](service-fabric-get-started-eclipse.md)
* [Criar um cluster do Service Fabric no portal do Azure](service-fabric-cluster-creation-via-portal.md)
* [Criar um cluster do Service Fabric usando o Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
* [Entender o modelo de aplicativo do Service Fabric](service-fabric-application-model.md)
* [Usar a CLI do Service Fabric para gerenciar seus aplicativos](service-fabric-application-lifecycle-sfctl.md)
* [Preparar um ambiente de desenvolvimento do Linux no Windows](service-fabric-local-linux-cluster-windows.md)

<!-- Images -->
[cluster-setup-script]: ./media/service-fabric-get-started-mac/cluster-setup-mac.png
[sfx-mac]: ./media/service-fabric-get-started-mac/sfx-mac.png
[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship
