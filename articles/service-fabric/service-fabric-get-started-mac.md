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
ms.date: 10/31/2017
ms.author: saysa
ms.openlocfilehash: cf67c377cd5c17f7b540fb23af48a333a9cddf69
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2017
---
# <a name="set-up-your-development-environment-on-mac-os-x"></a>Configurar seu ambiente de desenvolvimento no Mac OS X
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

Você pode criar aplicativos do Service Fabric para serem executados nos clusters do Linux usando o Mac OS X. Este artigo aborda como configurar seu Mac para o desenvolvimento.

## <a name="prerequisites"></a>Pré-requisitos
O Service Fabric não é executado nativamente no OS X. Para executar um cluster do Service Fabric local, fornecemos uma imagem de contêiner pré-configurada do Docker. Antes de começar, você precisa do:

* Pelo menos 4 GB de RAM
* Versão mais recente do [Docker](https://www.docker.com/)
* Acesso à [imagem](https://hub.docker.com/r/servicefabricoss/service-fabric-onebox/) do contêiner do Docker One-box no Service Fabric

>[!TIP]
> * Você pode seguir as etapas mencionadas na [documentação](https://docs.docker.com/docker-for-mac/install/#what-to-know-before-you-install) oficial do Docker para instalar o Docker no seu Mac. 
> * Após concluir a instalação, valide se ele foi instalado corretamente seguindo as etapas mencionadas [aqui](https://docs.docker.com/docker-for-mac/#check-versions-of-docker-engine-compose-and-machine)


## <a name="create-a-local-container-and-setup-service-fabric"></a>Criar um contêiner local e configuração do Service Fabric
Para configurar um contêiner do Docker local e ter um cluster do Service Fabric em execução, execute as seguintes etapas:

1. Faça um pull da imagem do repositório de hub do Docker:

    ```bash
    docker pull servicefabricoss/service-fabric-onebox
    ```

2. Inicie uma instância de contêiner One-box do Service Fabric com a imagem:

    ```bash
    docker run -itd -p 19080:19080 --name sfonebox servicefabricoss/service-fabric-onebox
    ```
    >[!TIP]
    >Ao especificar um nome para sua instância de contêiner, você pode tratá-lo de uma maneira mais legível. 

3. Logon para o contêiner do Docker no modo ssh interativo ssh:

    ```bash
    docker exec -it sfonebox bash
    ```

4. Execute o script de configuração, que buscará as dependências necessárias e depois inicie o cluster no contêiner.

    ```bash
    ./setup.sh     # Fetches and installs the dependencies required for Service Fabric to run
    ./run.sh       # Starts the local cluster
    ```

5. Após a conclusão com êxito da etapa 4, você pode ir para ``http://localhoist:19080`` a partir do seu Mac e você poderá ver o gerenciador do Service Fabric.


## <a name="set-up-the-service-fabric-cli-sfctl-on-your-mac"></a>Configurar a CLI do Service Fabric (sfctl) no seu Mac

Siga as instruções em [CLI do Service Fabric](service-fabric-cli.md#cli-mac) para instalar a CLI do Service Fabric (`sfctl`) no seu Mac.
Os comandos da CLI para interagir com entidades do Service Fabric, incluindo clusters, aplicativos e serviços.

## <a name="create-application-on-your-mac-using-yeoman"></a>Criar aplicativo no Mac usando o Yeoman

O Service Fabric fornece ferramentas de scaffolding que ajudam a criar um aplicativo do Service Fabric no terminal usando gerador de modelos Yeoman. Execute as etapas abaixo para garantir que você tenha o gerador de modelos Yeoman do Service Fabric funcionando em seu computador.

1. Node.js e NPM precisam estar instalados em seu Mac. Se não for possível instalar o Node.js e o NPM usando Homebrew, realize a seguinte etapa. Para verificar as versões do Node.js e do NPM instaladas em seu Mac, use a opção ``-v``.

    ```bash
    brew install node
    node -v
    npm -v
    ```
2. Instalar o gerador de modelos [Yeoman](http://yeoman.io/) em seu computador com base no NPM.

    ```bash
    npm install -g yo
    ```
3. Instale o gerador do Yeoman que você deseja usar, executando as etapas na [documentação](service-fabric-get-started-linux.md) do guia de introdução. Para criar Aplicativos do Service Fabric usando Yeoman, execute as etapas:

    ```bash
    npm install -g generator-azuresfjava       # for Service Fabric Java Applications
    npm install -g generator-azuresfguest      # for Service Fabric Guest executables
    npm install -g generator-azuresfcontainer  # for Service Fabric Container Applications
    ```
4. Para compilar um aplicativo Java do Service Fabric no Mac, você precisaria do JDK 1.8 e Gradle instalados no computador host. Se já não estiverem instalados, você pode instalá-los usando [HomeBrew](https://brew.sh/). 

    ```bash
    brew update
    brew cask install java
    brew install gradle
    ```

## <a name="deploy-application-on-your-mac-from-terminal"></a>Implantar o aplicativo no seu Mac a partir do terminal

Depois de criar e compilar o aplicativo do Service Fabric, você pode implantar seu aplicativo usando a [CLI do Service Fabric](service-fabric-cli.md#cli-mac), seguindo as etapas:

1. Conecte-se ao cluster do Service Fabric em execução na instância de contêiner no seu Mac.

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. Entre no diretório do projeto e execute o script de instalação.

    ```bash
    cd MyProject
    bash install.sh
    ```

## <a name="set-up-net-core-20-development"></a>Configurar o desenvolvimento do .NET Core 2.0

Instale o [SDK do .NET Core 2.0 para Mac](https://www.microsoft.com/net/core#macos) para iniciar a [criação de aplicativos do Service Fabric em C#](service-fabric-create-your-first-linux-application-with-csharp.md). Os pacotes de aplicativos do .NET Core 2.0 Service Fabric estão hospedados em NuGet.org, atualmente em versão prévia.

## <a name="install-the-service-fabric-plugin-for-eclipse-neon-on-your-mac"></a>Instalar o plug-in do Service Fabric para o Eclipse Neon no Mac

O Service Fabric fornece um plug-in para o **Eclipse Neon para Java IDE** que pode simplificar o processo de criação e implantação dos serviços Java. Você pode seguir as etapas de instalação mencionadas nesta [documentação](service-fabric-get-started-eclipse.md#install-or-update-the-service-fabric-plug-in-in-eclipse-neon) geral sobre como instalar ou atualizar o plug-in Eclipse do Service Fabric para a versão mais recente.

Todas as outras etapas mencionadas na [documentação do Eclipse do Service Fabric](service-fabric-get-started-eclipse.md) para criar um aplicativo, adicionar serviço ao aplicativo, instalar/desinstalar o aplicativo, etc. serão aplicáveis aqui também.

Além das etapas acima para que o plug-in de Eclipse do Service Fabric funcione com o contêiner do Docker no seu Mac, você deve criar uma instância do contêiner com um caminho compartilhado com o host, da seguinte maneira:
```bash
docker run -itd -p 19080:19080 -v /Users/sayantan/work/workspaces/mySFWorkspace:/tmp/mySFWorkspace --name sfonebox servicefabricoss/service-fabric-onebox
```
onde ``/Users/sayantan/work/workspaces/mySFWorkspace`` é o caminho totalmente qualificado do espaço de trabalho no Mac e ``/tmp/mySFWorkspace`` é o caminho dentro do contêiner, para onde ele é mapeado.

> [!NOTE]
>1. Se o nome do espaço de trabalho/caminho for diferente, atualize conforme o necessário no comando ``docker run`` acima.
>2. Se você iniciar o contêiner com um nome diferente de ``sfonebox``, atualize-o no arquivo ``testclient.sh`` em seu aplicativo Java do ator do Service Fabric.

## <a name="next-steps"></a>Próximas etapas
<!-- Links -->
* [Criar e implantar seu primeiro aplicativo Java de do Service Fabric no Linux usando Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* [Criar e implantar seu primeiro aplicativo Java do Service Fabric no Linux usando o plug-in do Service Fabric para o Eclipse](service-fabric-get-started-eclipse.md)
* [Criar um cluster do Service Fabric no portal do Azure](service-fabric-cluster-creation-via-portal.md)
* [Criar um cluster do Service Fabric no Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
* [Entender o modelo de aplicativo do Service Fabric](service-fabric-application-model.md)
* [Usar a CLI do Service Fabric para gerenciar seus aplicativos](service-fabric-application-lifecycle-sfctl.md)

<!-- Images -->
[cluster-setup-script]: ./media/service-fabric-get-started-mac/cluster-setup-mac.png
[sfx-mac]: ./media/service-fabric-get-started-mac/sfx-mac.png
[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship
