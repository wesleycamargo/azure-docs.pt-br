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
ms.date: 8/10/2017
ms.author: subramar
ms.translationtype: HT
ms.sourcegitcommit: 54454e98a2c37736407bdac953fdfe74e9e24d37
ms.openlocfilehash: da6a8b4824d7215eb1db131680856ac04003f5aa
ms.contentlocale: pt-br
ms.lasthandoff: 07/13/2017

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

Há suporte de desenvolvimento para as seguintes versões de sistema operacional:

* Ubuntu 16.04 (`Xenial Xerus`)

## <a name="update-your-apt-sources"></a>Atualizar suas fontes APT
Para instalar o SDK e o pacote de execução associado via ferramenta da linha de comando apt-get, primeiro atualize as fontes APT (Advanced Packaging Tool).

1. Abra um terminal.
2. Adicione o repositório do Service Fabric à sua lista de fontes.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] http://apt-mo.trafficmanager.net/repos/servicefabric/ trusty main" > /etc/apt/sources.list.d/servicefabric.list'
    ```

3. Adicionar o repositório `dotnet` à sua lista de fontes.

    ```bash
    sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
    ```

4. Adicione a nova chave Gnu Privacy Guard (GnuPG ou GPG) ao arquivo de chaves APT.

    ```bash
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 417A0893
    ```

5. Adicione a chave GPG oficial do Docker ao arquivo de chaves APT.

    ```bash
    sudo apt-get install curl
    sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    ```

6. Configure o repositório do Docker.

    ```bash
    sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
    ```

7. Atualize suas listas de pacote com base nos repositórios recém-adicionados.

    ```bash
    sudo apt-get update
    ```

## <a name="install-and-set-up-the-sdk-for-containers-and-guest-executables"></a>Instalar e configurar o SDK para contêineres e executáveis de convidado

Após a atualização das fontes, você poderá instalar o SDK.

1. Instale o pacote do SDK do Service Fabric, confirme a instalação e aceite o contrato de licença.

    ```bash
    sudo apt-get install servicefabricsdkcommon
    ```

    >   [!TIP]
    >   Os comandos a seguir automatizam a aceitação da licença para pacotes do Service Fabric:
    >   ```bash
    >   echo "servicefabric servicefabric/accepted-eula-v1 select true" | sudo debconf-set-selections
    >   echo "servicefabricsdkcommon servicefabricsdkcommon/accepted-eula-v1 select true" | sudo debconf-set-selections
    >   ```
    
2. Executar o script de configuração do SDK.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/sdkcommonsetup.sh
    ```

Depois de instalar o pacote SDK comum, você deverá conseguir criar aplicativos com os serviços do contêiner ou do executável do convidado executando `yo azuresfguest` ou `yo azuresfcontainer`. Talvez seja necessário definir a variável de ambiente $NODE_PATH para o local dos módulos do nó. 


```bash
    export NODE_PATH=$NODE_PATH:$HOME/.node/lib/node_modules 
```

Se você estiver usando o ambiente como raiz, poderá ser necessário definir a variável com o seguinte comando:

```bash
    export NODE_PATH=$NODE_PATH:/root/.node/lib/node_modules 
```


> [!TIP]
> Talvez, você queira adicionar esses comandos ao arquivo ~/.bashrc para não precisar definir a variável de ambiente em cada login.
>

## <a name="set-up-the-xplat-service-fabric-cli"></a>Configurar a CLI XPlat do Service Fabric
A [CLI XPlat][azure-xplat-cli-github] inclui comandos para interagir com entidades do Service Fabric, incluindo clusters e aplicativos. Ela é baseada no Node.js, portanto, [verifique se você instalou o Node][install-node] antes de prosseguir com as instruções a seguir:

1. Clone o repositório GitHub em sua máquina de desenvolvimento.

    ```bash
    git clone https://github.com/Azure/azure-xplat-cli.git
    ```

2. Alterne para o repositório clonado e instale as dependências da CLI usando o gerenciador de pacotes do node (npm).

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

### <a name="set-up-azure-cli-20"></a>Configurar a CLI do Azure 2.0

Como alternativa à CLI XPlat, agora há um módulo de comando do Service Fabric incluído na CLI do Azure.

Para obter mais informações sobre como instalar a CLI do Azure 2.0 e usar os comandos do Service Fabric, consulte [Introdução ao Service Fabric e 2.0 do CLI do Azure 2.0](service-fabric-azure-cli-2-0.md).

## <a name="set-up-a-local-cluster"></a>Configurar um cluster local
Se a instalação for bem-sucedida, você poderá iniciar um cluster local.

1. Execute o script de configuração do cluster.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

