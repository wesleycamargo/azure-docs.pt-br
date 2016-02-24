<properties
	pageTitle="Tutorial: Usar a Atividade de Cópia em um Pipeline do Azure Data Factory"
	description="Este tutorial mostra como usar a Atividade de Cópia em um pipeline do Azure Data Factory para copiar dados de um blob do Azure para um banco de dados do Azure SQL."
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
	ms.date="02/01/2016"
	ms.author="spelluru"/>

# Tutorial: Copiar dados de um blob do Azure para o SQL do Azure
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-get-started.md)
- [Using Data Factory Editor](data-factory-get-started-using-editor.md)
- [Using PowerShell](data-factory-monitor-manage-using-powershell.md)
- [Using Visual Studio](data-factory-get-started-using-vs.md)

O tutorial neste artigo o ajudará a começar rapidamente a usar o serviço Azure Data Factory. Neste tutorial, você criará uma Azure Data Factory e como criar um pipeline na fábrica de dados para copiar dados de um armazenamento de blobs do Azure para um banco de dados SQL do Azure.

> [AZURE.NOTE] Para obter uma visão geral detalhada do serviço Data Factory, consulte o artigo [Introdução à Azure Data Factory][data-factory-introduction].

##Pré-requisitos para o tutorial
Antes de começar este tutorial, você deve ter o seguinte:

- **Assinatura do Azure**. Se você não tiver uma assinatura, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Consulte o artigo [Avaliação gratuita][azure-free-trial] para obter detalhes.
- **Conta de Armazenamento do Azure**. Você usará o armazenamento de blob como um armazenamento de dados de **origem** dados neste tutorial. Se você não tiver uma conta de armazenamento do Azure, consulte o artigo [Criar uma conta de armazenamento][data-factory-create-storage] para conhecer as etapas para criar um.
- **Banco de dados SQL do Azure**. Você usará um banco de dados SQL do Azure como um armazenamento de dados de **destino** neste tutorial. Se você não tiver um banco de dados do SQL Azure que você possa usar no tutorial, consulte [Como criar e configurar um banco de dados SQL do Azure][data-factory-create-sql-database] para criar um.
- **SQL Server 2012/2014 ou Visual Studio 2013**. Você usará SQL Server Management Studio ou Visual Studio para criar um banco de dados de exemplo e exibir os dados de resultado no banco de dados.  

### Colete o nome da conta e chave de conta da sua Conta de Armazenamento do Azure
Você precisará do nome da conta e chave de conta da sua conta de armazenamento do Azure para concluir este tutorial. Anote o **nome da conta** e **chave de conta** para sua conta de armazenamento do Azure, seguindo as instruções abaixo:

1. Faça logon no [Portal do Azure][azure-portal].
2. Clique no hub **PROCURAR** à esquerda e selecione **Contas de armazenamento**.
3. Na folha **Contas de armazenamento**, selecione a **Conta de armazenamento do Azure** que você deseja usar neste tutorial.
4. Na folha **ARMAZENAMENTO**, clique no bloco **CHAVES**.
5. Na folha **Gerenciar chaves**, clique no botão **cópia** (imagem) ao lado da caixa de texto **NOME DA CONTA DE ARMAZENAMENTO** e salve-o/cole-o em algum lugar (por exemplo, em um arquivo de texto).  
6. Repita a etapa anterior para copiar ou anote a **CHAVE DE ACESSO PRIMÁRIA**.
7. Feche todas as folhas, clicando em **X**.

### Colete o nome do servidor, nome do banco de dados e conta de usuário para seu banco de dados SQL do Azure
Você precisará dos nomes do servidor, banco de dados e usuário SQL do Azure para concluir este tutorial. Anote os nomes do **servidor**, **banco de dados** e **usuário** para seu banco de dados SQL do Azure, seguindo as instruções abaixo:

1. No **Portal do Azure**, clique em **PROCURAR** à esquerda e selecione **Bancos de dados SQL**.
2. Na **folha Bancos de dados SQL**, clique no **banco de dados** que você deseja utilizar neste tutorial. Anote o **nome do banco de dados**.  
3. Em seguida, na folha do **BANCO DE DADOS SQL**, clique no bloco **PROPRIEDADES**.
4. Anote os valores para **NOME DO SERVIDOR** e **LOGON DE ADMIN DO SERVIDOR**.
5. Feche todas as folhas, clicando em **X**.

