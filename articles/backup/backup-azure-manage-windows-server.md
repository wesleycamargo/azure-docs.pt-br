<properties
	pageTitle="Gerenciar servidores e cofres de Backup do Azure | Microsoft Azure"
	description="Use este tutorial para aprender a gerenciar servidores e cofres de Backup do Azure."
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor="tysonn"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/01/2016"
	ms.author="jimpark;markgal"/>


# Gerenciar servidores e cofres de backup do Azure
Neste artigo, você encontra uma visão geral das tarefas de gerenciamento de backup disponíveis no portal de gerenciamento e o agente de Backup do Microsoft Azure.

>[AZURE.NOTE] Este artigo fornece os procedimentos para trabalhar no modelo de implantação clássico.

## Tarefas do portal de gerenciamento
1. Entre no [Portal de Gerenciamento](https://manage.windowsazure.com).

2. Clique em **Serviços de Recuperação**, em seguida, clique no nome do cofre de backup para exibir a página de Início Rápido.

    ![Guias Gerenciar Backup do Azure](./media/backup-azure-manage-windows-server/rs-left-nav.png)

Selecionando as opções na parte superior da página Início Rápido, você pode ver as tarefas de gerenciamento disponíveis.

![Guias Gerenciar Backup do Azure](./media/backup-azure-manage-windows-server/qs-page.png)

### Painel
Clique em **Painel** para ver a visão geral do uso para o servidor. A **visão geral de uso** inclui:

- O número de Windows Servers registrados na nuvem
- O número de máquinas virtuais do Azure protegidas na nuvem
- O armazenamento total consumido no Azure
- O status de trabalhos recentes

Na parte inferior do Painel, você pode executar as seguintes tarefas:

- **Gerenciar certificado** - se um certificado tiver sido usado para registrar o servidor, então use isso para atualizar o certificado. Se estiver usando as credenciais do cofre, não use **Gerenciar certificados**.
- **Excluir** - exclui o cofre de backup atual. Se não estiver sendo usado um cofre de backup, você poderá excluir para liberar mais espaço de armazenamento. **Excluir** é ativado somente depois que todos os servidores registrados foram excluídos do cofre.

![Tarefas do painel Backup](./media/backup-azure-manage-windows-server/dashboard-tasks.png)

## Itens registrados
Selecione **Itens registrados** para exibir os nomes dos servidores registrados para o cofre.

![Itens registrados](./media/backup-azure-manage-windows-server/registered-items.png)

O filtro **Tipo** tem como padrão a Máquina Virtual do Azure. Para exibir os nomes dos servidores registrados neste cofre, selecione **Windows Server** no menu suspenso.

A partir daqui, você pode executar as seguintes tarefas:

- **Permitir Novo Registro** - quando essa opção estiver selecionada para um servidor, você poderá usar o **Assistente de Registro** no agente de Backup do Microsoft Azure local para registrar o servidor com o cofre de backup uma segunda vez. Talvez você precise registrar novamente devido a um erro no certificado ou se um servidor tiver que ser refeito.
- **Excluir** - Exclui um servidor do cofre de backup. Todos os dados armazenados associados ao servidor serão excluídos imediatamente.

    ![Itens registrados](./media/backup-azure-manage-windows-server/registered-items-tasks.png)

## Itens protegidos
Clique em **Itens Protegidos** para ver os itens que foram colocados no backup dos servidores.

![Itens protegidos](./media/backup-azure-manage-windows-server/protected-items.png)

## Configurar

Na guia **Configurar**, você pode selecionar a opção de redundância de armazenamento apropriada. O melhor momento para selecionar a opção de redundância de armazenamento é logo após a criação de um cofre e antes de qualquer computador ser registrado nele.

>[AZURE.WARNING] Depois que um item tiver sido registrado no cofre, a opção de redundância de armazenamento será bloqueada e não poderá ser modificada.

![Configurar](./media/backup-azure-manage-windows-server/configure.png)

Confira este artigo para saber mais sobre a [redundância de armazenamento](../storage/storage-redundancy.md).

## Tarefas do agente de Backup do Microsoft Azure

### Console

Abra o **agente de Backup do Microsoft Azure** (você poderá localizá-lo ao procurar *Backup do Microsoft Azure* em seu computador).

![Agendar um Backup do Windows Server](./media/backup-azure-manage-windows-server/snap-in-search.png)

Na guia **Ações**, disponibilizada à direita do console do agente de backup, você pode executar as seguintes tarefas de gerenciamento:

- Registrar Servidor
- Agendar backup
- Fazer backup agora
- Alterar propriedades

![Ações do console do agente de Backup do Microsoft Azure](./media/backup-azure-manage-windows-server/console-actions.png)

>[AZURE.NOTE] Para **Recuperar Dados**, veja [Restauração de arquivos para um computador cliente Windows ou para um servidor Windows](backup-azure-restore-windows-server.md).

### Modificar um backup existente

1. No agente de Backup do Microsoft Azure, clique em **Agendar Backup**.

    ![Agendar um Backup do Windows Server](./media/backup-azure-manage-windows-server/schedule-backup.png)

2. No **Assistente de Agendamento de Backup**, deixe a opção **Fazer alterações nos itens ou nos horários de backup** selecionada e clique em **Avançar**.

    ![Agendar um Backup do Windows Server](./media/backup-azure-manage-windows-server/modify-or-stop-a-scheduled-backup.png)

3. Se quiser adicionar ou alterar itens, na tela **Selecionar Itens para Backup**, clique em **Adicionar Itens**.

    Você também pode definir **Configurações de Exclusão** nesta página do assistente. Se você quiser excluir arquivos ou tipos de arquivo, leia o procedimento para adicionar [configurações de exclusão](#exclusion-settings).

4. Selecione os arquivos e as pastas dos quais você deseja fazer backup e clique em **Ok**.

    ![Agendar um Backup do Windows Server](./media/backup-azure-manage-windows-server/add-items-modify.png)

5. Especifique o **agendamento de backup** e clique em **Avançar**.

    Você pode agendar backups diários (no máximo três vezes por dia) ou backups semanais.

    ![Itens para o backup do Windows Server](./media/backup-azure-manage-windows-server/specify-backup-schedule-modify-close.png)

    >[AZURE.NOTE] A especificação do agendamento de backup é explicada em detalhes neste [artigo](backup-azure-backup-cloud-as-tape.md).

6. Selecione a **Política de Retenção** para a cópia de backup e clique em **Avançar**.

    ![Itens para o backup do Windows Server](./media/backup-azure-manage-windows-server/select-retention-policy-modify.png)

7. Na tela **Confirmação**, examine as informações e clique em **Concluir**.

8. Depois que o assistente terminar de criar o **agendamento de backup**, clique em **Fechar**.

    Depois de modificar a proteção, é possível confirmar se os backups estão sendo acionados corretamente acessando a guia **Trabalhos** e confirmando se as alterações são refletidas nos trabalhos de backup.

### Habilitar a limitação de rede  
O agente de Backup do Azure fornece uma guia Limitação, que permite controlar como a largura de banda é usada durante a transferência de dados. Esse controle pode ser útil se você precisa fazer backup de dados durante o horário de expediente, mas não quer que o processo de backup interfira no outro tráfego de Internet. A limitação da transferência de dados aplica-se a atividades de backup e restauração.

Para habilitar a limitação:

1. No **agente de Backup**, clique em **Alterar Propriedades**.

2. Marque a caixa de seleção **Habilitar limitação de uso de largura de banda da Internet para operações de backup**.

    ![Limitação de rede](./media/backup-azure-manage-windows-server/throttling-dialog.png)

3. Depois de habilitar a limitação, especifique a largura de banda permitida para transferência de dados de backup durante as **Horas úteis** e **Horas não úteis**.

    Os valores de largura de banda começam em 512 quilobytes por segundo (Kbps) e podem ir até 1023 megabytes por segundo (Mbps). Você também pode indicar o início e o término para **Horas úteis** e quais dias da semana são considerados Dias úteis. O tempo fora das Horas úteis indicadas é considerado como hora não útil.

4. Clique em **OK**.

## Configurações de exclusão

1. Abra o **agente de Backup do Microsoft Azure** (você poderá localizá-lo ao procurar *Backup do Microsoft Azure* em seu computador).

    ![Agendar um Backup do Windows Server](./media/backup-azure-manage-windows-server/snap-in-search.png)

2. No agente de Backup do Microsoft Azure, clique em **Agendar Backup**.

    ![Agendar um Backup do Windows Server](./media/backup-azure-manage-windows-server/schedule-backup.png)

3. No Assistente de Agendamento de Backup, deixe a opção **Fazer alterações nos itens ou nos horários de backup** selecionada e clique em **Avançar**.

    ![Agendar um Backup do Windows Server](./media/backup-azure-manage-windows-server/modify-or-stop-a-scheduled-backup.png)

4. Clique em **Configurações de Exclusões**.

    ![Agendar um Backup do Windows Server](./media/backup-azure-manage-windows-server/exclusion-settings.png)

5. Clique em **Adicionar Exclusão**.

    ![Agendar um Backup do Windows Server](./media/backup-azure-manage-windows-server/add-exclusion.png)

6. Selecione o local e clique em **OK**.

    ![Agendar um Backup do Windows Server](./media/backup-azure-manage-windows-server/exclusion-location.png)

7. Adicione a extensão de arquivo no campo **Tipo de Arquivo**.

    ![Agendar um Backup do Windows Server](./media/backup-azure-manage-windows-server/exclude-file-type.png)

    Adição de uma extensão .mp3

    ![Agendar um Backup do Windows Server](./media/backup-azure-manage-windows-server/exclude-mp3.png)

    Para adicionar outra extensão, clique em **Adicionar Exclusão** e insira outra extensão de tipo de arquivo (adição de uma extensão .jpeg).

    ![Agendar um Backup do Windows Server](./media/backup-azure-manage-windows-server/exclude-jpg.png)

8. Quando tiver adicionado todas as extensões, clique em **OK**.

9. Prossiga com o Assistente de Agendamento de Backup clicando em **Avançar** até a **página Confirmação** e clique em **Concluir**.

    ![Agendar um Backup do Windows Server](./media/backup-azure-manage-windows-server/finish-exclusions.png)

## Próximas etapas
- [Restaurar o Windows Server ou o Windows Client do Azure](backup-azure-restore-windows-server.md)
- Para saber mais sobre o Backup do Azure, confira [Visão geral do backup do Azure](backup-introduction-to-azure-backup.md)
- Visite o [Fórum de backup do Azure](http://go.microsoft.com/fwlink/p/?LinkId=290933).

<!---HONumber=AcomDC_0413_2016-->