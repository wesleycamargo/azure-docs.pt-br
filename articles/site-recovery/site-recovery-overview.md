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

O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de Recursos e clássico](../resource-manager-deployment-model.md). Este artigo se aplica aos dois modelos. A Microsoft recomenda que a maioria das implantações novas use o modelo do Gerenciador de Recursos.

## Visão geral

Uma parte importante da estratégia de BCDR de sua organização é descobrir como manter as cargas de trabalho corporativas e aplicativos em execução durante interrupções planejadas e não planejadas.

A Recuperação de Site ajuda com essa tarefa orquestrando a replicação, o failover e a recuperação de aplicativos e cargas de trabalho, para que estejam disponíveis em um local secundário caso ocorra uma falha com o local primário.

## Por que usar a Recuperação de Site? 

Veja o que a Recuperação de Site pode fazer pela sua empresa:

- **Simplificar sua estratégia de BCDR**— a Recuperação de Site facilita o trabalho com a replicação, failover e recuperação de várias cargas de trabalho e aplicativos comerciais de um único local. A Recuperação de Site orquestra a replicação e o failover, mas não intercepta os dados do aplicativo ou tem quaisquer informações sobre ele.
- **Fornecer replicação flexível**— com a Recuperação de Site, você pode replicar cargas de trabalho em execução em máquinas virtuais Hyper-V, máquinas virtuais VMware e servidores físicos com Windows/Linux. 
- **Facilitar o failover e a recuperação**— a Recuperação de Site fornece failovers de teste para dar suporte à simulações de recuperação de desastre sem afetar os ambientes de produção. Você também pode executar failovers planejados sem perder qualquer dado durante interrupções esperadas, ou failovers não planejados com perda mínima de dados (dependendo da frequência de replicação) para desastres inesperados. Após o failover, você pode realizar o failback para seus sites primários. A Recuperação de Site fornece planos de recuperação que podem incluir scripts e pastas de trabalho de automação do Azure, para que você possa personalizar o failover e a recuperação de aplicativos de várias camadas. 
- **Eliminar datacenter secundário**— você pode replicar para um site secundário local ou no Azure. O uso do Azure como destino para a recuperação de desastre elimina o custo e a complexidade de manutenção de um site secundário, e os dados replicados são armazenados no Armazenamento do Azure, com toda a resiliência que ele fornece.
- **Integração com tecnologias de BCDR atuais**— a Recuperação de Site estabelece uma parceria com outros recursos de BCDR do aplicativo. Por exemplo, você pode usar a Recuperação de Site para proteger o back-end do SQL Server de cargas de trabalho corporativas, incluindo suporte nativo para o SQL Server AlwaysOn a fim de gerenciar o failover de grupos de disponibilidade. 

## O que posso replicar?

Veja um resumo do que a Recuperação de Site pode replicar.

![Local para o próprio local](./media/site-recovery-overview/asr-overview-graphic.png)

**REPLICATE** | **REPLICAR DE** | **REPLICAR PARA** | **ARTIGO**
---|---|---|---
Cargas de trabalho em execução em VMs VMware | Servidor VMware local | Armazenamento do Azure | [Implantar](site-recovery-vmware-to-azure-classic.md)
Cargas de trabalho em execução em VMs VMware | Servidor VMware local | Site secundário do VMware | [Implantar](site-recovery-vmware-to-vmware.md) 
Cargas de trabalho em execução em VMs Hyper-V | Servidor de host Hyper-V local em nuvem de VMM | Armazenamento do Azure | [Implantar](site-recovery-vmm-to-azure.md)
Cargas de trabalho em execução em VMs Hyper-V | Servidor de host Hyper-V local em nuvem de VMM | Site secundário de VMM | [Implantar](site-recovery-vmm-to-vmm.md)
Cargas de trabalho em execução em VMs Hyper-V | Servidor de host Hyper-V local em nuvem de VMM com armazenamento SAN| Site secundário de VMM com armazenamento SAN | [Implantar](site-recovery-vmm-san.md)
Cargas de trabalho em execução em VMs Hyper-V | Site local do Hyper-V (sem VMM) | Armazenamento do Azure | [Implantar](site-recovery-hyper-v-site-to-azure.md)
Cargas de trabalho em execução em servidores físicos com Windows/Linux | Servidor físico local | Armazenamento do Azure | [Implantar](site-recovery-vmware-to-azure-classic.md)
Cargas de trabalho em execução em servidores físicos com Windows/Linux | Servidor físico local | Armazenamento de dados secundário | [Implantar](site-recovery-vmware-to-vmware.md) 


## Quais cargas de trabalho posso proteger?

A Recuperação de Site pode ajudar com BCDR com reconhecimento de aplicativos, de modo que as cargas de trabalho e aplicativos continuem executando de forma consistente durante interrupções. A Recuperação de Site fornece:

- **Instantâneos consistentes com o aplicativo**—Replicação usando instantâneos consistentes com o aplicativo, para aplicativos simples ou de N camadas **Replicação quase síncrona**—frequência de replicação de até 30 segundos para o Hyper-,V e replicação contínua para VMware. **Integração com o SQL Server AlwaysOn**—você pode gerenciar o failover de grupos de disponibilidade nos planos de recuperação da Recuperação de Site. 
- **Planos de recuperação flexíveis**—você pode criar e personalizar planos de recuperação com scripts externos, ações manuais e runbooks da Automação do Azure que permitem a recuperação de uma pilha inteira aplicativos com um único clique.
- **Biblioteca de automação**—uma biblioteca rica da Automação do Azure fornece scripts pronto para produção e específicos ao aplicativo que podem ser baixados e integrados à Recuperação de Site. -** Gerenciamento de rede simples **—O gerenciamento de rede avançado na Recuperação de Site e no Azure simplifica os requisitos de rede do aplicativo, incluindo a reserva de endereços IP, a configuração de balanceadores de carga e a integração do Gerenciador de Tráfego do Azure para alternância de rede eficiente.


## Próximas etapas

- Leia mais em [Quais cargas de trabalho a Recuperação de Site pode proteger?](site-recovery-workload.md).
- Saiba mais sobre a arquitetura da Recuperação de Site em [Como funciona a Recuperação de Site?](site-recovery-components.md)
 

<!---HONumber=AcomDC_0316_2016-->