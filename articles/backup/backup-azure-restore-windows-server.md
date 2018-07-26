---
title: Restaurar dados no Azure para um computador Windows ou Windows Server
description: Saiba como restaurar os dados armazenados no Azure para um computador Windows ou Windows Server.
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 7/25/2018
ms.author: saurse
ms.openlocfilehash: a1c9df57ddebbb1cf471f705acfbd6651c151d7b
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39247271"
---
# <a name="restore-files-to-a-windows-server-or-windows-client-machine-using-resource-manager-deployment-model"></a>Restaurar arquivos em um computador de cliente do Windows ou Windows Server usando o modelo de implantação do Gerenciador de recursos

Este artigo explica como restaurar dados de um cofre de backup. Para restaurar dados, você pode usar o assistente recuperar dados do agente do Serviços de Recuperação do Microsoft Azure (MARS). Ao restaurar dados, é possível:

* Restaurar dados para o mesmo computador do qual os backups foram realizados.
* Restaurar dados para um computador alternativo.

Em janeiro de 2017, a Microsoft lançou uma atualização de Versão Prévia para o agente do MARS. Juntamente com as correções de bug, essa atualização permite Restauração Instantânea, que permite que você monte um instantâneo de ponto de recuperação gravável como um volume de recuperação. Em seguida, é possível explorar os arquivos de volume de recuperação e de cópia em um computador local e, assim, restaurar arquivos de forma seletiva.

