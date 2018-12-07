---
title: IBM Db2 pureScale no Azure
description: Neste artigo, mostramos uma arquitetura para executar um ambiente IBM Db2 pureScale no Azure.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/09/2018
ms.author: njray
ms.openlocfilehash: 5a7ae96a3730df2d0dae04dde6d7609ce69cdee5
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/19/2018
ms.locfileid: "51977934"
---
# <a name="ibm-db2-purescale-on-azure"></a>IBM Db2 pureScale no Azure

O ambiente IBM Db2 pureScale fornece uma solução de cluster de banco de dados para o Azure com alta disponibilidade e escalabilidade nos sistemas operacionais Linux. Neste artigo, mostramos uma arquitetura para executar o Db2 pureScale no Azure.

## <a name="overview"></a>Visão geral

As empresas há muito tempo usavam plataformas tradicionais de sistemas de gerenciamento de banco de dados relacional (RDBMS) para atender às necessidades de processamento de transações on-line (OLTP). Atualmente, muitos estão migrando seus ambientes de banco de dados baseados em mainframe para o Azure como uma forma de expandir a capacidade, reduzir custos e manter uma estrutura de custo operacional estável.

A migração é frequentemente o primeiro passo na modernização de uma plataforma legada. Por exemplo, uma empresa re-hospedou recentemente seu ambiente IBM Db2 em execução no z / OS para o IBM Db2 pureScale no Azure. Embora não seja idêntico ao ambiente original, o IBM Db2 pureScale no Linux oferece recursos semelhantes de alta disponibilidade e escalabilidade como o IBM Db2 para z / OS em execução em uma configuração de Parallel Sysplex no mainframe.

