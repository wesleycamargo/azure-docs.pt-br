---
title: Práticas recomendadas para o operador – Alta disponibilidade e recuperação de desastre no Serviço de Kubernetes do Azure (AKS)
description: Conheça as práticas recomendadas para o operador do cluster para maximizar o tempo de atividade de seus aplicativos e, dessa forma, fornecer alta disponibilidade e preparar-se para situações de recuperação de desastre no Serviço de Kubernetes do Azure (AKS)
services: container-service
author: lastcoolnameleft
ms.service: container-service
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: lastcoolnameleft
ms.openlocfilehash: 926f470b8a4dbdb6d6cbfe09ee61349a819600e7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58098620"
---
# <a name="best-practices-for-business-continuity-and-disaster-recovery-in-azure-kubernetes-service-aks"></a>Práticas recomendadas para continuidade dos negócios e recuperação de desastres no Serviço de Kubernetes do Azure (AKS)

À medida que você vai gerenciando clusters no Serviço de Kubernetes do Azure (AKS), o tempo de atividade do aplicativo vai ficando mais importante. O AKS fornece alta disponibilidade usando vários nós em um conjunto de disponibilidade. Esses vários nós não o protegem contra uma falha de região. Para maximizar o tempo de atividade, implemente alguns recursos de continuidade dos negócios e recuperação de desastres.

Este artigo sobre práticas recomendadas aborda considerações que o ajudarão a planejar-se para situações de continuidade dos negócios e recuperação de desastres no AKS. Você aprenderá como:

> [!div class="checklist"]
> * Planejar clusters do AKS em várias regiões
> * Rotear o tráfego entre vários clusters com o Gerenciador de Tráfego do Azure
> * Usar a replicação geográfica em seus registros de imagem de contêiner
> * Planejar o estado do aplicativo entre vários clusters
> * Replicar o armazenamento em várias regiões

## <a name="plan-for-multi-region-deployment"></a>Planejar para implantações em várias regiões

**Diretrizes de práticas recomendadas** - Quando você implantar vários clusters do AKS, escolha regiões onde o AKS está disponível e use regiões combinadas.

Um cluster do AKS é implantado em uma única região. Para se proteger contra falhas de região, implante seu aplicativo em vários clusters do AKS em regiões diferentes. Quando você planejar em quais regiões implantar o cluster do AKS, leve em conta o seguinte:

