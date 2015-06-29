<properties
	pageTitle="Introdução ao uso do Azure Data Factory"
	description="Este tutorial mostra como criar um pipeline de dados de exemplo que copia dados de um blob para uma instância do banco de dados SQL do Azure."
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
	ms.topic="hero-article" 
	ms.date="05/04/2015"
	ms.author="spelluru"/>

# Introdução ao Data Factory do Azure
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-get-started.md)
- [Using Data Factory Editor](data-factory-get-started-using-editor.md)
- [Using PowerShell](data-factory-monitor-manage-using-powershell.md)

O tutorial neste artigo o ajudará a começar rapidamente a usar o serviço Azure Data Factory. Neste tutorial, você criará uma Azure Data Factory e como criar um pipeline na fábrica de dados para copiar dados de um armazenamento de blobs do Azure para um banco de dados SQL do Azure.

> [AZURE.NOTE]Para obter uma visão geral detalhada do serviço Data Factory, consulte o artigo [Introdução à Azure Data Factory][data-factory-introduction].

##Pré-requisitos para o tutorial
Antes de começar este tutorial, você deve ter o seguinte:

- **Assinatura do Azure**. Se você não tiver uma assinatura, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Consulte o artigo [Avaliação gratuita][azure-free-trial] para obter detalhes.
- **Conta de Armazenamento do Azure**. Você usará o armazenamento de blob como um armazenamento de dados de **origem** dados neste tutorial. Se você não tiver uma conta de armazenamento do Azure, consulte o artigo [Criar uma conta de armazenamento][data-factory-create-storage] para conhecer as etapas para criar um.
- **Banco de dados SQL do Azure**. Você usará um banco de dados SQL do Azure como um armazenamento de dados de **destino** neste tutorial. Se você não tiver um banco de dados do SQL Azure que você possa usar no tutorial, consulte [Como criar e configurar um banco de dados SQL do Azure][data-factory-create-sql-database] para criar um.
- **SQL Server 2012/2014 ou Visual Studio 2013**. Você usará SQL Server Management Studio ou Visual Studio para criar um banco de dados de exemplo e exibir os dados de resultado no banco de dados.  

### Colete o nome da conta e chave de conta da sua Conta de Armazenamento do Azure
Você precisará do nome da conta e chave de conta da sua conta de armazenamento do Azure para concluir este tutorial. Anote o **nome da conta** e **chave de conta** para sua conta de armazenamento do Azure, seguindo as instruções abaixo:

1. Faça logon no [Portal de Visualização do Azure][azure-preview-portal].
2. Clique no hub **PROCURAR** à esquerda e selecione **Contas de armazenamento**.
3. Na folha **Contas de armazenamento**, selecione a **Conta de armazenamento do Azure** que você deseja usar neste tutorial.
4. Na folha **ARMAZENAMENTO**, clique no bloco **CHAVES**.
5. Na folha **Gerenciar chaves**, clique no botão **cópia** (imagem) ao lado da caixa de texto **NOME DA CONTA DE ARMAZENAMENTO** e salve-o/cole-o em algum lugar (por exemplo, em um arquivo de texto).  
6. Repita a etapa anterior para copiar ou anote a **CHAVE DE ACESSO PRIMÁRIA**.
7. Feche todas as folhas, clicando em **X**.

### Colete o nome do servidor, nome do banco de dados e conta de usuário para seu banco de dados SQL do Azure
Você precisará dos nomes do servidor, banco de dados e usuário SQL do Azure para concluir este tutorial. Anote os nomes do **servidor**, **banco de dados** e **usuário** para seu banco de dados SQL do Azure, seguindo as instruções abaixo:

1. No **Portal de Visualização do Azure**, clique em **Procurar** à esquerda e selecione **Bancos de dados SQL**.
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

1. Inicie o bloco de notas, cole o texto a seguir e salve-o como **emp.txt** na pasta **C:\ADFGetStarted** no disco rígido.

        John, Doe
		Jane, Doe

