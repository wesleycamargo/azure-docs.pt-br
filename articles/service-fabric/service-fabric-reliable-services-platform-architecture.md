---
title: Arquitetura do Reliable Service | Microsoft Docs
description: Visão geral da arquitetura do Reliable Service para serviços com e sem estado
services: service-fabric
documentationcenter: .net
author: AlanWarwick
manager: timlt
editor: vturecek

ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/30/2016
ms.author: alanwar

---
# Arquitetura de Reliable Services com e sem estado
Um serviço confiável do Service Fabric do Azure pode ser com ou sem estado. Cada tipo de serviço é executado em uma arquitetura específica. Essas arquiteturas são descritas neste artigo. Confira [Visão geral do Reliable Services](service-fabric-reliable-services-introduction.md) para saber mais sobre as diferenças entre serviços com e sem estado.

## Serviços Confiáveis com estado
### Arquitetura de um serviço com estado
![Diagrama da arquitetura de um serviço com estado](./media/service-fabric-reliable-services-platform-architecture/reliable-stateful-service-architecture.png)

### Serviço Confiável com estado
Um serviço confiável com estado pode ser derivado da classe StatefulService ou StatefulServiceBase. Ambas essas classes base são fornecidas pelo Service Fabric. Eles oferecem vários níveis de suporte e abstração para o serviço com estado para fazer interface com o Service Fabric – e para participar como um serviço dentro do cluster do Service Fabric.

StatefulService deriva de StatefulServiceBase. StatefulServiceBase oferece aos serviços mais flexibilidade, mas requer mais compreensão dos itens internos do Service Fabric. Confira a [Visão geral dos Reliable Services](service-fabric-reliable-services-introduction.md) e [Uso avançado dos Reliable Services](service-fabric-reliable-services-advanced-usage.md) para saber mais sobre os detalhes do desenvolvimento de serviços usando as classes StatefulService e StatefulServiceBase.

Ambas as classes base gerenciam a vida útil e a função da implementação do serviço. A implementação do serviço pode substituir métodos virtuais de qualquer classe base se a implementação do serviço tiver trabalho a fazer nesses pontos do ciclo de vida de implementação de serviço ou se quiser criar um objeto de ouvinte de comunicação. Embora uma implementação de serviço possa implementar seu próprio objeto de ouvinte de comunicação expondo ICommunicationListener, no diagrama acima, o ouvinte de comunicação é implementado pelo Service Fabric como a implementação de serviço usa um ouvinte de comunicação implementado pelo Service Fabric.

Um serviço confiável com estado usa o gerenciador de estado confiável para tirar proveito das coleções confiáveis. Coleções confiáveis são estruturas de dados locais que são altamente disponíveis para o serviço, ou seja, estão sempre disponíveis, independentemente de failovers de serviço. Cada tipo de coleção confiável é implementado por um provedor de estado confiável. Para obter mais informações sobre coleções confiáveis, consulte [a visão geral das coleções confiáveis](service-fabric-reliable-services-reliable-collections.md).

### Provedores e gerenciador de estado confiáveis
O gerenciador de estado confiável é o objeto que gerencia os provedores de estado confiável. Ele tem a funcionalidade para criar, excluir, enumerar e garantir que os provedores de estado confiável sejam persistentes e altamente disponíveis. Uma instância do provedor de estado confiável representa uma instância de uma estrutura de dados persistente e altamente disponível, como um dicionário ou uma fila.

Cada provedor de estado confiável expõe uma interface que é usada pelo serviço com estado para interagir com o provedor de estado confiável. Por exemplo, IReliableDictionary é usado para interagir com o dicionário confiável, enquanto IReliableQueue é usado para interagir com a fila confiável. Todos os provedores de estado confiável implementam a interface IReliableState.

O gerenciador de estado confiável tem uma interface chamada IReliableStateManager, que permite o acesso por meio do serviço com estado. Interfaces para provedores de estado confiável são retornadas por meio de IReliableStateManager.

O gerenciador de estado confiável usa uma arquitetura de plug-in para que novos tipos de coleções confiáveis possam ser conectados dinamicamente.

O dicionário confiável e a fila confiável são criados após a implementação de um repositório diferencial com controle de versão de alto desempenho.

### Replicator transacional
O componente de replicador transacional é responsável por garantir que o estado de um serviço (ou seja, o estado dentro do estado no gerenciador de estado confiável e nas coleções confiáveis), seja consistente em todas as réplicas que executam o serviço. Ela também garante que o estado seja mantido no log. O gerenciador de estado confiável faz interface com o replicador transacional por meio de um mecanismo privado.

