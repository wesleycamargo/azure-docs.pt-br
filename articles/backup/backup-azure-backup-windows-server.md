<properties
   pageTitle="Fazer backup de arquivos e pastas do Windows Server ou do Windows Client para o Azure | Microsoft Azure"
   description="Saiba como fazer backup de um Windows Server ou Windows Client para o Azure."
   services="backup"
   documentationCenter=""
   authors="aashishr"
   manager="jwhit"
   editor=""/>

<tags ms.service="backup" ms.workload="storage-backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/13/2015" ms.author="jimpark"; "aashishr"/>

# Fazer backup de pastas e arquivos do Windows Server ou do Windows Client para o Azure
Este artigo aborda as etapas necessárias para fazer backup de arquivos e pastas do Windows Server ou do Windows Client para o Azure.

## Antes de começar
Antes de prosseguir, verifique se todos os [pré-requisitos](backup-configure-vault.md#before-you-start) para usar o Backup do Microsoft Azure e proteger o Windows Server e o Windows Client foram atendidos. Os pré-requisitos abrangem tarefas como: criar um cofre de backup, baixar as credenciais do cofre, instalar o Agente de Backup do Azure e registrar o computador no cofre.

## Arquivos de backup
1. Depois que o computador for registrado, abra o snap-in do MMC do Backup do Microsoft Azure.

    ![Resultado da pesquisa](./media/backup-azure-backup-windows-server/result.png)

2. Clique em **Agendar backup**

    ![Agendar backup](./media/backup-azure-backup-windows-server/schedulebackup.png)

3. Selecione os itens dos quais você deseja fazer backup. O Backup do Azure em um cliente Windows/Windows Server (ou seja, sem o System Center Data Protection Manager) permite proteger arquivos e pastas.

    ![Itens para backup](./media/backup-azure-backup-windows-server/items.png)

4. Especifique o agendamento de backup e a política de retenção, que é explicada em detalhes no [artigo](backup-azure-backup-cloud-as-tape.md) a seguir

5. Escolha o método de envio do backup inicial. Sua escolha de concluir a propagação inicial depende da quantidade de dados de que você deseja fazer backup e a velocidade do link de carregamento da Internet. Se você planeja fazer backup de GBs/ TBs de dados em uma conexão de baixa largura de banda e alta latência, é recomendável realizar o backup inicial enviando um disco para o data center do Azure mais próximo. Isso é chamado de "Backup Offline" e é abordado em detalhes neste [artigo](backup-azure-backup-import-export.md). Se você tiver uma conexão com largura de banda suficiente, recomendamos que conclua o backup inicial pela rede.

    ![Backup inicial](./media/backup-azure-backup-windows-server/initialbackup.png)

6. Quando o processo de backup for concluído, vá novamente ao snap-in do MMC e clique em **Fazer Backup Agora** para concluir a propagação inicial pela rede.

    ![Fazer backup agora](./media/backup-azure-backup-windows-server/backupnow.png)

7. Depois que a propagação inicial for concluída, o modo de exibição **Trabalhos** no console do Backup do Azure indicará o status.

    ![IR completo](./media/backup-azure-backup-windows-server/ircomplete.png)

## Próximas etapas
- [Gerenciar o Windows Server ou o Windows Client](backup-azure-manage-windows-server.md)
- [Restaurar o Windows Server ou o Windows Client do Azure](backup-azure-restore-windows-server.md)
- [Backup do Azure - Perguntas frequentes](backup-azure-backup-faq.md)

<!---HONumber=Oct15_HO3-->