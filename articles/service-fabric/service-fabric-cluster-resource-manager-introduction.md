<properties
   pageTitle="Introdução ao Gerenciador de Recursos de Cluster do Service Fabric"
   description="Uma introdução ao Gerenciador de Recursos de Cluster do Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/03/2016"
   ms.author="masnider"/>

# Introdução ao Gerenciador de Recursos de Cluster do Service Fabric

Tradicionalmente, o gerenciamento de sistemas de TI ou de um conjunto de serviços significava ter alguns computadores dedicados a esses serviços ou sistemas específicos. Muitos serviços principais foram divididos em uma camada "Web" e em uma camada de "dados" ou de "armazenamento", talvez com alguns outros componentes especializados, como um cache. Outros tipos de aplicativo tinham uma camada de mensagens, em que as solicitações fluíam para dentro e para fora, conectada a uma camada de trabalho para qualquer análise ou transformação necessária como uma parte da mensagem. Cada parte tinha computadores específicos ou dois computadores dedicados a ela. O banco de dados tinha dois computadores dedicados a ele, os servidores Web, vários. Se um tipo específico de carga de trabalho fizesse com que os computadores ficassem sobrecarregados, só seria necessário adicionar mais computadores com o tipo de carga de trabalho configurado para execução neles ou substituir alguns dos computadores por outros maiores. Fácil. Se uma máquina falhava, essa parte do aplicativo geral era executada com menor capacidade até que o computador pudesse ser restaurado. Ainda assim, relativamente fácil (mesmo que não necessariamente divertido).

No entanto, vamos dizer que você sentiu a necessidade de escalar horizontalmente e resolveu se arriscar com os contêineres e/ou o microsserviço. De repente, você tem centenas ou milhares de computadores, dezenas de diferentes tipos de serviços, talvez centenas de diferentes instâncias desses serviços, cada um com uma ou mais instâncias ou réplicas para HA (Alta Disponibilidade).

