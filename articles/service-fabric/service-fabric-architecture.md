<properties
   pageTitle="Arquitetura da malha do serviço"
   description="Malha de serviços é uma plataforma de sistemas distribuídos, usada para criar aplicativos escalonáveis, confiáveis e facilmente gerenciados para a nuvem. Este artigo mostra a arquitetura da malha de serviço."
   services="service-fabric"
   documentationCenter=".net"
   authors="rishirsinha"
   manager="timlt"
   editor="rishirsinha"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/27/2015"
   ms.author="rsinha"/>

# Arquitetura da malha do serviço

Malha de serviços, como qualquer plataforma, é criada por meio de vários subsistemas. Esses subsistemas em camadas juntos permitem que os desenvolvedores de aplicativos escrevam aplicativos de malha do serviço que são:

* altamente disponíveis, 
* escalonáveis, 
* facilmente gerenciáveis e 
* fáceis de testar.

A figura a seguir mostra a arquitetura e os subsistemas principais da malha de serviço.

![](media/service-fabric-architecture/service-fabric-architecture.png)

Em um sistema distribuído, a capacidade de estabelecer uma comunicação segura entre nós diferentes é crucial. Na parte inferior da pilha é o subsistema de transporte, que fornece a capacidade de estabelecer uma comunicação segura entre os diferentes nós. Acima do subsistema de transporte encontra-se o subsistema de federação, que agrupa os diferentes nós em uma única entidade (chamada de clusters) para que o sistema possa detectar falhas, executar eleição de líder e rotear de forma consistente. O subsistema de confiabilidade, na camada mais alta do subsistema de federação é responsável pela alta confiabilidade dos serviços da malha de serviços através de mecanismos como replicação, gerenciamento de recursos e gerenciamento de failover. O subsistema de federação também serve como base para o subsistema de hospedagem, que gerencia o ciclo de vida de um aplicativo em um único nó. O subsistema de gerenciamento de cluster gerencia o ciclo de vida de vários computadores de aplicativos e serviços. O subsistema de capacidade de teste permite que o desenvolvedor de aplicativos teste seus serviços por meio de falhas simuladas antes da implantação de aplicativos e serviços para ambientes de produção. A malha de serviço também fornece a capacidade de resolver serviços locais através de seu subsistema de comunicação. O modelo de programação de aplicativo exposto aos desenvolvedores é posicionado sobre esses subsistemas.

## Subsistema de transporte
O subsistema de transporte implementa um canal de comunicação de datagrama ponto a ponto. Esse canal é usado para a comunicação dentro do cluster de malha do serviço e para a comunicação entre clientes e o cluster de malha do serviço. Ele suporta o padrão de comunicação unidirecional e de solicitação-resposta, que fornece a base para a implementação de difusão e multicast em camadas superiores. O subsistema de transporte protege a comunicação usando certificados X509 ou segurança do Windows. Esse subsistema é usado internamente pela malha do serviço e não é diretamente acessível para os desenvolvedores para programação de aplicativo.

## Subsistema de federação
Para justificar um conjunto de nós em um sistema distribuído, precisamos ter uma visão consistente do sistema. O subsistema de federação usa os primitivos da comunicação fornecidos pelo subsistema de transporte e junta vários nós em um único sistema unificado que ele pode justificar. Ele fornece os primitivos de sistemas distribuídos necessários para outros subsistemas - detecção de falhas, eleição de líder e roteamento consistente. O subsistema de federação está embutido nas tabelas hash distribuídas com um espaço de token de 128 bits e cria uma topologia de anel sobre os nós, com cada nó no anel sendo alocado a um subconjunto do espaço de token para propriedade. Para detecção de falha, a camada usa um mecanismo de concessão baseado em heartbeat e arbitragem. O subsistema de federação também garante através de uma junção complexa e protocolos de saída que somente um único proprietário de um token exista a qualquer momento. Isso nos permite fornecer eleição de líder e garantias de roteamento consistentes.

## Subsistema de confiabilidade
O subsistema de confiabilidade fornece o mecanismo para manter o estado de um serviço de malha do serviço altamente disponível por meio do uso do _replicator_, o _gerenciador de failover_, e o _balanceador de recurso_.

