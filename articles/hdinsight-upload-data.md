<properties 
	pageTitle="Carregar dados para trabalhos do Hadoop no HDInsight | Microsoft Azure" 
	description="Saiba como carregar e acessar dados no HDInsight usando o Gerenciador de Armazenamento do Azure, o Azure PowerShell, a linha de comando do Hadoop ou o Sqoop." 
	services="storage, hdinsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/31/2015" 
	ms.author="jgao"/>



#Carregar dados para trabalhos do Hadoop no HDInsight

O Azure HDInsight oferece um HDFS (Sistema de Arquivos Distribuído) do Hadoop completo no armazenamento de blob do Azure. Ele foi projetado como uma extensão HDFS para fornecer uma experiência perfeita aos clientes. Ele permite que o conjunto completo de componentes no ecossistema do Hadoop opere diretamente nos dados que ele gerencia. O armazenamento de blob do Azure e o HDFS são sistemas de arquivos distintos otimizados para armazenamento de dados e computação nesses dados. Para ver os benefícios do uso do armazenamento de Blob do Azure, consulte [Usar o armazenamento de Blob do Azure com o HDInsight][hdinsight-storage].

Os clusters do Azure HDInsight normalmente são implantados para executar trabalhos do MapReduce e são removidos quando esses trabalhos são concluídos. A manutenção dos dados nos clusters HDFS após a conclusão dos cálculos seria uma maneira cara de armazenar esses dados. O armazenamento de Blob do Azure é altamente disponível, escalonável, com alta capacidade, baixo custo e uma opção de armazenamento compartilhável para dados a serem processados usando o HDInsight. O armazenamento de dados em um blob permite que os clusters HDInsight usados para computação sejam liberados sem que ocorra perda de dados.

O armazenamento de blob do Azure pode ser acessado por meio do [AzCopy][azure-azcopy], do [PowerShell do Azure][azure-powershell], da [Biblioteca de Cliente de Armazenamento do Azure para .NET][azure-storage-client-library], da [Interface de Linha de Comando do Azure para Mac, Linux e Windows][xplatcli] ou por meio de ferramentas de exploração. Estas são algumas das ferramentas disponíveis:

