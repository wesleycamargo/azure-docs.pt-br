---
title: Configurar o ambiente de desenvolvimento no Linux | Microsoft Docs
description: "Instale o tempo de execução, o SDK e crie um cluster de desenvolvimento local no Linux. Depois de concluir a instalação, você estará pronto para criar aplicativos."
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: d552c8cd-67d1-45e8-91dc-871853f44fc6
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/04/2017
ms.author: subramar
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 8c0f3cc737b999d26359f33d3768dcc55893029c
ms.contentlocale: pt-br
ms.lasthandoff: 05/25/2017


---
# <a name="prepare-your-development-environment-on-linux"></a>Preparar seu ambiente de desenvolvimento no Linux
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

 Para implantar e executar os [aplicativos do Azure Service Fabric](service-fabric-application-model.md) em seu computador de desenvolvimento Linux, instale o tempo de execução e o SDK comum. Você também pode instalar os SDKs opcionais para Java e .NET Core.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="supported-operating-system-versions"></a>Versões de sistema operacional com suporte
Há suporte de desenvolvimento para as seguintes versões de sistema operacional:

* Ubuntu 16.04 (`Xenial Xerus`)

## <a name="update-your-apt-sources"></a>Atualizar suas fontes de apt
Para instalar o SDK e o pacote de tempo de execução associado via apt-get, primeiro atualize suas fontes de apt.

1. Abra um terminal.
2. Adicione o repositório do Service Fabric à sua lista de fontes.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] http://apt-mo.trafficmanager.net/repos/servicefabric/ trusty main" > /etc/apt/sources.list.d/servicefabric.list'
    ```

3. Adicionar o repositório `dotnet` à sua lista de fontes.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

4. Adicione a nova chave GPG ao seu token de autenticação apt.

    ```bash
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 417A0893
    ```

5. Atualize suas listas de pacote com base nos repositórios recém-adicionados.

    ```bash
    sudo apt-get update
    ```

## <a name="install-and-set-up-the-sdk-for-containers-and-guest-executables"></a>Instalar e configurar o SDK para contêineres e executáveis de convidado

Após a atualização de suas fontes, você pode instalar o SDK.

1. Instalar o pacote de SDK do Service Fabric. Você recebe uma solicitação para confirmar a instalação e aceitar um contrato de licença.

    ```bash
    sudo apt-get install servicefabricsdkcommon
    ```

   Os comandos a seguir automatizam a aceitação da licença para pacotes do Service Fabric:
    
    ```bash
    echo "servicefabric servicefabric/accepted-eula-v1 select true" | debconf-set-selections
    echo "servicefabricsdkcommon servicefabricsdkcommon/accepted-eula-v1 select true" | debconf-set-selections
    ```

2. Executar o script de configuração do SDK.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/sdkcommonsetup.sh
    ```

Depois de executar as etapas para instalar o pacote do SDK do Common, a criação de aplicativos com os serviços de arquivo executável ou contêiner do convidado deve ser possível com a execução de `yo azuresfguest`. Talvez seja necessário definir sua variável de ambiente **NODE_PATH $** onde se encontram os módulos do nó. 

    ```bash
    export NODE_PATH=$NODE_PATH:$HOME/.node/lib/node_modules 
    ```

Se você estiver usando o ambiente como raiz, talvez seja necessário definir a variável com o seguinte comando:

    ```bash
    export NODE_PATH=$NODE_PATH:/root/.node/lib/node_modules 
    ```

> [!TIP]
> Você talvez queira adicionar esses comandos ao arquivo ~/.bashrc para não precisar definir a variável de ambiente em cada login.
>

## <a name="set-up-the-azure-cli"></a>Configurar a CLI do Azure
A [CLI do Azure][azure-xplat-cli-github] inclui comandos para interagir com entidades do Service Fabric, incluindo clusters e aplicativos. Ela tem base em Node.js, portanto [certifique-se de que você tenha instalado o Node][install-node] antes de prosseguir com as instruções a seguir:

1. Clone o repositório do github em sua máquina de desenvolvimento.

    ```bash
    git clone https://github.com/Azure/azure-xplat-cli.git
    ```

2. Alterne para o repositório clonado e instale as dependências da CLI usando o Gerenciador de Pacotes do Node (npm).

    ```bash
    cd azure-xplat-cli
    npm install
    ```

3. Crie um symlink da pasta `bin/azure` do repositório clonado para `/usr/bin/azure`.

    ```bash
    sudo ln -s $(pwd)/bin/azure /usr/bin/azure
    ```

4. Por fim, habilite comandos de preenchimento automático do Service Fabric.

    ```bash
    azure --completion >> ~/azure.completion.sh
    echo 'source ~/azure.completion.sh' >> ~/.bash_profile
    source ~/azure.completion.sh
    ```

> [!NOTE]
> Os comandos do Service Fabric ainda não estão disponíveis no Azure CLI 2.0.


## <a name="set-up-a-local-cluster"></a>Configurar um cluster local
Se tudo tiver sido instalado com êxito, você poderá iniciar um cluster local.

1. Execute o script de configuração do cluster.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

2. Abra um navegador da Web e navegue até http://localhost:19080/Explorer. Se o cluster tiver sido iniciado, você deverá ver o painel do Service Fabric Explorer.

    ![Service Fabric Explorer no Linux][sfx-linux]

