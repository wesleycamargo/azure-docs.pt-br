---
title: O que é a Recuperação de Site? | Microsoft Docs
description: Fornece uma visão geral do serviço Azure Site Recovery e resume os cenários de implantação.
services: site-recovery
documentationcenter: ''
author: rayne-wiselman
manager: cfreeman
editor: ''

ms.service: site-recovery
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/13/2016
ms.author: raynew

---
# <a name="what-is-site-recovery?"></a>O que é a Recuperação de Site?
Bem-vindo ao Azure Site Recovery! Este artigo fornece uma visão geral rápida do serviço Site Recovery e como ele contribui para seu negócio.

Sua organização precisa de uma estratégia BCDR (recuperação de desastres continuidade de negócios) que mantêm os dados, cargas de trabalho e aplicativos seguros e disponíveis durante a inatividade planejada e não planejada, e recupera as condições normais de trabalho assim que possível. O Site Recovery é um serviço do Azure que contribui para essa estratégia.

O Site Recovery coordena a replicação das cargas de trabalho em execução nos servidores físicos locais e nas máquinas virtuais. Você pode replicar os servidores e as VMs a partir de um data center principal para a nuvem (Azure) ou para um data center secundário. Quando ocorrem paralisações em seu site primário, você realiza o failover em um site secundário para manter os aplicativos e as cargas de trabalho acessíveis e disponíveis. Quando o local primário retoma as operações normais, você realiza o failback.

