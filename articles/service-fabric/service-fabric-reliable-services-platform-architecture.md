<properties
   pageTitle="Arquitetura de Serviços Confiáveis da Malha de Serviços"
   description="Visão geral da arquitetura de serviços confiáveis"
   services="service-fabric"
   documentationCenter=".net"
   authors="AlanWarwick"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/13/2015"
   ms.author="alanwar"/>

# Arquitetura de Serviços Confiáveis

Um Serviço Confiável da Malha de Serviços pode ser com ou sem estado. Cada tipo de serviço é executado em uma arquitetura específica descrita neste artigo. Consulte [Visão geral dos Serviços Confiáveis](../Service-Fabric/service-fabric-reliable-services-introduction.md) para obter mais informações sobre as diferenças entre serviços com e sem estado.

## Serviço Confiável com estado

### Diagrama da arquitetura de Serviços Confiáveis com estado
![Diagrama da arquitetura](./media/service-fabric-reliable-services-platform-architecture/reliable-stateful-service-architecture.png)

### Serviço Confiável com estado

Um serviço confiável com estado pode ser derivado da classe StatefulService ou StatefulServiceBase. Ambas essas classes base são fornecidas pela Malha de Serviços e oferecem vários níveis de suporte e abstração para o serviço com estado para fazer interface com a Malha de Serviços e participar como um serviço no cluster da Malha de Serviços. StatefulService deriva de StatefulServiceBase; StatefulServiceBase oferece aos serviços mais flexibilidade, mas requer mais compreensão dos itens internos da Malha de Serviços. Consulte [Visão geral dos Serviços Confiáveis](../Service-Fabric/service-fabric-reliable-services-introduction.md) e [Uso avançado dos Serviços Confiáveis](../Service-Fabric/service-fabric-reliable-services-advanced-usage.md) para obter mais informações sobre os detalhes do desenvolvimento de serviços usando as classes StatefulService e StatefulServiceBase.

Ambas as classes base gerenciam a vida útil e a função da implementação do serviço. A implementação do serviço pode substituir métodos virtuais de qualquer classe base se a implementação do serviço tiver trabalho a fazer nesses pontos do ciclo de vida de implementação de serviço ou se quiser criar um objeto de ouvinte de comunicação. Embora uma implementação de serviço possa implementar seu próprio objeto de ouvinte de comunicação expondo ICommunicationListener, no diagrama acima, o ouvinte de comunicação é implementado pela Malha de Serviços como a implementação de serviço usa um ouvinte de comunicação implementado pela Malha de Serviços.

O serviço confiável com estado usa o gerenciador de estado confiável para tirar proveito das coleções confiáveis. Coleções confiáveis são estruturas de dados locais que são altamente disponíveis para o serviço, ou seja, estão sempre disponíveis, independentemente de failovers de serviço. Cada tipo de coleção confiável é implementado por um provedor de estado confiável. Para obter mais informações sobre coleções confiáveis, consulte [a visão geral das Coleções Confiáveis](service-fabric-reliable-services-reliable-collections.md)

### Provedores e gerenciador de estado confiável

O gerenciador de estado confiável é o objeto que gerencia os provedores de estado confiável e tem funcionalidade para criar, excluir, enumerar e garantir que os provedores de estado confiável sejam persistentes e altamente disponíveis. Uma instância do provedor de estado confiável representa uma instância de uma estrutura de dados persistente e altamente disponível, como um dicionário ou uma fila. Cada provedor de estado confiável expõe uma interface que é usada pelo serviço estado para interagir com o provedor de estado confiável. Por exemplo, IReliableDictionary é usado para interagir com o dicionário confiável, enquanto IReliableQueue é usado para interagir com a fila confiável. Todos os provedores de estado confiável implementam a interface IReliableState.

O gerenciador de estado confiável tem uma interface denominada IReliableStateManager que permite o acesso a ele pela implementação do serviço com estado. Interfaces para provedores de estado confiável são retornadas por meio de IReliableStateManager.

O gerenciador de estado confiável foi projetado com uma arquitetura de plug-in dinâmica para que novos tipos de coleções confiáveis possam ser conectados dinamicamente.

O dicionário confiável e a fila confiável são criados após a implementação de um repositório diferencial com controle de versão de alto desempenho.

### Replicator transacional

O componente de replicador transacional é responsável por garantir que o estado do seu serviço, que é o estado no gerenciador de estado confiável e nas coleções confiáveis, seja consistente em todas as réplicas que executam seu serviço e que o estado também seja mantido no log. O gerenciador de estado confiável faz interface com o replicador transacional por meio de um mecanismo privado.

O replicador transacional usa um protocolo de rede para comunicar o estado com outras réplicas da instância do serviço para que todas as réplicas tenham informações de estado atualizadas.

