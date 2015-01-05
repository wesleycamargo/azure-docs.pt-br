<properties urlDisplayName="Upload Data" pageTitle="Carregar dados para trabalhos do Hadoop no HDInsight | Azure" metaKeywords="" description="Learn how to upload and access data in HDInsight using Azure Storage Explorer, Azure PowerShell, the Hadoop command line, or Sqoop." metaCanonical="" services="storage,hdinsight" documentationCenter="" title="Upload data for Hadoop jobs in HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/12/2014" ms.author="jgao" />



#Carregar dados para trabalhos do Hadoop no HDInsight

O Azure HDInsight oferece um HDFS (Sistema de Arquivos Distribuído) do Hadoop completo no armazenamento de Blob do Azure. Ele foi projetado como uma extensão HDFS para fornecer uma experiência perfeita aos clientes, permitindo que o conjunto completo de componentes no ecossistema do Hadoop opere diretamente nos dados que ele gerencia. O armazenamento de Blob do Azure e o HDFS são sistemas de arquivos distintos otimizados para armazenamento de dados e computação nesses dados. Para ver os benefícios do uso do armazenamento de Blob do Azure, consulte [Usar o armazenamento de Blob do Azure com HDInsight][hdinsight-storage]. 

Os clusters do Azure HDInsight normalmente são implantados para executar trabalhos MapReduce e são removidos quando esses trabalhos são concluídos. A manutenção dos dados nos clusters HDFS após a conclusão dos cálculos seria uma maneira cara de armazenar esses dados. O armazenamento de Blob do Azure é altamente disponível, escalonável, com alta capacidade, baixo custo e uma opção de armazenamento compartilhável para dados a serem processados usando o HDInsight. O armazenamento de dados em um Blob permite que os clusters HDInsight usados para computação sejam liberados sem que ocorra perda de dados. 

O armazenamento de Blob do Azure pode ser acessado por meio do [AzCopy][azure-azcopy], [Azure PowerShell][azure-powershell], [Biblioteca de Cliente de Armazenamento do Azure para .NET][azure-storage-client-library] ou por meio de ferramentas de exploração.Estas são algumas das ferramentas disponíveis:

