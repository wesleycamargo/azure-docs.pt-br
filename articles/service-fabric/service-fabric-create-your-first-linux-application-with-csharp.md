---
title: Criar seu primeiro aplicativo do Service Fabric no Linux usando C# | Microsoft Docs
description: Criar e implantar um aplicativo do Service Fabric usando C#
services: service-fabric
documentationcenter: csharp
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: 5a96d21d-fa4a-4dc2-abe8-a830a3482fb1
ms.service: service-fabric
ms.devlang: csharp
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/04/2016
ms.author: subramar
translationtype: Human Translation
ms.sourcegitcommit: f31c8ab3d2b4fd871c92ac0e7d04bc8d5ab86830
ms.openlocfilehash: 046cc1286d894e28ed4d560c7c0b815f582e1e77


---
# <a name="create-your-first-azure-service-fabric-application"></a>Criar seu primeiro aplicativo do Azure Service Fabric
> [!div class="op_single_selector"]
> * [C# - Windows](service-fabric-create-your-first-application-in-visual-studio.md)
> * [Java - Linux](service-fabric-create-your-first-linux-application-with-java.md)
> * [C# - Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
> 
> 

O Service Fabric fornece SDKs para compilação de serviços no Linux em .NET Core e Java. Neste tutorial, vamos ver como criar um aplicativo para Linux e compilar um serviço usando C# (.NET Core).

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, verifique se você [configurar o ambiente de desenvolvimento Linux](service-fabric-get-started-linux.md). Se você estiver usando Mac OS X, poderá [configurar um ambiente de uma caixa do Linux em uma máquina virtual usando Vagrant](service-fabric-get-started-mac.md).

## <a name="create-the-application"></a>Criar o aplicativo
Um aplicativo do Service Fabric pode conter um ou mais serviços, cada um com uma função específica no fornecimento de funcionalidade do aplicativo. O SDK do Service Fabric para Linux inclui um gerador [Yeoman](http://yeoman.io/) que facilita a criação de seu primeiro serviço e a adição de mais serviços posteriormente. Vamos usar Yeoman para criar um novo aplicativo com um único serviço.

1. Em um terminal, digite o comando a seguir para começar a criar o scaffolding: `yo azuresfcsharp`
2. Nome do seu aplicativo.
3. Escolha o tipo de seu primeiro serviço e dê um nome para ele. Para os fins deste tutorial, escolheremos o Serviço Reliable Actor.
   
   ![Gerador de Yeoman do Service Fabric para C#][sf-yeoman]

> [!NOTE]
> Para obter mais informações sobre as opções, confira [Visão geral do modelo de programação do Service Fabric](service-fabric-choose-framework.md).
> 
> 

## <a name="build-the-application"></a>Compilar o aplicativo
Os modelos Yeoman do Service Fabric incluem um script de build para criar o aplicativo por meio do terminal (após navegar até a pasta do terminal).

  ```sh
 cd myapp 
 ./build.sh 
  ```

## <a name="deploy-the-application"></a>Implantar o aplicativo
Após a compilação do aplicativo, você pode implantá-lo no cluster local usando a CLI do Azure.

1. Conectar-se ao cluster local do Service Fabric.
   
    ```sh
    azure servicefabric cluster connect
    ```
2. Use o script de instalação fornecido no modelo para copiar o pacote de aplicativo no repositório de imagens do cluster, registrar o tipo de aplicativo e criar uma instância do aplicativo.
   
    ```bash
    ./install.sh
    ```
3. Abra um navegador e navegue até o Service Fabric Explorer em http://localhost:19080/Explorer (substitua localhost pelo IP privado da VM se estiver usando Vagrant no Mac OS X).
4. Expanda o nó Aplicativos e observe que agora há uma entrada para o seu tipo de aplicativo e outra para a primeira instância desse tipo.

## <a name="start-the-test-client-and-perform-a-failover"></a>Inicie o cliente de teste e execute um failover
Projetos de atores não fazem nada por conta própria. Eles exigem outro serviço ou cliente para enviar mensagens a eles. O modelo de ator inclui um script de teste simples que você pode usar para interagir com o serviço de ator.

1. Execute o script usando o utilitário de inspeção para ver a saída do serviço de ator.
   
    ```bash
    cd myactorsvcTestClient
    watch -n 1 ./testclient.sh
    ```
2. No Service Fabric Explorer, localize o nó que hospeda a réplica primária para o serviço de ator. Na captura de tela abaixo, é o nó 3.
   
    ![Localizar a réplica primária no Service Fabric Explorer][sfx-primary]
3. Clique no nó encontrado na etapa anterior e selecione **Desativar (Reiniciar)** no menu Ações. Esta ação reiniciará um nó no cluster local e forçará um failover para uma das réplicas secundárias em execução em outro nó. Ao fazer isso, preste atenção à saída do cliente de teste e observe que o contador continua a aumentar apesar do failover.



## <a name="adding-more-services-to-an-existing-application"></a>Adicionando mais serviços a um aplicativo existente

Para adicionar outro serviço a um aplicativo já criado usando `yo`, execute as seguintes etapas: 
1. Altere o diretório para a raiz do aplicativo existente.  Por exemplo, `cd ~/YeomanSamples/MyApplication`, se `MyApplication` é o aplicativo criado pelo Yeoman.
2. Execute o `yo azuresfcsharp:AddService`




## <a name="next-steps"></a>Próximas etapas
* [Reliable Actors](service-fabric-reliable-actors-introduction.md)
* [Interagindo com clusters do Service Fabric usando a CLI do Azure](service-fabric-azure-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-csharp/yeoman-csharp.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-csharp/sfx-primary.png



<!--HONumber=Dec16_HO1-->


