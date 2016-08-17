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
   ms.date="08/01/2016"
   ms.author="aglick"/>

#Orientações técnicas de resiliência do Azure

##Introdução

Atender aos requisitos de recuperação de desastres e de alta disponibilidade requer dois tipos de conhecimentos:

- Compreensão técnica detalhada dos recursos de uma plataforma de nuvem
- Conhecimento de como arquitetar corretamente um serviço distribuído

Esta série de artigos aborda o primeiro: os recursos e as limitações da plataforma Azure em relação à resiliência (às vezes chamada de continuidade de negócios). Se você estiver interessado no último, confira a série de artigos com foco em [Recuperação de desastres e alta disponibilidade para aplicativos do Azure](https://aka.ms/drtechguide). Embora esta série de artigos aborde padrões de arquitetura e design, esse não é o foco da série. Para obter diretrizes de design, você pode conferir o material na seção [Recursos adicionais](#additional-resources).

As informações são organizadas nos artigos a seguir:

- [Recuperação de falhas locais](resiliency-technical-guidance-recovery-local-failures.md). O hardware físico (por exemplo, unidades, servidores e dispositivos de rede) pode falhar. Os recursos podem ser esgotados quando há picos de carga. Este artigo descreve os recursos que o Azure fornece para manter a alta disponibilidade nessas condições.

- [Recuperação de uma interrupção de serviço em toda a região do Azure](resiliency-technical-guidance-recovery-loss-azure-region.md). Falhas generalizadas são raras, mas são teoricamente possíveis. Regiões inteiras podem se tornar isoladas devido a falhas de rede ou podem ser corrompidas fisicamente devido a desastres naturais. Este artigo explica como usar o Azure para criar aplicativos que abrangem várias regiões geograficamente.

- [Recuperação do local para o Azure](resiliency-technical-guidance-recovery-on-premises-azure.md). A nuvem altera significativamente a economia da recuperação de desastre, habilitando as organizações a usar o Azure para estabelecer um segundo local para recuperação. Você pode fazer isso a uma fração do custo de criação e manutenção de um datacenter secundário. Este artigo explica os recursos que o Azure fornece para estender um datacenter local para a nuvem.

- [Recuperação de dados corrompidos ou exclusão acidental](resiliency-technical-guidance-recovery-data-corruption.md). Os aplicativos podem ter bugs que corrompem dados. Os operadores podem excluir incorretamente dados importantes. Este artigo explica o que o Azure fornece para fazer backup de dados e restaurar para um ponto anterior no tempo.

##Recursos adicionais

- [Recuperação de desastres e alta disponibilidade para aplicativos baseados no Microsoft Azure](resiliency-disaster-recovery-high-availability-azure-applications.md). Este artigo é uma visão geral detalhada de disponibilidade e recuperação de desastres. Ele aborda o desafio da replicação manual para dados transacionais e de referência. As seções finais fornecem resumos de tipos diferentes de topologias de recuperação de desastres que abrangem regiões do Azure para o nível mais alto de disponibilidade.

- [Lista de verificação de alta disponibilidade](resiliency-high-availability-checklist.md). Este artigo é uma lista de recursos, serviços e designs que podem ajudá-lo a aumentar a resiliência e a disponibilidade do aplicativo.

- [Diretrizes de resiliência de serviço do Microsoft Azure](resiliency-service-guidance-index.md). Este artigo é um índice de serviços do Azure e fornece links para orientações de recuperação de desastre e diretrizes de design.

- [Visão geral: continuidade de negócios em nuvem e recuperação de desastre do banco de dados com o banco de dados SQL](../sql-database/sql-database-business-continuity.md). Este artigo fornece técnicas de Banco de Dados SQL para disponibilidade. Ele se concentra principalmente em estratégias de backup e restauração. Se você usar um banco de dados SQL do Azure em seu serviço de nuvem, examine este documento e seus recursos relacionados.

- [Alta disponibilidade e recuperação de desastres para SQL Server em Máquinas Virtuais do Azure](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md). Este artigo discute as opções de disponibilidade que você pode explorar ao usar IaaS (infraestrutura como serviço) para hospedar serviços de banco de dados. Discute Grupos de Disponibilidade AlwaysOn, espelhamento de banco de dados, envio de logs e backup/restauração. Vários tutoriais mostram como usar essas técnicas.

- [Práticas recomendadas para a criação de serviços em larga escala nos Serviços de Nuvem do Azure](https://azure.microsoft.com//blog/best-practices-for-designing-large-scale-services-on-windows-azure/). Este artigo foca o desenvolvimento de arquiteturas de nuvem altamente escalonáveis. Muitas das técnicas que você emprega para melhorar a escalabilidade também melhoram a disponibilidade. Além disso, se o aplicativo não puder ser dimensionado sob uma carga maior, a escalabilidade se tornará um problema de disponibilidade.

- [Backup e restauração do SQL Server em máquinas virtuais do Azure](../virtual-machines/virtual-machines-windows-sql-backup-recovery.md). Este artigo fornece orientações técnicas sobre como fazer backup e restauração do Microsoft SQL Server em execução em máquinas virtuais do Azure.

- [Failsafe: diretrizes para arquiteturas de nuvem resilientes](https://channel9.msdn.com/Series/FailSafe). Este artigo fornece diretrizes para criação de arquiteturas de nuvem resilientes, diretrizes para implementar essas arquiteturas em tecnologias da Microsoft e receitas para implementar essas arquiteturas em cenários específicos.

- [Estudo de caso técnico: uso de tecnologias de nuvem para melhorar a recuperação de desastre](https://www.microsoft.com/itshowcase/Article/Content/737/Using-cloud-technologies-to-improve-disaster-recovery). Este estudo de caso mostra como a TI da Microsoft usou o Azure para melhorar a recuperação de desastre.

##Próximas etapas

Este artigo faz parte de uma série que enfoca a orientação técnica para a resiliência do Azure. Se estiver interessado em ler outros artigos desta série, você poderá começar com [Recuperação de falhas locais](resiliency-technical-guidance-recovery-local-failures.md).

<!---HONumber=AcomDC_0803_2016-->