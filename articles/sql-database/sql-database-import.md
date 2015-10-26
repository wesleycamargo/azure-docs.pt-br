<properties
	pageTitle="Importar um arquivo BACPAC para criar um novo banco de dados SQL do Azure"
	description="Crie um novo banco de dados SQL do Azure importando um arquivo BACPAC existente."
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="10/13/2015"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Importar um arquivo BACPAC para criar um novo banco de dados SQL do Azure


**Banco de dados individual**

> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)

Este artigo fornece instruções para criar um novo banco de dados SQL do Azure por meio de um arquivo BACPAC usando o [portal de visualização do Azure](https://portal.azure.com).

Um BACPAC é um arquivo .bacpac que contém um esquema de banco de dados e dados. Para obter detalhes, consulte Pacote de backup (.bacpac) em [Aplicativos de camada de dados](https://msdn.microsoft.com/library/ee210546.aspx).

O banco de dados é criado de um BACPAC importado de um contêiner de blob de armazenamento do Azure. Se não tiver um arquivo .bacpac no armazenamento do Azure, você poderá criar um seguindo as etapas descritas em [Criar e exportar um BACPAC de um Banco de Dados SQL do Azure](sql-database-export.md).


> [AZURE.NOTE]O Banco de Dados SQL do Azure cria e mantém backups automaticamente de cada banco de dados de usuário que você pode restaurar. Para obter detalhes, consulte [Visão geral da continuidade dos negócios](sql-database-business-continuity.md).


Para importar um banco de dados SQL de um .bacpac, você precisará de:

- Uma assinatura do Azure. Se você precisar de uma assinatura do Azure basta clicar em **AVALIAÇÃO GRATUITA** na parte superior desta página e, em seguida, voltar para concluir este artigo.
- Um servidor do Banco de Dados SQL V12 do Azure. Se você não tiver um servidor V12, crie um seguindo as etapas neste artigo: [Criar seu primeiro Banco de Dados SQL do Azure](sql-database-get-started.md).
- Um arquivo .bacpac do banco de dados que você deseja importar em um contêiner de blob da [conta de armazenamento do Azure (clássico)](storage-create-storage-account.md).


## Selecione o servidor que conterá o banco de dados

Abra a folha SQL Server do banco de dados que você deseja importar:

1.	Vá para o [Portal de Visualização do Azure](https//:portal.azure.com).
2.	Clique em **PROCURAR TUDO**.
3.	Clique em **Servidores SQL**.
2.	Clique no servidor no qual restaurar o banco de dados.
3.	Na folha SQL Server, clique em **Importar banco de dados** para abrir a folha **Importar banco de dados**:

    ![importar banco de dados][1]

1.  Clique em **Armazenamento**, selecione sua conta de armazenamento, o contêiner de blob, o arquivo .bacpac e clique em **OK**.

    ![configurar opções de armazenamento][2]

1.  Selecione a camada de preços para o novo banco de dados e clique em **Selecionar**.

    ![selecionar camada de preços][3]

1.  Insira um **NOME DE BANCO DE DADOS**.
2.  Insira o **logon** e a **senha do administrador do servidor** do Azure SQL Server para o qual você está importando o banco de dados.
1.  Clique em **Criar** para criar o banco de dados do BACPAC.

    ![criar banco de dados][4]

Clicar em **Criar** envia para o serviço uma solicitação para importar o banco de dados. Dependendo do tamanho do banco de dados, a operação de importação pode demorar a ser concluída.

## Monitorar o progresso da operação de importação

2.	Clique em **PROCURAR TUDO**.
3.	Clique em **Servidores SQL**.
2.	Clique no servidor para o qual está restaurando.
3.	Na folha SQL Server, clique em **Histórico de importação/exportação**:

    ![histórico de importação exportação][5] ![histórico de importação exportação][6]





## Verificar se o banco de dados reside no servidor

2.	Clique em **PROCURAR TUDO**.
3.	Clique em **Bancos de dados SQL** e verifique se o novo banco de dados está **Online**.



## Próximas etapas

- [Conectar-se ao SQL Server Management Studio (SSMS)](sql-database-connect-to-database.md)



## Recursos adicionais

- [Documentação do Banco de Dados SQL](https://azure.microsoft.com/documentation/services/sql-database/)


<!--Image references-->
[1]: ./media/sql-database-import/import-database.png
[2]: ./media/sql-database-import/storage-options.png
[3]: ./media/sql-database-import/pricing-tier.png
[4]: ./media/sql-database-import/create.png
[5]: ./media/sql-database-import/import-history.png
[6]: ./media/sql-database-import/import-status.png

<!---HONumber=Oct15_HO3-->