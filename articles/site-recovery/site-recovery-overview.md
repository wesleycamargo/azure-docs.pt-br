---
title: "O que é a Recuperação de Site? | Microsoft Docs"
description: "Fornece uma visão geral do serviço Azure Site Recovery e resume os cenários de implantação."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: cfreeman
editor: 
ms.assetid: e9b97b00-0c92-4970-ae92-5166a4d43b68
ms.service: site-recovery
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/30/2016
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 64d085bff08d9a824204851d32504fac3e79024c


---
# <a name="what-is-site-recovery"></a>O que é a Recuperação de Site?
Bem-vindo ao Azure Site Recovery! Este artigo fornece uma visão geral rápida do Site Recovery.

Sua organização precisa de uma estratégia BCDR (recuperação de desastres continuidade de negócios) que mantêm os dados e aplicativos seguros e disponíveis durante a inatividade planejada e não planejada, e recupera as condições normais de trabalho assim que possível.

O Site Recovery contribui para sua estratégia de BCDR, gerenciando a replicação de máquinas virtuais locais e servidores físicos. Você replica os servidores e as VMs do data center primário local para a nuvem (Azure) ou para um datacenter secundário.

Quando ocorrem paralisações no site primário, você realiza o failover em um site secundário para manter as cargas de trabalho acessíveis e disponíveis. Quando o local primário retoma as operações normais, você realiza o failback.

## <a name="site-recovery-in-the-azure-portal"></a>Recuperação de Site no portal do Azure
O Azure tem dois [modelos de implantação](../resource-manager-deployment-model.md) diferentes para criar e trabalhar com recursos. Modelo do Azure Resource Manager e modelo de gerenciamento de serviços clássico. O Azure também tem dois portais: O [Portal Clássico do Azure](https://manage.windowsazure.com/) e o [Portal do Azure](https://portal.azure.com).

* O Site Recovery pode ser implantado no portal clássico e no portal do Azure.
* No portal clássico do Azure, você pode dar suporte ao Site Recovery com o modelo de gerenciamento de serviços clássico.
* No portal do Azure, você pode suportar o modelo clássico ou as implantações do Gerenciador de Recursos.

As informações neste artigo se aplicam às implantações clássicas e do portal do Azure. As diferenças são indicadas onde aplicável.

## <a name="why-deploy-site-recovery"></a>Por que implantar o Site Recovery?
Veja o que a Recuperação de Site pode fazer pela sua empresa:

* **Simplificar BCDR**— você pode replicar, realizar failover e recuperar várias cargas de trabalho de um único local no portal do Azure. O Site Recovery gerencia a replicação e o failover, sem interceptar dados de aplicativo.
* **Fornecer replicação flexível**— você pode duplicar as cargas de trabalho em execução nas VMs do Hyper-V com suporte, VMs VMware e servidores físicos Windows/Linux.
* **Eliminar um data center secundário**— você pode replicar as cargas de trabalho do Azure, em vez de em um site secundário. Isso elimina o custo e a complexidade de manter um data center secundário. Os dados replicados são armazenados no Armazenamento do Azure, com toda a resiliência que ele oferece. Quando ocorrer um failover, as VMs do Azure serão criadas com os dados replicados.
* **Executar testes de replicação fácil**— você pode facilmente executar failovers de teste para dar suporte à recuperação de desastre, sem afetar os ambientes de produção.
* **Failover e recuperação**— você pode executar failovers planejados para interrupções esperadas com perda de dados zero ou failovers não planejados com perda mínima de dados (dependendo da frequência de replicação), para desastres inesperados. Você pode executar failback para o site primário quando ele estiver disponível novamente.
* **Failover de várias VMs**— você pode configurar planos de recuperação que incluem scripts e runbooks de automação do Azure. Os planos de recuperação permitem que você modele e personalize o failover e a recuperação de aplicativos de várias camadas distribuídos por várias VMs.
* **Integração com as tecnologias BCDR existentes**– o Site Recovery se integra a outras tecnologias BCDR. Por exemplo, você pode usar o Site Recovery para proteger o back-end do SQL Server das cargas de trabalho corporativas, incluindo o suporte nativo para o SQL Server AlwaysOn, a fim de gerenciar o failover dos grupos de disponibilidade.

## <a name="what-can-i-replicate"></a>O que posso replicar?
Aqui está um resumo de como você pode replicar usando a Recuperação de Site.

![Local para o próprio local](./media/site-recovery-overview/asr-overview-graphic.png)

| **REPLICATE** | **REPLICAR PARA** |
| --- | --- |
| VMs VMware locais |[As tabelas](site-recovery-vmware-to-azure-classic.md)<br/><br/> [Site secundário](site-recovery-vmware-to-vmware.md) |
| VMs do Hyper-V locais gerenciadas nas nuvens do VMM |[As tabelas](site-recovery-vmm-to-azure.md)<br/><br/> [Site secundário](site-recovery-vmm-to-vmm.md) |
| VMs do Hyper-V locais gerenciadas nas nuvens do VMM, com o armazenamento SAN |[Site secundário](site-recovery-vmm-san.md) |
| VMs do Hyper-V locais, sem VMM |[As tabelas](site-recovery-hyper-v-site-to-azure.md) |
| Servidores físicos locais com Windows/Linux |[As tabelas](site-recovery-vmware-to-azure-classic.md)<br/><br/> [Site secundário](site-recovery-vmware-to-vmware.md) |

## <a name="how-does-site-recovery-protect-workloads"></a>Como o Site Recovery protege as cargas de trabalho?
O Site Recovery permite a replicação com reconhecimento do aplicativo para que as cargas de trabalho e os aplicativos continuem sendo executados de forma consistente quando ocorrem paralisações.

* **Instantâneos consistentes com aplicativos**— as máquinas replicam usando instantâneos consistentes com os aplicativos, para aplicativos com uma ou várias camadas. Além de capturar os dados do disco, os instantâneos consistentes com os aplicativos capturam todos os dados na memória e todas as transações em andamento.
* **Replicação quase síncrona**— o Site Recovery fornece a frequência da replicação, que é tão baixa quanto 30 segundos para o Hyper-V, e a replicação contínua para o VMware.
* **Planos de recuperação flexíveis**— Você pode criar e personalizar os planos de recuperação com scripts externos e ações manuais. A integração com runbooks de Automação do Azure permitem que você recupere uma pilha de aplicativos inteira com um único clique.
* **Integração com o SQL Server AlwaysOn**— você pode gerenciar o failover dos grupos de disponibilidade nos planos de recuperação.
* **Biblioteca de automação**— Uma biblioteca de Automação do Azure avançada fornece scripts prontos para a produção e específicos do aplicativo que podem ser baixados e integrados na Recuperação de Site.
* **Gerenciamento de rede simples**— O gerenciamento de rede avançado no Site Recovery e no Azure simplifica os requisitos de rede do aplicativo, incluindo reservar endereços IP, configurar balanceadores de carga e integrar o Gerenciador de Tráfego do Azure para ter uma troca de rede eficiente.

## <a name="next-steps"></a>Próximas etapas
* Leia mais em [Quais cargas de trabalho a Recuperação de Site pode proteger?](site-recovery-workload.md)
* Saiba mais sobre a arquitetura da Recuperação de Site em [Como funciona a Recuperação de Site?](site-recovery-components.md)




<!--HONumber=Nov16_HO2-->