2. Use ferramentas como o [Gerenciador de Armazenamento do Azure](https://azurestorageexplorer.codeplex.com/) para criar o contêiner **adftutorial** e carregar o arquivo **emp.txt** no contêiner.

    ![Gerenciador de Armazenamento do Azure][image-data-factory-get-started-storage-explorer]
3. Utilize o script SQL a seguir para criar a tabela **emp** no seu Banco de Dados SQL do Azure.  


        CREATE TABLE dbo.emp
		(
			ID int IDENTITY(1,1) NOT NULL,
			FirstName varchar(50),
			LastName varchar(50),
		)
		GO

		CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);

	**Se você tiver o SQL Server 2012/2014 instalado no computador:** siga as instruções da [Etapa 2: conectar-se ao banco de dados SQL do artigo Gerenciando o Banco de Dados SQL do Azure usando o SQL Server Management Studio][sql-management-studio] para se conectar ao seu SQL server do Azure e executar o script SQL. Observe que este artigo usa o portal de gerenciamento de versão (http://manage.windowsazure.com), não o portal de visualização (http://portal.azure.com), para configurar o firewall para um servidor SQL do Azure.

	**Se você tiver o Visual Studio 2013 instalado no computador:** no [Azure Preview Portal](http://portal.azure.com), clique em **Procurar** hub à esquerda, clique **servidores SQL**, selecione o banco de dados e clique no botão **Abrir no Visual Studio** na barra de ferramentas para conectar-se ao seu SQL Server do Azure e executar o script. Se o cliente não tem permissão para acessar o SQL Server do Azure, você precisará configurar o firewall para o SQL Server do Azure permitir o acesso no seu computador (endereço IP). Consulte o artigo acima para obter as etapas para configurar o firewall para o SQL Server do Azure.


Faça o seguinte:

- Clique no link [Usando o Editor Data Factory](data-factory-get-started-using-editor.md) na parte superior para executar o tutorial usando o Editor Data Factory, que faz parte do Portal do Azure.
- Clique no link [Usando o PowerShell](data-factory-monitor-manage-using-powershell.md) na parte superior para executar o tutorial usando o PowerShell do Azure.


<!--Link references-->
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[msdn-activities]: https://msdn.microsoft.com/library/dn834988.aspx
[msdn-linkedservices]: https://msdn.microsoft.com/library/dn834986.aspx
[data-factory-naming-rules]: https://msdn.microsoft.com/library/azure/dn835027.aspx

[azure-preview-portal]: https://portal.azure.com/
[download-azure-powershell]: http://azure.microsoft.com/documentation/articles/install-configure-powershell
[sql-management-studio]: http://azure.microsoft.com/documentation/articles/sql-database-manage-azure-ssms/#Step2
[sql-cmd-exe]: https://msdn.microsoft.com/library/azure/ee336280.aspx

[data-factory-editor]: data-factory-editor.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[copy-activity]: data-factory-copy-activity.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[data-factory-create-storage]: http://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account
[data-factory-create-sql-database]: ../sql-database-create-configure.md


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

<!--Image references-->

[DataSlicesBySliceTime]: ./media/data-factory-get-started/DataSlicesBySliceTime.png

[image-data-factory-getstarted-new-everything]: ./media/data-factory-get-started/GetStarted-New-Everything.png

[image-data-factory-gallery-storagecachebackup]: ./media/data-factory-get-started/getstarted-gallery-datastoragecachebackup.png

[image-data-factory-gallery-storagecachebackup-seeall]: ./media/data-factory-get-started/getstarted-gallery-datastoragecachebackup-seeall.png

[image-data-factory-getstarted-data-services-data-factory-selected]: ./media/data-factory-get-started/getstarted-data-services-data-factory-selected.png

[image-data-factory-getstarted-data-factory-create-button]: ./media/data-factory-get-started/getstarted-data-factory-create-button.png

[image-data-factory-getstarted-new-data-factory-blade]: ./media/data-factory-get-started/getstarted-new-data-factory.png

[image-data-factory-get-stated-factory-home-page]: ./media/data-factory-get-started/getstarted-data-factory-home-page.png

[image-author-deploy-tile]: ./media/data-factory-get-started/getstarted-author-deploy-tile.png

[image-editor-newdatastore-button]: ./media/data-factory-get-started/getstarted-editor-newdatastore-button.png

[image-editor-blob-storage-json]: ./media/data-factory-get-started/getstarted-editor-blob-storage-json.png

[image-editor-blob-storage-deploy]: ./media/data-factory-get-started/getstarted-editor-blob-storage-deploy.png

[image-editor-azure-sql-settings]: ./media/data-factory-get-started/getstarted-editor-azure-sql-settings.png

[image-editor-newpipeline-button]: ./media/data-factory-get-started/getstarted-editor-newpipeline-button.png

[image-datafactoryblade-diagramtile]: ./media/data-factory-get-started/getstarted-datafactoryblade-diagramtile.png


[image-data-factory-get-started-startboard]: ./media/data-factory-get-started/getstarted-data-factory-startboard.png

[image-data-factory-get-started-linked-services-link]: ./media/data-factory-get-started/getstarted-data-factory-linked-services-link.png

[image-data-factory-get-started-linked-services-add-store-button]: ./media/data-factory-get-started/getstarted-linked-services-add-store-button.png

[image-data-factory-linked-services-get-started-new-data-store]: ./media/data-factory-get-started/getstarted-linked-services-new-data-store.png

[image-data-factory-get-started-new-data-store-with-storage]: ./media/data-factory-get-started/getstarted-linked-services-new-data-store-with-storage.png

[image-data-factory-get-started-storage-account-name-key]: ./media/data-factory-get-started/getstarted-storage-account-name-key.png

[image-data-factory-get-started-linked-services-list-with-myblobstore]: ./media/data-factory-get-started/getstarted-linked-services-list-with-myblobstore.png

[image-data-factory-get-started-linked-azure-sql-properties]: ./media/data-factory-get-started/getstarted-linked-azure-sql-properties.png

[image-data-factory-get-started-azure-sql-connection-string]: ./media/data-factory-get-started/getstarted-azure-sql-connection-string.png

[image-data-factory-get-started-linked-services-list-two-stores]: ./media/data-factory-get-started/getstarted-linked-services-list-two-stores.png

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-get-started/getstarted-storage-explorer.png

[image-data-factory-get-started-diagram-link]: ./media/data-factory-get-started/getstarted-diagram-link.png

[image-data-factory-get-started-diagram-blade]: ./media/data-factory-get-started/getstarted-diagram-blade.png

[image-data-factory-get-started-home-page-pipeline-tables]: ./media/data-factory-get-started/getstarted-datafactory-home-page-pipeline-tables.png

[image-data-factory-get-started-datasets-blade]: ./media/data-factory-get-started/getstarted-datasets-blade.png

[image-data-factory-get-started-table-blade]: ./media/data-factory-get-started/getstarted-table-blade.png

[image-data-factory-get-started-dataslices-blade]: ./media/data-factory-get-started/getstarted-dataslices-blade.png

[image-data-factory-get-started-dataslice-blade]: ./media/data-factory-get-started/getstarted-dataslice-blade.png

[image-data-factory-get-started-sql-query-results]: ./media/data-factory-get-started/getstarted-sql-query-results.png

[image-data-factory-get-started-datasets-emptable-selected]: ./media/data-factory-get-started/DataSetsWithEmpTableFromBlobSelected.png

[image-data-factory-get-started-activity-run-details]: ./media/data-factory-get-started/ActivityRunDetails.png

[image-data-factory-create-resource-group]: ./media/data-factory-get-started/CreateNewResourceGroup.png

[image-data-factory-preview-storage-key]: ./media/data-factory-get-started/PreviewPortalStorageKey.png

[image-data-factory-database-connection-string]: ./media/data-factory-get-started/DatabaseConnectionString.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-get-started/NewDataFactoryMenu.png

[image-data-factory-sql-management-console]: ./media/data-factory-get-started/getstarted-azure-sql-management-console.png

[image-data-factory-sql-management-console-2]: ./media/data-factory-get-started/getstarted-azure-sql-management-console-2.png

[image-data-factory-name-not-available]: ./media/data-factory-get-started/getstarted-data-factory-not-available.png
 

<!---HONumber=58_postMigration-->