2. Abra um navegador da web e vá para o [Service Fabric Explorer](http://localhost:19080/Explorer). Se o cluster tiver sido iniciado, você deverá ver o painel do Service Fabric Explorer.

    ![Service Fabric Explorer no Linux][sfx-linux]

Neste ponto, você é capaz de implantar os pacotes do aplicativo Service Fabric predefinidos ou novos com base nos contêineres ou executáveis do convidado. Para compilar novos serviços usando os SDKs do Java ou .NET Core, siga as etapas de configuração opcionais fornecidas nas seções posteriores.


> [!NOTE]
> Não há suporte para os clusters autônomos no Linux. A versão prévia dá suporte a apenas aos clusters com uma caixa e com vários computadores Linux do Azure.
>

## <a name="install-the-java-sdk-optional-if-you-want-to-use-the-java-programming-models"></a>Instalar o SDK do Java (opcional, se você quiser usar os modelos de programação do Java)
O SDK do Java fornece as bibliotecas e modelos necessários para compilar os serviços do Service Fabric usando o Java.

1. Instalar o pacote SDK do Java.

    ```bash
    sudo apt-get install servicefabricsdkjava
    ```

2. Executar o script de configuração do SDK.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/java/sdkjavasetup.sh
    ```

## <a name="install-the-eclipse-neon-plug-in-optional"></a>Instalar o plug-in do Eclipse Neon (opcional)

Você pode instalar o plug-in Eclipse do Service Fabric de dentro do **IDE do Eclipse para Desenvolvedores Java**. Você pode usar o Eclipse para criar aplicativos executáveis de convidado do Service Fabric e aplicativos de contêiner além dos aplicativos Java do Service Fabric.

> [!NOTE]
> O SDK do Java é um pré-requisito para usar o plug-in do Eclipse, mesmo que você o utilize somente para os aplicativos de contêiner e executáveis do convidado.
>

1. No Eclipse, verifique se você tem o Eclipse Neon mais recente e a versão mais recente do Buildship (1.0.17 ou posterior) instalados. Você pode verificar as versões dos componentes instalados selecionando **Ajuda** > **Detalhes da Instalação**. Você pode atualizar Buildship usando as instruções em [Buildship do Eclipse: plug-ins do Eclipse para Gradle][buildship-update].

2. Para instalar o plug-in Service Fabric, selecione**Ajuda** > **Instalar Novo Software**.

3. Na caixa **Trabalhar com**, digite **http://dl.microsoft.com/eclipse**.

4. Clique em **Adicionar**.

    ![A página Software Disponível][sf-eclipse-plugin]

5. Selecione o plug-in **ServiceFabric** e clique em **Avançar**.

6. Conclua as etapas de instalação e aceite o contrato de licença do usuário final.

Se você já tiver o plug-in Service Fabric instalado, verifique se tem a versão mais recente. Você pode verificar selecionando **Ajuda** > **Detalhes da Instalação** e pesquisando Service Fabric na lista de plug-ins instalados. Se uma versão mais recente estiver disponível, selecione **Atualizar**.

Para obter mais informações, consulte [Plug-in Service Fabric para o desenvolvimento de aplicativos Java do Eclipse](service-fabric-get-started-eclipse.md).


## <a name="install-the-net-core-sdk-optional-if-you-want-to-use-the-net-core-programming-models"></a>Instalar o SDK do .NET Core (opcional se você quiser usar os modelos de programação do .NET Core)
O SDK do .NET Core fornece as bibliotecas e os modelos necessários para compilar os serviços do Service Fabric com o .NET Core.

1. Instalar o pacote do SDK do .NET Core.

   ```bash
   sudo apt-get install servicefabricsdkcsharp
   ```

2. Executar o script de configuração do SDK.

   ```bash
   sudo /opt/microsoft/sdk/servicefabric/csharp/sdkcsharpsetup.sh
   ```

## <a name="update-the-sdk-and-runtime"></a>Atualizar SDK e execução

Para atualizar para a versão mais recente do SDK e tempo de execução, execute os seguintes comandos (desmarque os SDKs que você não deseja):

```bash
sudo apt-get update
sudo apt-get install servicefabric servicefabricsdkcommon servicefabricsdkcsharp servicefabricsdkjava
```


> [!NOTE]
> Atualizar os pacotes pode causar a interrupção do seu cluster de desenvolvimento local. Reinicie o cluster local após uma atualização seguindo as instruções nesta página.

## <a name="next-steps"></a>Próximas etapas
* [Criar e implantar seu primeiro aplicativo Java do Service Fabric no Linux usando o Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* [Criar e implantar seu primeiro aplicativo Java do Service Fabric no Linux usando o plug-in Service Fabric para o Eclipse](service-fabric-get-started-eclipse.md)
* [Criar seu primeiro aplicativo do CSharp no Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
* [Preparar seu ambiente de desenvolvimento no OSX](service-fabric-get-started-mac.md)
* [Usar a CLI XPlat para gerenciar seus aplicativos do Service Fabric](service-fabric-azure-cli.md)
* [Diferenças Windows/Linux do Service Fabric](service-fabric-linux-windows-differences.md)

## <a name="related-articles"></a>Artigos relacionados

* [Introdução ao Service Fabric e à CLI do Azure 2.0](service-fabric-azure-cli-2-0.md)
* [Introdução à CLI XPlat do Service Fabric](service-fabric-azure-cli.md)

<!-- Links -->

[azure-xplat-cli-github]: https://github.com/Azure/azure-xplat-cli
[install-node]: https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!--Images -->

[sf-eclipse-plugin]: ./media/service-fabric-get-started-linux/service-fabric-eclipse-plugin.png
[sfx-linux]: ./media/service-fabric-get-started-linux/sfx-linux.png

