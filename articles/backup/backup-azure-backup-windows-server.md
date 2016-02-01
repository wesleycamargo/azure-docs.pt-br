<properties
   pageTitle="Fazer backup de arquivos e pastas do Windows Server ou do Windows Client para o Azure | Microsoft Azure"
   description="Faça backup de um Windows Server ou Cliente do Windows no Azure com esse procedimento simples. Você pode fazer backup de arquivos e pastas do Windows na nuvem em algumas etapas simples."
   services="backup"
   documentationCenter=""
   authors="Jim-Parker"
   manager="jwhit"
   editor=""
   keywords="backup do windows server; fazer backup do windows server"/>

<tags
   ms.service="backup"
   ms.workload="storage-backup-recovery"
	 ms.tgt_pltfrm="na"
	 ms.devlang="na"
	 ms.topic="article"
	 ms.date="01/19/2016"
	 ms.author="jimpark;"/>

# Fazer backup de pastas e arquivos do Windows Server ou do Windows Client para o Azure
É fácil fazer backup de arquivos e pastas do Windows no Azure com este procedimento simples. Se ainda não tiver feito isto, conclua os [pré-requisitos](backup-configure-vault.md#before-you-start) para preparar o ambiente para o backup do computador com Windows antes de continuar.

## Arquivos de backup
1. Depois que o computador for registrado, abra o snap-in do MMC do Backup do Microsoft Azure.

    ![Resultado da pesquisa](./media/backup-azure-backup-windows-server/result.png)

2. Clique em **Agendar Backup**

    ![Agendar um Backup do Windows Server](./media/backup-azure-backup-windows-server/schedulebackup.png)

3. Selecione os itens dos quais você deseja fazer backup. O Backup do Azure em um cliente Windows/Windows Server (ou seja, sem o System Center Data Protection Manager) permite proteger arquivos e pastas.

    ![Itens para o backup do Windows Server](./media/backup-azure-backup-windows-server/items.png)

4. Especifique o agendamento de backup e a política de retenção, que é explicada em detalhes no [artigo](backup-azure-backup-cloud-as-tape.md) a seguir.

5. Escolha o método de envio do backup inicial. Sua escolha de concluir a propagação inicial depende da quantidade de dados de que você deseja fazer backup e a velocidade do link de carregamento da Internet. Se você planeja fazer backup de GBs/ TBs de dados em uma conexão de baixa largura de banda e alta latência, é recomendável realizar o backup inicial enviando um disco para o data center do Azure mais próximo. Isso é chamado de "Backup Offline" e é abordado em detalhes neste [artigo](backup-azure-backup-import-export.md). Se você tiver uma conexão com largura de banda suficiente, recomendamos que conclua o backup inicial pela rede.

    ![Backup inicial do Windows Server](./media/backup-azure-backup-windows-server/initialbackup.png)

6. Quando o processo de backup de agendamento for concluído, volte para o snap-in do MMC e clique em **Fazer Backup Agora** para concluir a propagação inicial pela rede.

    ![Fazer backup do Windows Server agora](./media/backup-azure-backup-windows-server/backupnow.png)

7. Depois que a propagação inicial for concluída, a exibição **Trabalhos** no console do Backup do Azure indicará o status.

    ![IR completo](./media/backup-azure-backup-windows-server/ircomplete.png)

## Próximas etapas
- [Gerenciar o Windows Server ou o Windows Client](backup-azure-manage-windows-server.md)
- [Restaurar o Windows Server ou o Windows Client do Azure](backup-azure-restore-windows-server.md)
- [Backup do Azure - Perguntas frequentes](backup-azure-backup-faq.md)

<!---HONumber=AcomDC_0121_2016-->