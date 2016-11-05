---
title: Introdução ao Gerenciador de Recursos de Cluster do Service Fabric | Microsoft Docs
description: Uma introdução ao Gerenciador de Recursos de Cluster do Service Fabric.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''

ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/19/2016
ms.author: masnider

---
# Introdução ao Gerenciador de Recursos de Cluster do Service Fabric
Tradicionalmente, o gerenciamento de sistemas de TI ou de um conjunto de serviços significava ter alguns computadores ou máquinas virtuais dedicados a esses serviços ou sistemas específicos. Muitos serviços principais foram divididos em uma camada "Web" e em uma camada de "dados" ou de "armazenamento", talvez com alguns outros componentes especializados, como um cache. Outros tipos de aplicativo tinham uma camada de mensagens, em que as solicitações fluíam para dentro e para fora, conectada a uma camada de trabalho para qualquer análise ou transformação necessária como uma parte da mensagem. Cada tipo de carga de trabalho tinha computadores específicos dedicados a ela: o banco de dados tinha dois computadores dedicados a ele, os servidores Web alguns. Se um tipo específico de carga de trabalho fizesse com que os computadores ficassem sobrecarregados, só seria necessário adicionar mais computadores com o tipo de carga de trabalho configurado para execução neles ou substituir alguns dos computadores por outros maiores. Fácil. Se uma máquina falhava, essa parte do aplicativo geral era executada com menor capacidade até que o computador pudesse ser restaurado. Ainda assim, relativamente fácil (mesmo que não necessariamente divertido).

No entanto, vamos dizer que você sentiu a necessidade de escalar horizontalmente e resolveu se arriscar com os contêineres e/ou o microsserviço. De repente, você tem dezenas, centenas ou até mesmo milhares de computadores, dezenas de diferentes tipos de serviço, talvez centenas de diferentes instâncias desses serviços, cada um com uma ou mais instâncias ou réplicas para HA (Alta Disponibilidade).