* [Gerenciador de Armazenamento do Azure](http://azurestorageexplorer.codeplex.com/)
* [Cloud Storage Studio 2](http://www.cerebrata.com/Products/CloudStorageStudio/)
* [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer)
* [Azure Explorer](http://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx)
* [Azure Explorer PRO](http://www.cloudberrylab.com/microsoft-azure-explorer-pro.aspx)

##Pré-requisitos

Observe os seguintes requisitos antes de começar:

* Um cluster Azure HDInsight. Para obter instruções, consulte [Introdução ao Azure HDInsight][hdinsight-get-started] ou [Provisionar clusters HDInsight][hdinsight-provision].


##<a id="azcopy"></a>Carregar dados no armazenamento de Blob do Azure usando o AzCopy##

O AzCopy é uma ferramenta de linha de comando destinada a simplificar a tarefa de transferir dados de e para uma conta de Armazenamento do Azure. Você pode usá-lo como uma ferramenta independente ou incorporar essa ferramenta em um aplicativo existente. [Baixar o AzCopy][azure-azcopy-download].

A sintaxe do AzCopy é:

	AzCopy <Source> <Destination> [filePattern [filePattern...]] [Options]

Para saber mais, consulte [AzCopy - Carregando e baixando arquivos para Blobs do Azure][azure-azcopy].

##<a id="powershell"></a>Carregar dados no armazenamento de Blob usando o PowerShell do Azure##

O PowerShell do Azure é um ambiente de script poderoso que você pode usar para controlar e automatizar a implantação e o gerenciamento de suas cargas de trabalho no Azure. Você pode usar o PowerShell do Azure para carregar dados no armazenamento de Blob para que os dados possam ser processados por trabalhos do MapReduce. Para saber mais sobre como configurar sua estação de trabalho para executar o PowerShell do Azure, consulte [Instalar e configurar o PowerShell do Azure](powershell-install-configure.md).

**Para carregar um arquivo local para o Armazenamento de Blob do Azure**

1. Abra o console do PowerShell do Azure conforme instruído em [Instalar e configurar o PowerShell do Azure](powershell-install-configure.md).
2. Defina os valores das cinco primeiras variáveis no script a seguir:

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName = "<StorageAccountName>"
		$containerName = "<ContainerName>"
		
		$fileName ="<LocalFileName>"
		$blobName = "<BlobName>"
		
		# Get the storage account key
		Select-AzureSubscription $subscriptionName
		$storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
		
		# Create the storage context object
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey
		
		# Copy the file from local workstation to the Blob container		
		Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob $blobName -context $destContext
		
3. Cole o script no console do PowerShell do Azure para executá-lo.

Os contêineres de armazenamento de blob do Azure armazenam dados como pares de chave/valor e não há nenhuma hierarquia de diretório. No entanto, o caractere "/" pode ser usado dentro do nome de chave para parecer que um arquivo está armazenado em uma estrutura de diretório. Por exemplo, a chave de um blob pode ser *input/log1.txt*. Não existe nenhum diretório de "entrada" real, mas devido à presença do caractere "/" no nome da chave, ele parece um caminho de arquivo. No script anterior, você pode fornecer ao arquivo uma estrutura de pastas definindo a variável **$blobname**, por exemplo,  *$blobname="myfolder/myfile.txt"*.

Usando as ferramentas do Azure Explorer, você pode perceber alguns arquivos de 0 byte. Esses arquivos têm duas finalidades:

- Se houver pastas vazias, eles marcam a existência da pasta. O Armazenamento de blob do Azure é inteligente o suficiente para saber que se existir um blob denominado foo/bar, haverá uma pasta chamada **foo**. Mas se você desejar ter uma pasta vazia chamada **foo**, a única maneira de indicar isso é tendo esse arquivo de 0 byte especial no lugar.
- Elas contêm alguns metadados especiais de que o sistema de arquivos Hadoop precisa, especialmente as permissões e os proprietários das pastas.


##<a id="xplatcli"></a>Carregue dados para o armazenamento de blob do Azure usando o Azure CLI

A Azure CLI para Mac, Linux e Windows é uma ferramenta de plataforma cruzada que permite que você gerencie os serviços do Azure. Use as seguintes etapas para carregar dados no armazenamento de Blob do Azure:

1. [Instalar e configurar o Azure CLI para Mac, Linux e Windows](xplat-cli.md).

2. Abra um prompt de comando, bash ou outro shell e use os dados a seguir para autenticar a sua assinatura do Azure.

		azure login

	Quando solicitado, insira o nome de usuário e senha para a sua assinatura.

3. Use o seguinte comando para listar sites para as contas de armazenamento da sua assinatura:

		azure storage account list

4. Selecione a conta de armazenamento que contém o blob com que você deseja trabalhar e use o seguinte comando para recuperar a chave para esta conta:

		azure storage account keys list <storage-account-name>

	Isso deve retornar chaves **Primária** e **Secundária**. Copie o valor de chave **Primária**, já que ele será usado nas próximas etapas.

5. Use o seguinte comando para recuperar uma lista de contêineres de blob na conta de armazenamento:

		azure storage container list -a <storage-account-name> -k <primary-key>

6. Use os seguintes comandos para carregar e baixar arquivos para o blob:

	* Para carregar um arquivo:

			azure storage blob upload -a <storage-account-name> -k <primary-key> <source-file> <container-name> <blob-name>

	* Para baixar um arquivo:

			azure storage blob download -a <storage-account-name> -k <primary-key> <container-name> <blob-name> <destination-file>

> [AZURE.NOTE]Se você for sempre trabalhar com a mesma conta de armazenamento, pode definir as seguintes variáveis de ambiente em vez de especificar a conta e a chave para cada comando:
> 
> * **AZURE_STORAGE_ACCOUNT**: o nome da conta de armazenamento
> 
> * **AZURE_STORAGE_ACCESS_KEY**: a chave da conta de armazenamento

##<a id="storageexplorer"></a>Carregar dados no armazenamento de blob usando o Azure Storage Explorer

O *Azure Storage Explorer* é uma ferramenta útil para inspecionar e alterar os dados em seu Armazenamento do Azure. É uma ferramenta gratuita que pode ser baixada do CodePlex: [Azure Storage Explorer](http://azurestorageexplorer.codeplex.com/ "Gerenciador de Armazenamento do Azure").

Para usar a ferramenta, conheça sua chave e seu nome da conta de armazenamento do Azure. Para saber mais sobre como obter essas informações, confira a seção "Como exibir, copiar e regenerar chaves de acesso de armazenamento” em [Criar, gerenciar ou excluir uma conta de armazenamento][azure-create-storage-account].

1. Execute o Azure Storage Explorer.

	![HDI.AzureStorageExplorer][image-azure-storage-explorer]

2. Clique em **Adicionar Conta**. Depois que uma conta é adicionada ao Azure Storage Explorer, você não precisa passar por essa etapa novamente.

	![HDI.ASEAddAccount][image-ase-addaccount]

3. Digite o **Nome da conta de armazenamento** e a **Chave da conta de armazenamento** e clique em **Adicionar Conta de Armazenamento**. Você pode adicionar várias contas de armazenamento, e cada conta será exibida em uma guia.
4. Em **Tipo de Armazenamento**, clique em **Blobs**.

	![HDI.ASEBlob][image-ase-blob]

5. Em **Contêiner**, clique no nome do contêiner que está associado ao cluster HDInsight. Quando cria um cluster HDInsight, você deve especificar um contêiner. Caso contrário, o processo de criação de cluster criará um para você.
6. Em **Blob**, clique em **Carregar**.
7. Especifique um arquivo para carregar e **Abrir**.








































































##<a id="commandline"></a> Carregar dados no armazenamento de blob do Azure usando a linha de comando do Hadoop

Para usar a linha de comando do Hadoop, primeiro conecte-se ao cluster usando a Área de Trabalho Remota:



1. Entre no [Portal do Azure][azure-management-portal].
2. Clique em **HDINSIGHT**. Você verá uma lista de clusters Hadoop implantados.
3. Clique no cluster HDInsight onde deseja carregar dados.
4. Clique em **CONFIGURAÇÃO** na parte superior da página.
5. Clique em **HABILITAR CONEXÃO REMOTA**, se não tiver habilitado a Área de Trabalho Remota, e siga as instruções. Caso contrário, avance para a próxima etapa.
4. Na parte inferior da página, clique em **CONECTAR**.
7. Clique em **Abrir**.
8. Digite suas credenciais e **OK**.
9. Clique em **Sim**.
10. Na área de trabalho, clique em **Linha de Comando do Hadoop**.
12. O exemplo de código a seguir demonstra como copiar o arquivo davinci.txt do sistema de arquivos local no nó principal do HDInsight para o diretório /example/data.

		hadoop dfs -copyFromLocal C:\temp\davinci.txt /example/data/davinci.txt

	Como o sistema de arquivos padrão está no armazenamento de blob do Azure, /example/datadavinci.txt está, na verdade, no armazenamento de blob do Azure. Você também pode fazer referência ao arquivo como:

		wasb:///example/data/davinci.txt 

	ou

		wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

	O nome de domínio totalmente qualificado é necessário quando você usa *wasbs*.

13. Use o comando a seguir para listar os arquivos carregados:

		hadoop dfs -lsr /example/data


##<a id="sqoop"></a> Importar dados para o HDFS de um Banco de Dados SQL do Azure ou SQL Server usando o Sqoop

O Sqoop é uma ferramenta desenvolvida para transferir dados entre bancos de dados relacionais e o Hadoop. Você pode usá-lo para importar dados de um RDBMS (sistema de gerenciamento de banco de dados relacional), como SQL Server, MySQL ou Oracle para o HDFS (Sistema de Arquivos Distribuído) do Hadoop, transformar os dados no Hadoop com o MapReduce ou o Hive e, em seguida, exportar os dados de volta para um RDBMS. Para obter mais informações, consulte [Guia do usuário do Sqoop][apache-sqoop-guide].

Para importar os dados, você deve saber o nome do servidor do Banco de Dados SQL do Azure, o nome da conta de banco de dados, a senha da conta e o nome do banco de dados.

Por padrão, um banco de dados SQL do Azure permite conexões de serviços do Azure, como o Azure HDInsight. Se essa configuração de firewall estiver desabilitada, você deverá habilitá-la no Portal do Azure. Para saber mais sobre como criar um Banco de Dados SQL do Azure e configurar regras de firewall, consulte [Criar e configurar o Banco de Dados SQL][sqldatabase-create-configure].

O procedimento a seguir usa o PowerShell do Azure para enviar um trabalho do Sqoop.

**Para importar dados no HDInsight usando o Sqoop e o PowerShell do Azure**

1. Abra o console do PowerShell do Azure conforme instruído em [Instalar e configurar o PowerShell do Azure](powershell-install-configure.md).
2. Defina os valores das oito primeiras variáveis no script a seguir:

		$subscriptionName = "<AzureSubscriptionName>"
		$clusterName = "<HDInsightClusterName>"
		
		$sqlDatabaseServerName = "<SQLDatabaseServerName>"
		$sqlDatabaseUserName = "<SQLDatabaseDatabaseName>"
		$sqlDatabasePassword = "<SQLDatabasePassword>"
		$sqlDatabaseDatabaseName = "<SQLDatabaseDatabaseName>"
		$tableName = "<SQLDatabaseTableName>"
		
		$hdfsOutputDir = "<OutputPath>"  # This is the HDFS path for the output file, for example "/lineItemData".
		
		Select-AzureSubscription $subscriptionName		
		$sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "import --connect jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseUserName@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseDatabaseName --table $tableName --target-dir $hdfsOutputDir -m 1" 

		$sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
		Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob
		
		Write-Host "Standard Error" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
		Write-Host "Standard Output" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

3. Cole o script no console do PowerShell do Azure para executá-lo. Consulte [Introdução ao HDInsight][hdinsight-get-started] para obter uma amostra para recuperar o arquivo de dados do armazenamento de blob do Azure.

Para saber mais sobre como usar o Sqoop, consulte [Usar o Sqoop com o HDInsight][hdinsight-use-sqoop].

## Próximas etapas
Agora que você compreende como obter dados no HDInsight, leia os seguintes artigos para aprender a executar uma análise:

* [Introdução ao Azure HDInsight][hdinsight-get-started]
* [Enviar trabalhos do Hadoop de forma programática][hdinsight-submit-jobs]
* [Usar o Hive com o HDInsight][hdinsight-use-hive]
* [Usar o Pig com o HDInsight][hdinsight-use-pig]




[azure-management-portal]: https://manage.windowsazure.com
[azure-powershell]: http://msdn.microsoft.com/library/windowsazure/jj152841.aspx

[azure-storage-client-library]: /develop/net/how-to-guides/blob-storage/
[azure-create-storage-account]: storage-create-storage-account.md
[azure-azcopy-download]: storage-use-azcopy.md
[azure-azcopy]: storage-use-azcopy.md

[hdinsight-use-sqoop]: hdinsight-use-sqoop.md

[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-get-started]: hdinsight-get-started.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-provision]: hdinsight-provision-clusters.md

[sqldatabase-create-configure]: ../sql-database/sql-database-create-configure.md

[apache-sqoop-guide]: http://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

[Powershell-install-configure]: powershell-install-configure.md

[xplatcli]: xplat-cli.md


[image-azure-storage-explorer]: ./media/hdinsight-upload-data/HDI.AzureStorageExplorer.png
[image-ase-addaccount]: ./media/hdinsight-upload-data/HDI.ASEAddAccount.png
[image-ase-blob]: ./media/hdinsight-upload-data/HDI.ASEBlob.png


<!--HONumber=54-->