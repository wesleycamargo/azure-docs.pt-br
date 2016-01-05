<properties 
    pageTitle="Criar uma cópia de um Banco de Dados SQL do Azure usando o Transact-SQL" 
    description="Criar cópia de um Banco de Dados SQL do Azure usando o Transact-SQL" 
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="12/01/2015"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Criar uma cópia de um Banco de Dados SQL do Azure usando o Transact-SQL

**Banco de dados individual**

> [AZURE.SELECTOR]
- [Azure Portal](sql-database-copy.md)
- [PowerShell](sql-database-copy-powershell.md)
- [SQL](sql-database-copy-transact-sql.md)



As etapas a seguir mostram como copiar um banco de dados SQL com o Transact-SQL. A operação de cópia do banco de dados copia um banco de dados SQL para um novo banco de dados usando a instrução [CREATE DATABASE](). A cópia é um backup de instantâneo do banco de dados que você cria no mesmo servidor ou em um servidor diferente.


> [AZURE.NOTE]O Banco de Dados SQL do Azure cria e mantém backups automaticamente de cada banco de dados de usuário que você pode restaurar. Para obter detalhes, consulte [Visão geral da continuidade dos negócios](sql-database-business-continuity.md).


Quando o processo de cópia for concluído, o novo banco de dados será um banco de dados totalmente funcional independente do banco de dados de origem. O novo banco de dados é transacionalmente consistente com o banco de dados de origem no momento da conclusão da cópia. O desempenho e a camada de nível de serviço (faixa de preço) da cópia do banco de dados são iguais aos do banco de dados de origem. Após a conclusão da cópia, a cópia se tornará um banco de dados independente e totalmente funcional. Os logons, os usuários e as permissões podem ser gerenciados independentemente.


Quando você copia um banco de dados no mesmo servidor lógico, os mesmos logons podem ser usados em ambos os bancos de dados. A entidade de segurança usada para copiar o banco de dados se tornará o proprietário do banco de dados (DBO) do banco de dados. Todos os usuários do banco de dados, suas permissões e seus identificadores de segurança (SIDs) são copiados para a cópia do banco de dados.


Para concluir as etapas neste artigo, você precisa do seguinte:

- Uma assinatura do Azure. Se você precisar de uma assinatura do Azure basta clicar em **AVALIAÇÃO GRATUITA** na parte superior desta página e, em seguida, voltar para concluir este artigo.
- Um banco de dados SQL Azure. Se você não tiver um banco de dados SQL, crie um seguindo as etapas neste artigo: [Criar seu primeiro Banco de Dados SQL do Azure](sql-database-get-started.md).
- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms174173.aspx). Se você não tiver o SSMS, ou se os recursos descritos neste artigo não estiverem disponíveis, [Baixe a versão mais recente](https://msdn.microsoft.com/library/mt238290.aspx).




## Copiar seu banco de dados SQL

Faça logon no banco de dados mestre usando o logon principal no nível do servidor ou o logon que criou o banco de dados que você deseja copiar. Os logons que não forem a entidade de segurança de nível de servidor deverão ser membros da função dbmanager para copiar os bancos de dados. Para saber mais sobre os logons e a conexão com o servidor, consulte Gerenciando bancos de dados, Logons e Usuários no Banco de Dados SQL do Azure e Desenvolvimento de Banco de Dados SQL do Azure: tópicos de instruções, respectivamente.

Inicie a cópia do banco de dados de origem com a instrução CREATE DATABASE. A execução dessa instrução inicia o processo de cópia do banco de dados. Como a cópia um banco de dados é um processo assíncrono, a instrução CREATE DATABASE retorna antes da conclusão da cópia do banco de dados.


### Copiar um banco de dados SQL para o mesmo servidor

Faça logon no banco de dados mestre usando o logon principal no nível do servidor ou o logon que criou o banco de dados que você deseja copiar. Os logons que não forem a entidade de segurança de nível de servidor deverão ser membros da função dbmanager para copiar os bancos de dados.

Esse comando copia o Database1 para um novo banco de dados chamado Database2 no mesmo servidor. Dependendo do tamanho do banco de dados, a operação de cópia poderá demorar a ser concluída.

    -- Execute on the master database.
    -- Start copying.
    CREATE DATABASE Database1_copy AS COPY OF Database1;

### Copiar um banco de dados SQL para um servidor diferente

Faça logon no banco de dados mestre do servidor de destino, o servidor do Banco de Dados SQL do Azure, onde o novo banco de dados deve ser criado. Use um logon que tenha o mesmo nome e senha como o proprietário do banco de dados (DBO) do banco de dados de origem no servidor do Banco de Dados SQL do Azure de origem. O logon no servidor de destino também deve ser membro da função dbmanager ou ser o logon principal no nível de servidor.

Esse comando copia o Database1 no server1- para um novo banco de dados chamado Database2 no server2. Dependendo do tamanho do banco de dados, a operação de cópia poderá demorar a ser concluída.


    -- Execute on the master database of the target server (server2)
    -- Start copying from Server1 to Server2
    CREATE DATABASE Database1_copy AS COPY OF server1.Database1;
    

## Monitorar o andamento da operação de cópia

Monitore o processo de cópia consultando as exibições sys.databases e sys.dm\_database\_copies. Enquanto a cópia estiver em andamento, a coluna state\_desc da exibição sys.databases para o novo banco de dados é definida como COPYING.


- Se a cópia falhar, a coluna state\_desc da exibição sys.databases para o novo banco de dados será definida como SUSPECT. Nesse caso, execute a instrução DROP no novo banco de dados e tente novamente mais tarde.
- Se a cópia for bem-sucedida, a coluna state\_desc da exibição sys.databases para o novo banco de dados será definida como ONLINE. Nesse caso, a cópia foi concluída e o novo banco de dados é um banco de dados normal, capaz de ser alterado de forma independente do banco de dados de origem.



## Próximas etapas


- Se você decidir cancelar a cópia enquanto ela estiver em andamento, execute a instrução [DROP DATABASE](https://msdn.microsoft.com/library/ms178613.aspx) no novo banco de dados. Como alternativa, a execução da instrução DROP DATABASE no banco de dados de origem também cancelará o processo de cópia.
- Depois que o novo banco de dados estiver online no servidor de destino, use a instrução [ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx) para remapear os usuários do novo banco de dados para logons no servidor de destino. Todos os usuários no novo banco de dados mantêm as permissões que tinham no banco de dados de origem. O usuário que iniciou a cópia do banco de dados se tornará o proprietário do novo banco de dados e receberá um novo identificador de segurança (SID). Depois que a cópia for bem-sucedida e antes que outros usuários sejam remapeados, somente o logon que tiver iniciado a cópia, o proprietário do banco de dados (DBO), poderá fazer logon no novo banco de dados.




## Recursos adicionais

- [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)
- [Executar análise de recuperação de desastres](sql-database-disaster-recovery-drills.md)
- [Documentação do Banco de Dados SQL](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_1203_2015-->