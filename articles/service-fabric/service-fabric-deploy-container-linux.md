---
title: "Service Fabric e implantação de contêineres no Linux| Microsoft Docs"
description: "Service Fabric e o uso de contêineres do Docker para implantar aplicativos de microsserviço. Este artigo descreve os recursos que o Service Fabric fornece para contêineres e como implantar uma imagem de contêiner do Docker em um cluster"
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: 
ms.assetid: 4ba99103-6064-429d-ba17-82861b6ddb11
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/24/2016
ms.author: msfussell
translationtype: Human Translation
ms.sourcegitcommit: 57aec98a681e1cb5d75f910427975c6c3a1728c3
ms.openlocfilehash: 9c45513f7d2987a83026adab39257d298292b7a5


---
# <a name="deploy-a-docker-container-to-service-fabric"></a>Implantar um contêiner do Docker no Service Fabric
> [!div class="op_single_selector"]
> * [Implantar contêiner do Windows](service-fabric-deploy-container.md)
> * [Implantar contêiner do Docker](service-fabric-deploy-container-linux.md)
>
>

Este artigo orienta a criação de serviços contentorizados em contêineres do Docker no Linux.

O Service Fabric tem vários recursos de contêiner que ajudam na compilação de aplicativos que são compostos por microsserviços que estão em contêineres. Eles são chamados de serviços em contêineres.

Os recursos incluem;

* Implantação e ativação de imagens de contêiner
* Governança de recursos
* Autenticação do repositório
* Porta do contêiner para o mapeamento da porta de host
* Descoberta e comunicação de contêiner para contêiner
* Capacidade de configurar e definir as variáveis de ambiente

## <a name="packaging-a-docker-container-with-yeoman"></a>Empacotando um contêiner do Docker com yeoman
Ao empacotar um contêiner no Linux, você pode optar por usar um modelo yeoman ou por [criar o pacote de aplicativos manualmente](service-fabric-deploy-container.md#manually).

Um aplicativo do Service Fabric pode conter um ou mais contêineres, cada um com uma função específica no fornecimento de funcionalidade do aplicativo. O SDK do Service Fabric para Linux inclui um gerador [Yeoman](http://yeoman.io/) que facilita a criação de seu aplicativo e a adição de uma imagem de contêiner. Vamos usar o Yeoman para criar um novo aplicativo, com um único contêiner do Docker, chamado de *SimpleContainerApp*. Você pode adicionar mais serviços mais tarde editando os arquivos de manifesto gerados.

## <a name="create-the-application"></a>Criar o aplicativo
1. Em um terminal, digite **yo azuresfguest**.
2. Para o framework, escolha **Contêiner**.
3. Dê um nome para o seu aplicativo, por exemplo, SimpleContainerApp
4. Forneça a URL da imagem do contêiner de um repositório DockerHub. Isso assume a forma [repositório]/[nome da imagem]

![Gerador de Yeoman do Service Fabric para contêineres][sf-yeoman]

## <a name="deploy-the-application"></a>Implantar o aplicativo
Após a compilação do aplicativo, você pode implantá-lo no cluster local usando a CLI do Azure.

1. Conectar-se ao cluster local do Service Fabric.

    ```bash
    azure servicefabric cluster connect
    ```
2. Use o script de instalação fornecido no modelo para copiar o pacote de aplicativo no repositório de imagens do cluster, registrar o tipo de aplicativo e criar uma instância do aplicativo.

    ```bash
    ./install.sh
    ```
3. Abra um navegador e navegue até o Service Fabric Explorer em http://localhost:19080/Explorer (substitua localhost pelo IP privado da VM se estiver usando Vagrant no Mac OS X).
4. Expanda o nó Aplicativos e observe que agora há uma entrada para o seu tipo de aplicativo e outra para a primeira instância desse tipo.
5. Use o script de desinstalação fornecido com o modelo para excluir a instância do aplicativo e cancelar o registro do tipo de aplicativo.

    ```bash
    ./uninstall.sh
    ```

## <a name="next-steps"></a>Próximas etapas
* [Visão geral do Service Fabric e contêineres](service-fabric-containers-overview.md)
* [Interagindo com clusters do Service Fabric usando a CLI do Azure](service-fabric-azure-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-deploy-container-linux/sf-container-yeoman.png



<!--HONumber=Nov16_HO3-->


