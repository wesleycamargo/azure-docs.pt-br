<properties
   pageTitle="Índice de orientações técnicas de resiliência | Microsoft Azure"
   description="Índice de artigos técnicos sobre compreensão e design de aplicativos resilientes, altamente disponíveis e tolerante a falhas, bem como planejamento de continuidade de negócios e recuperação de desastres"
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="hongfeig"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/13/2016"
   ms.author="patw;jroth;aglick"/>

#Orientações técnicas de resiliência do Azure

##Introdução
Atender aos requisitos de recuperação de desastres e alta disponibilidade requer dois tipos de conhecimento: 1) compreensão técnica detalhada das capacidades de uma plataforma de nuvem e 2) como arquitetar corretamente um serviço distribuído. Esta série de artigos aborda o primeiro - as capacidades e limitações da plataforma do Azure em relação à resiliência (às vezes indicada como continuidade de negócios). Se você estiver interessado no último, consulte a série de artigos com foco em [Recuperação de desastres e alta disponibilidade para aplicativos do Azure](https://aka.ms/drtechguide). Embora esta série de artigos aborde padrões de arquitetura e design, esse não é o foco principal desta série. O leitor deve consultar o material na seção [recursos adicionais](#additional-resources) seção para diretrizes de design.

As informações são organizadas nos artigos a seguir:
##[Recuperação de falhas locais](resiliency-technical-guidance-recovery-local-failures.md)
Hardware físico (por exemplo, unidades, servidores e dispositivos de rede) podem falhar e os recursos podem ser esgotados quando houver picos de carga. Esta seção descreve as capacidades que o Azure fornece para manter a alta disponibilidade nessas condições.

##[Recuperação de uma interrupção de serviço em toda a região do Azure](resiliency-technical-guidance-recovery-loss-azure-region.md)
Falhas generalizadas são raras, mas teoricamente possíveis. Regiões inteiras podem se tornar isoladas devido a falhas de rede, ou serem corrompidas fisicamente devido a desastres naturais. Esta seção explica como usar as capacidades do Azure para criar aplicativos que abrangem várias regiões geograficamente.

##[Recuperação do local para o Azure](resiliency-technical-guidance-recovery-on-premises-azure.md)
A nuvem altera significativamente a economia da recuperação de desastres, tornando possível para as organizações usarem o Azure para estabelecer um segundo local de recuperação. Isso pode ser feito em uma fração do custo de criar e manter de um data center secundário. Esta seção explica as capacidades que Azure fornece para estender um data center local para a nuvem.

##[Recuperação de dados corrompidos ou exclusão acidental](resiliency-technical-guidance-recovery-data-corruption.md)
Os aplicativos podem ter bugs que corrompem os dados e os operadores podem excluir incorretamente dados importantes. Esta seção explica o que o Azure fornece para fazer backup de dados e restaurar para um ponto anterior no tempo.

##Recursos adicionais

###[Orientações técnicas de continuidade de negócios do Azure](resiliency-technical-guidance.md)
Este artigo é uma lista detalhada de detalhes dos conceitos e práticas recomendadas para alcançar continuidade de negócios e resiliência com o Microsoft Azure para seus aplicativos de nuvem locais, híbridos e públicos.

###[Recuperação de desastres e alta disponibilidade para aplicativos baseados no Microsoft Azure](resiliency-disaster-recovery-high-availability-azure-applications.md)
Este artigo é uma visão geral detalhada de disponibilidade e recuperação de desastres. Ele aborda o desafio da replicação manual para dados transacionais e de referência. As seções finais fornecem resumos de tipos diferentes de topologias de recuperação de desastres que abrangem regiões do Azure para o nível mais alto de disponibilidade.

###[Lista de verificação de alta disponibilidade](resiliency-high-availability-checklist.md)
Este artigo é uma lista de recursos, serviços e designs a ser usada para ajudá-lo a aumentar a resiliência e a disponibilidade do aplicativo.

###[Diretrizes de resiliência de serviço do Microsoft Azure](resiliency-service-guidance-index.md)
Este artigo é um índice de serviços do Azure e links para suas diretrizes de recuperação de desastre, bem como diretrizes de design.

###[Visão geral: continuidade de negócios em nuvem e recuperação de desastre do banco de dados com o banco de dados SQL](../sql-database/sql-database-business-continuity.md)
Este artigo concentra-se exclusivamente nas técnicas de banco de dados SQL do Azure para disponibilidade, que se concentra principalmente em estratégias de backup e restauração. Se você usar um banco de dados SQL do Azure em seu serviço de nuvem, examine este documento e seus recursos relacionados.

###[Alta disponibilidade e recuperação de desastre para SQL Server nas Máquinas Virtuais do Azure](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md)
Este artigo discute as opções de disponibilidade abertas quando você usa IaaS (Infraestrutura como Serviço) para hospedar seus serviços de banco de dados. Discute grupos de disponibilidade AlwaysOn, espelhamento de banco de dados, envio de logs e backup/restauração. Observe que também há vários tutoriais na mesma seção que mostram como usar essas técnicas.

###[Práticas recomendadas para a criação de serviços em larga escala nos serviços de nuvem do Azure](https://azure.microsoft.com//blog/best-practices-for-designing-large-scale-services-on-windows-azure/)
Este artigo foca o desenvolvimento de arquiteturas de nuvem altamente escalonáveis. Muitas das técnicas que você emprega para melhorar a escalabilidade também melhoram a disponibilidade. Além disso, se seu aplicativo não puder ser dimensionado sob uma carga maior, a escalabilidade se tornará um problema de disponibilidade.

###[Backup e restauração do SQL Server em máquinas virtuais do Azure](../virtual-machines/virtual-machines-windows-sql-backup-recovery.md)
Este artigo fornece orientações técnicas sobre como fazer backup e restauração do Microsoft SQL Server em execução em máquinas virtuais do Azure.

###[Failsafe: Diretrizes para arquiteturas de nuvem resilientes](https://channel9.msdn.com/Series/FailSafe)
Este artigo fornece diretrizes para criação de arquiteturas de nuvem resilientes, diretrizes para implementar essas arquiteturas em tecnologias da Microsoft e receitas para implementar essas arquiteturas em cenários específicos.

###[Estudo de caso técnico: usando tecnologias de nuvem para melhorar a recuperação de desastre](https://www.microsoft.com/itshowcase/Article/Content/737/Using-cloud-technologies-to-improve-disaster-recovery)
Este artigo é um estudo de caso técnico mostrando como a TI da Microsoft usou o Azure para melhorar a recuperação de desastre.

##Próximas etapas
Este artigo faz parte de uma série que tem como foco [Orientações técnicas de resiliência do Azure](resiliency-technical-guidance.md). Se você estiver interessado em ler outros artigos desta série, inicie o próximo artigo intitulado [Recuperação de falhas locais](resiliency-technical-guidance-recovery-local-failures.md).

<!---HONumber=AcomDC_0608_2016-->