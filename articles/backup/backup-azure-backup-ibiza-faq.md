<properties
   pageTitle="Perguntas frequentes sobre o cofre dos Serviços de Recuperação | Microsoft Azure"
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
	 ms.date="08/21/2016"
	 ms.author="trinadhk; markgal; jimpark;"/>

# Cofre dos Serviços de Recuperação - Perguntas Frequentes

> [AZURE.SELECTOR]
- [Perguntas frequentes sobre backup para o modo Clássico](backup-azure-backup-faq.md)
- [Perguntas frequentes sobre backup para o modo do Gerenciador de recursos](backup-azure-backup-ibiza-faq.md)

Este artigo fornece informações específicas ao cofre de Serviços de Recuperação e complementa as [Perguntas frequentes sobre Backup do Azure](backup-azure-backup-faq). As perguntas frequentes sobre o Backup do Azure oferecem o conjunto completo de perguntas e respostas sobre o serviço Backup do Azure.

Você pode fazer perguntas sobre o Backup do Azure na seção Disqus deste artigo ou do artigo relacionado. Você também pode postar perguntas sobre o serviço de Backup do Azure no [fórum de discussão](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

## Os cofres dos Serviços de Recuperação se baseiam no Gerenciador de Recursos; os Cofres de backup (modo clássico) ainda têm suporte? <br/>
Sim, os cofres de Backup ainda têm suporte. Crie os cofres de Backup no [portal Clássico](https://manage.windowsazure.com). Crie cofres dos Serviços de Recuperação no [portal do Azure](https://portal.azure.com). No entanto, é altamente recomendável que você crie o cofre de serviços de recuperação, pois todos os aperfeiçoamentos futuros estarão disponíveis somente no cofre de Serviços de Recuperação.

## Pode migrar um cofre de Backup para um cofre dos Serviços de Recuperação? <br/>
Infelizmente não, neste momento você não pode migrar o conteúdo de um cofre de Backup para um cofre dos Serviços de Recuperação. Estamos trabalhando na adição dessa funcionalidade, mas ela não está disponível como parte da Visualização Pública.

## Os cofres de Serviços de Recuperação dão suporte a VMs clássicas ou VMs com base no Gerenciador de Recursos? <br/>
Os cofres dos Serviços de Recuperação dão suporte a ambos os modelos. Você pode fazer backup de uma VM criada no portal Clássico (trata-se de VMs do modo clássico) ou de uma VM criada no portal do Azure (com base no Gerenciador de Recursos) em um cofre dos Serviços de Recuperação.

## Fiz backup de minhas VMs clássicas no cofre de backup. Agora quero migrar minhas VMs do modo clássico para o modo do Gerenciador de Recursos. Como fazer backup delas no cofre dos serviços de recuperação?
Os backups de VMs clássicas no cofre de backup não migrarão automaticamente para o cofre dos serviços de recuperação quando você migrar as VMs do modo clássico para o modo do Gerenciador de Recursos. Siga estas etapas para a migração de backups de VMs:

1. No cofre de backup, vá para a guia **Itens Protegidos** e selecione a VM. Clique em [Interromper a Proteção](backup-azure-manage-vms-classic.md#stop-protecting-virtual-machines). Deixe a opção *Excluir dados de backup associados* **desmarcada**.
2. Migre a máquina virtual do modo clássico para o modo do Gerenciador de Recursos. Verifique se o armazenamento e a rede correspondentes à máquina virtual também são migrados para o modo do Gerenciador de Recursos.
3. Criar um cofre dos serviços de recuperação e configure o backup na máquina virtual migrada usando a ação **Backup** na parte superior do painel do cofre. Saiba mais sobre como [habilitar o backup no cofre dos serviços de recuperação](backup-azure-vms-first-look-arm.md)

<!---HONumber=AcomDC_0824_2016-->