### Permitir que os serviços do Azure acessem o SQL Server do Azure
Certifique-se de a configuração **Permitir acesso aos serviços do Azure** esteja definida como **ATIVADA** para o servidor do SQL Azure para que o serviço Data Factory possa acessar seu SQL Server do Azure. Para verificar e ativar essa configuração, faça o seguinte:

1. Clique no hub **PROCURAR** à esquerda e clique em **SQL Servers**.
2. Selecione **seu servidor**, e clique em **CONFIGURAÇÕES** sobre a folha **SQL SERVER**.
3. Na folha **CONFIGURAÇÕES**, clique em **Firewall**.
4. Na folha **Configurações de Firewall**, clique em **ATIVADO** para **Permitir acesso aos serviços do Azure**.
5. Feche todas as folhas, clicando em **X**.

### Preparar o armazenamento de blobs do Azure e o banco de dados SQL do Azure para o tutorial
Agora, prepare seu armazenamento de blob do Azure e o banco de dados SQL do Azure para o tutorial, executando as seguintes etapas:

1. Inicie o bloco de notas, cole o texto a seguir e salve-o como **emp.txt** na pasta **C:\\ADFGetStarted** no disco rígido.

        John, Doe
		Jane, Doe

2. Use ferramentas como o [Gerenciador de Armazenamento do Azure](https://azurestorageexplorer.codeplex.com/) para criar o contêiner **adftutorial** e carregar o arquivo **emp.txt** no contêiner.

    ![Gerenciador de Armazenamento do Azure](./media/data-factory-get-started/getstarted-storage-explorer.png)
3. Utilize o script SQL a seguir para criar a tabela **emp** no seu Banco de Dados SQL do Azure.  


        CREATE TABLE dbo.emp
		(
			ID int IDENTITY(1,1) NOT NULL,
			FirstName varchar(50),
			LastName varchar(50),
		)
		GO

		CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);

	**Se você tiver o SQL Server 2012/2014 instalado no computador:** siga as instruções da [Etapa 2: conectar-se ao banco de dados SQL do artigo Gerenciando o Banco de Dados SQL do Azure usando o SQL Server Management Studio][sql-management-studio] para se conectar ao seu SQL server do Azure e executar o script SQL. Observe que este artigo usa o [Portal Clássico do Azure](http://manage.windowsazure.com), e não o [Portal do Azure](https://portal.azure.com), para configurar o firewall para um servidor SQL do Azure.

	**Se você tiver o Visual Studio 2013 instalado no seu computador:** no [Portal do Azure](https://portal.azure.com), clique no hub **PROCURAR** à esquerda, clique em **Servidores SQL**, selecione seu banco de dados e clique no botão **Abrir no Visual Studio** na barra de ferramentas para se conectar ao servidor SQL do Azure e executar o script. Se o cliente não tiver permissão para acessar o servidor SQL do Azure, você precisará configurar o firewall para o servidor SQL do Azure permitir o acesso no seu computador (endereço IP). Consulte o artigo acima para obter as etapas para configurar o firewall para o SQL Server do Azure.


Faça o seguinte:

- Clique no link [Usando o Data Factory Editor](data-factory-get-started-using-editor.md) na parte superior para executar o tutorial usando o Data Factory Editor, que faz parte do Portal Clássico do Azure.
- Clique no link [Usando o PowerShell](data-factory-monitor-manage-using-powershell.md) na parte superior para executar o tutorial usando o PowerShell do Azure.
- Clique no link [Usando o Visual Studio](data-factory-get-started-using-vs.md) link na parte superior para executar o tutorial usando o Visual Studio 2013.
 


<!--Link references-->
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-portal]: https://portal.azure.com/
[sql-management-studio]: http://azure.microsoft.com/documentation/articles/sql-database-manage-azure-ssms/#Step2

[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[data-factory-introduction]: data-factory-introduction.md
[data-factory-create-storage]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account
[data-factory-create-sql-database]: ../sql-database/sql-database-get-started.md

<!---HONumber=AcomDC_0204_2016-->