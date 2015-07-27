<properties 
   pageTitle="Restaurar um banco de dados do SQL Azure excluído no portal do Azure" 
   description="Banco de dados SQL do Microsoft Azure, restaurar banco de dados excluído, recuperar banco de dados excluído, Portal de Gerenciamento do Azure, portal do Azure" 
   services="sql-database" 
   documentationCenter="" 
   authors="elfisher" 
   manager="jeffreyg" 
   editor="v-romcal"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="storage-backup-recovery" 
   ms.date="07/15/2015"
   ms.author="elfish; v-romcal"/>

# Restaurar um banco de dados do SQL Azure excluído no portal do Azure

> [AZURE.SELECTOR]
- [Restore deleted database - PowerShell](sql-database-restore-deleted-database-tutorial-powershell.md)
- [Restore deleted database - REST API](sql-database-restore-deleted-database-tutorial-rest.md)

## Visão geral

Este tutorial mostra como restaurar um banco de dados do SQL Azure excluído no [Portal do Azure](http://manage.windowsazure.com). Você pode restaurar um banco de dados que foi excluído durante seu período de retenção para o ponto em que ele foi excluído. O período de retenção é determinado pela camada de serviço do banco de dados.

Restaurar um banco de dados do SQL Azure excluído cria um novo banco de dados. O serviço seleciona automaticamente a camada de serviço com base no backup usado no ponto de restauração. Certifique-se de ter cota disponível no servidor lógico para criar outro banco de dados. Se desejar solicitar uma cota maior, entre em contato com [Suporte do Azure](http://azure.microsoft.com/support/options/).

## Segurança e restrições

* Restaurar um banco de dados do SQL Azure excluído está habilitado para todas as camadas de serviço Basic, Standard e Premium. 

* Períodos de retenção de backup são Basic, 7 dias; Standard, 14 dias; e Premium, 35 dias.

* Somente o administrador ou coadministrador da assinatura pode enviar uma solicitação de restauração.

* O logon de entidade de nível de servidor será o proprietário do banco de dados restaurado.
 
* Camadas de serviço da Web e Business Edition não oferecem suporte a restauração de um banco de dados SQL excluído.
 
	* Se você tiver um banco de dados Web ou Business Edition, você pode usar cópia de banco de dados para obter uma cópia transacional consistente do banco de dados e, em seguida, exportar o banco de dados copiado para uma conta de armazenamento do Microsoft Azure. Para obter mais informações, consulte [Como: usar cópia de banco de dados (banco de dados do SQL Azure)](http://msdn.microsoft.com/library/azure/ff951631.aspx) e [Como: usar o serviço de importação e exportação no Banco de Dados SQL do Azure](http://msdn.microsoft.com/library/azure/hh335292.aspx).
	* A Web e Business Editions serão desativadas em setembro de 2015. Para saber mais, confira [Perguntas frequentes sobre a descontinuação das edições Web e Business](http://msdn.microsoft.com/library/azure/dn741330.aspx).

## Como: Restaurar um banco de dados do SQL Azure excluído no portal do Azure

> [AZURE.VIDEO restore-a-deleted-sql-database]

1. Entrar no [portal do Azure](http://manage.windowsazure.com) usando sua conta da Microsoft.

2. No painel de navegação esquerdo, clique em **BANCOS DE DADOS SQL**.

3. Na parte superior da página, clique em **BANCOS DE DADOS EXCLUÍDOS**. Uma lista de **BANCOS DE DADOS EXCLUÍDOS RESTAURÁVEIS** é mostrado.

4. Clique no banco de dados que deseja restaurar.

6. Na parte inferior da página na barra de comandos, clique em **Restaurar**. Isso inicia a caixa de diálogo **Especificar as configurações de restauração**.

7. Escolha um **NOME DO BANCO DE DADOS**. Por padrão, um nome de banco de dados é escolhido para você, mas você pode alterá-lo se desejar.

	* Observe que um banco de dados excluído pode apenas ser restaurado para o servidor que foi excluído e o ponto no tempo em que ele foi excluído.   

8. Clique na marca de seleção para enviar a solicitação de restauração.

Uma restauração pode levar algum tempo para concluir. Para monitorar o status da restauração, clique no ícone de Status na parte inferior direita da página na barra de comandos e, em seguida, clique em **DETALHES**.

## Próximas etapas

Para obter mais informações, consulte o seguinte:

[Continuidade dos negócios no Banco de dados SQL do Azure](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Restauração e Backup de banco de dados do SQL Azure](http://msdn.microsoft.com/library/azure/jj650016.aspx)

<!---HONumber=July15_HO3-->