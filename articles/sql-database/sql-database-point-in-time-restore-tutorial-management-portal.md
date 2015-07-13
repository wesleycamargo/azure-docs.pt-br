<properties 
   pageTitle="Restaurar um banco de dados do SQL Azure usando o ponto de restauração pontual no portal do Azure." 
   description="Ponto de restauração pontual, Banco de dados SQL do Microsoft Azure, banco de dados de restauração, banco de dados de recuperação, Portal de Gerenciamento do Azure, portal do Azure" 
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

# Restaurar um banco de dados do SQL Azure usando o ponto de restauração pontual no portal do Azure.

> [AZURE.SELECTOR]
- [Point in Time Restore - PowerShell](sql-database-point-in-time-restore-tutorial-powershell.md)
- [Point in Time Restore - REST API](sql-database-point-in-time-restore-tutorial-rest.md) 

## Visão geral

Este tutorial mostra como restaurar um banco de dados do SQL Azure usando o ponto de restauração pontual no [portal Azure](http://manage.windowsazure.com). Banco de dados do SQL Azure tem backups internos para dar suporte a ponto de autoatendimento na restauração pontual para camadas de serviço Basic, Standard e Premium.

A restauração pontual cria um novo banco de dados. O serviço seleciona automaticamente a camada de serviço com base no backup usado no ponto de restauração. Certifique-se de ter cota disponível no servidor lógico para criar outro banco de dados. Se desejar solicitar uma cota maior, entre em contato com [Suporte do Azure](http://azure.microsoft.com/support/options/).

## Segurança e restrições

* O ponto de restauração pontual está habilitado para as camadas de serviço Basic, Standard e Premium.

* Períodos de retenção de backup são Basic, 7 dias; Standard, 14 dias; e Premium, 35 dias.
 
* Somente o administrador ou coadministrador da assinatura pode enviar uma solicitação de restauração.

* O logon de entidade de nível de servidor será o proprietário do banco de dados restaurado.

* Camadas de serviço da Web e Business Edition não oferecem suporte a ponto de restauração pontual.
 
	* Se você tiver um banco de dados Web ou Business Edition, você pode usar cópia de banco de dados para obter uma cópia transacional consistente do banco de dados e, em seguida, exportar o banco de dados copiado para uma conta de armazenamento do Microsoft Azure. Para obter mais informações, consulte [Como: usar cópia de banco de dados (banco de dados do SQL Azure)](http://msdn.microsoft.com/library/azure/ff951631.aspx) e [Como: usar o serviço de importação e exportação no Banco de Dados SQL do Azure](http://msdn.microsoft.com/library/azure/hh335292.aspx).

	* A Web e Business Editions serão desativadas em setembro de 2015. Para saber mais, confira [Perguntas frequentes sobre a descontinuação das edições Web e Business](http://msdn.microsoft.com/library/azure/dn741330.aspx).

## Como: Restaurar um banco de dados do SQL Azure usando o ponto de restauração pontual no portal do Azure.

> [AZURE.VIDEO restore-a-sql-database-using-point-in-time-restore]

1. Entrar no [portal do Azure](http://manage.windowsazure.com) usando sua conta da Microsoft.

2. No painel de navegação esquerdo, clique em **BANCOS DE DADOS SQL**.
  
3. Na lista de **BANCO DE DADOS**, clique em banco de dados que você deseja restaurar.

4. Na parte inferior da página na barra de comandos, clique em **RESTAURAR**. Isso inicia a caixa de diálogo **Especificar as configurações de restauração**.

5. Escolha um **NOME DO BANCO DE DADOS**. Por padrão, um nome de banco de dados é escolhido para você, mas você pode alterá-lo se desejar.

6. Escolha o ponto em que seu banco de dados deve ser restaurado. O ponto no tempo deve estar dentro do período de retenção do banco de dados.
	
7. Clique na marca de seleção para enviar a solicitação de restauração.

Uma restauração pode levar algum tempo para concluir. Para monitorar o status da restauração, clique no ícone de Status na parte inferior direita da página na barra de comandos e, em seguida, clique em **DETALHES**.

## Próximas etapas

Para obter mais informações, consulte o seguinte:

[Continuidade dos negócios no Banco de dados SQL do Azure](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Restauração e Backup de banco de dados do SQL Azure](http://msdn.microsoft.com/library/azure/jj650016.aspx)

[Ponto de banco de dados do SQL Azure em tempo de restauração (blog)](http://azure.microsoft.com/blog/2014/10/01/azure-sql-database-point-in-time-restore/)

<!---HONumber=62-->