---
title: Configurar o cluster do Linux do Azure Service Fabric no Windows | Microsoft Docs
description: "Este artigo aborda como configurar clusters do Service Fabric Linux executando em máquinas de desenvolvimento do Windows. Isto é particularmente útil para o desenvolvimento de plataformas cruzadas."
services: service-fabric
documentationcenter: .net
author: suhuruli
manager: mfussell
editor: 
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/20/2017
ms.author: suhuruli
ms.openlocfilehash: 57f9dae1b353b873fdc0ec5903018d160cfe384f
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2017
---
# <a name="set-up-a-linux-service-fabric-cluster-on-your-windows-developer-machine"></a>Configurar um cluster do Linux do Service Fabric no seu computador de desenvolvedor do Windows

Este documento aborda como configurar um Service Fabric do Linux local em computadores de desenvolvedor do Windows. Configurar um cluster do Linux local é útil para testar rapidamente aplicativos segmentados para clusters do Linux, mas são desenvolvidos em um computador do Windows.

## <a name="prerequisites"></a>Pré-requisitos
Os clusters do Service Fabric baseados no Linux não são executados nativamente no Windows. Para executar um cluster do Service Fabric local, é fornecida uma imagem de contêiner Docker pré-configurada. Antes de começar, você precisa do:

* Pelo menos 4 GB de RAM
* Versão mais recente do [Docker](https://store.docker.com/editions/community/docker-ce-desktop-windows)
* Acesso à [imagem](https://hub.docker.com/r/servicefabricoss/service-fabric-onebox/) do contêiner do Docker One-box no Service Fabric

>[!TIP]
> * Você pode seguir as etapas mencionadas na [documentação](https://store.docker.com/editions/community/docker-ce-desktop-windows/plans/docker-ce-desktop-windows-tier?tab=instructions) de Docker oficial para instalar o Docker no Windows. 
> * Após concluir a instalação, valide se ele foi instalado corretamente seguindo as etapas mencionadas [aqui](https://docs.docker.com/docker-for-windows/#check-versions-of-docker-engine-compose-and-machine)


## <a name="create-a-local-container-and-setup-service-fabric"></a>Criar um contêiner local e configuração do Service Fabric
Para configurar um contêiner de Docker local e ter um cluster do Service Fabric em execução, execute as seguintes etapas:

1. Faça um pull da imagem do repositório de hub do Docker:

    ```powershell
    docker pull servicefabricoss/service-fabric-onebox
    ```

2. Atualize a configuração do daemon de Docker em seu host com o seguinte e reinicie o daemon de Docker: 

    ```json
    {
      "ipv6": true,
      "fixed-cidr-v6": "2001:db8:1::/64"
    }
    ```
    A maneira recomendada de atualização é - vá para o ícone do Docker > Configurações> Daemon> Avançado e atualize-o lá. Em seguida, reinicie o daemon de Docker para que as alterações entrem em vigor. 

3. Inicie uma instância de contêiner One-box do Service Fabric com a imagem:

    ```powershell
    docker run -itd -p 19080:19080 --name sfonebox servicefabricoss/service-fabric-onebox
    ```
    >[!TIP]
    > * Ao especificar um nome para sua instância de contêiner, você pode tratá-lo de uma maneira mais legível. 
    > * Se o seu aplicativo estiver escutando em determinadas portas, ele deverá ser especificado usando marcas -p adicionais. Por exemplo, se o seu aplicativo estiver escutando na porta 8080, execute o docker run -itd -p 19080:19080 -p 8080:8080 --name sfonebox servicefabricoss/service-fabric-onebox

4. Faça logon no contêiner de Docker no modo ssh interativo:

    ```powershell
    docker exec -it sfonebox bash
    ```

5. Execute o script de configuração, que buscará as dependências necessárias e depois inicie o cluster no contêiner.

    ```bash
    ./setup.sh     # Fetches and installs the dependencies required for Service Fabric to run
    ./run.sh       # Starts the local cluster
    ```

6. Após a conclusão com êxito da etapa 5, você poderá ir para ``http://localhost:19080`` do seu Windows e poderá visualizar o Service Fabric Explorer. Neste ponto, você poderá se conectar ao cluster usando qualquer ferramenta do seu computador de desenvolvedor do Windows e implantar o aplicativo direcionado para clusters do Service Fabric do Linux. 

    > [!NOTE]
    > Atualmente, não há suporte para o plugin Eclipse no Windows. 

## <a name="next-steps"></a>Próximas etapas
* Introdução ao [Eclipse](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-get-started-eclipse)
* Confira outras [amostras de Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)


<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png