<properties
	pageTitle="Copiar dados do Armazenamento de Blob para o Banco de Dados SQL | Microsoft Azure"
	description="Este tutorial mostra como usar a Atividade de Cópia em um pipeline do Azure Data Factory para copiar dados do Armazenamento de Blobs para um banco de dados SQL."
	Keywords="sql para blob, armazenamento de blobs, cópia de dados"
	services="data-factory"
	documentationCenter=""
	authors="spelluru"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="data-factory"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article" 
	ms.date="08/01/2016"
	ms.author="spelluru"/>

# Copiar dados do Armazenamento de Blobs para o Banco de Dados SQL usando o Data Factory 
> [AZURE.SELECTOR]
- [Visão geral do tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Como usar o Editor do Data Factory](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Usando o PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [Como usar o Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [Usando a API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
- [Como usar a API do .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)
- [Usando o Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md)

Neste tutorial, você cria um data factory com um pipeline para copiar dados do Armazenamento de Blobs para o banco de dados SQL.

A atividade de cópia realiza a movimentação de dados no Azure Data Factory. Ela é habilitada por um serviço disponível globalmente que pode copiar dados entre vários repositórios de dados de forma segura, confiável e escalonável. Veja o artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md) para obter detalhes sobre a Atividade de Cópia.

> [AZURE.NOTE] Para obter uma visão geral detalhada do serviço Data Factory, veja o artigo [Introdução à Azure Data Factory][data-factory-introduction].

##Pré-requisitos para o tutorial
Antes de começar este tutorial, você deve ter o seguinte:

- **Assinatura do Azure**. Se você não tiver uma assinatura, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Consulte o artigo [Avaliação gratuita][azure-free-trial] para obter detalhes.
- **Conta de Armazenamento do Azure**. Você usa o armazenamento de blobs como um armazenamento de dados de **origem** dados neste tutorial. Se você não tiver uma conta de armazenamento do Azure, veja o artigo [Criar uma conta de armazenamento][data-factory-create-storage] para conhecer as etapas para criar um.
- **Banco de dados SQL do Azure**. Você usa um banco de dados SQL do Azure como um armazenamento de dados de **destino** neste tutorial. Se você não tiver um banco de dados do SQL Azure que você possa usar no tutorial, consulte [Como criar e configurar um banco de dados SQL do Azure][data-factory-create-sql-database] para criar um.
- **SQL Server 2012/2014 ou Visual Studio 2013**. Você usa SQL Server Management Studio ou Visual Studio para criar um banco de dados de exemplo e exibir os dados de resultado no banco de dados.

## Coletar o nome e a chave da conta de armazenamento de blobs 
Você precisa do nome da conta e chave de conta da sua conta de armazenamento do Azure para concluir este tutorial. Anote o **nome da conta** e a **chave de conta** da sua conta de armazenamento do Azure.

1. Faça logon no [Portal do Azure][azure-portal].
2. Clique no hub **PROCURAR** à esquerda e selecione **Contas de armazenamento**.
3. Na folha **Contas de armazenamento**, selecione a **Conta de armazenamento do Azure** que você deseja usar neste tutorial.
4. Selecione o link **Chaves de acesso** em **CONFIGURAÇÕES**.
5.  Clique no botão **copiar** (imagem) ao lado da caixa de texto **Nome da conta de armazenamento** e salve-a/cole-a em algum lugar (por exemplo, em um arquivo de texto).
6. Repita a etapa anterior para copiar ou anote a **chave1**.
7. Feche todas as folhas, clicando em **X**.

## Coletar o servidor SQL, o banco de dados, os nomes de usuário
Você precisa dos nomes do servidor, banco de dados e usuário SQL do Azure para concluir este tutorial. Anote os nomes do **servidor**, **banco de dados** e **usuário** para seu banco de dados SQL do Azure.

1. No **Portal do Azure**, clique em **PROCURAR** à esquerda e selecione **Bancos de dados SQL**.
2. Na **folha Bancos de dados SQL**, clique no **banco de dados** que você deseja utilizar neste tutorial. Anote o **nome do banco de dados**.
3. Em seguida, na folha do **BANCO DE DADOS SQL**, clique no bloco **PROPRIEDADES**.
4. Anote os valores para **NOME DO SERVIDOR** e **LOGON DE ADMIN DO SERVIDOR**.
5. Feche todas as folhas, clicando em **X**.

## Permitir que os serviços do Azure acessem o servidor 
Certifique-se de a configuração **Permitir acesso aos serviços do Azure** esteja definida como **ATIVADA** para o servidor do SQL Azure para que o serviço Data Factory possa acessar seu SQL Server do Azure. Para verificar e ativar essa configuração, faça o seguinte:

1. Clique no hub **PROCURAR** à esquerda e clique em **SQL Servers**.
2. Selecione **seu servidor**, e clique em **CONFIGURAÇÕES** sobre a folha **SQL SERVER**.
3. Na folha **CONFIGURAÇÕES**, clique em **Firewall**.
4. Na folha **Configurações de Firewall**, clique em **ATIVADO** para **Permitir acesso aos serviços do Azure**.
5. Feche todas as folhas, clicando em **X**.

## Preparar o Armazenamento de Blobs e o banco de dados SQL 
Agora, prepare seu armazenamento de blob do Azure e o banco de dados SQL do Azure para o tutorial, executando as seguintes etapas:

1. Inicie o bloco de notas, cole o texto a seguir e salve-o como **emp.txt** na pasta **C:\\ADFGetStarted** no disco rígido.

        John, Doe
		Jane, Doe

2. Use ferramentas como o [Gerenciador de Armazenamento do Azure](https://azurestorageexplorer.codeplex.com/) para criar o contêiner **adftutorial** e carregar o arquivo **emp.txt** no contêiner.

    ![Gerenciador de Armazenamento do Azure. Copiar dados do Armazenamento de Blobs para o Banco de Dados SQL usando o Data Factory](./media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/getstarted-storage-explorer.png)
3. Utilize o script SQL a seguir para criar a tabela **emp** no seu Banco de Dados SQL do Azure.


        CREATE TABLE dbo.emp
		(
			ID int IDENTITY(1,1) NOT NULL,
			FirstName varchar(50),
			LastName varchar(50),
		)
		GO

		CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);

	**Se você tiver o SQL Server 2012/2014 instalado no computador:** siga as instruções da [Etapa 2: conectar-se ao banco de dados SQL do artigo Gerenciando o Banco de Dados SQL do Azure usando o SQL Server Management Studio][sql-management-studio] para se conectar ao seu SQL server do Azure e executar o script SQL. Este artigo usa o [Portal do Azure](http://manage.windowsazure.com), e não o [Portal do Azure](https://portal.azure.com), para configurar o firewall para um SQL server do Azure.

	Se o cliente não tiver permissão para acessar o servidor SQL do Azure, você precisará configurar o firewall para o servidor SQL do Azure permitir o acesso no seu computador (endereço IP). Consulte [este artigo](../sql-database/sql-database-configure-firewall-settings.md) para obter as etapas para configurar o firewall para o SQL Server do Azure.


Faça o seguinte:

- Clique no link [Usando o Editor Data Factory](data-factory-copy-activity-tutorial-using-azure-portal.md) na parte superior para executar o tutorial usando o Editor Data Factory, que faz parte do Portal do Azure.
- Clique no link [Usando o PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) na parte superior para executar o tutorial usando o PowerShell do Azure.
- Clique no link [Usando o Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) link na parte superior para executar o tutorial usando o Visual Studio 2013.

## A Atividade de Cópia
Confira o artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md) para obter informações detalhadas sobre a Atividade de Cópia no Azure Data Factory.


<!--Link references-->
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-portal]: https://portal.azure.com/
[sql-management-studio]: http://azure.microsoft.com/documentation/articles/sql-database-manage-azure-ssms/#Step2

[data-factory-introduction]: data-factory-introduction.md
[data-factory-create-storage]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account
[data-factory-create-sql-database]: ../sql-database/sql-database-get-started.md

<!---HONumber=AcomDC_0824_2016-->