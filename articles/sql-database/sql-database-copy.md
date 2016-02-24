<properties
	pageTitle="Copiar um banco de dados SQL do Azure | Microsoft Azure"
	description="Criar uma cópia de um Banco de Dados SQL do Azure"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="01/20/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>



# Copiar um Banco de Dados SQL do Azure

**Banco de dados individual**

> [AZURE.SELECTOR]
- [Azure Portal](sql-database-copy.md)
- [PowerShell](sql-database-copy-powershell.md)
- [SQL](sql-database-copy-transact-sql.md)

As etapas a seguir mostram como copiar um banco de dados SQL com o [portal do Azure](https://portal.azure.com). A operação de cópia do banco de dados cria um novo banco de dados SQL. A cópia é um backup de instantâneo do banco de dados que você cria no mesmo servidor ou em um servidor diferente.

> [AZURE.NOTE] O Banco de Dados SQL do Azure cria e mantém backups automaticamente de cada banco de dados de usuário que você pode restaurar. Para obter detalhes, consulte [Visão geral da continuidade dos negócios](sql-database-business-continuity.md).

Quando o processo de cópia for concluído, o novo banco de dados será um banco de dados totalmente funcional independente do banco de dados de origem. O novo banco de dados é transacionalmente consistente com o banco de dados de origem no momento da conclusão da cópia. O desempenho e a camada de nível de serviço (faixa de preço) da cópia do banco de dados são iguais aos do banco de dados de origem. Após a conclusão da cópia, a cópia se tornará um banco de dados independente e totalmente funcional. Os logons, os usuários e as permissões podem ser gerenciados independentemente.


Quando você copia um banco de dados no mesmo servidor lógico, os mesmos logons podem ser usados em ambos os bancos de dados. A entidade de segurança usada para copiar o banco de dados se tornará o proprietário do banco de dados (DBO) do banco de dados. Todos os usuários do banco de dados, suas permissões e seus identificadores de segurança (SIDs) são copiados para a cópia do banco de dados.


Para copiar um banco de dados SQL, você precisará de:

- Uma assinatura do Azure. Se você precisar de uma assinatura do Azure basta clicar em **AVALIAÇÃO GRATUITA** na parte superior desta página e, em seguida, voltar para concluir este artigo.
- Um banco de dados SQL para copiar. Se você não tiver um banco de dados SQL, crie um seguindo as etapas neste artigo: [Criar seu primeiro Banco de Dados SQL do Azure](sql-database-get-started.md).



## Copiar seu banco de dados SQL

Abra a folha do banco de dados SQL do banco de dados que você deseja copiar:

1.	Vá para o [Portal do Azure](https://portal.azure.com).
2.	Vá para o banco de dados que você deseja copiar: Procurar > Bancos de dados SQL
3.	Na lâmina do banco de dados SQL, clique em **Copiar** para abrir a folha **Copiar**:

    ![copiar banco de dados][1]

1.  Insira um nome para a cópia do banco de dados. Um nome padrão será fornecido, mas você poderá alterá-lo se quiser.
2.  Selecione um **Servidor de destino**. O servidor de destino é onde a cópia do banco de dados será criada. Você pode criar um novo servidor ou selecione um servidor existente na lista.
3.  Clique em **OK** para iniciar o processo de cópia.

    ![nome e servidor de banco de dados][2]




## Monitorar o andamento da operação de cópia

2.	Depois de iniciar a cópia, clique na notificação de portal para obter detalhes.


    ![notificação][3]

 
    ![monitor][4]





## Verificar se o banco de dados reside no servidor

- Clique em **PROCURAR** > **Bancos de dados SQL** e verifique se o novo banco de dados está **Online**.



## Próximas etapas

- [Conectar-se ao Banco de Dados SQL com o SQL Server Management Studio e executar um exemplo de consulta T-SQL](sql-database-connect-query-ssms.md)
- [Exportar o banco de dados para um BACPAC](sql-database-export.md)



## Recursos adicionais

- [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)
- [Documentação do banco de dados SQL](https://azure.microsoft.com/documentation/services/sql-database/)


<!--Image references-->
[1]: ./media/sql-database-copy/copy.png
[2]: ./media/sql-database-copy/copy-ok.png
[3]: ./media/sql-database-copy/copy-notification.png
[4]: ./media/sql-database-copy/monitor-copy.png

<!---HONumber=AcomDC_0211_2016-->