* O Replicador garante que as alterações de estado da réplica principal serviço automaticamente serão replicadas para réplicas secundárias, mantendo a consistência entre as réplicas primárias e secundárias em um conjunto de réplicas do serviço. O Replicador é responsável pelo gerenciamento de quorum entre as réplicas no conjunto de réplicas. Ele interage com a unidade de failover para obter a lista de operações para replicar e o agente de reconfiguração (RA) fornece a configuração do conjunto de réplicas, que indica as réplicas para a quais as operações devem ser replicadas. A Malha de serviço fornece um replicador padrão chamado Replicador de malha, que pode ser usado por desenvolvedores de serviço para tornar o estado do serviço altamente disponível e confiável.
* O Gerenciador de failover garante que quando nós forem adicionados ou removidos do cluster, carga é automaticamente redistribuída entre os nós disponíveis. Se um nó do cluster falhar, o cluster automaticamente reconfigure as réplicas de serviço para que não haja nenhuma perda de disponibilidade.
* O Balanceador de recurso coloca réplicas do serviço em um domínio de falha no cluster e garante que todas as unidades de failover estão operacionais. O Balanceador de recursos também faz o equilíbrio de recursos para serviços no pool compartilhado subjacente de nós de cluster para alcançar a distribuição de carga uniforme ideal.

## Subsistema de gerenciamento
O subsistema de gerenciamento oferece serviços de ponta a ponta e gerenciamento de ciclo de vida do aplicativo. Cmdlets do PowerShell e APIs administrativas permitem provisionar, implantar, corrigir, atualizar e desprovisionar aplicativos sem perda de disponibilidade. O subsistema de gerenciamento faz isso por meio dos serviços a seguir.

* Gerenciador de cluster - Esse é o principal serviço que interage com o Gerenciador de failover de confiabilidade para colocar os aplicativos com vários nós com base nas restrições de posicionamento de serviço. O balanceador de recursos de failover garante que as restrições nunca sejam desfeitas. O gerenciador de cluster gerencia o ciclo de vida dos aplicativos de provisionamento para o desaprovisionamento. Ele se integra com o gerenciador de integridade descrito na próxima etapa para garantir que a disponibilidade de aplicativos não é perdida de uma perspectiva de semântica de integridade durante atualizações.
* Gerenciador de integridade - Este serviço permite o monitoramento de integridade de aplicativos e serviços e entidades de cluster. Entidades de cluster (por exemplo, nós, serviço partições e réplicas) podem relatar informações de integridade, que são agregadas em um armazenamento centralizado de integridade. Essas informações de integridade fornecem um instantâneo da integridade geral de um ponto no tempo dos serviços e nós distribuídas em vários nós no cluster, permitindo que você tome as ações corretivas necessárias. As APIs de consulta de integridade permitem consultar os eventos de integridade relatados para o subsistema de integridade. As APIs de consulta de integridade retornam os dados brutos de integridade armazenados no armazenamento de integridade ou os dados de integridade agregada e interpretadas para uma entidade de cluster específica.
* Repositório de imagem - esse serviço permite o armazenamento e a distribuição dos binários do aplicativo. Este serviço fornece armazenamento de arquivos distribuídos simples onde os aplicativos são carregados e de onde são baixados.


## Subsistema de hospedagem
O gerenciador de cluster informa o subsistema de hospedagem (em execução em cada nó), os serviços que precisa para gerenciar um nó específico. O subsistema de hospedagem, em seguida, gerencia o ciclo de vida do aplicativo nesse nó. Ele interage com os componentes de confiabilidade e integridade para garantir que as réplicas estão posicionadas corretamente e estão íntegras.

## Subsistema de comunicação
Esse subsistema fornece um sistema de mensagens confiável dentro do cluster e descoberta de serviço por meio do serviço de nomenclatura. O serviço de nomenclatura resolve nomes de serviço para um local no cluster e permite aos usuários gerenciar propriedades e nomes de serviço. Usando o serviço de nomenclatura, os clientes podem comunicar-se com segurança com qualquer nó no cluster para resolver um nome de serviço e recuperar metadados de serviço. Usando uma API de cliente simples de nomenclatura, os usuários da malha de serviço podem desenvolver serviços e clientes capazes de resolver o local de rede atual, independentemente do dinamismo do nó ou do tamanho do cluster.

## Subsistema de capacidade de teste
Capacidade de teste é um conjunto de ferramentas desenvolvidas especificamente para testar serviços criados com base na malha de serviço. As ferramentas permitem que você induza com facilidade falhas significativas e execute cenários de teste para praticar e validar os diferentes estados e transições pelos quais seus serviços passam durante o tempo de vida, tudo de maneira segura e controlada. A capacidade de teste também fornece um mecanismo para executar testes de execução demorada que podem iterar por meio de vários possíveis falhas sem perder a disponibilidade fornecendo aos usuários um ambiente de teste em produção.
 

<!---HONumber=August15_HO6-->