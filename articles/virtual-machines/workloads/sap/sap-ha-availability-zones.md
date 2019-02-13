---
title: Configurações de carga de trabalho do SAP com Zonas de Disponibilidade do Azure | Microsoft Docs
description: Arquitetura e cenários de alta disponibilidade para SAP NetWeaver usando Zonas de Disponibilidade do Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 887caaec-02ba-4711-bd4d-204a7d16b32b
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/03/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 409a304296d3fdff897a203177e2c150162755c6
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2019
ms.locfileid: "55746210"
---
# <a name="sap-workload-configurations-with-azure-availability-zones"></a>Configurações de carga de trabalho do SAP com Zonas de Disponibilidade do Azure

Uma das funcionalidades de alta disponibilidade que o Azure oferece para melhorar a disponibilidade geral da carga de trabalho do SAP no Azure são as [Zonas de Disponibilidade do Azure](https://docs.microsoft.com/azure/availability-zones/az-overview). As zonas de disponibilidade já estão disponíveis em diferentes [Regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions/). Mais regiões serão adicionadas. 

A arquitetura básica de alta disponibilidade do SAP pode ser descrita como exibido neste gráfico:

![Configuração de HA padrão](./media/sap-ha-availability-zones/standard-ha-config.png)

A camada de aplicativo do SAP é implantada em um [conjunto de disponibilidade](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) do Azure. Para alta disponibilidade do SAP Central Services, implemente duas VMs em um conjunto de disponibilidade separado e use o Windows Failover Cluster Services ou o Pacemaker (Linux) para implantar uma estrutura de alta disponibilidade que permita failover automático no caso de um problema de infraestrutura ou software. Documentação que detalha uma lista de implantações, como:

- [Clusterizar uma instância do SAP ASCS/SCS em um cluster de failover do Windows usando um disco compartilhado de cluster no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [Clusterizar uma instância do SAP ASCS/SCS em um cluster de failover do Windows usando um compartilhamento de arquivo](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)
- [Alta disponibilidade do SAP NetWeaver em VMs do Azure no SUSE Linux Enterprise Server para aplicativos SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)
- [Alta disponibilidade de máquinas virtuais do Azure para SAP NetWeaver no Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)

Uma arquitetura semelhante se aplica à camada do gerenciador de banco de dados (DBMS) dos sistemas SAP NetWeaver, S/4HANA ou Hybris. Implante a camada do DBMS em um modo ativo/passivo com uma solução de cluster de failover, que usa estruturas de failover específicas do DBMS, Windows Failover Cluster Services ou Pacemaker para iniciar atividades de failover em caso de falha de infraestrutura ou software. 

Para implantar a mesma arquitetura usando zonas de disponibilidade do Azure, algumas alterações precisam ser feitas na arquitetura descrita. Essas alterações são descritas em diferentes partes deste documento.

## <a name="considerations-deploying-across-availability-zones"></a>Considerações sobre a implantação em Zonas de Disponibilidade

Ao usar Zonas de Disponibilidade, há alguns aspectos a serem considerados. A lista de considerações é semelhante à seguinte:

- As Zonas de Disponibilidade do Azure não fornecem nenhuma garantia de determinada distância entre diferentes zonas em uma região do Azure
- As Zonas de Disponibilidade do Azure não são uma solução ideal de DR (recuperação de desastre). Em épocas em que grandes desastres naturais estão causando danos generalizados em algumas regiões do mundo, incluindo danos pesados à infraestrutura de energia, a distância entre as diferentes zonas pode não ser grande o suficiente para se qualificar como uma solução adequada de DR
- A latência da rede entre diferentes Zonas de Disponibilidade do Azure nas diferentes regiões do Azure variam conforme a região do Azure. Há casos em que você pode executar a camada de aplicativo SAP implantada em diferentes zonas porque a latência da rede de uma zona para a VM ativa do DBMS ainda é aceitável de um impacto do processo empresarial. Mas há cenários em algumas regiões do Azure em que a latência entre a VM ativa do DBMS em uma zona e uma instância do aplicativo SAP em outra zona poderá ser muito invasiva e não aceitável para os processos empresariais do SAP. Como resultado, as arquiteturas de implantação precisam ser diferentes com uma arquitetura ativa/ativa para o aplicativo ou uma arquitetura ativa/passiva quando a latência entre zonas é muito alta.
- Decida para qual configuração você poderia usar Zonas de Disponibilidade do Azure. Com base na latência de rede que você está medindo entre as diferentes zonas. A latência da rede desempenha um papel importante em duas áreas diferentes:
    - Latência entre as duas instâncias de DBMS que precisam ter uma replicação síncrona estabelecida. Quanto maior a latência de rede, maior será o potencial de escalabilidade do impacto da sua carga de trabalho
    - A diferença na latência de rede entre uma VM que executa uma instância de diálogo do SAP na mesma zona que a instância de DBMS ativa e uma VM semelhante em outra zona. Quanto maior essa diferença, maior o impacto no tempo de execução de processos de negócios e de tarefas em lote, dependendo se eles são executados na mesma zona com o DBMS ou em uma zona diferente


A partir do uso de recursos do Azure, há algumas restrições de funcionalidade que podem ser usadas ao implantar VMs do Azure nas zonas e ao estabelecer soluções de failover em diferentes zonas de disponibilidade na mesma região do Azure. Essas restrições indicam como:

- O uso do [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) é obrigatório para a implantação em Zonas de Disponibilidade do Azure 
- O mapeamento de enumerações de zona para as zonas físicas é fixado por assinatura do Azure. Se você estiver usando assinaturas diferentes para implantar seus sistemas SAP, precisará definir as zonas ideais para cada assinatura separadamente
- Não é possível implantar conjuntos de disponibilidade do Azure em uma Zona de Disponibilidade. Escolha uma Zona de Disponibilidade do Azure ou um conjunto de disponibilidade do Azure como estrutura de implantação para máquinas virtuais.
- Não é possível usar um [Azure Basic Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus) para criar soluções de cluster de failover baseadas em Serviços de Cluster de Failover do Windows ou no Linux Pacemaker. Em vez disso, você precisará usar o [SKU do Azure Standard Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)



## <a name="ideal-zone-combination"></a>Combinação ideal de zonas
Para decidir como usar zonas de disponibilidade, você precisa realizar uma investigação que revele:

- A latência de rede entre as três diferentes zonas de uma região do Azure. Portanto, você pode escolher as zonas que têm a menor latência no tráfego de rede entre zonas
- A diferença entre a latência de VM para VM dentro de uma das zonas escolhidas por você e a latência de rede entre as duas zonas que você escolheu
- Uma instrução indicando se os tipos de VM que você precisa implantar entre as zonas de disponibilidade de escolha estão disponíveis nas duas regiões selecionadas. Especialmente com máquinas virtuais da série M, você vai se deparar com situações em que diferentes SKUs de VM da série M estarão disponíveis apenas em duas ou três zonas

### <a name="network-latency-between-zones-and-within-zone"></a>Latência de rede entre zonas e dentro da zona
Para descobrir qual é a latência entre diferentes zonas, você precisa:

- Implantar a SKU da VM que você deseja usar para sua instância do DBMS nas três zonas. Certifique-se de que a [Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) esteja habilitada ao executar essa medida
- Ao encontrar as duas zonas com menos latência de rede, implante outras três VMs da SKU da VM que você deseja usar como VM da camada de aplicativo nas três zonas de disponibilidade. Meça a latência de rede em relação às duas “VMs do DBMS” em duas zonas diferentes do “DBMS” de sua escolha. 
- Como ferramenta para medir, use **niping**. Uma ferramenta do SAP, que funciona conforme descrito nas notas de suporte do SAP [#500235](https://launchpad.support.sap.com/#/notes/500235) e [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Concentre-se nos comandos do SAP documentados para medições de latência. O uso de **ping** não é uma ferramenta recomendada porque **ping** não funciona nos caminhos de código de rede acelerados do Azure.

Com base nas medidas e na disponibilidade das suas SKUs de VM em diferentes zonas, você precisará tomar as decisões:

- Definir as zonas ideais para a camada do DBMS
- Responda se, com base nas diferenças de latência de rede dentro de uma zona ou entre zonas, você poderia distribuir sua camada de aplicativo SAP ativa em uma, duas ou três zonas diferentes
- Responda se você deseja implantar uma configuração ativa/passiva ou ativa/ativa do ponto de vista do aplicativo (veja mais adiante)

Ao tomar essas decisões, lembre-se também das recomendações de latência de rede do SAP, conforme documentado na nota do SAP [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E).

### <a name="be-aware-of"></a>Lembre-se

> [!IMPORTANT]
> As medições e decisões que você toma são válidas para a assinatura do Azure usada para fazer essas medições. Se você usar outra assinatura do Azure, terá que repetir as medições porque o mapeamento de zonas enumeradas pode ser diferente para outra assinatura do Azure


> [!IMPORTANT]
> Espera-se que as medições executadas acima estejam mostrando resultados diferentes em todas as regiões do Azure que oferecem suporte a [zonas de disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-overview). Mesmo que seus requisitos de latência de rede sejam os mesmos, talvez seja necessário adotar estratégias de implantação diferentes em regiões diferentes do Azure, já que a latência de rede entre as zonas pode ser diferente. Espera-se que, em algumas regiões do Azure, a latência de rede entre as três zonas diferentes possa ser muito diferente. Enquanto, em outras regiões, a latência de rede entre as três zonas diferentes é mais uniforme. A afirmação de que **sempre** existe uma latência de rede entre zonas de 1 milissegundo a 2 milissegundos está **errada**. A latência de rede entre zonas de disponibilidade nas regiões do Azure não pode ser generalizada.


## <a name="activeactive-deployment"></a>Implantação ativa/ativa
Essa arquitetura de implantação é chamada ativa/ativa porque você implanta suas instâncias de diálogo do SAP ativas em duas ou três zonas. O SAP Central Services que usa replicação de enfileiramento será implantado entre duas zonas. O mesmo vale para a camada de DBMS, que será implantada nas mesmas zonas que o SAP Central Service.

Para contemplar essa configuração, você precisa encontrar as duas zonas de disponibilidade em sua região que oferecem uma latência de rede entre zonas aceitável para sua carga de trabalho e para sua replicação síncrona de DBMS. Além disso, convém que o delta entre a latência de rede nas zonas selecionadas e a latência de rede entre zonas não seja muito grande. O motivo disso é que não é ideal ter variações muito grandes nos tempos de execução de seus processos de negócios ou de tarefas em lote, dependendo se uma tarefa é executada na zona com o servidor DBMS ou entre zonas. Algumas variações são aceitáveis, mas não fatores de diferença.

Um esquema simplificado de implantação ativa/ativa em duas zonas poderia ser semelhante a:

![active_active_xone_deployment](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

As seguintes considerações se aplicam a essa configuração:

- Trate as zonas de disponibilidade do Azure como falhas e atualize os domínios de todas as VMs porque os conjuntos de disponibilidade não podem ser implantados nas zonas de disponibilidade do Azure
- Os balanceadores de carga do Azure que você usa para os clusters de failover do SAP Central Services, bem como a camada de DBMS, precisam ser o [balanceador de carga de SKU padrão](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). O balanceador de carga básico não funcionará entre zonas
- A rede virtual do Azure e suas sub-redes implantadas para hospedar o sistema SAP são ampliadas entre zonas. **Não é necessário** ter redes virtuais separadas para cada zona
- Em todas as máquinas virtuais implantadas, use [Azure managed disks](https://azure.microsoft.com/services/managed-disks/). Não há suporte para discos gerenciados em implantações de zona
- O armazenamento Premium do Azure ou o [armazenamento de SSD Ultra](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) não oferecem suporte a nenhum tipo de replicação de armazenamento entre zonas. Como resultado, fica a cargo do aplicativo (DBMS ou SAP Central Services) replicar os dados importantes
- O mesmo ocorre com o diretório sapmnt compartilhado, que é um disco compartilhado (Windows), um compartilhamento de CIFS (Windows) ou um compartilhamento de NFS (Linux). Você precisa usar uma tecnologia que replique um disco compartilhado ou o compartilhamento entre as zonas. No momento, há suporte para as seguintes tecnologias:
    - No Windows, há suporte entre zonas para uma solução de cluster que usa SIOS Datakeeper, como documentado em [Clusterizar uma instância do SAP ASCS/SCS em um cluster de failover do Windows usando um disco compartilhado de cluster no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
    - No SUSE Linux, há suporte para um compartilhamento de NFS, como documentado em [Alta disponibilidade para NFS em VMs do Azure no SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)
    - No momento, **não há suporte nas zonas** para a solução que usa SOFS (Windows scale-out file services) como documentado em [Preparar a infraestrutura do Azure para alta disponibilidade do SAP usando um cluster de failover do Windows e compartilhamento de arquivos para instâncias do SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)
- A terceira zona é usada para hospedar o dispositivo SBD caso você crie um [cluster do SUSE Linux pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) ou instâncias de aplicativo adicionais
- Para obter consistência no tempo de execução de algumas transações comerciais e/ou tarefas críticas. Você pode tentar direcionar determinadas tarefas em lote e usuários para instâncias de aplicativos que estejam na zona com a instância do DBMS ativa usando grupos do SAP Batch Server, grupos de Logon ou grupos de RFC. No entanto, no caso de um failover de zona, você precisa mover manualmente esses grupos para as instâncias de diálogo, que estão na(s) zona(s) restante(s) 
- Decida se você deseja implantar algumas instâncias de diálogos inativos em cada uma das zonas para poder obter imediatamente a antiga capacidade de recursos, caso uma zona em que você implantou parte de suas instâncias do aplicativo esteja fora de serviço


## <a name="activepassive-deployment"></a>Implantação ativa/passiva
Quando você não está encontrando um delta aceitável entre a latência de rede em uma zona e o tráfego de rede de zona cruzada, a arquitetura que você pode implantar tem um caractere ativo/passivo do ponto de vista da camada de aplicativo SAP. Defina uma zona “ativa”, que é a zona em que você implanta a camada de aplicativo completa e onde você tenta executar a instância ativa do DBMS e do SAP Central Services. Com essa configuração, você garante que não haja variações extremas de tempo de execução em transações comerciais e em lote, dependendo se uma tarefa é executada na zona com a instância ativa do DBMS ou não.

O layout básico de tal arquitetura é semelhante a esta:

![active_active_xone_deployment](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

As seguintes considerações se aplicam a essa configuração:

- Não é possível implantar conjuntos de disponibilidade em Zonas de Disponibilidade do Azure. Portanto, nesse caso, você está terminando com um domínio de atualização e falha para sua camada de aplicativo. O motivo é que ele só é implantado em uma zona. Essa configuração é um pequeno retrocesso em comparação com a arquitetura de referência que prevê a implantação da camada do aplicativo em um conjunto de disponibilidade do Azure.
- Ao operar tal arquitetura, você precisa monitorar de perto e tentar manter as instâncias de DBMS e SAP Central Services ativas na mesma zona da sua camada de aplicativo implantada. Caso ocorra um failover do SAP Central Service ou da instância do DBMS, certifique-se de que você possa realizar o failover manual de volta para a zona com a camada de aplicativo SAP implantada o quanto antes
- Os balanceadores de carga do Azure que você usa para os clusters de failover do SAP Central Services, bem como a camada de DBMS, precisam ser o [balanceador de carga de SKU padrão](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). O balanceador de carga básico não funcionará entre zonas
- A rede virtual do Azure e suas sub-redes implantadas para hospedar o sistema SAP são ampliadas entre zonas. **Não é necessário** ter redes virtuais separadas para cada zona
- Em todas as máquinas virtuais implantadas, você precisa usar [Azure managed disks](https://azure.microsoft.com/services/managed-disks/). Não há suporte para discos gerenciados em implantações de zona
- O armazenamento Premium do Azure ou o [armazenamento de SSD Ultra](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) não oferecem suporte a nenhum tipo de replicação de armazenamento entre zonas. Como resultado, fica a cargo do aplicativo (DBMS ou SAP Central Services) replicar os dados importantes
- O mesmo ocorre com o diretório sapmnt compartilhado, que é um disco compartilhado (Windows), um compartilhamento de CIFS (Windows) ou um compartilhamento de NFS (Linux). Você precisa usar uma tecnologia que replique um disco compartilhado ou o compartilhamento entre as zonas. No momento, há suporte para as seguintes tecnologias:
    - No Windows, há suporte entre zonas para uma solução de cluster que usa SIOS Datakeeper, como documentado em [Clusterizar uma instância do SAP ASCS/SCS em um cluster de failover do Windows usando um disco compartilhado de cluster no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
    - No SUSE Linux, há suporte para um compartilhamento de NFS, como documentado em [Alta disponibilidade para NFS em VMs do Azure no SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)
    - No momento, **não há suporte nas zonas** para a solução que usa SOFS (Windows scale-out file services) como documentado em [Preparar a infraestrutura do Azure para alta disponibilidade do SAP usando um cluster de failover do Windows e compartilhamento de arquivos para instâncias do SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)
- A terceira zona é usada para hospedar o dispositivo SBD caso você crie um [cluster do SUSE Linux pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) ou instâncias de aplicativo adicionais
- Implante VMs inativas na zona passiva (de um ponto de vista do DBMS) para poder iniciar os recursos do aplicativo em caso de falha de zona
    - Não é possível usar o [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) para replicar VMs ativas para VMs inativas entre as zonas. No momento, o Azure Site Recovery não pode cumprir essa função
- Invista na automação que permite, em caso de falha de zona, iniciar automaticamente a camada de aplicativo SAP na segunda zona

## <a name="combined-high-availability-and-disaster-recovery-configuration"></a>Configuração combinada de alta disponibilidade e recuperação de desastre
Embora a Microsoft não forneça informações sobre a distância geográfica entre as instalações que hospedam diferentes zonas de disponibilidade do Azure em determinada região do Azure, alguns clientes estão usando zonas para uma configuração combinada de HA e DR que promete **R**ecovery **P**oint **O**bjective (RPO) de zero. O que significa que você não deve perder nenhuma transação de banco de dados confirmada, mesmo no caso de recuperação de desastre. 

> [!NOTE]
> Uma configuração como essa é recomendada apenas em circunstâncias específicas. Por exemplo, casos em que os dados não podem sair da região do Azure por motivos de segurança e conformidade. 

Um exemplo de como essa configuração pode ser exibida é demonstrado neste gráfico:

![combined_ha_dr_in_zones](./media/sap-ha-availability-zones/combined_ha_dr_in_zones.png)

As seguintes considerações se aplicam a essa configuração:

- Você está assumindo que há uma distância significativa entre as instalações que hospedam uma zona de disponibilidade. Ou você é forçado a ficar com determinada região do Azure. Não é possível implantar conjuntos de disponibilidade em Zonas de Disponibilidade do Azure. Portanto, nesse caso, você está terminando com um domínio de atualização e falha para sua camada de aplicativo. O motivo é que ele só é implantado em uma zona. Esse é um retrocesso em comparação com a arquitetura de referência que prevê a implantação da camada do aplicativo em um conjunto de disponibilidade do Azure.
- Ao operar tal arquitetura, você precisa monitorar de perto e tentar manter as instâncias de DBMS e SAP Central Services ativas na mesma zona da sua camada de aplicativo implantada. Caso ocorra um failover do SAP Central Service ou da instância do DBMS, certifique-se de que você possa realizar o failover manual de volta para a zona com a camada de aplicativo SAP implantada o quanto antes
- Você tem instâncias do aplicativo de produção pré-instaladas nas VMs que executam as instâncias do aplicativo QA ativas.
- No caso de uma falha de zona, encerre as instâncias do aplicativo QA e inicie as instâncias de produção. Lembre-se de que você precisa trabalhar com nomes virtuais para as instâncias do aplicativo para fazer isso funcionar
- Os balanceadores de carga do Azure que você usa para os clusters de failover do SAP Central Services, bem como a camada de DBMS, precisam ser o [balanceador de carga de SKU padrão](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). O balanceador de carga básico não funcionará entre zonas
- A rede virtual do Azure e suas sub-redes implantadas para hospedar o sistema SAP são ampliadas entre zonas. **Não é necessário** ter redes virtuais separadas para cada zona
- Em todas as máquinas virtuais implantadas, você precisa usar [Azure managed disks](https://azure.microsoft.com/services/managed-disks/). Não há suporte para discos gerenciados em implantações de zona
- O armazenamento Premium do Azure ou o [armazenamento de SSD Ultra](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) não oferecem suporte a nenhum tipo de replicação de armazenamento entre zonas. Como resultado, fica a cargo do aplicativo (DBMS ou SAP Central Services) replicar os dados importantes
- O mesmo ocorre com o diretório sapmnt compartilhado, que é um disco compartilhado (Windows), um compartilhamento de CIFS (Windows) ou um compartilhamento de NFS (Linux). Você precisa usar uma tecnologia que replique um disco compartilhado ou o compartilhamento entre as zonas. No momento, há suporte para as seguintes tecnologias:
    - No Windows, há suporte entre zonas para uma solução de cluster que usa SIOS Datakeeper, como documentado em [Clusterizar uma instância do SAP ASCS/SCS em um cluster de failover do Windows usando um disco compartilhado de cluster no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
    - No SUSE Linux, há suporte para um compartilhamento de NFS, como documentado em [Alta disponibilidade para NFS em VMs do Azure no SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)
    - No momento, **não há suporte nas zonas** para a solução que usa SOFS (Windows scale-out file services) como documentado em [Preparar a infraestrutura do Azure para alta disponibilidade do SAP usando um cluster de failover do Windows e compartilhamento de arquivos para instâncias do SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)
- A terceira zona é usada para hospedar o dispositivo SBD caso você crie um [cluster do SUSE Linux pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) ou instâncias de aplicativo adicionais





## <a name="next-steps"></a>Próximas etapas
Verifique as próximas etapas para implantar em zonas de disponibilidade do Azure:

- [Clusterizar uma instância do SAP ASCS/SCS em um cluster de failover do Windows usando um disco compartilhado de cluster no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [Preparar a infraestrutura do Azure para alta disponibilidade do SAP usando um cluster de failover do Windows e compartilhamento de arquivos para instâncias ASCS/SCS do SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)