Neste ponto, você é capaz de implantar pacotes de aplicativos do Service Fabric pré-compilados ou novos com base em contêineres de convidado ou executáveis de convidado. Para compilar novos serviços usando os SDKs do Java ou .NET Core, execute as etapas de configuração opcionais fornecidas nas seções subsequentes.


> [!NOTE]
> Não há suporte para clusters autônomos no Linux – somente os clusters de uma caixa e de vários computadores Linux do Azure têm suporte na visualização.
>

## <a name="install-the-java-sdk-optional-if-you-wish-to-use-the-java-programming-models"></a>Instalar o SDK do Java (opcional, se você quiser usar os modelos de programação do Java)
O SDK do Java fornece as bibliotecas e modelos necessários para compilar serviços do Service Fabric usando o Java.

1. Instalar o pacote SDK do Java.

    ```bash
    sudo apt-get install servicefabricsdkjava
    ```

2. Executar o script de configuração do SDK.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/java/sdkjavasetup.sh
    ```

## <a name="install-the-eclipse-neon-plugin-optional"></a>Instalar o plug-in do Eclipse Neon (opcional)

Você pode instalar o plug-in Eclipse para o Service Fabric de dentro do **IDE do Eclipse para Desenvolvedores Java**. Você pode usar o Eclipse para criar aplicativos executáveis de convidado do Service Fabric e aplicativos de contêiner além dos aplicativos Java do Service Fabric.

> [!NOTE]
> A instalação do SDK do Java é um pré-requisito para usar o plug-in do Eclipse, mesmo se você o usa somente para executáveis convidados e aplicativos de contêiner.
>

1. No Eclipse, verifique se você tem o Eclipse **Neon** mais recente e a versão mais recente do Buildship (1.0.17 ou posterior) instalados. Você pode verificar as versões dos componentes instalados escolhendo **Ajuda > Detalhes da Instalação**. Você pode atualizar o Buildship usando as instruções [aqui][buildship-update].
2. Para instalar o plug-in do Service Fabric, escolha **Ajuda > Instalar Novo Software...**
3. Na caixa de texto "Trabalhar com", digite: http://dl.microsoft.com/eclipse
4. Clique em Adicionar.

    ![Plug-in Eclipse][sf-eclipse-plugin]

5. Escolha o plug-in do Service Fabric e clique em **Avançar**.
6. Continue com a instalação e aceite o contrato de licença do usuário final.

Se você já tiver o plug-in Eclipse do Service Fabric instalado, verifique se está usando a versão mais recente. Você pode verificar selecionando ``Help => Installation Details`` e procurando o Service Fabric na lista de plug-ins instalados. Selecione a atualização se existir uma versão mais recente disponível. 

Para saber mais, confira [Service Fabric: Introdução ao Eclipse](service-fabric-get-started-eclipse.md).


## <a name="install-the-net-core-sdk-optional-if-you-wish-to-use-the-net-core-programming-models"></a>Instalar o SDK do .NET Core (opcional se você quiser usar os modelos de programação do .NET Core)
O SDK do .Net Core fornece as bibliotecas e modelos necessários para compilar serviços do Service Fabric usando o .NET Core.

1. Instalar o pacote do SDK do .NET Core.

   ```bash
   sudo apt-get install servicefabricsdkcsharp
   ```

2. Executar o script de configuração do SDK.

   ```bash
   sudo /opt/microsoft/sdk/servicefabric/csharp/sdkcsharpsetup.sh
   ```

## <a name="updating-the-sdk-and-runtime"></a>Atualização do SDK e Tempo de Execução

Para atualizar para a versão mais recente do SDK e tempo de execução, execute os seguintes comandos (desmarque os SDKs que você não deseja):

   ```bash
   sudo apt-get update
   sudo apt-get install servicefabric servicefabricsdkcommon servicefabricsdkcsharp servicefabricsdkjava
   ```
   
> [!NOTE]
> Atualizar os pacotes pode resultar na interrupção do seu cluster de desenvolvimento local. Reinicie o cluster local após uma atualização seguindo as instruções nesta página.
>
>

Para atualizar a CLI, navegue até o diretório onde você clonou a CLI e execute `git pull` para atualizar.  As notas de versão podem conter etapas adicionais. 


## <a name="next-steps"></a>Próximas etapas
* [Criar e implantar seu primeiro aplicativo Java de do Service Fabric no Linux usando Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* [Criar e implantar seu primeiro aplicativo Java do Service Fabric no Linux usando o plug-in do Service Fabric para o Eclipse](service-fabric-get-started-eclipse.md)
* [Criar seu primeiro aplicativo do CSharp no Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
* [Preparar seu ambiente de desenvolvimento no OSX](service-fabric-get-started-mac.md)
* [Usar a CLI do Azure para gerenciar seus aplicativos do Service Fabric](service-fabric-azure-cli.md)
* [Diferenças Windows/Linux do Service Fabric](service-fabric-linux-windows-differences.md)

<!-- Links -->

[azure-xplat-cli-github]: https://github.com/Azure/azure-xplat-cli
[install-node]: https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!--Images -->

[sf-eclipse-plugin]: ./media/service-fabric-get-started-linux/service-fabric-eclipse-plugin.png
[sfx-linux]: ./media/service-fabric-get-started-linux/sfx-linux.png