## <a name="site-recovery-in-the-azure-portal"></a>Recuperação de Site no portal do Azure
O Azure tem dois [modelos de implantação](../resource-manager-deployment-model.md) diferentes para criar e trabalhar com recursos. Modelo do Azure Resource Manager e modelo de gerenciamento de serviços clássico. O Azure também tem dois portais – o [portal clássico do Azure](https://manage.windowsazure.com/) que suporta o modelo de implantação clássico e o [portal do Azure](https://portal.azure.com) com suporte para os modelos clássico e do Gerenciador de Recursos.

* A Recuperação de Site está disponível no Portal Clássico e no Portal do Azure.
* No portal clássico do Azure, você pode dar suporte ao Site Recovery com o modelo de gerenciamento de serviços clássico.
* No portal do Azure, você pode dar suporte às implantações do modelo clássico ou do Gerenciador de Recursos. 

As informações neste artigo se aplicam às implantações clássicas e do portal do Azure. As diferenças são indicadas onde aplicável.

## <a name="why-deploy-site-recovery?"></a>Por que implantar o Site Recovery?
Veja o que a Recuperação de Site pode fazer pela sua empresa:

* **Simplificar BCDR**— você pode lidar com a replicação, failover e recuperação de várias cargas de trabalho em um único local no portal do Azure. A Recuperação de Site orquestra a replicação e o failover, mas não intercepta os dados do aplicativo ou tem quaisquer informações sobre ele.
* **Fornecer replicação flexível**— usando o Site Recovery, você pode duplicar as cargas de trabalho em execução nas VMs do Hyper-V com suporte, VMs VMware e servidores físicos Windows/Linux.
* **Executar testes de replicação fácil**— o Site Recovery fornece failovers de teste para dar suporte à recuperação de desastre, sem afetar os ambientes de produção.
* **Failover e recuperação**— você pode executar failovers planejados para interrupções esperadas sem perda de dados ou failovers não planejados com uma perda mínima de dados (dependendo da frequência da replicação) para os desastres inesperados. Após o failover, você pode realizar o failback para seus sites principais. O Site Recovery fornece planos de recuperação que podem incluir scripts e pastas de trabalho de automação do Azure para que você possa personalizar o failover e a recuperação dos aplicativos com várias camadas.
* **Eliminar um data center secundário**— você pode replicar as cargas de trabalho do Azure, em vez de em um site secundário. Isso elimina o custo e a complexidade de manter um data center secundário. Os dados replicados são guardados no Armazenamento do Azure, com toda resiliência que ele oferece. As VMs são criadas com os dados replicados quando ocorre um failover.
* **Integração com as tecnologias BCDR existentes**– O Site Recovery se integra nos outros recursos BCDR. Por exemplo, você pode usar o Site Recovery para proteger o back-end do SQL Server das cargas de trabalho corporativas, incluindo o suporte nativo para o SQL Server AlwaysOn, a fim de gerenciar o failover dos grupos de disponibilidade.

## <a name="what-can-i-replicate?"></a>O que posso replicar?
Aqui está um resumo de como você pode replicar usando a Recuperação de Site.

![Local para o próprio local](./media/site-recovery-overview/asr-overview-graphic.png)

| **REPLICATE** | **REPLICAR PARA** |
| --- | --- |
| Cargas de trabalho em execução nas VMs VMware locais |[Azure](site-recovery-vmware-to-azure-classic.md)<br/><br/> [Site secundário](site-recovery-vmware-to-vmware.md) |
| Cargas de trabalho em execução nas VMs do Hyper-V locais gerenciadas nas nuvens do VMM |[Azure](site-recovery-vmm-to-azure.md)<br/><br/> [Site secundário](site-recovery-vmm-to-vmm.md) |
| Cargas de trabalho em execução nas VMs do Hyper-V locais gerenciadas nas nuvens do VMM, com o armazenamento SAN |[Site secundário](site-recovery-vmm-san.md) |
| Cargas de trabalho em execução nas VMs do Hyper-V locais, sem VMM |[Azure](site-recovery-hyper-v-site-to-azure.md) |
| Cargas de trabalho em execução nos servidores Windows/Linux físicos |[Azure](site-recovery-vmware-to-azure-classic.md)<br/><br/> [Site secundário](site-recovery-vmware-to-vmware.md) |

## <a name="what-workloads-can-i-protect?"></a>Quais cargas de trabalho posso proteger?
O Site Recovery permite o BCDR com reconhecimento do aplicativo para que as cargas de trabalho e os aplicativos continuem sendo executados de forma consistente quando ocorrem paralisações. A Recuperação de Site fornece:

* **Instantâneos consistentes com aplicativos**— As máquinas replicam usando instantâneos consistentes com os aplicativos, para aplicativos com uma ou várias camadas. Além de capturar os dados do disco, os instantâneos consistentes com os aplicativos capturam todos os dados na memória e todas as transações em andamento.
* **Replicação quase síncrona**— O Site Recovery fornece a frequência da replicação, que é tão baixa quanto 30 segundos para o Hyper-V, e a replicação contínua para o VMware.
* **Planos de recuperação flexíveis**— Você pode criar e personalizar os planos de recuperação com scripts externos e ações manuais. A integração com runbooks de Automação do Azure permitem que você recupere uma pilha de aplicativos inteira com um único clique.
* **Integração com o SQL Server AlwaysOn**—Você pode gerenciar o failover dos grupos de disponibilidade nos planos de Recuperação de Site.
* **Biblioteca de automação**— Uma biblioteca de Automação do Azure avançada fornece scripts prontos para a produção e específicos do aplicativo que podem ser baixados e integrados na Recuperação de Site.
* **Gerenciamento de rede simples**— O gerenciamento de rede avançado no Site Recovery e no Azure simplifica os requisitos de rede do aplicativo, incluindo reservar endereços IP, configurar balanceadores de carga e integrar o Gerenciador de Tráfego do Azure para ter uma troca de rede eficiente.

## <a name="next-steps"></a>Próximas etapas
* Leia mais em [Quais cargas de trabalho a Recuperação de Site pode proteger?](site-recovery-workload.md)
* Saiba mais sobre a arquitetura da Recuperação de Site em [Como funciona a Recuperação de Site?](site-recovery-components.md)

<!--HONumber=Oct16_HO2-->


