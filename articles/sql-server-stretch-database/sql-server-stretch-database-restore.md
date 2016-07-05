<properties
	pageTitle="Restaurar bancos de dados habilitados para Stretch | Microsoft Azure"
	description="Saiba como restaurar os bancos de dados habilitados para Stretch."
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglaslMS"
	manager=""
	editor=""/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/14/2016"
	ms.author="douglasl"/>

# Restaurar o backup de bancos de dados habilitados para o Stretch

Restaure um banco de dados que passou por backup, quando necessário, para recuperação de muitos tipos de desastres, erros e falhas.

Para obter mais informações sobre o backup, consulte [Fazer backup de bancos de dados habilitados para o Stretch](sql-server-stretch-database-backup.md).

>   [AZURE.NOTE] O backup é apenas uma parte de uma solução de continuidade de negócios de alta disponibilidade. Para obter mais informações sobre a alta disponibilidade, consulte [Soluções de alta disponibilidade](https://msdn.microsoft.com/library/ms190202.aspx).

## Restaurar os dados do SQL Server
Para recuperar-se de falhas de hardware ou corrupção, restaure de um backup o banco de dados do SQL Server habilitado para Stretch. Você pode continuar a usar os métodos de restauração do SQL Server que você utiliza atualmente. Para obter mais informações, consulte [Visão geral de recuperação e restauração](https://msdn.microsoft.com/library/ms191253.aspx).

Depois de restaurar o banco de dados do SQL Server, você precisa executar o procedimento armazenado **sys.sp\_rda\_reauthorize\_db** para restabelecer a conexão entre o banco de dados do SQL Server habilitado para o Stretch e o banco de dados remoto do Azure. Para obter mais informações, consulte [Restaurar a conexão entre o banco de dados do SQL Server e banco de dados remoto do Azure](#Restore-the-connection-between-the-SQL-Server-database-and-the-remote-Azure-database).

## Restaurar os dados remotos do Azure

### Recuperar um banco de dados dinâmico do Azure
O serviço SQL Server Stretch Database no Azure faz cópias instantâneas de todos os dados dinâmicos pelo menos a cada 8 horas usando Instantâneos de Armazenamento do Azure. Esses instantâneos são mantidos por 7 dias. Isso permite restaurar os dados para pelo menos 21 pontos no tempo nos últimos 7 dias até o momento em que o último instantâneo foi capturado.

Para restaurar um banco de dados do Azure ao vivo para um ponto anterior no tempo por meio do portal do Azure, faça o descrito a seguir.

1. Faça logon no Portal do Azure.
2. No lado esquerdo da tela, selecione **PROCURAR** e, em seguida, **Bancos de Dados SQL**.
3. Navegue até o banco de dados e selecione-o.
4. Na parte superior da folha do banco de dados, clique em **Restaurar**.
5. Especifique um novo **Nome do banco de dados**, selecione um **Ponto de Restauração** e clique em **Criar**.
6. O processo de restauração do banco de dados começará e poderá ser monitorado usando **NOTIFICAÇÕES**.

### Recuperar um banco de dados do Azure excluído
O serviço SQL Server Stretch Database no Azure captura um instantâneo antes de um banco de dados ser removido e o retém por sete dias. Após isso ocorrer, ele deixa de reter instantâneos do banco de dados dinâmico. Isso permite que você restaure um banco de dados excluído para o ponto quando ele foi excluído.

Para restaurar um banco de dados do Azure excluído para o ponto no tempo em que ele foi excluído por meio do portal do Azure, realize as ações descritas a seguir.

1. Faça logon no Portal do Azure.
2. No lado esquerdo da tela, selecione **PROCURAR** e, em seguida, **Servidores SQL**.
3. Navegue até o servidor e selecione-o.
4. Role para baixo até Operações na folha do servidor e clique no bloco **Bancos de Dados Excluídos**.
5. Selecione o banco de dados excluído que deseja restaurar.
5. Especifique um novo **Nome de banco de dados** e clique em **Criar**.
6. O processo de restauração do banco de dados começará e poderá ser monitorado usando **NOTIFICAÇÕES**.

### Recupere um banco de dados do Azure em uma região do Azure diferente  
O serviço SQL Server Stretch Database no Azure copia instantâneos assincronamente para uma região geográfica do Azure diferente para capacidade de recuperação adicionada em caso de falha regional. Se você não conseguir acessar o banco de dados devido a uma falha em uma região do Azure, você poderá restaurar seu banco de dados para um dos instantâneos com redundância geográfica.

>   [AZURE.NOTE] Recuperar o banco de dados do Azure em uma região do Azure diferente exige a alteração da cadeia de conexão em aplicativos cliente após a recuperação, e pode resultar em perda de dados permanente. Faça esse tipo de recuperação somente quando for provável que a interrupção dure um longo tempo.

Para recuperar um banco de dados do Azure ao vivo para um ponto anterior no tempo em uma região do Azure diferente por meio do portal do Azure, faça o descrito a seguir.

1. Faça logon no Portal do Azure.
2. No lado esquerdo da tela, selecione **+NOVO**, selecione **Dados e Armazenamento** e selecione **SQL Data Warehouse**
3. Selecione **BACKUP** como a origem e selecione o backup redundante geograficamente do qual você deseja recuperar
4. Especifique o restante das propriedades do banco de dados e clique em **Criar**
5. O processo de restauração do banco de dados começará e poderá ser monitorado usando **NOTIFICAÇÕES**

Depois de restaurar o banco de dados do Azure em uma região diferente, você precisa executar os procedimentos armazenados **sys.sp\_rda\_deauthorize\_db** e **sys.sp\_rda\_reauthorize\_db** para restabelecer a conexão entre o banco de dados do SQL Server habilitado para o Stretch e o banco de dados remoto do Azure. Para obter mais informações, consulte [Restaurar a conexão entre o banco de dados do SQL Server e banco de dados remoto do Azure](#Restore-the-connection-between-the-SQL-Server-database-and-the-remote-Azure-database).

## Restaurar a conexão entre o banco de dados do SQL Server e banco de dados remoto do Azure

1.  Se você vai se conectar a um banco de dados do Azure restaurado com um nome diferente ou em uma região diferente, execute o procedimento armazenado [sys.sp\_rda\_deauthorize\_db](https://msdn.microsoft.com/library/mt703716.aspx) para desconectar-se do banco de dados do Azure anterior.  

2.  Execute o procedimento armazenado [sys.sp\_rda\_reauthorize\_db](https://msdn.microsoft.com/library/mt131016.aspx) para reconectar o banco de dados habilitado para Stretch local com o banco de dados do Azure.

	-   Forneça as credenciais existentes do banco de dados com escopo como um sysname ou um valor varchar(128). (Não use varchar(max).) Você pode procurar o nome da credencial no modo de exibição **sys.database\_scoped\_credentials**.  

	-   Especifique se deseja fazer uma cópia dos dados remotos e conecte-se à cópia (recomendado).

    ```tsql  
    USE <Stretch-enabled database name>;
	GO
	EXEC sp_rda_reauthorize_db
	    @credential = N'<existing_database_scoped_credential_name>',
		@with_copy = 1 ;  
	GO
	```  

## Consulte também

[Gerenciar e solucionar problemas do Banco de Dados de Stretch](sql-server-stretch-database-manage.md)

[sys.sp\_rda\_reauthorize\_db (Transact-SQL)](https://msdn.microsoft.com/library/mt131016.aspx)

[Backup e restauração de bancos de dados do SQL Server](https://msdn.microsoft.com/library/ms187048.aspx)

<!---HONumber=AcomDC_0622_2016-->