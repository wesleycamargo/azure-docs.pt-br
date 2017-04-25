---
title: "Perguntas frequentes sobre o cofre dos Serviços de Recuperação | Microsoft Docs"
description: "Esta versão das perguntas frequentes é compatível com a versão de Visualização Pública do serviço Backup do Azure. Respostas para perguntas frequentes sobre agente de backup, backup e retenção, recuperação, segurança e outras perguntas comuns sobre a solução de Backup do Azure."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "solução de backup; serviço de backup"
ms.assetid: 5f55b500-1ee9-4f64-9306-02d6f7a8eded
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/21/2016
ms.author: markgal;trinadhk;
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: e5ef305d926a57e32cdebd44f3dbe2185c735dd4
ms.lasthandoff: 04/15/2017


---
# <a name="recovery-services-vault---faq"></a>Cofre dos Serviços de Recuperação - Perguntas Frequentes
Este artigo fornece informações específicas ao cofre de Serviços de Recuperação e complementa as [Perguntas frequentes sobre Backup do Azure](backup-azure-backup-faq.md). As perguntas frequentes sobre o Backup do Azure oferecem o conjunto completo de perguntas e respostas sobre o serviço Backup do Azure.  

Você pode fazer perguntas sobre o Backup do Azure na seção Disqus deste artigo ou do artigo relacionado. Você também pode postar perguntas sobre o serviço de Backup do Azure no [fórum de discussão](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

## <a name="recovery-services-vaults-are-resource-manager-based-are-backup-vaults-classic-mode-still-supported-br"></a>Os cofres dos Serviços de Recuperação se baseiam no Resource Manager. Os cofres de Backup (modo clássico) ainda têm suporte? <br/>
Sim, os cofres de Backup ainda têm suporte. Crie os cofres de Backup no [portal Clássico](https://manage.windowsazure.com). Crie cofres dos Serviços de Recuperação no [portal do Azure](https://portal.azure.com). No entanto, é altamente recomendável que você crie o cofre de serviços de recuperação, pois todos os aperfeiçoamentos futuros estarão disponíveis somente no cofre de Serviços de Recuperação.

## <a name="can-i-migrate-a-backup-vault-to-a-recovery-services-vault-br"></a>Pode migrar um cofre de Backup para um cofre dos Serviços de Recuperação? <br/>
Infelizmente não, neste momento você não pode migrar o conteúdo de um cofre de Backup para um cofre dos Serviços de Recuperação. Estamos trabalhando na adição dessa funcionalidade, mas ela não está disponível como parte da Visualização Pública.

## <a name="do-recovery-services-vaults-support-classic-vms-or-resource-manager-based-vms-br"></a>Os cofres de Serviços de Recuperação dão suporte a VMs clássicas ou VMs com base no Gerenciador de Recursos? <br/>
Os cofres dos Serviços de Recuperação dão suporte a ambos os modelos.  Você pode fazer backup de uma VM criada no portal Clássico (trata-se de VMs do modo clássico) ou de uma VM criada no portal do Azure (com base no Gerenciador de Recursos) em um cofre dos Serviços de Recuperação.

## <a name="i-have-backed-up-my-classic-vms-in-backup-vault-now-i-want-to-migrate-my-vms-from-classic-mode-to-resource-manager-mode--how-can-i-backup-them-in-recovery-services-vault"></a>Fiz backup de minhas VMs clássicas no cofre de backup. Agora quero migrar minhas VMs do modo clássico para o modo do Gerenciador de Recursos.  Como fazer backup delas no cofre dos serviços de recuperação?
Os backups de VMs clássicas no cofre de backup não migrarão automaticamente para o cofre dos serviços de recuperação quando você migrar as VMs do modo clássico para o modo do Gerenciador de Recursos. Siga estas etapas para a migração de backups de VMs:

1. No cofre de backup, vá para a guia **Itens Protegidos** e selecione a VM. Clique em [Interromper a Proteção](backup-azure-manage-vms-classic.md#stop-protecting-virtual-machines). Deixe a opção *Excluir dados de backup associados***desmarcada**.
2. No [portal do Azure](https://portal.azure.com), acesse o menu **extensões**para a VM e desinstale a extensão **VMSnapshot/VMSnapshotLinux**.
3. Migre a máquina virtual do modo clássico para o modo do Gerenciador de Recursos. Verifique se o armazenamento e a rede correspondentes à máquina virtual também são migrados para o modo do Gerenciador de Recursos.
4. Criar um cofre dos serviços de recuperação e configure o backup na máquina virtual migrada usando a ação **Backup** na parte superior do painel do cofre. Saiba mais sobre como [habilitar o backup no cofre dos serviços de recuperação](backup-azure-vms-first-look-arm.md)

