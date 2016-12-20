---
title: "Restaurar dados para um Windows Server ou Client do Azure usando o modelo de implantação clássico | Microsoft Docs"
description: Saiba como restaurar de um Windows Server ou Windows Client.
services: backup
documentationcenter: 
author: saurabhsensharma
manager: shivamg
editor: 
ms.assetid: 85585dfc-c764-4e8c-8f0e-40b969640ac2
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2016
ms.author: trinadhk; jimpark; markgal;
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 8b22f1a70c516ae2b1a9d095670d1b6719590eaa


---
# <a name="restore-files-to-a-windows-server-or-windows-client-machine-using-the-classic-deployment-model"></a>Restaurar arquivos em um computador de cliente do Windows ou Windows Server usando o modelo de implantação do clásico
> [!div class="op_single_selector"]
> * [Portal clássico](backup-azure-restore-windows-server-classic.md)
> * [Portal do Azure](backup-azure-restore-windows-server.md)
> 
> 

Este artigo aborda as etapas necessárias para executar dois tipos de operações de restauração:

* Restaurar dados para o mesmo computador do qual os backups foram realizados.
* Restaurar dados para qualquer outra máquina.

Em ambos os casos, os dados são recuperados do cofre de backup do Azure.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

## <a name="recover-data-to-the-same-machine"></a>Recuperar dados para o mesmo computador
Se você excluiu acidentalmente um arquivo e deseja restaurá-lo para o mesmo computador (do qual o backup foi feito), as etapas a seguir o ajudarão a recuperar os dados.

1. Abra o snap-in do **Backup do Microsoft Azure** .
2. Clique em **Recuperar Dados** para iniciar o fluxo de trabalho.
   
    ![Recuperar Dados](./media/backup-azure-restore-windows-server-classic/recover.png)
3. Selecione a opção **Este servidor (*nomeseucomputador*)** para restaurar o backup do arquivo no mesmo computador.
   
    ![Mesmo computador](./media/backup-azure-restore-windows-server-classic/samemachine.png)
4. Escolha **Procurar arquivos** ou **Pesquisar arquivos**.
   
    Deixe a opção padrão se você planeja restaurar um ou mais arquivos cujo caminho é conhecido. Se você não tiver certeza sobre a estrutura de pastas, mas gostaria de procurar por um arquivo, selecione a opção **Pesquisar arquivos** . Para fins desta seção, prosseguiremos com a opção padrão.
   
    ![Procurar arquivos](./media/backup-azure-restore-windows-server-classic/browseandsearch.png)
5. Selecione o volume do qual você deseja restaurar o arquivo.
   
    Você pode restaurar de qualquer momento anterior. As datas que aparecem em **negrito** no controle de calendário indicam a disponibilidade de um ponto de restauração. Quando uma data é selecionada, com base em seu agendamento de backup (e o sucesso de uma operação de backup), você pode selecionar um ponto no tempo no menu suspenso **Tempo** .
   
    ![Volume e data](./media/backup-azure-restore-windows-server-classic/volanddate.png)
6. Selecione os itens que serão recuperados. Você pode selecionar várias pastas/arquivos que deseja restaurar.
   
    ![Selecionar arquivos](./media/backup-azure-restore-windows-server-classic/selectfiles.png)
7. Especifique os parâmetros de recuperação.
   
    ![Opções de recuperação](./media/backup-azure-restore-windows-server-classic/recoveroptions.png)
   
   * Você tem uma opção de restaurar no local original (em que a pasta/o arquivo será substituído) ou em outro local no mesmo computador.
   * Se o arquivo/pasta que você deseja restaurar existir no local de destino, você tem a opção de criar cópias (duas versões do mesmo arquivo), substituir os arquivos no local de destino ou ignorar a recuperação dos arquivos que existem no destino.
   * É altamente recomendável que você deixe a opção padrão de restaurar as ACLs nos arquivos que estão sendo recuperados.
