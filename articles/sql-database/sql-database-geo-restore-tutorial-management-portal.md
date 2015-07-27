<properties 
   pageTitle="Recuperar um banco de dados do SQL Azure usando a restauração geográfica no portal do Azure." 
   description="Restauração geográfica, Banco de dados SQL do Microsoft Azure, banco de dados de restauração, banco de dados de recuperação, Portal de Gerenciamento do Azure, portal do Azure" 
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
   ms.date="03/18/2015"
   ms.author="elfish; v-romcal"/>

# Recuperar um banco de dados do SQL Azure usando a restauração geográfica no portal do Azure.

> [AZURE.SELECTOR]
- [Geo-Restore - PowerShell](sql-database-geo-restore-tutorial-powershell.md)
- [Geo-Restore - REST API](sql-database-geo-restore-tutorial-rest.md)   

## Visão geral

Este tutorial mostra como recuperar um banco de dados do SQL Azure usando a restauração geográfica no [portal Azure](http://manage.windowsazure.com). A restauração geográfica é a proteção de recuperação de desastres do núcleo incluída para camadas de serviço do banco de dados do SQL Azure Basic, Standard e Premium.

## Segurança e restrições

* A restauração geográfica está habilitada para todas as camadas de serviço Basic, Standard e Premium.

* Períodos de retenção de backup são Basic, 7 dias; Standard, 14 dias; e Premium, 35 dias.

* Somente o administrador ou coadministrador da assinatura pode enviar uma solicitação de restauração.

* O logon de entidade de nível de servidor será o proprietário do banco de dados restaurado.

* As camadas de serviço da Web e Business Edition não oferecem suporte a restauração geográfica.
 
	* Se você tiver um banco de dados Web ou Business Edition, você pode usar cópia de banco de dados para obter uma cópia transacional consistente do banco de dados e, em seguida, exportar o banco de dados copiado para uma conta de armazenamento do Microsoft Azure. Para obter mais informações, consulte [Como: usar cópia de banco de dados (banco de dados do SQL Azure)](http://msdn.microsoft.com/library/azure/ff951631.aspx) e [Como: usar o serviço de importação e exportação no Banco de Dados SQL do Azure](http://msdn.microsoft.com/library/azure/hh335292.aspx).

	* A Web e Business Editions serão desativadas em setembro de 2015. Para saber mais, confira [Perguntas frequentes sobre a descontinuação das edições Web e Business](http://msdn.microsoft.com/library/azure/dn741330.aspx).

## Como: Recuperar um banco de dados do SQL Azure usando a restauração geográfica no portal do Azure.

> [AZURE.VIDEO restore-a-sql-database-using-geo-restore]

1. Entre no [portal do Azure](http://manage.windowsazure.com) usando sua conta da Microsoft e selecione **bancos de dados SQL**.

2. No painel de navegação esquerdo, clique em **BANCOS DE DADOS SQL**.

3. Na parte superior da página, clique em **SERVIDORES**.

4. Na lista de **SERVIDORES** , clique no servidor **Degradado**.

4. Na parte superior da página, clique em **BACKUPS**.

5. Clique no banco de dados que deseja restaurar.

6. Na parte inferior da página na barra de comandos, clique em **Restaurar**. Isso inicia a caixa de diálogo **Especificar as configurações de restauração**.

7. Escolha o **NOME DO BANCO DE DADOS** e o **SERVIDOR DE DESTINO** que você deseja restaurar o banco de dados. Por padrão, um nome de banco de dados é escolhido para você, mas você pode alterá-lo se desejar.

9. Clique na marca de seleção para enviar a solicitação de restauração.

Uma restauração pode levar algum tempo para concluir. Para monitorar o status da restauração, clique no ícone de Status na parte inferior direita da página na barra de comandos e, em seguida, clique em **DETALHES**.

## Próximas etapas

Para obter mais informações, consulte o seguinte:

[Restaurar um banco de dados do SQL Azure usando o ponto de restauração pontual no portal do Azure](sql-database-point-in-time-restore-tutorial-management-portal.md)

[Restaurar um banco de dados do SQL Azure excluído no portal do Azure](sql-database-restore-deleted-database-tutorial-management-portal.md)

[Continuidade dos negócios no Banco de dados SQL do Azure](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Restauração e Backup de banco de dados do SQL Azure](http://msdn.microsoft.com/library/azure/jj650016.aspx)

[Restauração geográfica do banco de dados do SQL Azure (blog)](http://azure.microsoft.com/blog/2014/09/13/azure-sql-database-geo-restore/)

<!---HONumber=July15_HO3-->