O replicador transacional usa um log para manter informações de estado para que as informações de estado sobrevive processo ou falha de nó. A interface para o log é através de um mecanismo privado.

### Registro

O componente de log fornece um repositório persistente de alto desempenho que pode ser otimizado para gravação em discos de estado sólido ou rotatórios e também otimizado para uso mais eficiente do espaço em disco. O design do log é para que o armazenamento persistente (ou seja, discos rígidos) seja local para os nós que estão executando o serviço com estado para permitir latências baixas e alta taxa de transferência em comparação com o armazenamento persistente que não é local para o nó.

O componente de log usa dois tipos de arquivos de log. Há um arquivo de log compartilhado em todo o nó que deve estar em um disco que é usado somente para esse arquivo de log. Esse arquivo é colocado no diretório de trabalho de nó da Malha de Serviços. Cada réplica do serviço também tem um arquivo de log dedicado e é colocada no diretório de trabalho do serviço. O log compartilhado é uma área de transição para as informações de estado, enquanto o arquivo de log dedicado é seu destino final, onde ele é mantido. Nesse design, as informações de estado são primeiro gravadas no arquivo de log compartilhado e transferidas lentamente ao arquivo de log dedicado em segundo plano. Dessa forma, a gravação no log compartilhado teria a menor latência e a maior taxa de transferência para permitir que o serviço progrida com mais rapidez.

No entanto, quando o componente de log está configurado para otimização para discos de estado sólido usando a configuração OptimizeForLocalSSD, as informações de estado são gravadas diretamente no arquivo de log dedicado e ignoram o arquivo de log compartilhado. Como discos de estado sólido não sofrem atrasos devido à contenção de movimentação do cabeçote, não há penalidade para gravar diretamente no arquivo de log dedicado.

Quando o componente de log é otimizado para minimizar o uso de espaço em disco usando o OptimizeLogForLowerDiskUsage, os arquivos de log dedicados são criados como arquivos esparsos do NTFS. Como geralmente os arquivos de log nem sempre são completamente cheios de informações de estado, o uso de arquivos esparsos permite o superprovisionamento do espaço em disco disponível para mais réplicas. Se não configurado dessa forma, o espaço do arquivo de log é pré-alocado e o componente de log pode gravar diretamente no arquivo com o melhor desempenho.

Além de uma interface de modo de usuário mínima para o log, o log é gravado como um driver de modo kernel. Executado como um driver de modo kernel, o log pode fornecer o melhor desempenho para todos os serviços que o usam.

Para obter mais informações sobre como configurar o log, consulte [Configurando Serviços Confiáveis com estado](../Service-Fabric/service-fabric-reliable-services-configuration.md).

## Serviço Confiável sem estado

### Diagrama da arquitetura de Serviços Confiáveis sem estado
![Diagrama da arquitetura](./media/service-fabric-reliable-services-platform-architecture/reliable-stateless-service-architecture.png)

### Serviço Confiável sem estado

Implementações de serviços sem estado derivam da classe StatelessService ou StatelessServiceBase, em que a classe StatelessServiceBase permite mais flexibilidade do que StatelessService. Ambas as classes base gerenciam a vida útil e a função do serviço. A implementação do serviço pode substituir métodos virtuais de qualquer classe base se o serviço tiver trabalho a fazer nesses pontos do ciclo de vida de serviço ou se quiser criar um objeto de ouvinte de comunicação. Embora seu serviço possa implementar seu próprio objeto de ouvinte de comunicação expondo ICommunicationListener, no diagrama acima, o ouvinte de comunicação é implementado pela Malha de Serviços como essa implementação de serviço usa um ouvinte de comunicação implementado pela Malha de Serviços.

Consulte [Visão geral dos Serviços Confiáveis](../Service-Fabric/service-fabric-reliable-services-introduction.md) e [Uso avançado dos Serviços Confiáveis](../Service-Fabric/service-fabric-reliable-services-advanced-usage.md) para obter mais informações sobre os detalhes do desenvolvimento de serviços usando as classes StatelessService e StatelessServiceBase.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Próximas etapas

Para obter mais informações sobre a Malha de Serviços, consulte:

[Visão geral dos Serviços Confiáveis](../Service-Fabric/service-fabric-reliable-services-introduction.md)

[Início rápido](service-fabric-reliable-services-quick-start.md)

[Visão geral das Coleções Confiáveis](service-fabric-reliable-services-reliable-collections.md)

[Uso avançado dos Serviços Confiáveis](../Service-Fabric/service-fabric-reliable-services-advanced-usage.md)

[Configuração de Serviços Confiáveis](../Service-Fabric/service-fabric-reliable-services-configuration.md)
 

<!---HONumber=August15_HO6-->