Inesperadamente, já não era tão simples gerenciar seu ambiente com poucos computadores dedicados a tipos de carga de trabalho únicos. Os servidores são virtuais e não têm mais nomes (sua mentalidade *passou* a valorizar a [coletividade](http://www.slideshare.net/randybias/architectures-for-open-and-scalable-clouds/20)). A configuração tem menos a ver com os computadores e mais a ver com os próprios serviços. O hardware dedicado é coisa do passado, e os serviços tornaram-se pequenos sistemas distribuídos, abrangendo várias partes menores do hardware de uso geral.

Como consequência dessa divisão do seu antigo aplicativo monolítico em camadas em serviços separados executados no hardware de uso geral, agora você tem muitas combinações com as quais lidar. Quem decide quais tipos de carga de trabalho podem ser executados em qual hardware, ou quantos? Quais cargas de trabalho funcionam bem no mesmo hardware e quais entram em conflito? Quando uma máquina falha... Alguém sabe o que estava em execução lá? Quem é o responsável por garantir que a carga de trabalho comece a execução novamente? Você espera que a máquina (virtual) volte ou suas cargas de trabalho fazem failover automaticamente para outras máquinas e continuam em execução? Há necessidade de intervenção humana? E quanto às atualizações nesse tipo de ambiente?

Como desenvolvedores e operadores vivendo nesse tipo de ambiente, precisaremos de ajuda para gerenciar essa complexidade, e você sabe que fazer muitas contratações e tentar esconder a complexidade com uma folha de papel não é a resposta certa.

O que fazer?

## Introdução aos orquestradores
Um "Orquestrador" é o termo geral para um componente de software que ajuda os administradores a gerenciar esses tipos de ambiente. Os orquestradores são os componentes que recebem solicitações como "Eu gostaria de ter cinco cópias desse serviço em execução no meu ambiente", as transformam em verdade e (tentam) mantê-las assim.

Os orquestradores (não humanos) são os que entram em ação quando um computador falha ou quando uma carga de trabalho é encerrada por algum motivo inesperado. A maioria dos Orquestradores faz mais do que lidar com a falha, como ajudar em novas implantações, tratar atualizações e lidar com o consumo de recursos, mas tudo diz respeito basicamente a manter algum estado de configuração desejado no ambiente. Você quer poder dizer a um Orquestrador o que deseja para que ele faça o trabalho pesado. Chronos ou Marathon sobre Mesos, Fleet, Swarm, Kubernetes e Service Fabric são todos exemplos de Orquestradores (ou os têm internamente). Mais estão sendo criados o tempo todo, já que as complexidades de gerenciar as implantações reais em diferentes tipos de ambientes e de condições crescem e mudam.

## Orquestração como um serviço
O trabalho do Orquestrador em um cluster do Service Fabric é tratado principalmente pelo Gerenciador de Recursos de Cluster. O Gerenciador de Recursos de Cluster do Service Fabric é um dos Serviços do Sistema no Service Fabric e é iniciado automaticamente em cada cluster. Geralmente, o trabalho do Gerenciador de Recursos de Cluster é dividido em três partes:

1. Imposição de regras
2. Otimização do seu ambiente
3. Auxílio a outros processos

### O que não é
Nos aplicativos Web de N camadas tradicionais, sempre houve uma noção de "Balanceador de Carga", normalmente chamado de NLB (Balanceador de Carga de Rede) ou de ALB (Balanceador de Carga de Aplicativo), dependendo de onde ele estava na pilha de rede. Alguns balanceadores de carga se baseiam em Hardware, como a oferta BigIP da F5, outros em software, como o NLB da Microsoft. Em outros ambientes, você poderá ver algo como HAProxy nessa função. Nessas arquiteturas, o trabalho de balanceamento de carga é garantir que todos os computadores de front-end sem estado diferentes ou os computadores diferentes no cluster recebam (aproximadamente) a mesma quantidade de trabalho. As estratégias para isso variam, do envio de cada chamada diferente para um servidor diferente, da fixação/adesão à sessão, à alocação de estimativa e de chamada real com base em seu custo esperado e a carga atual do computador.

Observe que isso era, no máximo, o mecanismo para garantir que a camada da Web permaneceria relativamente balanceada. As estratégias para balancear a camada de dados eram completamente diferentes e dependiam do mecanismo de armazenamento de dados, normalmente centralizando na fragmentação de dados, no cache, nas exibições gerenciadas de banco de dados e nos procedimentos armazenados etc.

Embora algumas dessas estratégias sejam interessantes, o Gerenciador de Recursos de Cluster do Service Fabric não é um balanceador de carga de rede ou um cache. Embora um Balanceador de Carga de Rede garanta que front-ends sejam balanceados movendo o tráfego para onde os serviços estão em execução, o Gerenciador de Recursos de Cluster do Service Fabric usa uma estratégia completamente diferente: basicamente, o Service Fabric move *serviços* para onde eles fazem mais sentido (e espera tráfego ou carga posterior). Um exemplo disso podem ser nós que estejam frios atualmente porque os serviços que existem no momento não estão realizando muitos trabalhos, ou que foram excluídos ou movidos para qualquer outro lugar. Citando outro exemplo, o Gerenciador de Recursos de Cluster também poderia mover um serviço para fora de um computador que esteja prestes a ser atualizado ou que esteja sobrecarregado devido a um pico no consumo pelos serviços que estavam em execução nele. Como o Gerenciador de Recursos de Cluster é responsável por mover os serviços (e não fornecer tráfego de rede para onde os serviços já estão), ele contém um conjunto de recursos consideravelmente diferente comparado ao que você encontraria em um balanceador de carga de rede e, basicamente, utiliza estratégias diferentes a fim de garantir que os recursos de hardware no cluster sejam bem utilizados.

## Próximas etapas
* Para obter informações sobre arquitetura e fluxo de informações no Gerenciador de Recursos de Cluster, confira [este artigo](service-fabric-cluster-resource-manager-architecture.md)
* O Gerenciador de Recursos de Cluster tem muitas opções para descrever o cluster. Para saber mais sobre elas, confira este artigo sobre a [descrição de um cluster do Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)
* Para saber mais sobre outras opções disponíveis para a configuração de serviços, confira o tópico sobre outras configurações disponíveis do Gerenciador de Recursos de Cluster em [Saiba mais sobre a configuração de serviços](service-fabric-cluster-resource-manager-configure-services.md)
* As métricas são como o Gerenciador de Recursos de Cluster do Service Fabric gerencia o consumo e a capacidade no cluster. Para saber mais sobre eles e como configurá-los, confira [este artigo](service-fabric-cluster-resource-manager-metrics.md)
* O Gerenciador de Recursos de Cluster funciona com recursos de gerenciamento do Service Fabric. Para saber mais sobre essa integração, leia [este artigo](service-fabric-cluster-resource-manager-management-integration.md)
* Para descobrir como o Gerenciador de Recursos de Cluster gerencia e balanceia carga no cluster, confira o artigo sobre [como balancear carga](service-fabric-cluster-resource-manager-balancing.md)

<!---HONumber=AcomDC_0824_2016-->