8. Depois que essas entradas forem fornecidas, clique em **Próximo**. O fluxo de trabalho de recuperação, que restaura os arquivos para essa máquina, será iniciado.

## <a name="recover-to-an-alternate-machine"></a>Recuperar em um computador alternativo
Se o servidor inteiro for perdido, você ainda pode recuperar dados do backup do Azure para um computador diferente. As etapas a seguir ilustram o fluxo de trabalho.  

A terminologia usada nessas etapas inclui:

* *Máquina de origem* : a máquina original da qual o backup foi feito e que está indisponível no momento.
* *Computador de destino* – O computador para o qual os dados estão sendo recuperados.
* *Cofre de exemplo* – Cofre de backup em que o *Computador de origem* e o *Computador de destino são registrados*. <br/>

> [!NOTE]
> Os backups de um computador não podem ser restaurados em um computador que esteja executando uma versão anterior do sistema operacional. Por exemplo, se um backup for de um computador com Windows 7, ele poderá ser restaurado em um computador com Windows 8 ou superior. No entanto, o contrário não pode ocorrer.
> 
> 

1. Abra o snap-in do **Backup do Microsoft Azure** no *Computador de destino*.
2. Verifique se o *Computador de destino* e o *Computador de origem* serão restaurados no mesmo cofre de backup.
3. Clique em **Recuperar Dados** para iniciar o fluxo de trabalho.
   
    ![Recuperar Dados](./media/backup-azure-restore-windows-server-classic/recover.png)
4. Selecione **Outro servidor**
   
    ![Outro servidor](./media/backup-azure-restore-windows-server-classic/anotherserver.png)
5. Forneça o arquivo de credencial de cofre que corresponde ao *Cofre de exemplo*. Se o arquivo de credencial do cofre for inválido (ou tiver expirado), baixe um novo arquivo de credencial do *Cofre de exemplo* no Portal do Azure clássico. Depois que o arquivo de credencial de cofre for fornecido, o cofre de backup relacionado ao arquivo de credencial de cofre será exibido.
6. Selecione o *Computador de origem* na lista de computadores exibidos.
   
    ![Lista de computadores](./media/backup-azure-restore-windows-server-classic/machinelist.png)
7. Selecione a opção **Pesquisar arquivos** ou **Procurar arquivos**. Para os fins desta seção, usaremos a opção **Pesquisar arquivos** .
   
    ![Pesquisar](./media/backup-azure-restore-windows-server-classic/search.png)
8. Na próxima tela, selecione o volume e a data. Procure o nome do arquivo/pasta que você deseja restaurar.
   
    ![Pesquisar itens](./media/backup-azure-restore-windows-server-classic/searchitems.png)
9. Selecione o local no qual os arquivos precisam ser restaurados.
   
    ![Restaurar local](./media/backup-azure-restore-windows-server-classic/restorelocation.png)
10. Forneça a senha de criptografia determinada durante o registro da *Máquina de origem* no *Cofre de exemplo*.
    
    ![Criptografia](./media/backup-azure-restore-windows-server-classic/encryption.png)
11. Após a entrada ser fornecida, clique no botão **Recuperar**, o que inicia a restauração dos arquivos de backup no destino fornecido.

## <a name="next-steps"></a>Próximas etapas
* [Backup do Azure - Perguntas frequentes](backup-azure-backup-faq.md)
* Visite o [Fórum de backup do Azure](http://go.microsoft.com/fwlink/p/?LinkId=290933).

## <a name="learn-more"></a>Saiba mais
* [Visão geral do backup do Azure](http://go.microsoft.com/fwlink/p/?LinkId=222425)
* [Fazer backup de máquinas virtuais do Azure](backup-azure-vms-introduction.md)
* [Fazer backup de cargas de trabalho Microsoft](backup-azure-dpm-introduction.md)




<!--HONumber=Nov16_HO3-->


