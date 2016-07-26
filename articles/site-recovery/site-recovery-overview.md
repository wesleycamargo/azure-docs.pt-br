<properties
	pageTitle="O que é o Site Recovery? | Microsoft Azure" 
	description="Fornece uma visão geral do serviço Azure Site Recovery e explica como o serviço pode ser implantado." 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="02/22/2016" 
	ms.author="raynew"/>

#  O que é a Recuperação de Site?

Bem-vindo ao Azure Site Recovery! Comece com este artigo para ter uma visão geral rápida do serviço de Recuperação de Site e como ele pode colaborar com sua estratégia de BCDR (continuidade de negócios e recuperação de desastre).

## Visão geral

As organizações precisam de uma estratégia de BCDR que determine como os aplicativos, as cargas de trabalho e os dados permanecerão em execução e disponíveis durante o tempo de inatividade planejado e não planejado, e como recuperarão as condições normais de trabalho assim que possível. Sua estratégia de BCDR devem manter os dados comerciais seguros e passíveis de recuperação e garantir que as cargas de trabalho permaneçam continuamente disponíveis mediante um desastre.

A Recuperação de Site é um serviço do Azure que colabora com sua estratégia de BCDR por meio da coordenação da replicação de servidores físicos e máquinas virtuais locais na nuvem (Azure) ou em um datacenter secundário. Quando ocorrem paralisações em seu local primário, você realiza o failover em um local secundário a fim de manter os aplicativos e cargas de trabalho disponíveis. Quando o local primário retoma as operações normais, você realiza o failback. Saiba mais em [O que é Recuperação de Site?](site-recovery-overview.md)

## Recuperação de Site no portal do Azure

