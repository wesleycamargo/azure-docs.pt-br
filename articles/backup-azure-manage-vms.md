<properties
	pageTitle="O Backup do Azure - gerenciar máquinas virtuais"
	description="Saiba como gerenciar uma máquina virtual do Azure"
	services="backup"
	documentationCenter=""
	authors="jimpark"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/28/2015"
	ms.author="jimpark"/>

# Gerenciar máquinas virtuais


## Gerenciar máquinas virtuais protegidas

1. Para exibir e gerenciar as configurações de backup para uma máquina virtual, clique o **itens protegidos** guia.

  - Clique no nome de um item protegido para ver o **detalhes de Backup** guia, que mostra informações sobre o último backup.

        ![Virtual machine backup](./media/backup-azure-manage-vms/backup-vmdetails.png)

2. Para exibir e gerenciar as configurações de política de backup para uma máquina virtual, clique o **políticas** guia.

  - O **políticas de Backup** guia exibe a política existente. Você pode modificar conforme necessário. Se você precisar criar uma nova diretiva, clique em **criar** sobre o **políticas** página. Observe que se você quiser remover uma política que não devia todas as máquinas virtuais associadas a ele.

        ![Virtual machine policy](./media/backup-azure-manage-vms/backup-vmpolicy.png)

3. Você pode obter mais informações sobre ações ou status de uma máquina virtual **trabalhos** página. Clique em um trabalho na lista para obter mais detalhes ou filtrar trabalhos para uma máquina virtual específica.

    ![Trabalhos](./media/backup-azure-manage-vms/backup-job.png)

## Backup sob demanda de uma máquina virtual
Você pode tirar uma demanda backup de uma máquina virtual quando ela está configurada para proteção. Se o backup inicial está pendente para a máquina virtual, backup sob demanda criará uma cópia completa da máquina virtual no cofre de backup do Azure. Se o primeiro backup for concluído, o backup por demanda enviará apenas alterações de backup anterior Cofre de backup do Azure.

Para fazer backup de uma máquina virtual uma demanda:

1. Navegue até **itens protegidos** página e selecione **Máquina Virtual do Azure** como **tipo** (se ainda não estiver selecionada) e clique em **Selecione** botão.

    ![Tipo de VM](./media/backup-azure-manage-vms/vm-type.png)

2. Selecione a máquina virtual no qual você deseja fazer uma demanda backup e clique em **Backup agora** botão na parte inferior da página.

    ![Fazer backup agora](./media/backup-azure-manage-vms/backup-now.png)

    Isso criará um trabalho de backup na máquina virtual selecionada. Período de retenção de ponto de recuperação criado por esse trabalho será o mesmo que o especificado na política associada com a máquina virtual.

    ![Criando um trabalho de backup](./media/backup-azure-manage-vms/creating-job.png)

    >[AZURE.NOTE]Para exibir a política associada a uma máquina virtual, fazer uma busca detalhada na máquina virtual de **itens protegidos** página e vá para a guia Diretiva de backup.

3. Depois que o trabalho é criado, você pode clicar em **Exibir trabalho** botão na barra de notificação do sistema para ver o trabalho correspondente na página de trabalhos.

    ![Trabalho de backup criado](./media/backup-azure-manage-vms/created-job.png)

4. Depois da conclusão do trabalho, um ponto de recuperação será criado que você pode usar para restaurar a máquina virtual. Isso também será incrementar o valor de coluna de ponto de recuperação em 1 no **itens protegidos** página.

## Interromper a proteção de máquinas virtuais
Você pode optar por parar futuros backups de uma máquina virtual com as seguintes opções:

- Reter dados de backup associados a máquina virtual no cofre de Backup do Azure
- Excluir dados de backup associados a máquina virtual

