<properties
	pageTitle="Copiar um banco de dados SQL do Azure | Microsoft Azure"
	description="Criar uma cópia de um Banco de Dados SQL do Azure"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="06/16/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>



# Copiar um Banco de Dados SQL do Azure

> [AZURE.SELECTOR]
- [Visão geral](sql-database-copy.md)
- [Portal do Azure](sql-database-copy-portal.md)
- [PowerShell](sql-database-copy-powershell.md)
- [T-SQL](sql-database-copy-transact-sql.md)

Você pode usar os [backups automatizados do Banco de Dados SQL](sql-database-automated-backups.md) do Azure para criar uma cópia do seu banco de dados SQL. A operação de cópia copia a parte final do log de transações e usa os backups do log de transações completos e diferenciais que fazem parte dos backups automatizados para criar o que é transacionalmente consistente com o banco de dados de origem a partir da hora do backup final do log de transações.

Você pode criar a cópia do banco de dados no mesmo servidor ou em outro servidor. O desempenho e a camada de nível de serviço (faixa de preço) da cópia do banco de dados são iguais aos do banco de dados de origem. Após a conclusão da cópia, a cópia se tornará um banco de dados independente e totalmente funcional. Os logons, os usuários e as permissões podem ser gerenciados independentemente.

Quando você copia um banco de dados no mesmo servidor lógico, os mesmos logons podem ser usados em ambos os bancos de dados. A entidade de segurança usada para copiar o banco de dados se tornará o proprietário do banco de dados (DBO) do banco de dados. Todos os usuários do banco de dados, suas permissões e seus identificadores de segurança (SIDs) são copiados para a cópia do banco de dados.

Quando você copia um banco de dados para um servidor lógico diferente, a entidade de segurança no novo servidor torna-se a proprietária do banco de dados no novo banco de dados. Os usuários do banco de dados que são usuários independentes podem ser usados no banco de dados copiado. No entanto, quando você copia o banco de dados para um novo servidor, os usuários que se baseiam em logons não funcionarão porque os logons não existirão no novo servidor, e se existirem, seus SIDs podem não corresponder. Depois que o novo banco de dados estiver online no servidor de destino, use a instrução [ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx) para remapear os usuários do novo banco de dados para logons no servidor de destino. Para resolver usuários órfãos, confira [Solução de problemas de usuários órfãos](https://msdn.microsoft.com/library/ms175475.aspx).


Para copiar um banco de dados SQL, você precisará de:

- Uma assinatura do Azure. Se você precisar de uma assinatura do Azure basta clicar em **AVALIAÇÃO GRATUITA** na parte superior desta página e, em seguida, voltar para concluir este artigo.
- Um banco de dados SQL para copiar. Se você não tiver um banco de dados SQL, crie um executando as etapas neste artigo: [Criar seu primeiro Banco de Dados SQL do Azure](sql-database-get-started.md).

## Próximas etapas

- Confira [Copy an Azure SQL database using the Azure Portal (Copiar um banco de dados SQL do Azure usando o Portal do Azure)](sql-database-copy-portal.md) para copiar um banco de dados usando o Portal do Azure.
- Confira [Copiar um Banco de Dados SQL do Azure usando o PowerShell](sql-database-copy-powershell.md) para copiar um banco de dados usando o PowerShell.
- Confira [Copiar um banco de dados SQL do Azure usando o Transact-SQL](sql-database-copy-transact-sql.md) para copiar um banco de dados usando o Transact-SQL.
- Confira [Como gerenciar a segurança do banco de dados SQL do Azure após a recuperação de desastre](sql-database-geo-replication-security-config.md) para saber mais sobre como gerenciar logons e usuários ao copiar um banco de dados para um servidor lógico diferente.



## Recursos adicionais

- [Gerenciar logons](sql-database-manage-logins.md)
- [Conectar-se ao Banco de Dados SQL com o SQL Server Management Studio e executar um exemplo de consulta T-SQL](sql-database-connect-query-ssms.md)
- [Exportar o banco de dados para um BACPAC](sql-database-export.md)
- [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)
- [Documentação do Banco de Dados SQL](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0622_2016-->