Inesperadamente, já não era tão simples gerenciar seu ambiente com poucos computadores dedicados a tipos de carga de trabalho únicos. Os servidores são virtuais e não têm mais nomes (a mentalidade passou a valorizar a [virtualização](http://www.slideshare.net/randybias/architectures-for-open-and-scalable-clouds/20)) e os computadores não são dedicados a tipos de cargas de trabalho únicos. A configuração tem menos a ver com os computadores e mais a ver com os próprios serviços.

Como consequência dessa desassociação e da divisão do seu aplicativo monolítico em serviços separados, agora você tem muitas combinações com que lidar. Quem decide quais tipos de cargas de trabalho podem ser executados em um hardware específico ou próximos uns dos outros? Quando uma máquina falha... Alguém sabe o que estava em execução lá? Quem é o responsável por garantir o retorno ao funcionamento? Você espera que a máquina (virtual) volte ou suas cargas de trabalho continuam em execução? A intervenção humana é necessária paratudo voltar a funcionar novamente? E as atualizações nesse tipo de ambiente? Você vai precisar de ajuda e sabe que uma profusão de contratações para tentar enfrentar a complexidade não é a resposta certa.

O que fazer?

## Introdução aos Orquestradores
Um "Orquestrador" é o termo geral para um componente de software que ajuda os administradores a gerenciar esses tipos de implantações. Os orquestradores são os componentes que recebem solicitações como "Eu gostaria de ter cinco cópias desse serviço em execução no meu ambiente", as transformam em verdade e (tentam) mantê-las assim. Os orquestradores (não humanos) são os que entram em ação quando um computador falha ou quando uma carga de trabalho é encerrada por algum motivo inesperado. A maioria dos Orquestradores faz mais do que lidar com a falha, como ajudar em novas implantações, tratar atualizações e lidar com o consumo de recursos, mas tudo diz respeito basicamente a manter algum estado de configuração desejado no ambiente. Você quer poder dizer a um Orquestrador o que deseja para que ele faça o trabalho pesado. Chronos sobre Mesosphere, Fleet, Swarm, Kubernetes e Service Fabric são todos Orquestradores ou têm Orquestradores internos. Mais estão sendo criados o tempo todo, já que as complexidades de gerenciar as implantações reais em diferentes tipos de ambientes e de condições crescem e mudam.

## Orquestração como um serviço
O trabalho do Orquestrador em um cluster do Service Fabric é tratado principalmente pelo Gerenciador de Recursos de Cluster. O Gerenciador de Recursos de Cluster do Service Fabric é um dos Serviços do Sistema no Service Fabric e é iniciado automaticamente em cada cluster. Geralmente, o trabalho do Gerenciador de Recursos é dividido em três partes:

1. Imposição de regras
2. Otimização do seu ambiente
3. Auxílio a outros processos

Antes de examinarmos todos os recursos do Gerenciador de Recursos, vamos ver rapidamente como ele funciona. O Gerenciador de Recursos de Cluster do Service Fabric

## Arquitetura e implementação gerais
Antes de entrarmos nos detalhes do Gerenciador de Recursos de Cluster e de todos os seus recursos, primeiro vamos falar um pouco sobre o que ele realmente é e como funciona.

### O que não é
Nos aplicativos Web de N camadas tradicionais, sempre houve uma noção de "Balanceador de Carga", normalmente chamado de NLB (Balanceador de Carga de Rede) ou de ALB (Balanceador de Carga de Aplicativo), dependendo de onde ele estava na pilha de rede. Alguns balanceadores de carga se baseiam em Hardware, como a oferta BigIP da F5, outros em software, como o NLB da Microsoft. Nessas arquiteturas, o trabalho de balanceamento de carga é garantir que todos os computadores de front-end sem estado diferentes ou os computadores diferentes no cluster recebam (aproximadamente) a mesma quantidade de trabalho. As estratégias para isso variam, do envio de cada chamada diferente para um servidor diferente, da fixação/adesão à sessão, à alocação de estimativa e de chamada real com base em seu custo esperado e a carga atual do computador.

Observe que isso era, no máximo, o mecanismo para garantir que a camada da Web permaneceria relativamente balanceada. As estratégias para balancear a camada de dados eram completamente diferentes e dependiam do mecanismo de armazenamento de dados, normalmente centralizando na fragmentação de dados, no cache, nas exibições gerenciadas de banco de dados e nos procedimentos armazenados etc.

Embora algumas dessas estratégias sejam interessantes, o Gerenciador de Recursos de Cluster do Service Fabric não é um balanceador de carga de rede ou um cache. Embora um NLB garanta que o front-ends seja balanceado movendo o tráfego para onde os serviços estão em execução, o Gerenciador de Recursos do Service Fabric usa uma solução completamente diferente: basicamente, o Service Fabric move serviços para onde eles fazem mais sentido. Isso pode ser, por exemplo, nós que estejam frios atualmente porque os serviços que existem no momento não estão realizando muitos trabalhos. Ele também poderia estar longe de um nó prestes a ser atualizado ou que esteja sobrecarregado devido a um aumento no consumo de serviços em execução nele. Já que ele é responsável pela movimentação dos serviços (não fornecendo o tráfego de rede para onde os serviços já estejam), o Gerenciador de Recursos do Service Fabric é mais versátil e também contém recursos adicionais para controlar para onde e como os serviços são movidos.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Próximas etapas
- [Saiba mais sobre a arquitetura do Gerenciador de Recursos de Cluster](service-fabric-cluster-resource-manager-architecture.md)
- [Descrever o cluster](service-fabric-cluster-resource-manager-cluster-description.md)
- [Saiba como configurar os Serviços](service-fabric-cluster-resource-manager-configure-services.md)
- [Saiba mais sobre métricas](service-fabric-cluster-resource-manager-metrics.md)
- [Saiba mais sobre como o Gerenciador de Recursos de Cluster se integra ao restante dos recursos de gerenciamento do Service Fabric](service-fabric-cluster-resource-manager-management-integration.md)

<!---HONumber=AcomDC_0309_2016-->