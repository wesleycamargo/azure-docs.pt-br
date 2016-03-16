<properties
	pageTitle="Fazer backup e restaurar bancos de dados habilitados para Stretch | Microsoft Azure"
	description="Saiba como fazer backup e restaurar os bancos de dados habilitados para Stretch."
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglasl"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/26/2016"
	ms.author="douglasl"/>


# Fazer backup e restaurar bancos de dados habilitados para Stretch 

Para fazer backup e restaurar os bancos de dados habilitados para Stretch, você pode continuar a usar os métodos utilizados atualmente. Para obter mais informações sobre o backup e a restauração do SQL Server, consulte [Fazer Backup e Restaurar Bancos de Dados do SQL Server](https://msdn.microsoft.com/library/ms187048.aspx).

O backup de um banco de dados habilitado para Stretch é um backup superficial que não inclui os dados migrados para o servidor remoto.

O Banco de Dados de Stretch oferece suporte completo para a restauração pontual. Depois de restaurar o banco de dados do SQL Server para um ponto no tempo e autorizar novamente a conexão com o Azure, o Banco de Dados de Stretch reconciliará os dados remotos com o mesmo ponto no tempo. Para obter mais informações sobre a restauração pontual no SQL Server, consulte [Restaurar um Banco de Dados do SQL Server para um Ponto no Tempo (Modelo de Restauração Completa)](https://msdn.microsoft.com/library/ms179451.aspx). Para obter informações sobre o procedimento armazenado que você precisa executar após uma restauração para autorizar novamente a conexão com o Azure, consulte [sys.sp\_rda\_reauthorize\_db (Transact-SQL)](https://msdn.microsoft.com/library/mt131016.aspx).

## <a name="Reconnect"></a>Restaurar um banco de dados habilitado para Stretch a partir de um backup

1.  Restaure o banco de dados a partir de um backup.

2.  Crie uma chave mestra do banco de dados para o banco de dados habilitado para Stretch. Para obter mais informações, consulte [CRIAR CHAVE MESTRA (Transact-SQL)](https://msdn.microsoft.com/library/ms174382.aspx)

3.  Crie uma credencial com escopo do banco de dados para o banco de dados habilitado para Stretch. Para obter mais informações, consulte [CRIAR CREDENCIAL COM ESCOPO DO BANCO DE DADOS (Transact-SQL)](https://msdn.microsoft.com/library/mt270260.aspx).

4.  Execute o procedimento armazenado [sys.sp\_rda\_reauthorize\_db (Transact-SQL)](https://msdn.microsoft.com/library/mt131016.aspx) para reconectar o banco de dados habilitado para Stretch local com o Azure. Forneça a credencial com escopo do banco de dados criada na etapa anterior como um sysname ou um valor varchar (128). (Não use varchar(max).)

    ```tsql
    Declare @credentialName nvarchar(128);
    SET @credentialName = N’<database_scoped_credential_name_created_previously>’;
    EXEC sp_rda_reauthorize_db @credentialName;
    ```

## <a name="MoreInfo"></a>Mais informações sobre o backup e a restauração
Os backups em um banco de dados com o Banco de Dados de Stretch habilitado contêm apenas os dados locais e os dados qualificados no ponto do tempo quando o backup é executado. Esses backups também contêm informações sobre o ponto de extremidade remoto onde residem os dados remotos do banco de dados. Isso é conhecido como "backup superficial". Os backups profundos que contêm todos os dados contidos no banco de dados, local e remoto, não são compatíveis.

![Diagrama de backup do Banco de Dados de Stretch][StretchBackupImage1]

Quando você restaura um backup de um banco de dados com o Banco de Dados de Stretch habilitado, esta operação restaura os dados locais e os dados qualificados para o banco de dados conforme o esperado. (Dados qualificados são dados que ainda não foram movidos, mas serão movidos para o Azure com base na configuração do Banco de Dados de Stretch das tabelas.) Depois da operação de restauração ser executada, o banco de dados contém os dados locais e qualificados a partir do ponto quando o backup foi feito, mas não tem as credenciais necessárias e os artefatos para se conectar ao ponto de extremidade remoto.

![Banco de Dados de Stretch após backup][StretchBackupImage2]

Você precisa executar o procedimento armazenado **sys.sp\_rda\_reauthorize\_db** para restabelecer a conexão entre o banco de dados local e seu ponto de extremidade remoto. Somente um db\_owner pode executar essa operação. Esse procedimento armazenado também requer o nome de usuário do administrador e a senha do ponto de extremidade remoto. Isso significa que você precisa fornecer o logon de administrador e a senha para o servidor do Azure de destino.

![Banco de Dados de Stretch após backup][StretchBackupImage3]

Depois de restabelecer a conexão, o Banco de Dados de Stretch tenta reconciliar os dados qualificados no banco de dados local com os dados remotos criando uma cópia dos dados remotos no ponto de extremidade remoto e vinculando-os ao banco de dados local. Esse processo é automático e não requer intervenções do usuário. Após essa reconciliação ser executada, o banco de dados local e o ponto de extremidade remoto ficam em um estado consistente.

![Banco de Dados de Stretch após backup][StretchBackupImage4]

## Consulte também
[Gerenciar e solucionar problemas do banco de Dados de Stretch](sql-server-stretch-database-manage.md) [sys.sp\_rda\_reauthorize\_db (Transact-SQL)](https://msdn.microsoft.com/library/mt131016.aspx) [Fazer Backup e Restaurar os Bancos de Dados do SQL Server](https://msdn.microsoft.com/library/ms187048.aspx)

<!--Image references-->
[StretchBackupImage1]: ./media/sql-server-stretch-database-backup/StretchDBBackup1.png
[StretchBackupImage2]: ./media/sql-server-stretch-database-backup/StretchDBBackup2.png
[StretchBackupImage3]: ./media/sql-server-stretch-database-backup/StretchDBBackup3.png
[StretchBackupImage4]: ./media/sql-server-stretch-database-backup/StretchDBBackup4.png

<!---HONumber=AcomDC_0302_2016-->