* [Disponibilidade da região para o AKS](https://docs.microsoft.com/azure/aks/container-service-quotas#region-availability)
  * Escolha regiões perto de seus usuários. O AKS está sempre expandindo para novas regiões.
* [Regiões combinadas do Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)
  * Para sua área geográfica, escolha duas regiões emparelhadas umas com as outras. Essas regiões coordenam as atualizações de plataforma e priorizam os esforços de recuperação conforme necessário.
* Nível de disponibilidade do serviço (Hot/Hot, Hot/Warm, Hot/Cold)
  * Você deseja executar ambas as regiões ao mesmo tempo, com uma região *pronta* para começar a receber o tráfego ou uma região que precisa de tempo de preparo para receber o tráfego.

A disponibilidade de região do AKS e as regiões emparelhadas são consideradas em conjunto. Implante seus clusters do AKS em regiões emparelhadas que são projetadas para gerenciar a recuperação de desastre de região juntas. Por exemplo, o AKS está disponível no *Leste dos EUA* e no *Oeste dos EUA*. Essas regiões também estão emparelhadas. Essas duas regiões seriam recomendadas na criação de uma estratégia de BC/DR do AKS.

Quando você implanta seu aplicativo, também deve adicionar outra etapa ao pipeline de CI/CD para implantar esses vários clusters do AKS. Se você não atualizar os pipelines de implantação, os aplicativos só poderão ser implantadas em uma de suas regiões e clusters do AKS. O tráfego de cliente que é direcionado para uma região secundária não receberá as atualizações mais recentes do código.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Usar o Gerenciador de Tráfego do Azure para rotear o tráfego

**Diretrizes de práticas recomendadas** - o Gerenciador de Tráfego do Azure pode direcionar clientes para sua instância de aplicativo e cluster do AKS mais próximo. Para obter melhor desempenho e redundância, direcione todo o tráfego do aplicativo por meio do Gerenciador de Tráfego antes de ir para o cluster do AKS.

Com vários clusters do AKS em regiões diferentes, você precisa controlar como o tráfego é direcionado para os aplicativos executados em cada cluster. O [Gerenciador de Tráfego do Azure](https://docs.microsoft.com/azure/traffic-manager/) é um balanceador de carga de tráfego com base em DNS que pode distribuir o tráfego de rede entre regiões. Você pode rotear os usuários com base no tempo de resposta do cluster ou com base na área geográfica.

![AKS com o Gerenciador de Tráfego do Azure](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

Com um único cluster do AKS, os clientes normalmente se conectam ao *IP do serviço* ou ao nome DNS de determinado aplicativo. Em uma implantação de vários clusters, os clientes devem se conectar a um nome DNS do Gerenciador de Tráfego que aponta para os serviços em cada cluster do AKS. Esses serviços são definidos usando pontos de extremidade do Gerenciador de Tráfego. Cada ponto de extremidade é o *IP do Balanceador de Carga do Serviço*. Essa configuração permite direcionar o tráfego de rede do ponto de extremidade do Gerenciador de Tráfego em uma região para o ponto de extremidade em uma região diferente.

![Roteamento geográfico com o Gerenciador de Tráfego do Azure](media/operator-best-practices-bc-dr/traffic-manager-geographic-routing.png)

O Gerenciador de Tráfego é usado para executar pesquisas de DNS e retornar o ponto de extremidade mais apropriado para cada usuário. Perfis aninhados podem ser usados, com a prioridade dada para uma localização primária. Por exemplo, um usuário deve se conectar principalmente à região geográfica mais próxima. Se essa região tem um problema, o Gerenciador de Tráfego o direcionará para uma região secundária. Essa abordagem faz com que os clientes possam sempre se conectar a uma instância de aplicativo, mesmo se sua região geográfica mais próxima não estiver disponível.

Para saber as etapas sobre como configurar esses pontos de extremidade e roteamento, confira [Configurar o método geográfico de roteamento de tráfego usando o Gerenciador de Tráfego](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-geographic-routing-method).

### <a name="layer-7-application-routing-with-azure-front-door"></a>Roteamento de aplicativo de camada 7 com o Azure Front Door

O Gerenciador de Tráfego do Azure usa o DNS (camada 3) para dar forma ao tráfego. [Frente do Azure (atualmente em versão prévia)](https://docs.microsoft.com/azure/frontdoor/front-door-overview) fornece uma opção de roteamento de HTTP/HTTPS (camada 7). Outros recursos do Front Door incluem encerramento de SSL, domínio personalizado, Firewall do Aplicativo Web, Reescrita de URL e Afinidade de Sessão.

Examine as necessidades de seu tráfego de aplicativo para entender qual é a solução mais adequada.

## <a name="enable-geo-replication-for-container-images"></a>Habilitar a replicação geográfica para imagens de contêiner

**Diretrizes de práticas recomendadas** - armazene suas imagens de contêiner no Registro de Contêiner do Azure e faça a replicação geográfica do registro para cada região do AKS.

Para implantar e executar seus aplicativos no AKS, você precisará ter uma maneira de armazenar e efetuar pull das imagens de contêiner. O Registro de Contêiner do Azure (ACR) pode se integrar ao AKS para armazenar suas imagens de contêiner ou gráficos do Helm com segurança. O ACR dá suporte à replicação geográfica com vários mestres para replicar automaticamente suas imagens para regiões do Azure em todo o mundo. Para melhorar o desempenho e a disponibilidade, use a replicação geográfica do ACR para criar um registro em cada região onde há um cluster do AKS. Cada cluster do AKS efetua pull de imagens de contêiner do registro ACR local na mesma região:

![Replicação geográfica do Registro de Contêiner do Azure para imagens de contêiner](media/operator-best-practices-bc-dr/acr-geo-replication.png)

Os benefícios de usar a replicação geográfica do ACR incluem o seguinte:

* **A extração de imagens da mesma região é mais rápida.** É possível efetuar pull de imagens de conexões de rede com alta velocidade e baixa latência na mesma região do Azure.
* **A extração de imagens da mesma região é mais confiável.** Se uma região não estiver disponível, seu cluster do AKS puxará a imagem de um registro do ACR diferente que permanece disponível.
* **A extração de imagens da mesma região é mais barata.** Não há nenhum encargo de saída de rede entre datacenters.

A replicação geográfica é um recurso de registros de ACR do SKU *Premium*. Para obter etapas sobre como configurar a replicação, consulte [Replicação geográfica do Registro de Contêiner do Azure](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication)

## <a name="remove-service-state-from-inside-containers"></a>Remover o estado do serviço de dentro dos contêineres

**Diretrizes de práticas recomendadas** - Sempre que possível, não armazene o estado do serviço dentro do contêiner. Em vez disso, use os serviços de PaaS do Azure que dão suporte à replicação de várias regiões.

Estado do serviço refere-se aos dados em disco ou na memória que um serviço requer para funcionar. O estado inclui as estruturas de dados e variáveis de membro que o serviço lê e grava. Dependendo de como o serviço é estruturado, o estado também pode incluir arquivos ou outros recursos armazenados em disco. Por exemplo, os arquivos que um banco de dados usaria para armazenar dados e logs de transação.

O estado pode ser externalizado ou localizado em conjunto com o código que está manipulando o estado. Normalmente, a externalização do estado é feita usando um banco de dados ou outro armazenamento de dados executado em máquinas diferentes na rede ou fora do processo no mesmo computador.

Os contêineres e microsserviços são mais resilientes quando os processos que são executados dentro deles não retêm o estado. Como os aplicativos quase sempre contêm algum estado, use uma solução Plataforma como um Serviço, como o Banco de Dados do Azure para MySQL/Postgres ou o SQL do Azure.

Para obter detalhes sobre como criar aplicativos mais portáteis, consulte as diretrizes abaixo:

* [A metodologia de aplicativo de doze fatores](https://12factor.net/).
* [Executar um aplicativo Web em várias regiões do Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region)

## <a name="create-a-storage-migration-plan"></a>Criar um plano de migração de armazenamento

**Diretrizes de práticas recomendadas** - se você usa o Armazenamento do Azure, prepare e teste como migrar o armazenamento da região primária para a região de backup.

Os aplicativos podem usar o Armazenamento do Azure para seus dados. Como os aplicativos estão distribuídos entre vários clusters do AKS em regiões diferentes, você precisa manter o armazenamento sincronizado. Duas maneiras comuns de replicar o armazenamento incluem o seguinte:

* Replicação assíncrona baseada em aplicativo
* Replicação assíncrona baseada em infraestrutura

### <a name="infrastructure-based-asynchronous-replication"></a>Replicação assíncrona baseada em infraestrutura

Seus aplicativos poderão exigir armazenamento persistente, mesmo depois de um pod ser excluído. No Kubernetes, você pode usar Volumes Persistentes para persistir dados de armazenamento. Esses volumes persistentes são montados na VM do nó e, em seguida, expostos aos pods. Volumes persistentes seguem os pods, mesmo quando o pod é movido para um nó diferente dentro do mesmo cluster.

Dependendo do uso da solução de armazenamento, as estratégias de replicação podem ser diferentes. Soluções de armazenamento comuns, como [Gluster](https://docs.gluster.org/en/latest/Administrator%20Guide/Geo%20Replication/), [CEPH](http://docs.ceph.com/docs/master/cephfs/disaster-recovery/), [Rook](https://rook.io/docs/rook/master/disaster-recovery.html) e [Portworx](https://docs.portworx.com/scheduler/kubernetes/going-production-with-k8s.html#disaster-recovery-with-cloudsnaps), têm suas próprias diretrizes.

A abordagem central é um ponto de armazenamento comum onde os aplicativos gravam seus dados. Esses dados são replicados entre regiões e acessados localmente.

![Replicação assíncrona baseada em infraestrutura](media/operator-best-practices-bc-dr/aks-infra-based-async-repl.png)

Se você usa o Azure Managed Disks, as soluções de recuperação de desastre e replicação disponíveis incluem o uso de uma das seguintes abordagens:

* [Ark no Azure](https://github.com/heptio/ark/blob/master/docs/azure-config.md)
* [Azure Site Recovery](https://azure.microsoft.com/blog/asr-managed-disks-between-azure-regions/)

### <a name="application-based-asynchronous-replication"></a>Replicação assíncrona baseada em aplicativo

Atualmente, não há nenhuma implementação nativa do Kubernetes para a replicação assíncrona baseada em aplicativo. Como os contêineres e o Kubernetes são menos rígidos, qualquer abordagem tradicional de aplicativo ou linguagem deve funcionar. A abordagem central é para os próprios aplicativos replicarem as solicitações de armazenamento que são gravadas no armazenamento de dados subjacente de cada cluster.

![Replicação assíncrona baseada em aplicativo](media/operator-best-practices-bc-dr/aks-app-based-async-repl.png)

## <a name="next-steps"></a>Próximas etapas

Este artigo aborda considerações de continuidade dos negócios e recuperação de desastres em clusters do AKS. Para obter mais informações sobre operações de cluster no AKS, consulte as seguintes práticas recomendadas:

* [Isolamento de multi locação e cluster][aks-best-practices-cluster-isolation]
* [Recursos básicos de Agendador Kubernetes][aks-best-practices-scheduler]

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