Este artigo descreve a arquitetura usada para essa migração do Azure. O Red Hat Linux 7.4 foi usado para testar a configuração. Esta versão está disponível no Azure Marketplace. Antes de escolher uma distribuição do Linux, certifique-se de verificar as versões atualmente suportadas. Para obter detalhes, consulte a documentação de [IBM Db2 pureScale](https://www.ibm.com/support/knowledgecenter/SSEPGG) e [GlusterFS](https://docs.gluster.org/en/latest/).

Este artigo é simplesmente um ponto de partida para o seu plano de implementação do Db2. Seus requisitos de negócios serão diferentes, mas o mesmo padrão básico se aplica. Esse padrão arquitetural também pode ser usado para aplicativos de processamento analítico online (OLAP) no Azure.

Este artigo não cobre diferenças e possíveis tarefas de migração para mover um banco de dados IBM Db2 para z / OS para o IBM Db2 pureScale em execução no Linux. Nem fornece estimativas de dimensionamento e análises de carga de trabalho equivalentes para passar do Db2 z / OS para o Db2 pureScale. Para ajudá-lo a decidir sobre a melhor arquitetura Db2 pureScale para o seu ambiente, é altamente recomendável que você conclua um exercício de estimativa de tamanho completo e estabeleça uma hipótese. Entre outros fatores, no sistema de origem, considere Db2 z / OS Parallel Sysplex com Data Sharing Architecture, configuração do Coupling Facility e estatísticas de uso do DDF.

> [!NOTE]
> Este artigo descreve uma abordagem à migração Db2, mas há outras. Por exemplo, o Db2 pureScale também pode ser executado em ambientes locais virtualizados. A IBM da suporte para o Db2 no Microsoft Hyper-V em várias configurações. Para obter mais informações, consulte [Arquitetura de virtualização Db2 pureScale](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/r0061462.html) no IBM Knowledge Center.

## <a name="architecture"></a>Arquitetura

Para oferecer suporte a alta disponibilidade e escalabilidade no Azure, uma arquitetura de dados compartilhada em escala pode ser usada para o Db2 pureScale. A arquitetura de exemplo a seguir foi usada para a migração de nossos clientes.

![](media/db2-purescale-on-azure/pureScaleArchitecture.png "Db2 pureScale em máquinas virtuais do Azure mostrando armazenamento e rede")


O diagrama arquitetural descreve as camadas lógicas necessárias para um cluster Db2 pureScale. Isso inclui máquinas virtuais para um cliente, para gerenciamento, para armazenamento em cache, para o mecanismo de banco de dados e para armazenamento compartilhado. Além dos nós do mecanismo do banco de dados, o diagrama também inclui dois nós usados para os que são conhecidos como os recursos de armazenamento em cache do cluster (CFs). Um mínimo de dois nós é usado para o próprio mecanismo do banco de dados; um servidor Db2 que pertence a um cluster pureScale é chamado de membro. O cluster é conectado via iSCSI a um cluster de armazenamento compartilhado GlusterFS de três nós para fornecer armazenamento de escalabilidade e alta disponibilidade. O Db2 pureScale é instalado em máquinas virtuais do Azure que executam o Linux.

Essa abordagem é simplesmente um modelo que você pode modificar para se adequar ao tamanho e escala necessários para sua organização e baseia-se no seguinte:

-   Dois ou mais membros do banco de dados são combinados com pelo menos dois nós CF, que gerenciam um buffer pool global (GBP) para serviços de memória compartilhada e gerenciador de bloqueio global (GLM) para controlar o acesso compartilhado e bloquear a contenção de vários membros ativos. Um nó CF atua como primário e o outro como o nó CF secundário de failover. Para evitar a criação de um ambiente que tenha um único ponto de falha, um mínimo de quatro nós é necessário para um cluster Db2 pureScale.

-   Armazenamento compartilhado de alto desempenho (mostrado em tamanho P30 na Figura 1), que é usado por cada um dos nós do Gluster FS.

-   Rede de alto desempenho para os membros de dados e armazenamento compartilhado.

### <a name="compute-considerations"></a>Considerações de computação

Essa arquitetura executa os níveis de aplicativo, armazenamento e dados nas máquinas virtuais do Azure. Os [scripts de configuração de implantação](http://aka.ms/db2onazure) criam o seguinte:

-   Um cluster Db2 pureScale. O tipo de recursos de computação necessários no Azure depende da sua configuração. Em geral, existem duas abordagens que você pode usar:

    -   Use uma rede no estilo de computação de alto desempenho e vários nós (HPC), em que várias instâncias de pequeno a médio porte acessam o armazenamento compartilhado. Para esse tipo HPC da configuração, do Azure com otimização de memória E series ou otimizados para armazenamento série L [máquinas virtuais](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) fornecem a computação energia necessária.

    -   Use menos instâncias grandes de máquinas virtuais para os mecanismos de dados. Para grandes instâncias, as máquinas virtuais [M-series](https://azure.microsoft.com/pricing/details/virtual-machines/series/) otimizadas para maior memória são ideais para cargas de trabalho com memória pesada, mas uma instância dedicada pode ser necessária, dependendo do tamanho da Partição Lógica (LPAR) usada para executar o Db2.

-   O Db2 CF usa máquinas virtuais otimizadas para memória, como E-series ou L-series.

-   O armazenamento GlusterFS usa máquinas virtuais Standard \_DS4\_ v2 executando Linux.

-   Um Jumpbox do GlusterFS é uma máquina virtual Standard \_DS2\_ v2 executando Linux.

-   O cliente é uma máquina virtual padrão \_DS3\_ v2 executando o Windows (usada para teste).

-   Um servidor testemunha é uma máquina virtual Standard \_DS3\_ v2 executando Linux (usada para Db2 pureScale).

> [!NOTE]
> No mínimo, duas instâncias Db2 são necessárias em um cluster Db2 pureScale. Uma instância de Cache e uma instância do Gerenciador de Bloqueio também são necessárias.

### <a name="storage-considerations"></a>Considerações de armazenamento

Como o Oracle RAC, o Db2 pureScale é um banco de dados de scale out de I/O de bloco de alto desempenho. Recomendamos o uso do maior [Armazenamento Premium Azure](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage) disponível que atenda às suas necessidades. Por exemplo, opções de armazenamento menores podem ser adequadas para ambientes de desenvolvimento e teste, enquanto ambientes de produção geralmente exigem maior capacidade de armazenamento. A arquitetura de exemplo usa [P30](https://azure.microsoft.com/pricing/details/managed-disks/) devido à sua proporção de IOPS para tamanho e preço. Independentemente do tamanho, use o Armazenamento Premium para obter o melhor desempenho.

O Db2 pureScale usa uma arquitetura de tudo compartilhado, na qual todos os dados são acessíveis de todos os nós do cluster. O armazenamento Premium deve ser compartilhado entre várias instâncias, seja sob demanda ou em instâncias dedicadas.

Um grande cluster Db2 pureScale pode exigir 200 terabytes (TB) ou mais de armazenamento compartilhado Premium, com IOPS de 100.000. Db2 pureScale suporta uma interface de bloco iSCSI que pode ser usada no Azure. A interface iSCSI requer um cluster de armazenamento compartilhado que pode ser implementado com o GlusterFS, S2D ou outra ferramenta. Esse tipo de solução cria um dispositivo de rede de área de armazenamento virtual (vSAN) no Azure. O Db2 pureScale usa o vSAN para instalar o sistema de arquivos em cluster que é usado para compartilhar dados entre várias máquinas virtuais.

Para essa arquitetura, usamos o sistema de arquivos GlusterFS, um sistema de arquivos distribuído gratuito, escalável e de código aberto, otimizado especificamente para armazenamento em nuvem.

### <a name="networking-considerations"></a>Considerações de rede

A IBM recomenda a rede InfiniBand para todos os membros em um cluster Db2 pureScale; O Db2 pureScale também usa o acesso remoto à memória direta (RDMA), quando disponível, para os FCs usados.

Durante a instalação, um [grupo de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) do Azure é criado para conter todas as máquinas virtuais. Em geral, os recursos são agrupados com base em sua vida útil e quem irá gerenciá-los. As máquinas virtuais nessa arquitetura exigem [rede acelerada](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/), um recurso do Azure que fornece latência de rede consistente e ultrabaixa por meio da virtualização de E / S de raiz única (SR-IOV) para uma máquina virtual.

Cada máquina virtual do Azure é implantada em uma rede virtual segmentada em várias sub-redes: main, front end Gluster FS (gfsfe), back-end Gluster FS (bfsbe), Db2 pureScale (db2be) e front-end db2 purescale (db2fe). O script de instalação também cria as [NICs](https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics) primárias nas máquinas virtuais na sub-rede principal.

[Os grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (NSGs) são usados para restringir o tráfego de rede dentro da rede virtual e para isolar as sub-redes.

No Azure, o Db2 pureScale precisa usar o TCP / IP como a conexão de rede para armazenamento.

## <a name="next-steps"></a>Próximas etapas

-   [Implemente esta arquitetura no Azure](deploy-ibm-db2-purescale-azure.md)