O replicador transacional usa um protocolo de rede para comunicar o estado com outras réplicas da instância do serviço para que todas as réplicas tenham informações de estado atualizadas.

O replicador transacional usa um log para manter informações de estado para que as informações de estado sobrevive processo ou falha de nó. A interface para o log é através de um mecanismo privado.

### Registro
O componente de log fornece um armazenamento persistente de alto desempenho que pode ser otimizado para gravação discos giratórios ou de estado sólido. O design do log é para o armazenamento persistente (ou seja, discos rígidos) ser local para os nós que estão executando o serviço com estado. Isso permite baixas latências e altas taxas de transferência em comparação ao armazenamento persistente remoto, que não é local ao nó.

O componente de log usa vários arquivos de log. Há um arquivo de log compartilhado em todo o nó que todas as réplicas usam, uma vez que ele pode fornecer a menor latência e a maior taxa de transferência para armazenar dados de estado. Por padrão, o log compartilhado é colocado no diretório de trabalho do nó do Service Fabric, mas também pode ser configurado para ser colocado em outro local, idealmente em um disco reservado apenas para o log compartilhado. Cada réplica do serviço também tem um arquivo de log dedicado e é colocada no diretório de trabalho do serviço. Não há nenhum mecanismo para configurar o log dedicado a ser colocado em um local diferente.

O log compartilhado é uma área de transição para as informações de estado da réplica, enquanto o arquivo de log dedicado é seu destino final, onde ele é mantido. Nesse design, as informações de estado são primeiro gravadas no arquivo de log compartilhado e então transferidas lentamente para o arquivo de log dedicado em segundo plano. Dessa forma, a gravação para o log compartilhado teria a menor latência e a maior taxa de transferência, permitindo ao serviço progredir com mais rapidez.

Leituras e gravações no log compartilhado são feitas por meio de E/S direta para o espaço pré-alocado no disco para o arquivo de log compartilhado. Para permitir o uso ideal de espaço em disco na unidade com logs dedicados, o arquivo de log dedicado é criado como um arquivo esparso do NTFS. Observe que isso permitirá excesso de provisionamento de espaço em disco, e o SO mostrará os arquivos de log dedicados usando muito mais espaço em disco do que é realmente usado.

Além de uma interface de modo de usuário mínima para o log, o log é gravado como um driver de modo kernel. Executado como um driver de modo kernel, o log pode fornecer o melhor desempenho para todos os serviços que o usam.

Para saber mais sobre como configurar o log, confira [Configurando Reliable Services com estado](service-fabric-reliable-services-configuration.md).

## Serviço Confiável sem estado
### Arquitetura de um serviço sem estado
![Diagrama da arquitetura de um serviço sem estado](./media/service-fabric-reliable-services-platform-architecture/reliable-stateless-service-architecture.png)

### Serviço Confiável sem estado
Implementações de serviço sem estado derivam da classe StatelessService ou StatelessServiceBase. A classe StatelessServiceBase permite mais flexibilidade do que a classe StatelessService. Ambas as classes base gerenciam o tempo de vida e a função de um serviço.

A implementação do serviço pode substituir métodos virtuais de qualquer classe base se o serviço tiver trabalho a fazer nesses pontos do ciclo de vida de serviço ou se quiser criar um objeto de ouvinte de comunicação. Embora seu serviço possa implementar o próprio objeto de ouvinte de comunicação expondo ICommunicationListener, no diagrama acima, o ouvinte de comunicação é implementado pelo Service Fabric, uma vez que essa implementação de serviço usa um ouvinte de comunicação implementado pelo Service Fabric.

Confira [Visão geral dos Reliable Services](service-fabric-reliable-services-introduction.md) e [Uso avançado dos Reliable Services](service-fabric-reliable-services-advanced-usage.md) para saber mais sobre os detalhes do desenvolvimento de serviços usando as classes StatelessService e StatelessServiceBase.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Próximas etapas
Para obter mais informações sobre a Malha de Serviços, consulte:

[Visão geral de Reliable Services](service-fabric-reliable-services-introduction.md)

[Início rápido](service-fabric-reliable-services-quick-start.md)

[Visão geral das coleções confiáveis](service-fabric-reliable-services-reliable-collections.md)

[Uso avançado de Reliable Services](service-fabric-reliable-services-advanced-usage.md)

[Configuração de Reliable Services](service-fabric-reliable-services-configuration.md)

<!---HONumber=AcomDC_0406_2016-->