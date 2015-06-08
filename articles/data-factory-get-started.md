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

O tutorial neste artigo o ajudará a iniciar rapidamente com o uso do serviço de fábrica de dados do Azure. Neste tutorial, você irá criar uma fábrica de dados do Azure e criar um pipeline na fábrica dados para copiar dados de um armazenamento de BLOBs do Azure para um banco de dados do SQL Azure.

> [AZURE.NOTE]Para obter uma visão geral detalhada do service Factory de dados, consulte o [Introdução ao Azure dados Factory][data-factory-introduction] artigo.

##Pré-requisitos para o tutorial
Antes de começar este tutorial, você deve ter o seguinte:

- **Assinatura do Azure**. Se você não tiver uma assinatura, você pode criar uma conta de avaliação gratuita em apenas alguns minutos. Consulte o [avaliação gratuita][azure-free-trial] para obter detalhes.
- **Conta de armazenamento do azure**. Você usará o armazenamento de blob como um **fonte** armazenam dados neste tutorial se você não tiver uma conta de armazenamento do Azure, consulte o [criar uma conta de armazenamento][data-factory-create-storage] artigo para conhecer as etapas criar um.
- **Banco de dados SQL do Azure**. Você usará um banco de dados do SQL Azure como um **destino** armazenam dados neste tutorial. Se você não tiver um banco de dados do SQL Azure que você pode usar o tutorial, consulte [como criar e configurar um banco de dados do SQL Azure][data-factory-create-sql-database] para criar um.
- **SQL Server 2012/2014 ou Visual Studio 2013**. Você usará SQL Server Management Studio ou Visual Studio para criar um banco de dados de exemplo e exibir os dados de resultado no banco de dados.  

### Coletar o nome da conta e chave de conta para sua conta de armazenamento do Azure
Você precisará do nome da conta e chave de conta da sua conta de armazenamento do Azure para fazer este tutorial. Anote o **nome da conta** e **chave de conta** para sua conta de armazenamento do Azure, seguindo as instruções abaixo:

1. Faça logon para o [Portal de visualização do Azure][azure-preview-portal].
2. Clique em **Procurar** hub à esquerda e selecione **contas de armazenamento**.
3. No **contas de armazenamento** lâmina, selecione o **conta de armazenamento do Azure** que você deseja usar neste tutorial.
4. No **armazenamento** lâmina, clique em **chaves** lado a lado.
5. No **Gerenciar chaves** lâmina, clique em **cópia** botão (imagem) ao lado **nome da conta de armazenamento** texto Salvar/colá-lo em algum lugar e caixa (por exemplo: em um arquivo de texto).  
6. Repita a etapa anterior para copiar ou anote o **chave de acesso primária**.
7. Feche todos os blades clicando **X**.

### Coletar o nome do servidor, nome do banco de dados e conta de usuário para seu banco de dados do SQL Azure
Você precisará os nomes do servidor SQL Azure, banco de dados e o usuário fazer este tutorial. Anote os nomes dos **server**, **banco de dados**, e **usuário** para seu banco de dados do SQL Azure, seguindo as instruções abaixo:

1. No **Azure Preview Portal**, clique em **Procurar** à esquerda e selecione **bancos de dados SQL**.
2. No **lâmina de bancos de dados SQL**, selecione o **banco de dados** que você deseja usar neste tutorial. Anote o **nome do banco de dados**.  
3. No **banco de dados SQL** lâmina, clique em **propriedades** lado a lado.
4. Anote os valores para **nome do servidor** e **logon de administração de servidor**.
5. Feche todos os blades clicando **X**.

### Permitir que os serviços do Azure acessar o servidor do SQL Azure
Certifique-se de que **Permitir acesso aos serviços do Azure** definindo folheada **ON** para o servidor do SQL Azure para que o serviço de dados fábrica possa acessar seu servidor do SQL Azure. Para verificar e ativar essa configuração, faça o seguinte:

1. Clique em **Procurar** hub à esquerda e clique em **servidores SQL**.
2. Selecione **seu servidor**, e clique em **configurações** sobre o **SQL SERVER** blade.
3. No **configurações** lâmina, clique em **Firewall**.
4. No **as configurações de Firewall** lâmina, clique em **ON** para **Permitir acesso aos serviços do Azure**.
5. Feche todos os blades clicando **X**.

### Preparar o armazenamento de blobs do Azure e o banco de dados SQL do Azure para o tutorial
Agora, prepare seu armazenamento de BLOBs do Azure e o banco de dados do SQL Azure para o tutorial, executando as seguintes etapas:

1. Inicie o bloco de notas, cole o seguinte texto e salve-o como **emp.txt** para **C:\ADFGetStarted** pasta no disco rígido.

        John, Doe
		Jane, Doe

2. Use ferramentas como [Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/) para criar o **adftutorial** contêiner e carregar o **emp.txt** arquivo ao contêiner.

    ![Gerenciador de Armazenamento do Azure][image-data-factory-get-started-storage-explorer]
3. Use o seguinte script SQL para criar a **emp** tabela em seu banco de dados do SQL Azure.  


        CREATE TABLE dbo.emp
		(
			ID int IDENTITY(1,1) NOT NULL,
			FirstName varchar(50),
			LastName varchar(50),
		)
		GO

		CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);

	**Se você tiver instalado no computador do SQL Server 2012/2014:** siga as instruções do [etapa 2: conectar-se ao banco de dados SQL do gerenciamento do Azure SQL banco de dados usando o SQL Server Management Studio][sql-management-studio] artigo para se conectar ao seu servidor do SQL Azure e execute o script SQL. Observe que este artigo usa o portal de gerenciamento de versão (http://manage.windowsazure.com), não o portal de visualização (http://portal.azure.com), para configurar o firewall para um servidor do SQL Azure.

	**Se você tiver o Visual Studio 2013 instalado no computador:** no [Azure Preview Portal](http://portal.azure.com), clique em **Procurar** hub à esquerda, clique **servidores SQL**, selecione o banco de dados e clique em **aberto no Visual Studio** botão na barra de ferramentas para conectar-se ao seu servidor do SQL Azure e execute o script. Se o cliente não tem permissão para acessar o servidor do SQL Azure, você precisará configurar o firewall para o servidor do SQL Azure permitir o acesso do computador (endereço IP). Consulte o artigo acima para obter as etapas configurar o firewall para o servidor do SQL Azure.


Faça o seguinte:

- Clique em [usando o Editor de fábrica dados](data-factory-get-started-using-editor.md) link na parte superior para executar o tutorial usando o Editor de fábrica de dados, que faz parte do Portal do Azure.
- Clique em [usando o PowerShell](data-factory-monitor-manage-using-powershell.md) link na parte superior para executar o tutorial usando o PowerShell do Azure.


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
[data-factory-create-sql-database]: sql-database-create-configure.md


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

<!---HONumber=GIT-SubDir-->