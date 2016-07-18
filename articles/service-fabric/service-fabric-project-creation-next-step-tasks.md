<properties
   pageTitle="Próximas etapas da criação do projeto no Service Fabric | Microsoft Azure"
   description="Este artigo contém links para um conjunto de tarefas básicas de desenvolvimento para o Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/27/2016"
   ms.author="seanmck"/>

# O seu aplicativo do Service Fabric e as próximas etapas
O seu aplicativo do Service Fabric do Azure foi criado. Este artigo descreve a composição do seu projeto e algumas das próximas etapas possíveis.

## Seu aplicativo
Cada novo aplicativo inclui um projeto de aplicativo. Pode haver um ou dois projetos adicionais dependendo do tipo de serviço escolhido.

### O projeto de aplicativo
O projeto de aplicativo consiste em:

- Um conjunto de referências para os serviços que compõem o aplicativo.

- Dois perfis de publicação, um Local e outro de Nuvem, que você pode usar por padrão para manter as preferências de trabalho com ambientes diferentes, como preferências relacionadas a um ponto de extremidade do cluster e a realizar ou não as implantações de atualização por padrão.

- Dois arquivos de parâmetro de aplicativos, um Local e outro de Nuvem, que servem para manter as configurações do aplicativo específicas do ambiente, como o número de partições para criar um serviço.

- Um script de implantação que você pode usar para implantar o aplicativo na linha de comando ou como parte de uma pipeline de implantação e integração contínua automatizada.

- O manifesto do aplicativo, que o descreve. Você pode encontrar o manifesto na pasta ApplicationPackageRoot.

### Serviço sem estado
Ao adicionar um novo serviço sem estado, o Visual Studio adiciona à sua solução um projeto de serviço que inclua um tipo descendente de `StatelessService`. O serviço incrementa uma variável local em um contador.

### Serviço com estado
Ao adicionar um novo serviço com estado, o Visual Studio adiciona à sua solução um projeto de serviço que inclua um tipo descendente de `StatefulService`. O serviço incrementa um contador em seu método `RunAsync` e armazena o resultado em um `ReliableDictionary`.

### Serviço de ator
Ao adicionar um novo reliable actor, o Visual Studio adiciona dois projetos à sua solução: um projeto de ator e um projeto de interface.

O projeto de ator fornece métodos para a configuração e a obtenção do valor de um contador mantido de forma confiável no estado do ator. O projeto de interface fornece uma interface que outros serviços podem usar para invocar o ator.

### API Web sem monitoração de estado
O projeto de API da Web sem monitoração de estado fornece um serviço Web básico que você pode usar para abrir o aplicativo para clientes externos. Para ter mais informações sobre como o projeto é estruturado, consulte [Serviços da API Web do Service Fabric com a auto-hospedagem OWIN](service-fabric-reliable-services-communication-webapi.md).

## Próximas etapas
### Criar um cluster do Azure
O SDK do Service Fabric fornece um cluster local para desenvolvimento e teste. Para criar um cluster no Azure, veja [Configurando um cluster do Service Fabric no portal do Azure][create-cluster-in-portal].

### Tente implantar no Azure gratuitamente com clusters de terceiros

Se você quiser experimentar implantação e gerenciamento de aplicativos no Azure sem configurar seus próprios clusters, você pode usar o [serviço de cluster de terceiros](http://aka.ms/tryservicefabric) gratuito.

### Publicar seu aplicativo no Azure
Você pode publicar o seu aplicativo diretamente do Visual Studio para um cluster do Azure. Para saber como, confira [Publicando o aplicativo no Azure][publish-app-to-azure].

### Usar o Explorador do Service Fabric para visualizar o seu cluster
O Explorador do Service Fabric oferece uma maneira fácil de visualizar o seu cluster, incluindo aplicativos implantados e layout físico. Para saber mais, veja [Visualizando o cluster com o Service Fabric Explorer][visualize-with-sfx].

### Versão e atualização de serviços
O Service Fabric permite controle de versão independente e atualização de serviços independentes em um aplicativo. Para saber mais, confira [Controle de versão e atualização dos serviços][app-upgrade-tutorial].

### Configurar a integração contínua com o Visual Studio Team Services
Para saber como definir um processo de integração contínuo para o aplicativo do Service Fabric, confira [Configurar a integração contínua com o Visual Studio Team Services][ci-with-vso].



<!-- Links -->
[add-web-frontend]: service-fabric-add-a-web-frontend.md
[create-cluster-in-portal]: service-fabric-cluster-creation-via-portal.md
[publish-app-to-azure]: service-fabric-publish-app-remote-cluster.md
[visualize-with-sfx]: service-fabric-visualizing-your-cluster.md
[ci-with-vso]: service-fabric-set-up-continuous-integration.md
[reliable-services-webapi]: service-fabric-reliable-services-communication-webapi.md
[app-upgrade-tutorial]: service-fabric-application-upgrade-tutorial.md

<!---HONumber=AcomDC_0706_2016-->