> [!NOTE]
> A [atualização do Backup do Azure de janeiro de 2017](https://support.microsoft.com/en-us/help/3216528?preview) será necessária se você desejar usar a Restauração Instantânea para restaurar dados. Além disso, os dados de backup devem ser protegidos em cofres nas localidades listadas no artigo de suporte. Consulte a [atualização do Backup do Azure de janeiro de 2017](https://support.microsoft.com/en-us/help/3216528?preview) para obter a lista mais recente de localidades que oferecem suporte à Restauração Instantânea. No momento, a Restauração Instantânea **não** está disponível em todas as localidades.
>

Usar a Restauração Instantânea com os cofres dos Serviços de Recuperação no Portal do Azure. Se você tiver armazenado os dados em cofres de Backup, eles terão sido convertidos em cofres dos Serviços de Recuperação. Se desejar usar a Restauração Instantânea, baixe a atualização do MARS e siga os procedimentos que mencionam a Restauração Instantânea.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="use-instant-restore-to-recover-data-to-the-same-machine"></a>Use a Restauração Instantânea para recuperar dados no mesmo computador

Se você excluiu acidentalmente um arquivo e deseja restaurá-lo para o mesmo computador (do qual o backup foi feito), as etapas a seguir o ajudarão a recuperar os dados.

1. Abra o snap-in do **Backup do Microsoft Azure** . Se você não souber onde o snap-in foi instalado, pesquise **Backup do Microsoft Azure** no computador ou servidor.

    O aplicativo da área de trabalho deve aparecer nos resultados da pesquisa.

2. Clique em **Recuperar Dados** para iniciar o assistente.

    ![Recuperar Dados](./media/backup-azure-restore-windows-server/recover.png)

3. No painel **Introdução**, para restaurar os dados para o mesmo computador ou servidor, selecione **Este servidor (`<server name>`)** e clique em **Próximo**.

    ![Escolha a opção Este servidor para restaurar os dados para o mesmo computador](./media/backup-azure-restore-windows-server/samemachine_gettingstarted_instantrestore.png)

4. No painel **Selecionar Modo de Recuperação**, escolha **Pastas e arquivos individuais** e, em seguida, clique em **Avançar**.

    ![Procurar arquivos](./media/backup-azure-restore-windows-server/samemachine_selectrecoverymode_instantrestore.png)

5. No painel **Selecionar Volume e Data**, selecione o volume que contém os arquivos e/ou pastas que deseja restaurar.

    No calendário, selecione um ponto de recuperação. Você pode restaurar de qualquer ponto de recuperação. As datas em **negrito** indicam a disponibilidade de pelo menos um ponto de recuperação. Depois de selecionar uma data, se houver vários pontos de recuperação disponíveis, escolha o ponto de recuperação específico no menu suspenso **Hora**.

    ![Volume e data](./media/backup-azure-restore-windows-server/samemachine_selectvolumedate_instantrestore.png)

6. Depois de ter escolhido o ponto de recuperação a ser restaurado, clique em **Montar**.

    O Backup do Azure monta o ponto de recuperação local e o usa como um volume de recuperação.

7. No painel **Procurar e Recuperar Arquivos**, clique em **Procurar** para abrir o Windows Explorer e localize os arquivos e pastas desejados.

    ![Opções de recuperação](./media/backup-azure-restore-windows-server/samemachine_browserecover_instantrestore.png)


8. No Windows Explorer, copie os arquivos e/ou pastas que deseja restaurar e cole-os em qualquer localização local no servidor ou computador. Você pode abrir ou transmitir os arquivos diretamente do volume de recuperação e verificar se as versões corretas são recuperadas.

    ![Copiar e colar arquivos e pastas do volume montado na localização local](./media/backup-azure-restore-windows-server/samemachine_copy_instantrestore.png)

9. Quando você terminar de restaurar os arquivos e/ou pastas, no painel **Procurar e Recuperar Arquivos**, clique em **Desmontar**. Clique em **Sim** para confirmar que deseja desmontar o volume.

    ![Desmontar o volume e confirmar](./media/backup-azure-restore-windows-server/samemachine_unmount_instantrestore.png)

    > [!Important]
    > Se você não clicar em Desmontar, o Volume de Recuperação permanecerá montado por seis horas a partir da hora em que foi montado. No entanto, o tempo de montagem é estendido até um máximo de 24 horas no caso de uma cópia de arquivo em andamento. Não será executada nenhuma operação de backup enquanto o volume estiver montado. Qualquer operação de backup agendada para execução durante o tempo em que o volume estiver montado será executada após o volume de recuperação ser desmontado.
    >


## <a name="use-instant-restore-to-restore-data-to-an-alternate-machine"></a>Usar a Restauração Instantânea para restaurar dados em um computador alternativo
Se o servidor inteiro for perdido, você ainda pode recuperar dados do backup do Azure para um computador diferente. As etapas a seguir ilustram o fluxo de trabalho.


A terminologia usada nessas etapas inclui:

* *Máquina de origem* : a máquina original da qual o backup foi feito e que está indisponível no momento.
* *Computador de destino* – O computador para o qual os dados estão sendo recuperados.
* *Cofre de exemplo* – o cofre Serviços de Recuperação no qual a *Máquina de origem* e a *Máquina de destino* estão registradas. <br/>

> [!NOTE]
> Os backups não podem ser restaurados em um computador de destino executando uma versão anterior do sistema operacional. Por exemplo, um backup feito em um computador com Windows 7 pode ser restaurado em um computador com Windows 8 ou mais recente. Um backup feito em um computador com Windows 8 não pode ser restaurado em um computador com Windows 7.
>
>

1. Abra o snap-in do **Backup do Microsoft Azure** no *Computador de destino*.

2. Verifique se o *Computador de destino* e o *Computador de origem* estão registrados no mesmo cofre de Serviços de Recuperação.

3. Clique em **Recuperar Dados** para abrir o **Assistente de Recuperação de Dados**.

    ![Recuperar Dados](./media/backup-azure-restore-windows-server/recover.png)

4. No painel **Introdução**, selecione **Outro servidor**

    ![Outro servidor](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

5. Forneça o arquivo de credencial de cofre que corresponde ao *Cofre de exemplo* e clique em **Próximo**.

    Se o arquivo de credencial de cofre for inválido (ou tiver expirado), baixe um novo arquivo de credencial de cofre do *Cofre de exemplo* no Portal do Azure. Depois de fornecer uma credencial de cofre válida, o nome do Cofre de Backup correspondente aparecerá.


6. No painel **Selecionar Servidor de Backup**, selecione o *Computador de origem* na lista de computadores exibidos e forneça a senha. Em seguida, clique em **Próximo**.

    ![Lista de computadores](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

7. No painel **Selecionar Modo de Recuperação**, selecione **Pastas e arquivos individuais** e clique em **Próximo**.

    ![Search](./media/backup-azure-restore-windows-server/alternatemachine_selectrecoverymode_instantrestore.png)

8. No painel **Selecionar Volume e Data**, selecione o volume que contém os arquivos e/ou pastas que deseja restaurar.

    No calendário, selecione um ponto de recuperação. Você pode restaurar de qualquer ponto de recuperação. As datas em **negrito** indicam a disponibilidade de pelo menos um ponto de recuperação. Depois de selecionar uma data, se houver vários pontos de recuperação disponíveis, escolha o ponto de recuperação específico no menu suspenso **Hora**.

    ![Pesquisar itens](./media/backup-azure-restore-windows-server/alternatemachine_selectvolumedate_instantrestore.png)

9. Clique em **Montar** para montar localmente o ponto de recuperação como um volume de recuperação em seu *Computador de destino*.

10. No painel **Procurar e Recuperar Arquivos**, clique em **Procurar** para abrir o Windows Explorer e localize os arquivos e pastas desejados.

    ![Criptografia](./media/backup-azure-restore-windows-server/alternatemachine_browserecover_instantrestore.png)

11. No Windows Explorer, copie os arquivos e/ou pastas do volume de recuperação e cole-os na localização de seu *Computador de destino*. Você pode abrir ou transmitir os arquivos diretamente do volume de recuperação e verificar se as versões corretas são recuperadas.

    ![Criptografia](./media/backup-azure-restore-windows-server/alternatemachine_copy_instantrestore.png)

12. Quando você terminar de restaurar os arquivos e/ou pastas, no painel **Procurar e Recuperar Arquivos**, clique em **Desmontar**. Clique em **Sim** para confirmar que deseja desmontar o volume.

    ![Criptografia](./media/backup-azure-restore-windows-server/alternatemachine_unmount_instantrestore.png)

    > [!Important]
    > Se você não clicar em Desmontar, o Volume de Recuperação permanecerá montado por seis horas a partir da hora em que foi montado. No entanto, o tempo de montagem é estendido até um máximo de 24 horas no caso de uma cópia de arquivo em andamento. Não será executada nenhuma operação de backup enquanto o volume estiver montado. Qualquer operação de backup agendada para execução durante o tempo em que o volume estiver montado será executada após o volume de recuperação ser desmontado.
    >

## <a name="next-steps"></a>Próximas etapas
* Agora que você restaurou seus arquivos e pastas, poderá [gerenciar seus backups](backup-azure-manage-windows-server.md).
