<properties
   pageTitle="Perguntas frequentes sobre a versão de visualização pública do serviço Backup do Azure | Microsoft Azure"
   description="Esta versão das perguntas frequentes é compatível com a versão de Visualização Pública do serviço Backup do Azure. Respostas para perguntas frequentes sobre agente de backup, backup e retenção, recuperação, segurança e outras perguntas comuns sobre a solução de Backup do Azure."
   services="backup"
   documentationCenter=""
   authors="markgalioto"
   manager="jwhit"
   editor=""
   keywords="solução de backup; serviço de backup"/>

<tags
   ms.service="backup"
   ms.workload="storage-backup-recovery"
	 ms.tgt_pltfrm="na"
	 ms.devlang="na"
	 ms.topic="get-started-article"
	 ms.date="03/30/2016"
	 ms.author="trinadhk; markgal; jimpark;"/>

# Versão de Visualização Pública do serviço Backup do Azure - perguntas frequentes

> [AZURE.SELECTOR]
- [Perguntas frequentes sobre backup para o modo Clássico](backup-azure-backup-faq.md)
- [Perguntas frequentes sobre backup para o modo ARM](backup-azure-backup-ibiza-faq.md)

Este artigo fornece informações específicas para a versão de Visualização Pública do serviço Backup do Azure. Este artigo será atualizado quando novas perguntas frequentes chegarem e complementa as [perguntas frequentes sobre o Backup do Azure](backup-azure-backup-faq). As perguntas frequentes sobre o Backup do Azure oferecem o conjunto completo de perguntas e respostas sobre o serviço Backup do Azure.

Você pode fazer perguntas sobre o Backup do Azure na seção Disqus deste artigo ou do artigo relacionado. Você também pode postar perguntas sobre o serviço de Backup do Azure no [fórum de discussão](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

## Novidades na versão de Visualização Pública
A versão de Visualização Pública apresenta o cofre dos Serviços de Recuperação e o suporte a ARM ao proteger as VMs do Azure. O cofre dos Serviços de Recuperação é a próxima geração de cofres. É o cofre usado pelo Backup do Azure e os serviços do Azure Site Recovery (ASR). Pense nele como o cofre v.2.

## Cofres dos Serviços de Recuperação e de Backup

**P1. Os cofres dos Serviços de Recuperação são v.2, os cofres de Backup (v.1) .ainda têm suporte?** <br/> R1. Sim, os cofres de Backup ainda têm suporte. Crie os cofres de Backup no portal Clássico. Crie cofres dos Serviços de Recuperação no portal do Azure.

**P2. Pode migrar um cofre de Backup para um cofre dos Serviços de Recuperação?** <br/> R2. Infelizmente não, neste momento você não pode migrar o conteúdo de um cofre de Backup para um cofre dos Serviços de Recuperação. Estamos trabalhando na adição dessa funcionalidade, mas ela não está disponível como parte da Visualização Pública.

**P3. Os cofres dos Serviços de Recuperação oferecem suporte às VMs v.1 ou v.2?** <br/> R3. Os cofres dos Serviços de Recuperação oferecem suporte às VMs v.1 ou v.2. Você pode fazer backup de uma VM criada no portal Clássico (que é a v.1) ou uma VM criada no portal do Azure (que é a v.2) em um cofre dos Serviços de Recuperação.


## Suporte do ARM para VMs do Azure

**P1. Existem limitações ao suporte do ARM para VMs do Azure?** <br/> R1. Os cmdlets do PowerShell para ARM não estão disponíveis no momento. Você deve usar a interface do usuário do portal do Azure para adicionar recursos a um grupo de recursos.

<!---HONumber=AcomDC_0406_2016-->