Se você tiver selecionado a primeira opção, você pode usar os dados de backup para restaurar a máquina virtual. Para obter detalhes nessas máquinas virtuais de preço, clique [aqui](http://azure.microsoft.com/pricing/details/backup/).

Para interromper a proteção para uma máquina virtual:

1. Navegue até **itens protegidos** página e selecione **máquina virtual do Azure** como o tipo de filtro (se ainda não estiver selecionada) e clique em **Selecione** botão.

    ![Tipo de VM](./media/backup-azure-manage-vms/vm-type.png)

2. Selecione a máquina virtual e clique em **Parar proteção** na parte inferior da página.

    ![Interromper a proteção](./media/backup-azure-manage-vms/stop-protection.png)

3. Por padrão, Backup do Azure não exclui os dados de backup associados a máquina Virtual.

    ![Confirme a proteção](./media/backup-azure-manage-vms/confirm-stop-protection.png)

    Se você deseja excluir os dados de backup, marque a caixa de seleção.

    ![Caixa de seleção](./media/backup-azure-manage-vms/checkbox.png)

    Selecione um motivo para interromper o backup. Embora seja opcional, fornecer um motivo ajudará a Backup do Azure para trabalhar nos comentários e priorizar os cenários de cliente.

4. Clique em **Enviar** botão para enviar o **Interromper proteção** trabalho. Clique em **Exibir trabalho** para ver o trabalho em correspondente **trabalhos** página.

    ![Interromper a proteção](./media/backup-azure-manage-vms/stop-protect-success.png)

    Se você não selecionou **Delete associados a dados de backup** opção durante **Parar proteção** assistente, em seguida, postar a conclusão do trabalho, as alterações de status de proteção para **proteção interrompida**.

    ![Proteção parada](./media/backup-azure-manage-vms/protection-stopped-status.png)

    Se você tiver selecionado o **Delete associados a dados de backup** opção, uma máquina virtual não fará parte dos **itens protegidos** página.

## Proteger novamente a máquina Virtual
Se você não selecionou o **Excluir associados dados de backup** opção **Parar proteção**, você pode proteger novamente a máquina virtual seguindo as etapas semelhantes para backup registradas máquinas virtuais. Depois de protegido, essa máquina virtual terá retidos antes da proteção de dados de backup e pontos de recuperação criados após protege novamente.

Após proteger novamente, o status da proteção da máquina virtual será alterado para **protegido** se houver pontos de recuperação antes de **Parar proteção**.

  ![Máquina virtual protegida](./media/backup-azure-manage-vms/reprotected-status.png)

>[AZURE.NOTE]Ao proteger novamente a máquina virtual, você pode escolher uma diretiva diferente que a política com a qual a máquina virtual foi inicialmente protegida.

## Cancelar o registro de máquinas virtuais

Se você quiser remover a máquina virtual do Cofre de backup:

1. Clique no **UNREGISTER** botão na parte inferior da página.

    ![Desabilitar a proteção](./media/backup-azure-manage-vms/unregister-button.png)

    Uma notificação será exibida na parte inferior da tela solicitando confirmação. Clique em **Sim** para continuar.

    ![Desabilitar a proteção](./media/backup-azure-manage-vms/confirm-unregister.png)

## Excluir dados de Backup
Você pode excluir os dados de backup associados a uma máquina virtual, ou:

- Durante o trabalho de interromper proteção
- Depois de uma interrupção da proteção trabalho é concluído em uma máquina virtual

Para excluir dados de backup em uma máquina virtual, que está na "proteção interrompida" estado postar a conclusão bem-sucedida da **Parar Backup** trabalho:

1. Navegue até **itens protegidos** página e selecione **Máquina Virtual do Azure** como tipo e clique em **Selecione** botão.

    ![Tipo de VM](./media/backup-azure-manage-vms/vm-type.png)

2. Selecione a máquina virtual. A máquina virtual estará em **proteção interrompida** estado.

    ![Proteção interrompida](./media/backup-azure-manage-vms/protection-stopped-b.png)

3. Clique no **Excluir** botão na parte inferior da página.

    ![Exclusão de backup](./media/backup-azure-manage-vms/delete-backup.png)

4. No **Excluir dados de backup** assistente, selecione um motivo para a exclusão de dados de backup (recomendados) e clique em **Enviar**.

    ![Excluir dados de backup](./media/backup-azure-manage-vms/delete-backup-data.png)

5. Isso criará um trabalho para excluir dados de backup da máquina virtual selecionada. Clique em **Exibir trabalho** para ver o trabalho correspondente na página de trabalhos.

    ![Exclusão de dados bem-sucedida](./media/backup-azure-manage-vms/delete-data-success.png)

    Depois que o trabalho for concluído, entrada correspondente a máquina virtual será removida do **itens protegidos** página.


###Painel

Sobre o **painel** página você pode examinar as informações sobre máquinas virtuais do Azure, armazenamento e trabalhos associados a eles nas últimas 24 horas. Você pode exibir o status de backup e quaisquer erros de backup associados.

  ![Painel](./media/backup-azure-manage-vms/dashboard-protectedvms.png)

<!---HONumber=GIT-SubDir-->