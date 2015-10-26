<properties
	pageTitle="Gerenciar servidores e cofres de Backup do Azure | Microsoft Azure"
	description="Use este tutorial para aprender a gerenciar servidores e cofres de Backup do Azure."
	services="backup"
	documentationCenter=""
	authors="aashishr"
	manager="jwhit"
	editor="tysonn"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/05/2015"
	ms.author="jimpark; aashishr; giridham"/>


# Gerenciar servidores e cofres de backup do Azure
Neste artigo, você encontra uma visão geral das tarefas de gerenciamento de backup disponíveis no portal de gerenciamento.

1. Entre no [Portal de Gerenciamento](https://manage.windowsazure.com).
2. Clique em **Serviços de Recuperação**, em seguida, clique no nome do cofre de backup para exibir a página de Início Rápido.

    Selecionando as opções na parte superior da página Início Rápido, você pode ver as tarefas de gerenciamento disponíveis.

    ![Itens protegidos](./media/backup-azure-manage-windows-server/RS_tabs.png)

## Painel
Clique em **Painel** para ver a visão geral do uso para o servidor. Na parte inferior do Painel, você pode executar as seguintes tarefas:

- **Gerenciar certificados**. Se um certificado foi usado para registrar o servidor, então use isso para atualizar o certificado. Se estiver usando as credenciais do cofre, não use **Gerenciar certificados**.
- **Excluir**. Exclui o cofre de backup atual. Se não estiver sendo usado um cofre de backup, você poderá excluir para liberar mais espaço de armazenamento. **Excluir** é ativado somente depois que todos os servidores registrados foram excluídos do cofre.
- **Credenciais do cofre**. Use este item de menu de Visão rápida para configurar suas credenciais de cofre.

## Itens Protegidos
Clique em **Itens Protegidos** para ver os itens que foram colocados no backup dos servidores. Esta lista é somente para fins informativos.

![Itens protegidos](./media/backup-azure-manage-windows-server/RS_protecteditems.png)

## Itens registrados
Selecione **Itens registrados** para exibir os nomes dos servidores registrados para o cofre.

![Servidor excluído](./media/backup-azure-manage-windows-server/RS_deletedserver.png)

A partir daqui, você pode executar as seguintes tarefas:

- **Permitir Novo Registro** - Quando esta opção estiver selecionada para um servidor, você poderá usar o **Assistente de Registro** no agente para registrar o servidor com o cofre de backup uma segunda vez. Talvez você precise registrar novamente devido a um erro no certificado ou se um servidor tiver que ser refeito.
- **Excluir** - Exclui um servidor do cofre de backup. Todos os dados armazenados associados ao servidor serão excluídos imediatamente.

## Próximas etapas
- [Restaurar o Windows Server ou o Windows Client do Azure](backup-azure-restore-windows-server.md)
- Para saber mais sobre o Backup do Azure, confira [Visão geral do backup do Azure](backup-introduction-to-azure-backup.md)
- Visite o [Fórum de backup do Azure](http://go.microsoft.com/fwlink/p/?LinkId=290933).

<!---HONumber=Oct15_HO3-->