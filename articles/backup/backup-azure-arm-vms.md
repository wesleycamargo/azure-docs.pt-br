---
title: "Fazer backup de VMs do Azure em um cofre dos Serviços de Recuperação | Microsoft Docs"
description: "Descubra, registre e faça backup de máquinas virtuais do Azure em um cofre dos Serviços de Recuperação."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: "backup de máquinas virtuais; fazer backup de máquina virtual, backup e recuperação de desastres; backup de vm arm"
ms.assetid: 5c68481d-7be3-4e68-b87c-0961c267053e
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/30/2017
ms.author: trinadhk;jimpark;markgal;
translationtype: Human Translation
ms.sourcegitcommit: 39147f2db1e660a21d6ed622206787ea0c569056
ms.openlocfilehash: 28a5014f7ee73b30f879d249811e7fc303b13ac6


---
# <a name="back-up-azure-vms-to-a-recovery-services-vault"></a>Fazer backup de máquinas virtuais do Azure em um cofre dos Serviços de Recuperação
> [!div class="op_single_selector"]
> * [Fazer backup de VMs no cofre dos Serviços de Recuperação](backup-azure-arm-vms.md)
> * [Fazer backup de VMs no cofre de Backup](backup-azure-vms.md)
>
>

Este artigo detalha como para fazer backup de VMs do Azure (implantadas pelo Resource Manager ou com a implantação clássica) em um cofre dos Serviços de Recuperação. A maior parte do trabalho para fazer backup de VMs é a preparação. Antes de fazer backup ou proteger uma VM, você deverá atender aos [pré-requisitos](backup-azure-arm-vms-prepare.md) para preparar o ambiente e proteger suas VMs. Depois de concluir os pré-requisitos, você pode iniciar a operação de backup para tirar instantâneos da sua VM.


[!INCLUDE [learn about backup deployment models](../../includes/backup-deployment-models.md)]

Para obter mais informações, confira os artigos sobre [planejamento da infraestrutura de backup de VM no Azure](backup-azure-vms-introduction.md) e sobre [máquinas virtuais do Azure](https://azure.microsoft.com/documentation/services/virtual-machines/).

## <a name="triggering-the-backup-job"></a>Disparar o trabalho de backup
A política de backup associada ao cofre dos Serviços de Recuperação define a frequência e quando a operação de backup é executada. Por padrão, o primeiro backup agendado é o backup inicial. Até que o backup inicial ocorra, o Status do Último Backup na folha **Trabalhos de Backup** aparece como **Aviso (backup inicial pendente)**.

![Backup pendente](./media/backup-azure-vms-first-look-arm/initial-backup-not-run.png)

A menos que o backup inicial esteja prestes a começar, é recomendável que você execute **Fazer Backup Agora**. O procedimento a seguir começa no painel do cofre. Esse procedimento serve para executar o trabalho de backup inicial depois de concluir todos os pré-requisitos. Se o trabalho de backup inicial já tiver sido executado, esse procedimento não estará disponível. A política de backup associada determina o próximo trabalho de backup.  

Para executar o trabalho de backup inicial:

1. No painel do cofre, no bloco **Backup**, clique em **Máquinas Virtuais do Azure**. <br/>
    ![Ícone Configurações](./media/backup-azure-vms-first-look-arm/rs-vault-in-dashboard-backup-vms.png)

    A folha **Itens de Backup** será aberta.
2. Na folha **Itens de Backup**, clique com o botão direito do mouse no cofre do qual deseja fazer backup e clique em **Fazer backup agora**.

    ![Ícone Configurações](./media/backup-azure-vms-first-look-arm/back-up-now.png)

    O trabalho de Backup será disparado. <br/>

    ![Trabalho de backup iniciado](./media/backup-azure-vms-first-look-arm/backup-triggered.png)
3. Para saber se o backup inicial foi concluído, no painel do cofre, no bloco **Trabalhos de Backup**, clique em **Máquinas virtuais do Azure**.

    ![Bloco dos Trabalhos de Backup](./media/backup-azure-vms-first-look-arm/open-backup-jobs.png)

    A folha Trabalhos de Backup será aberta.
4. Na folha **Trabalhos de backup** , você pode ver o status de todos os trabalhos.

    ![Bloco dos Trabalhos de Backup](./media/backup-azure-vms-first-look-arm/backup-jobs-in-jobs-view.png)

   > [!NOTE]
   > Durante a operação de backup, a extensão de backup em cada máquina virtual libera todas as gravações e tira um instantâneo consistente.
   >
   >

    Quando o trabalho de backup for concluído, o status será *Concluído*.

## <a name="troubleshooting-errors"></a>Solucionar erros
Se você enfrentar problemas ao copiar a sua máquina virtual, confira o [artigo de solução de problemas de VM](backup-azure-vms-troubleshoot.md) para obter ajuda.

## <a name="next-steps"></a>Próximas etapas
Agora que você protegeu sua VM, consulte os seguintes artigos para aprender sobre tarefas de gerenciamento de VM e a restauração de VMs.

* [Gerenciar e monitorar suas máquinas virtuais](backup-azure-manage-vms.md)
* [Restaurar máquinas virtuais](backup-azure-arm-restore-vms.md)



<!--HONumber=Jan17_HO5-->