* [Gerenciador de Armazenamento do Azure](http://azurestorageexplorer.codeplex.com/)
* [Cloud Storage Studio 2](http://www.cerebrata.com/Products/CloudStorageStudio/)
* [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer)
* [Azure Explorer](http://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx)
* [Azure Explorer PRO](http://www.cloudberrylab.com/microsoft-azure-explorer-pro.aspx)

**Pré-requisitos**

Observe os seguintes requisitos antes de começar este artigo:

* Um cluster Azure HDInsight. Para obter instruções, consulte [Introdução ao Azure HDInsight][hdinsight-get-started] ou [Provisionar clusters HDInsight][hdinsight-provision].

##Neste artigo

* [Carregar dados no Armazenamento de Blob usando AzCopy](#azcopy)
* [Carregar dados no armazenamento de Blob usando o PowerShell do Azure](#powershell)
* [Carregar dados no Armazenamento de Blob usando o Azure Storage Explorer](#storageexplorer)
* [Carregar dados no Armazenamento de Blob usando a linha de comando do Hadoop](#commandline)
* [Importar dados do Banco de Dados SQL do Azure para o armazenamento de Blob usando o Sqoop](#sqoop)

##<a id="azcopy"></a>Carregar dados no Armazenamento de Blob usando AzCopy##

O AzCopy é um utilitário de linha de comando desenvolvido para simplificar a tarefa de transferir dados de uma conta de Armazenamento do Azure e para ela. Você pode usá-lo como uma ferramenta independente ou incorporar esse utilitário em um aplicativo existente. [Baixar o AzCopy][azure-azcopy-download].

A sintaxe do AzCopy é:

	AzCopy <Source> <Destination> [filePattern [filePattern...]] [Options]

Para obter mais informações, consulte [AzCopy - Carregando e baixando arquivos de Blobs do Azure][azure-azcopy]

##<a id="powershell"></a>Carregar dados no armazenamento de Blob usando o PowerShell do Azure##

O PowerShell do Azure é um ambiente de script poderoso que você pode usar para controlar e automatizar a implantação e o gerenciamento de suas cargas de trabalho no Azure. Você pode usar o PowerShell do Azure para carregar dados no armazenamento de Blob, para que os dados possam ser processados por trabalhos MapReduce. Para obter informações sobre como configurar sua estação de trabalho para executar o PowerShell do Azure, consulte [Instalar e configurar o PowerShell do Azure][powershell-install-configure].

**Para carregar um arquivo local para o Armazenamento de Blob**

1. Abra a janela do console do PowerShell do Azure, conforme instruído em [Instalar e configurar o PowerShell do Azure][powershell-install-configure].
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
		
3. Cole o script na janela do console do PowerShell do Azure para executá-lo.

Os contêineres de armazenamento de blob armazenam dados como pares de chave/valor, e não há nenhuma hierarquia de diretório. No entanto, o caractere "/" pode ser usado dentro do nome de chave para parecer que um arquivo está armazenado em uma estrutura de diretório. Por exemplo, a chave de um blob pode ser *input/log1.txt*. Não existe nenhum diretório de "entrada" real, mas devido à presença do caractere "/" no nome da chave, ele parece um caminho de arquivo. No script anterior, você pode fornecer ao arquivo uma estrutura de pastas definindo a variável $blobname. Por exemplo, *$blobname="myfolder/myfile.txt"*.

Usando as ferramentas do Azure Explorer, você pode perceber alguns arquivos de 0 byte. Esses arquivos têm duas finalidades:

- No caso de pastas vazias, elas servem como um marcador da existência da pasta. O Armazenamento de blob é inteligente o suficiente para saber que se existir um blob denominado foo/bar, haverá uma pasta chamada foo. Mas se você desejar ter uma pasta vazia chamada foo, a única maneira de indicar isso é tendo esse arquivo de 0 byte especial no lugar.
- Elas contêm alguns metadados especiais que o sistema de arquivos Hadoop necessita, especialmente as permissões e os proprietários das pastas.








##<a id="storageexplorer"></a>Carregar dados no Armazenamento de Blob usando o Azure Storage Explorer

*Azure Storage Explorer* é uma ferramenta útil para inspecionar e alterar os dados em seu Armazenamento do Azure. É uma ferramenta gratuita que pode ser baixada de[http://azurestorageexplorer.codeplex.com/](http://azurestorageexplorer.codeplex.com/ "Azure Storage Explorer").

Para usar a ferramenta, conheça sua chave e seu nome da conta de armazenamento do Azure. Para obter instruções sobre como obter essas informações, consulte a seção "Como: Exibir, copiar e gerar novamente chaves de acesso de armazenamento" em [Como criar, gerenciar ou excluir uma conta de armazenamento][azure-create-storageaccount].  

1. Execute o Azure Storage Explorer.

	![HDI.AzureStorageExplorer][image-azure-storage-explorer]

2. Clique em **Adicionar Conta**. Depois que uma conta é adicionada ao Azure Storage Explorer, você não precisa passar por essa etapa novamente. 

	![HDI.ASEAddAccount][image-ase-addaccount]

3. Digite o **Nome da conta de armazenamento** e a **Chave da conta de armazenamento** e **Adicionar Conta de Armazenamento**. Você pode adicionar várias contas de armazenamento, e cada conta será exibida em uma guia. 
4. Em **Tipo de Armazenamento**, clique em **Blobs**.

	![HDI.ASEBlob][image-ase-blob]

5. No**Contêiner**, clique no contêiner associado ao cluster HDInsight.  Quando cria um cluster HDInsight, você deve especificar um contêiner.Caso contrário, o processo de criação de cluster criará um para você.
6. Em **Blob**, clique em **Carregar**.
7. Especifique um arquivo para carregar e **Abrir**.








































































##<a id="commandline"></a> Carregar dados no Armazenamento de Blob usando a linha de comando do Hadoop

Para usar a linha de comando do Hadoop, primeiro conecte-se ao cluster usando a área de trabalho remota. 



1. Entre [no Portal de Gerenciamento][azure-management-portal].
2. Clique em **HDINSIGHT**.Você verá uma lista de clusters Hadoop implantados.
3. Clique no cluster HDInsight onde deseja carregar dados.
4. Clique em **CONFIGURAÇÃO** na parte superior da página.
5. Clique em**HABILITAR CONEXÃO REMOTA** se não tiver habilitado a área de trabalho remota e siga as instruções.Caso contrário, avance para a próxima etapa.
4. Clique em **CONECTAR** na parte inferior da página.
7. Clique em **Abrir**.
8. Digite suas credenciais e clique em **OK**.
9. Clique em **Sim**.
10. Na área de trabalho, clique em **Linha de Comando do Hadoop**.
12. O exemplo a seguir demonstra como copiar o arquivo davinci.txt do sistema de arquivos local no headnode do HDInsight para o diretório /example/data.

		hadoop dfs -copyFromLocal C:\temp\davinci.txt /example/data/davinci.txt

	Como o sistema de arquivos padrão está no armazenamento de Blob do Azure, /example/datadavinci.txt está, na verdade, no armazenamento de Blob do Azure.  Você também pode fazer referência ao arquivo como:

		wasb:///example/data/davinci.txt 

	ou

		wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

	O nome de domínio totalmente qualificado é necessário quando você usa *wasbs*.

13. Use o comando a seguir para listar os arquivos carregados:

		hadoop dfs -lsr /example/data


##<a id="sqoop"></a> Importar dados para o HDFS do Banco de Dados SQL/SQL Server usando o Sqoop

O Sqoop é uma ferramenta desenvolvida para transferir dados entre bancos de dados relacionais e o Hadoop. Você pode usá-lo para importar dados de um RDBMS (sistema de gerenciamento de banco de dados relacional), como SQL ou MySQL ou Oracle para o HDFS (Sistema de Arquivos Distribuído) do Hadoop, transformar os dados no Hadoop com MapReduce ou Hive e, em seguida, exportar os dados de volta para um RDBMS. Para obter mais informações, consulte [Guia do usuário do Sqoop][apache-sqoop-guide].

Para importar os dados, saiba o nome do servidor do Banco de Dados SQL do Azure, o nome da conta de banco de dados, a senha da conta e o nome do banco de dados. 

Por padrão, um banco de dados SQL do Azure permite conexões de serviços do Azure, como o Azure HDInsight. Se essa configuração de firewall estiver desabilitada, você deverá habilitá-la no Portal de Gerenciamento do Azure. Para obter instruções sobre como criar um Banco de Dados SQL e configurar regras de firewall, consulte [Criar e configurar o Banco de Dados SQL][sqldatabase-create-configue]. 

O procedimento a seguir usa o PowerShell para enviar um trabalho Sqoop. 

**Para importar dados no HDInsight usando Sqoop e PowerShell**

1. Abra a janela do console do PowerShell do Azure, conforme instruído em [Instalar e configurar o PowerShell do Azure][powershell-install-configure].
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

3. Cole o script na janela do console do PowerShell do Azure para executá-lo. Consulte [Introdução ao HDInsight][hdinsight-get-started] para obter uma amostra do PowerShell para recuperar o arquivo de dados no armazenamento de Blob do Azure.

Para obter mais informações sobre como usar o Sqoop, consulte [Usar o Sqoop com o HDInsight][hdinsight-use-sqoop].

## Próximas etapas
Agora que você compreende como obter dados no HDInsight, use os seguintes artigos para aprender a executar uma análise:

* [Introdução ao Azure HDInsight][hdinsight-get-started]
* [Enviar trabalhos do Hadoop de forma programática][hdinsight-submit-jobs]
* [Usar o Hive com o HDInsight][hdinsight-use-hive]
* [Usar o Pig com o HDInsight][hdinsight-use-pig]




[azure-management-portal]: https://manage.windowsazure.com
[azure-powershell]: http://msdn.microsoft.com/pt-br/library/windowsazure/jj152841.aspx

[azure-storage-client-library]: /pt-br/develop/net/how-to-guides/blob-storage/
[azure-create-storage-account]: ../storage-create-storage-account/
[azure-azcopy-download]: http://aka.ms/WaCopy
[azure-azcopy]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx

[hdinsight-use-sqoop]: ../hdinsight-use-sqoop/

[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-get-started]: ../hdinsight-get-started/

[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig
[hdinsight-provision]: ../hdinsight-provision-clusters/

[sqldatabase-create-configue]: ../sql-database-create-configure/

[apache-sqoop-guide]: http://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

[Powershell-install-configure]: ../install-configure-powershell/


[image-azure-storage-explorer]: ./media/hdinsight-upload-data/HDI.AzureStorageExplorer.png
[image-ase-addaccount]: ./media/hdinsight-upload-data/HDI.ASEAddAccount.png
[image-ase-blob]: ./media/hdinsight-upload-data/HDI.ASEBlob.png


<!--HONumber=35.1-->
