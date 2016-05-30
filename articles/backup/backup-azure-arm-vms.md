<properties
	pageTitle="Fazer backup de máquinas virtuais do Azure Resource Manager | Microsoft Azure"
	description="Descubra, registre e faça backup de máquinas virtuais ARM em um cofre de serviços de recuperação com esses procedimentos para backup de máquinas virtuais do Azure."
	services="backup"
	documentationCenter=""
	authors="markgalioto"
	manager="jwhit"
	editor=""
	keywords="backup de máquinas virtuais; fazer backup de máquina virtual, backup e recuperação de desastres; backup de vm arm"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/04/2016"
	ms.author="trinadhk; jimpark; markgal;"/>


# Fazer backup de máquinas virtuais do Azure Resource Manager (ARM)

> [AZURE.SELECTOR]
- [Fazer backup de VMs ARM no Azure](backup-azure-arm-vms.md)
- [Fazer backup de máquinas virtuais do Azure](backup-azure-vms.md)

Este artigo fornece o procedimento para fazer backup de VMs (máquinas virtuais) do Azure Resource Manager (ARM) em um cofre de serviços de recuperação. A maior parte do trabalho de fazer backup de uma VM ARM está na preparação. Antes de fazer backup ou proteger uma máquina virtual do Azure, você deve concluir os [pré-requisitos](backup-azure-arm-vms-prepare.md) para preparar o ambiente para proteger suas VMs. Depois de concluir os pré-requisitos, você pode iniciar a operação de backup para tirar instantâneos da sua VM.

>[AZURE.NOTE] O Azure tem dois modelos de implantação para criar e trabalhar com recursos: [Gerenciador de Recursos e Clássico](../resource-manager-deployment-model.md). Este artigo deve ser usado com as VMs baseadas em ARM e no Gerenciador de Recursos. Confira [Fazer backup de máquinas virtuais do Azure](backup-azure-vms.md) para obter detalhes sobre como trabalhar com VMs do modelo de implantação Clássico.

Para obter informações adicionais, confira os artigos em [planejamento da infraestrutura de backup de VM no Azure](backup-azure-vms-introduction.md) e em [máquinas virtuais do Azure](https://azure.microsoft.com/documentation/services/virtual-machines/).

## Disparar o trabalho de backup

A política de backup associada ao cofre dos Serviços de Recuperação define a frequência e quando a operação de backup é executada. Por padrão, o primeiro backup agendado é o backup inicial. Até que o backup inicial ocorra, o Status do Último Backup na folha **Trabalhos de Backup** aparece como **Aviso (backup inicial pendente)**.

![Backup pendente](./media/backup-azure-vms-first-look-arm/initial-backup-not-run.png)

A menos que o backup inicial esteja prestes a começar, é recomendável que você execute **Fazer Backup Agora**. O procedimento a seguir começa no painel do cofre. Esse procedimento serve para executar o trabalho de backup inicial depois de concluir todos os pré-requisitos. Se o trabalho de backup inicial já tiver sido executado, esse procedimento não estará disponível. A política de backup associada determina o próximo trabalho de backup.

Para executar o trabalho de backup inicial:

1. No painel do cofre, no bloco **Backup**, clique em **Máquinas Virtuais do Azure** <br/> ![Ícone Configurações](./media/backup-azure-vms-first-look-arm/rs-vault-in-dashboard-backup-vms.png)

    A folha **Itens de Backup** será aberta.

2. Na folha **Itens de Backup**, clique com o botão direito do mouse no cofre do qual deseja fazer backup e clique em **Fazer backup agora**.

    ![Ícone Configurações](./media/backup-azure-vms-first-look-arm/back-up-now.png)

    O trabalho de Backup será disparado. <br/>

    ![Trabalho de backup iniciado](./media/backup-azure-vms-first-look-arm/backup-triggered.png)

3. Para saber se o backup inicial foi concluído, no painel do cofre, no bloco **Trabalhos de Backup**, clique em **Máquinas virtuais do Azure**.

    ![Bloco dos Trabalhos de Backup](./media/backup-azure-vms-first-look-arm/open-backup-jobs.png)

    A folha Trabalhos de Backup será aberta.

4. Na folha **Trabalhos de backup**, você pode ver o status de todos os trabalhos.

    ![Bloco dos Trabalhos de Backup](./media/backup-azure-vms-first-look-arm/backup-jobs-in-jobs-view.png)

    >[AZURE.NOTE] Como parte da operação de backup, o serviço de Backup do Azure emite um comando para a extensão de backup em cada máquina virtual para limpar todas as gravações e capturar um instantâneo consistente.

    Quando o trabalho de backup for concluído, o status será *Concluído*.


## Solucionar erros
Se você enfrentar problemas ao copiar a sua máquina virtual, confira o [artigo de solução de problemas de VM](backup-azure-vms-troubleshoot.md) para obter ajuda.

## Próximas etapas

Agora que você protegeu a sua VM, confira os seguintes artigos para ver as tarefas de gerenciamento adicional que pode fazer com as suas VMs e saber como restaurá-las.

- [Gerenciar e monitorar suas máquinas virtuais](backup-azure-manage-vms.md)
- [Restaurar máquinas virtuais](backup-azure-arm-restore-vms.md)

<!---HONumber=AcomDC_0518_2016-->