O Azure tem dois [modelos de implantação](../resource-manager-deployment-model.md) diferentes para criar e trabalhar com recursos: o modelo Azure Resource Manager e o modelo de gerenciamento de serviços clássico. O Azure também tem dois portais – o [portal clássico do Azure](https://manage.windowsazure.com/), que suporta o modelo de implantação clássico, e o [portal do Azure](https://portal.azure.com), com suporte para ambos os modelos de implantação.

A Recuperação de Site está disponível no portal clássico e no portal do Azure. No portal clássico do Azure, você pode dar suporte à Recuperação de Site com o modelo de gerenciamento de serviços clássico. No portal do Azure, você pode suportar o modelo clássico ou as implantações do Gerenciador de Recursos. [Ler mais](site-recovery-overview.md#site-recovery-in-the-azure-portal) sobre a implantação com o portal do Azure.

As informações neste artigo se aplicam às implantações clássicas e do portal do Azure. As diferenças são indicadas onde aplicável.


## Por que usar a Recuperação de Site? 

Veja o que a Recuperação de Site pode fazer pela sua empresa:

- **Simplificar sua estratégia de BCDR**— a Recuperação de Site facilita lidar com a replicação, failover e recuperação de várias cargas de trabalho e aplicativos comerciais a partir de um único local. A Recuperação de Site orquestra a replicação e o failover, mas não intercepta os dados do aplicativo ou tem quaisquer informações sobre ele.
- **Fornecer replicação flexível**— com a Recuperação de Site, você pode replicar as cargas de trabalho em execução nas máquinas virtuais Hyper-V, máquinas virtuais VMware e servidores físicos com Windows/Linux.
- **Facilitar o failover e a recuperação**— a Recuperação de Site fornece failovers de teste para dar suporte à simulações de recuperação de desastre sem afetar os ambientes de produção. Você também pode executar failovers planejados sem perder qualquer dado durante interrupções esperadas, ou failovers não planejados com perda mínima de dados (dependendo da frequência de replicação) para desastres inesperados. Após o failover, você pode realizar o failback para seus sites primários. A Recuperação de Site fornece planos de recuperação que podem incluir scripts e pastas de trabalho de automação do Azure, para que você possa personalizar o failover e a recuperação de aplicativos de várias camadas.
- **Eliminar datacenter secundário**— você pode replicar para um site secundário local ou o Azure. O uso do Azure como destino para a recuperação de desastre elimina o custo e a complexidade de manutenção de um site secundário, e os dados replicados são armazenados no Armazenamento do Azure, com toda a resiliência que ele fornece.
- **Integração com tecnologias de BCDR existentes**— a Recuperação de Site estabelece uma parceria com outros recursos de BCDR do aplicativo. Por exemplo, você pode usar a Recuperação de Site para proteger o back-end do SQL Server de cargas de trabalho corporativas, incluindo suporte nativo para o SQL Server AlwaysOn a fim de gerenciar o failover de grupos de disponibilidade.

## O que posso replicar?

Aqui está um resumo de como você pode replicar usando a Recuperação de Site.

![Local para o próprio local](./media/site-recovery-overview/asr-overview-graphic.png)

**REPLICATE** | **REPLICAR DE (LOCAL)** | **REPLICAR PARA** | **ARTIGO**
---|---|---|---
VMs VMware | Servidor do VMware | As tabelas | [Saiba mais](site-recovery-vmware-to-azure-classic.md)
VMs VMware | Servidor do VMware | Site secundário do VMware | [Saiba mais](site-recovery-vmware-to-vmware.md) 
VMs Hyper-V | Host do Hyper-V na nuvem do VMM | As tabelas | [Saiba mais](site-recovery-vmm-to-azure.md) 
VMs Hyper-V | Host do Hyper-V na nuvem do VMM | Site secundário de VMM | [Saiba mais](site-recovery-vmm-to-vmm.md)
VMs Hyper-V | Host do Hyper-V na nuvem do VMM com o armazenamento SAN| Site secundário de VMM com armazenamento SAN | [Saiba mais](site-recovery-vmm-san.md)
VMs Hyper-V | Host do Hyper-V (sem VMM) | As tabelas | [Saiba mais](site-recovery-hyper-v-site-to-azure.md)
Servidores físicos com Windows/Linux | Servidor físico | As tabelas | [Saiba mais](site-recovery-vmware-to-azure-classic.md)
Cargas de trabalho em execução em servidores físicos com Windows/Linux | Servidor físico | Armazenamento de dados secundário | [Saiba mais](site-recovery-vmware-to-vmware.md) 


## Quais cargas de trabalho posso proteger?

A Recuperação de Site pode ajudar com BCDR com reconhecimento de aplicativos, de modo que as cargas de trabalho e aplicativos continuem executando de forma consistente durante interrupções. A Recuperação de Site fornece:

- **Instantâneos consistentes com o aplicativo**—Replicação usando instantâneos consistentes com o aplicativo para aplicativos simples ou com N camadas.
- **Replicação quase síncrona**—Frequência de replicação de até 30 segundos para o Hyper-V e replicação contínua para o VMware.
- **Integração com o SQL Server AlwaysOn**—Você pode gerenciar o failover dos grupos de disponibilidade nos planos de Recuperação de Site.
- **Planos de recuperação flexíveis**—Você pode criar e personalizar os planos de recuperação com scripts externos, ações manuais e runbooks de Automação do Azure que permitem a recuperação de uma pilha inteira de aplicativos com um único clique.
- **Biblioteca de automação**— Uma biblioteca de Automação do Azure avançada fornece scripts prontos para a produção e específicos do aplicativo que podem ser baixados e integrados na Recuperação de Site.
- **Gerenciamento de rede simples**— O gerenciamento de rede avançado na Recuperação de Site e no Azure simplifica os requisitos de rede do aplicativo, incluindo reservar endereços IP, configurar balanceadores de carga e integrar o Gerenciador de Tráfego do Azure para uma troca de rede eficiente.


## Próximas etapas

- Leia mais em [Quais cargas de trabalho a Recuperação de Site pode proteger?](site-recovery-workload.md)
- Saiba mais sobre a arquitetura da Recuperação de Site em [Como funciona a Recuperação de Site?](site-recovery-components.md)
 

<!---HONumber=AcomDC_0720_2016-->