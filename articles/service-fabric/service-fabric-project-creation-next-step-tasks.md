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
   ms.date="12/06/2015"
   ms.author="seanmck"/>

# O seu aplicativo do Service Fabric e as próximas etapas
O seu aplicativo do Service Fabric foi criado. Este artigo descreve a composição do seu projeto e algumas das próximas etapas possíveis.

## Seu aplicativo
Cada novo aplicativo inclui um projeto de aplicativo. Pode haver um ou dois projetos adicionais dependendo do tipo de serviço escolhido.

### O projeto de aplicativo
O projeto de aplicativo consiste em:

- Um conjunto de referências para os serviços que compõem o aplicativo.

- Dois perfis de publicação, um Local e outro de Nuvem, que você pode usar por padrão para manter as preferências de trabalho com ambientes diferentes, como um ponto de extremidade do cluster e as implantações de atualização.

- Dois arquivos de parâmetro de aplicativos, um Local e outro de Nuvem, que servem para manter as configurações do aplicativo específicas do ambiente, como o número de partições para criar um serviço.

- Um script de implantação que você pode usar para implantar o aplicativo na linha de comando ou como parte de uma pipeline de integração contínua automatizada.

- O manifesto do aplicativo, que o descreve.

### Reliable Services
Ao adicionar um novo Reliable Services, o Visual Studio adiciona um projeto de serviço à sua solução. O projeto de serviço contém uma classe que se estende de `StatelessService` ou de `StatefulService` dependendo do tipo escolhido.

### Reliable Actors
Ao adicionar um novo Reliable Actor, o Visual Studio adiciona dois projetos à sua solução: um projeto de ator e um projeto de interface.

O projeto de ator define o tipo de ator e seu estado (para atores com estado). O projeto de interface fornece uma interface que outros serviços podem usar para invocar o ator.

Observe que os projetos de ator não contêm nenhum comportamento de inicialização padrão, uma vez que os atores devem ser ativados por outros serviços. Considere adicionar um Reliable Service ou um projeto do ASP.NET para criar e interagir com seus atores.

### ASP.NET 5
Os modelos do ASP.NET 5 fornecidos para uso em aplicativos de Service Fabric são quase idênticos aos disponíveis para projetos do ASP.NET 5 criados independentemente. As únicas diferenças são:

- O projeto contém uma pasta **PackageRoot** para armazenar o ServiceManifest junto com pacotes de dados e de configuração.

- O projeto faz referência a um pacote NuGet adicional (Microsoft.ServiceFabric.AspNet.Hosting), que atua como uma ponte entre o DNX e o Service Fabric.

## Próximas etapas
### Adicionar um front-end da Web ao seu aplicativo
O Service Fabric fornece integração com o ASP.NET 5 para a criação de pontos de entrada baseados na Web para o seu aplicativo. Confira [Adicionando um front-end da Web ao aplicativo][add-web-frontend] para saber como criar uma interface REST baseada na API Web ASP.NET.

### Criar um cluster do Azure
O SDK do Service Fabric fornece um cluster local para desenvolvimento e teste. Para criar um cluster no Azure, confira [Configurando um cluster do Service Fabric no portal do Azure][create-cluster-in-portal]

### Tente implantar no Azure gratuitamente com clusters de terceiros

Se você quiser experimentar implantação e gerenciamento de aplicativos no Azure sem configurar seus próprios clusters, você pode usar o [serviço de cluster de terceiros](http://aka.ms/tryservicefabric) gratuito.

### Publicar seu aplicativo no Azure
Você pode publicar o seu aplicativo diretamente do Visual Studio para um cluster do Azure. Para saber como, confira [Publicando o aplicativo no Azure][publish-app-to-azure].

### Usar o Explorador do Service Fabric para visualizar o seu cluster
O Explorador do Service Fabric oferece uma maneira fácil de visualizar o seu cluster, incluindo aplicativos implantados e layout físico. Para saber mais, confira [Visualizando o cluster com o Gerenciador do Service Fabric][visualize-with-sfx].

### Versão e atualização de serviços
O Service Fabric permite um controle de versão independente e a atualização de serviços independentes em um aplicativo. Para saber mais, confira [Controle de versão e atualização dos serviços][app-upgrade-tutorial].

### Configurar a integração contínua com o Visual Studio Online
Para saber como definir um processo de integração contínuo para o aplicativo do Service Fabric, confira [Configurar a integração contínua com o Visual Studio Online][ci-with-vso].


<!-- Links -->
[add-web-frontend]: ./service-fabric-add-a-web-frontend.md
[create-cluster-in-portal]: ./service-fabric-cluster-creation-via-portal.md
[publish-app-to-azure]: ./service-fabric-publish-app-remote-cluster.md
[visualize-with-sfx]: ./service-fabric-visualizing-your-cluster.md
[ci-with-vso]: ./service-fabric-configure-continuous-integration-with-vso.md
[reliable-services-webapi]: ./service-fabric-reliable-services-communication-webapi.md
[app-upgrade-tutorial]: ./service-fabric-application-upgrade-tutorial.md

<!---HONumber=AcomDC_1210_2015-->