<properties
	pageTitle="Copiar um banco de dados SQL do Azure usando o Portal do Azure | Microsoft Azure"
	description="Criar uma cópia de um Banco de Dados SQL do Azure"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="09/19/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>



# Copiar um Banco de Dados SQL do Azure usando o Portal do Azure

> [AZURE.SELECTOR]
- [Visão geral](sql-database-copy.md)
- [Portal do Azure](sql-database-copy-portal.md)
- [PowerShell](sql-database-copy-powershell.md)
- [T-SQL](sql-database-copy-transact-sql.md)

As etapas a seguir mostram como copiar um banco de dados SQL com o [portal do Azure](https://portal.azure.com) para o mesmo ou outro servidor.

Para copiar um banco de dados SQL, você precisa dos seguintes itens:

- Uma assinatura do Azure. Caso você precise de uma assinatura do Azure, basta clicar em **AVALIAÇÃO GRATUITA** na parte superior desta página e, em seguida, voltar para concluir este artigo.
- Um banco de dados SQL para copiar. Se você não tiver um banco de dados SQL, crie um seguindo as etapas neste artigo: [Criar seu primeiro Banco de Dados SQL do Azure](sql-database-get-started.md).


## Copiar seu banco de dados SQL

Abra a página do banco de dados SQL do banco de dados que você deseja copiar:

1.	Vá para o [Portal do Azure](https://portal.azure.com).
2.	Clique em **Mais serviços** > **Bancos de dados SQL** e clique no banco de dados desejado.
3.	Na página do banco de dados SQL, clique em **Copiar**:

    ![Banco de Dados SQL](./media/sql-database-copy-portal/sql-database-copy.png)

1.  Na página **Copiar**, é fornecido um nome de banco de dados padrão. Digite um nome diferente se desejar (todos os bancos de dados em um servidor devem ter nomes exclusivos).
2.  Selecione um **Servidor de destino**. A cópia do banco de dados é criada no servidor de destino. Você pode copiar o banco de dados no mesmo servidor ou em outro servidor. Você pode criar um servidor ou escolher um servidor existente na lista.
3.  Depois de escolher o **Servidor de destino**, o **Pool de banco de dados elástico** e o **Tipo de preço** as opções serão habilitadas. Se o servidor tiver um pool, você poderá copiar o banco de dados nele.
3.  Clique em **OK** para iniciar o processo de cópia.

    ![Banco de Dados SQL](./media/sql-database-copy-portal/copy-page.png)


## Monitorar o andamento da operação de cópia

- Depois de iniciar a cópia, clique na notificação de portal para obter detalhes.

    ![notificação][3]
 
    ![monitor][4]


## Verificar se o banco de dados reside no servidor

- Clique em **Mais serviços** > **Bancos de dados SQL** e verifique se o novo banco de dados está **Online**.


## Resolver logons

Para resolver logons após a conclusão da operação de cópia, confira [Resolver logons](sql-database-copy-transact-sql.md#resolve-logins-after-the-copy-operation-completes)


## Próximas etapas

- Confira [Copiar um Banco de Dados SQL do Azure](sql-database-copy.md) para ter uma visão geral de como copiar um Banco de Dados SQL do Azure.
- Confira [Copiar um Banco de Dados SQL do Azure usando o PowerShell](sql-database-copy-powershell.md) para copiar um banco de dados usando o PowerShell.
- Confira [Copiar um banco de dados SQL do Azure usando o Transact-SQL](sql-database-copy-transact-sql.md) para copiar um banco de dados usando o Transact-SQL.
- Confira [Como gerenciar a segurança do banco de dados SQL do Azure após a recuperação de desastre](sql-database-geo-replication-security-config.md) para saber mais sobre como gerenciar logons e usuários ao copiar um banco de dados para um servidor lógico diferente.



## Recursos adicionais

- [Gerenciar logons](sql-database-manage-logins.md)
- [Conectar-se ao Banco de Dados SQL com o SQL Server Management Studio e executar um exemplo de consulta T-SQL](sql-database-connect-query-ssms.md)
- [Exportar o banco de dados para um BACPAC](sql-database-export.md)
- [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)
- [Documentação do banco de dados SQL](https://azure.microsoft.com/documentation/services/sql-database/)




<!--Image references-->
[1]: ./media/sql-database-copy-portal/copy.png
[2]: ./media/sql-database-copy-portal/copy-ok.png
[3]: ./media/sql-database-copy-portal/copy-notification.png
[4]: ./media/sql-database-copy-portal/monitor-copy.png

<!---HONumber=AcomDC_0921_2016-->