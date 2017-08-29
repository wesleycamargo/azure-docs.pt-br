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
ms.date: 8/23/2017
ms.author: subramar
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: 4f51030446d2d2a5a11018b1fce7d7e9193f3dfc
ms.contentlocale: pt-br
ms.lasthandoff: 08/24/2017

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
    sudo sh -c 'echo "deb [arch=amd64] http://apt-mo.trafficmanager.net/repos/servicefabric/ xenial main" > /etc/apt/sources.list.d/servicefabric.list'
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

## <a name="install-and-set-up-the-sdk-for-local-cluster-setup"></a>Instalar e configurar o SDK para a configuração do cluster local

Após a atualização das fontes, você poderá instalar o SDK. Instale o pacote do SDK do Service Fabric, confirme a instalação e aceite o contrato de licença.

```bash
sudo apt-get install servicefabricsdkcommon
```

>   [!TIP]
>   Os comandos a seguir automatizam a aceitação da licença para pacotes do Service Fabric:
>   ```bash
>   echo "servicefabric servicefabric/accepted-eula-v1 select true" | sudo debconf-set-selections
>   echo "servicefabricsdkcommon servicefabricsdkcommon/accepted-eula-v1 select true" | sudo debconf-set-selections
>   ```

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

## <a name="set-up-the-service-fabric-cli"></a>Configurar a CLI do Service Fabric

A [CLI do Service Fabric](service-fabric-cli.md) tem comandos para interagir com as entidades do Service Fabric, inclusive clusters e aplicativos. Ela se baseia no python, portanto, tenha o python e o pip instalados antes de prosseguir com o seguinte comando:

```bash
pip install sfctl
```

## <a name="install-and-set-up-the-generators-for-containers-and-guest-executables"></a>Instalar e configurar os geradores para os contêineres e executáveis do convidado
O Service Fabric fornece ferramentas de scaffolding que ajudarão a criar um aplicativo do Service Fabric no terminal usando gerador de modelos Yeoman. Siga as etapas abaixo para garantir que você tenha o gerador de modelos yeoman do Service Fabric funcionando em seu computador.

1. Instalar o nodejs e o NPM em seu computador

  ```bash
  sudo apt-get install npm
  sudo apt install nodejs-legacy
  ```
2. Instalar o gerador de modelos [Yeoman](http://yeoman.io/) em seu computador a partir do NPM

  ```bash
  sudo npm install -g yo
  ```
3. Instalar o gerador de contêineres Yeo do Service Fabric e o gerador de executáveis do convidado a partir do NPM

  ```bash
  sudo npm install -g generator-azuresfcontainer  # for Service Fabric container application
  sudo npm install -g generator-azuresfguest      # for Service Fabric guest executable application
  ```

Depois de instalar os geradores acima, você deverá conseguir criar aplicativos com os serviços do contêiner ou do executável do convidado executando `yo azuresfguest` ou `yo azuresfcontainer` respectivamente.

## <a name="install-the-necessary-java-artifacts-optional-if-you-want-to-use-the-java-programming-models"></a>Instalar os artefatos do Java necessários (opcional, se você quiser usar os modelos de programação do Java)

Para criar serviços do Service Fabric usando o Java, tenha o 1.8 JDK instalado junto com o Gradle que é usado para executar tarefas de compilação. O trecho a seguir instala o Open JDK 1.8 junto com o Gradle. As bibliotecas Java do Service Fabric são extraídas do Maven.

  ```bash
  sudo apt-get install openjdk-8-jdk-headless
  sudo apt-get install gradle
  ```

## <a name="install-the-eclipse-neon-plug-in-optional"></a>Instalar o plug-in do Eclipse Neon (opcional)

Você pode instalar o plug-in Eclipse do Service Fabric de dentro do **IDE do Eclipse para Desenvolvedores Java**. Você pode usar o Eclipse para criar aplicativos executáveis de convidado do Service Fabric e aplicativos de contêiner além dos aplicativos Java do Service Fabric.

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
O SDK do .NET Core fornece as bibliotecas e os modelos necessários para compilar os serviços do Service Fabric com o .NET Core. Instalar o pacote do SDK do .NET Core executando o seguinte:

   ```bash
   sudo apt-get install servicefabricsdkcsharp
   ```

## <a name="update-the-sdk-and-runtime"></a>Atualizar SDK e execução

Para atualizar para a versão mais recente do SDK e tempo de execução, execute os seguintes comandos (desmarque os SDKs que você não deseja):

```bash
sudo apt-get update
sudo apt-get install servicefabric servicefabricsdkcommon servicefabricsdkcsharp
```
Para atualizar os binários SDK do Java a partir do Maven, você precisa atualizar os detalhes da versão do binário correspondente no arquivo ``build.gradle`` para apontar para a versão mais recente. Para saber exatamente onde você precisa atualizar a versão, poderá referenciar qualquer arquivo ``build.gradle`` nos exemplos de introdução do Service Fabric [aqui](https://github.com/Azure-Samples/service-fabric-java-getting-started).

> [!NOTE]
> Atualizar os pacotes pode causar a interrupção do seu cluster de desenvolvimento local. Reinicie o cluster local após uma atualização seguindo as instruções nesta página.

## <a name="next-steps"></a>Próximas etapas

* [Criar e implantar seu primeiro aplicativo Java do Service Fabric no Linux usando o Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* [Criar e implantar seu primeiro aplicativo Java do Service Fabric no Linux usando o plug-in Service Fabric para o Eclipse](service-fabric-get-started-eclipse.md)
* [Criar seu primeiro aplicativo do CSharp no Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
* [Preparar seu ambiente de desenvolvimento no OSX](service-fabric-get-started-mac.md)
* [Usar a CLI do Service Fabric para gerenciar seus aplicativos](service-fabric-application-lifecycle-sfctl.md)
* [Diferenças Windows/Linux do Service Fabric](service-fabric-linux-windows-differences.md)
* [Introdução à CLI do Service Fabric](service-fabric-cli.md)

<!-- Links -->

[azure-xplat-cli-github]: https://github.com/Azure/azure-xplat-cli
[install-node]: https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!--Images -->

[sf-eclipse-plugin]: ./media/service-fabric-get-started-linux/service-fabric-eclipse-plugin.png
[sfx-linux]: ./media/service-fabric-get-started-linux/sfx-linux.png

