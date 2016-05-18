<properties
	pageTitle="Carregar dados para trabalhos do Hadoop no HDInsight | Microsoft Azure"
	description="Saiba como carregar e acessar os dados de trabalhos do Hadoop no HDInsight usando o Azure CLI, Azure Storage Explorer, Azure PowerShell, a linha de comando do Hadoop ou o Sqoop."
	services="hdinsight,storage"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/22/2016"
	ms.author="jgao"/>



#Carregar dados para trabalhos do Hadoop no HDInsight

O Azure HDInsight oferece um HDFS (Sistema de Arquivos Distribuído) do Hadoop completo no armazenamento de blob do Azure. Ele foi projetado como uma extensão HDFS para fornecer uma experiência perfeita aos clientes. Ele permite que o conjunto completo de componentes no ecossistema do Hadoop opere diretamente nos dados que ele gerencia. O armazenamento de blob do Azure e o HDFS são sistemas de arquivos distintos otimizados para armazenamento de dados e computação nesses dados. Para ver os benefícios do uso do armazenamento de Blob do Azure, consulte [Usar o armazenamento de Blob do Azure com o HDInsight][hdinsight-storage].

**Pré-requisitos**

Observe os seguintes requisitos antes de começar:

* Um cluster Azure HDInsight. Para obter instruções, consulte [Introdução ao Azure HDInsight][hdinsight-get-started] ou [Provisionar clusters HDInsight][hdinsight-provision].

##Por que o armazenamento de blob?

Os clusters do Azure HDInsight normalmente são implantados para executar trabalhos do MapReduce e são removidos quando esses trabalhos são concluídos. A manutenção dos dados nos clusters HDFS após a conclusão dos cálculos seria uma maneira cara de armazenar esses dados. O armazenamento de Blob do Azure é altamente disponível, escalonável, com alta capacidade, baixo custo e uma opção de armazenamento compartilhável para dados a serem processados usando o HDInsight. O armazenamento de dados em um blob permite que os clusters HDInsight usados para computação sejam liberados sem que ocorra perda de dados.

###Diretórios

Os contêineres de armazenamento de blob do Azure armazenam dados como pares de chave/valor e não há nenhuma hierarquia de diretório. No entanto, o caractere "/" pode ser usado dentro do nome de chave para parecer que um arquivo está armazenado em uma estrutura de diretório. O HDInsight os vê como se fossem diretórios reais.

Por exemplo, a chave de um blob pode ser *input/log1.txt*. Não existe nenhum diretório de "entrada" real, mas devido à presença do caractere "/" no nome da chave, ele parece um caminho de arquivo.

Por isso, se você usar as ferramentas do Gerenciador do Azure, poderá perceber alguns arquivos de 0 byte. Esses arquivos têm duas finalidades:

- Se houver pastas vazias, eles marcam a existência da pasta. O Armazenamento de blob do Azure é inteligente o suficiente para saber que se existir um blob denominado foo/bar, haverá uma pasta chamada **foo**. Mas se você desejar ter uma pasta vazia chamada **foo**, a única maneira de indicar isso é tendo esse arquivo de 0 byte especial no lugar.

- Elas contêm alguns metadados especiais de que o sistema de arquivos Hadoop precisa, especialmente as permissões e os proprietários das pastas.

##Utilitários de linha de comando

A Microsoft fornece os seguintes utilitários para trabalhar com armazenamento de Blob do Azure:

| Ferramenta | Linux | OS X | Windows |
| ---- |:-----:|:----:|:-------:|
| [Interface de linha de comando do Azure][azurecli] | ✔ | ✔ | ✔ |
| [PowerShell do Azure][azure-powershell] | | | ✔ |
| [AzCopy][azure-azcopy] | | | ✔ |
| [Comando do Hadoop](#commandline) | ✔ | ✔ | ✔ |

> [AZURE.NOTE] Enquanto o CLI do Azure, o Azure PowerShell e o AzCopy podem ser usados fora do Azure, o comando Hadoop só está disponível no cluster HDInsight e só permite carregar dados do sistema de arquivos local para o armazenamento de Blob do Azure.

###<a id="xplatcli"></a>Azure CLI

O Azure CLI é uma ferramenta de plataforma cruzada que permite que você gerencie os serviços do Azure. Use as seguintes etapas para carregar dados no armazenamento de Blob do Azure:

[AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

1. [Instalar e configurar o Azure CLI para Mac, Linux e Windows](../xplat-cli-install.md).

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

> [AZURE.NOTE] Se você for sempre trabalhar com a mesma conta de armazenamento, pode definir as seguintes variáveis de ambiente em vez de especificar a conta e a chave para cada comando:
>
> * **AZURE\_STORAGE\_ACCOUNT**: o nome da conta de armazenamento
>
> * **AZURE\_STORAGE\_ACCESS\_KEY**: a chave da conta de armazenamento

###<a id="powershell"></a>Azure PowerShell

O Azure PowerShell é um ambiente de script que você pode usar para controlar e automatizar a implantação e o gerenciamento de suas cargas de trabalho no Azure. Para saber mais sobre como configurar sua estação de trabalho para executar o PowerShell do Azure, consulte [Instalar e configurar o PowerShell do Azure](../powershell-install-configure.md).

[AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell.md)]

**Para carregar um arquivo local para o Armazenamento de Blob do Azure**

1. Abra o console do PowerShell do Azure conforme instruído em [Instalar e configurar o PowerShell do Azure](../powershell-install-configure.md).
2. Defina os valores das cinco primeiras variáveis no script a seguir:

		$resourceGroupName = "<AzureResourceGroupName>"
		$storageAccountName = "<StorageAccountName>"
		$containerName = "<ContainerName>"

		$fileName ="<LocalFileName>"
		$blobName = "<BlobName>"

		# Get the storage account key
		$storageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName | %{ $_.Key1 }
		# Create the storage context object
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

		# Copy the file from local workstation to the Blob container
		Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob $blobName -context $destContext

3. Cole o script no console do Azure PowerShell para executá-lo para copiar o arquivo.

Por exemplo, os scripts do PowerShell criados para funcionar com o HDInsight, consulte [Ferramentas HDInsight](https://github.com/blackmist/hdinsight-tools).

###<a id="azcopy"></a>AzCopy

O AzCopy é uma ferramenta de linha de comando destinada a simplificar a tarefa de transferir dados de e para uma conta de Armazenamento do Azure. Você pode usá-lo como uma ferramenta independente ou incorporar essa ferramenta em um aplicativo existente. [Baixar o AzCopy][azure-azcopy-download].

A sintaxe do AzCopy é:

	AzCopy <Source> <Destination> [filePattern [filePattern...]] [Options]

Para saber mais, consulte [AzCopy - Carregando e baixando arquivos para Blobs do Azure][azure-azcopy].


###<a id="commandline"></a>Linha de comando do Hadoop

A linha de comando do Hadoop só é útil para armazenar dados no armazenamento de blob quando os dados já estão presentes no nó principal do cluster.

Para usar o comando Hadoop, primeiro você deve se conectar ao nó de cabeçalho usando um dos seguintes métodos:

* **HDInsight baseado em Windows**: [conecte-se usando a área de trabalho remota](hdinsight-administer-use-management-portal.md#connect-to-hdinsight-clusters-by-using-rdp)

* **HDInsight baseado em Linux**: conecte-se usando SSH ([o comando SSH](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster) ou [PuTTY](hdinsight-hadoop-linux-use-ssh-windows.md#connect-to-a-linux-based-hdinsight-cluster))

Uma vez conectado, você pode usar a seguinte sintaxe para carregar um arquivo no armazenamento.

	hadoop -copyFromLocal <localFilePath> <storageFilePath>

Por exemplo, `hadoop fs -copyFromLocal data.txt /example/data/data.txt`

Como o sistema de arquivos padrão para o HDInsight está no armazenamento de Blob do Azure, /example/data.txt está, na verdade, no armazenamento de Blob do Azure. Você também pode fazer referência ao arquivo como:

	wasb:///example/data/data.txt

ou

	wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

Para obter uma lista dos outros comandos Hadoop que trabalham com os arquivos, consulte [http://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html](http://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html)

##Clientes gráficos

Também há vários aplicativos que fornecem uma interface gráfica para trabalhar com o Armazenamento do Azure. Segue uma lista de alguns desses aplicativos:

| Cliente | Linux | OS X | Windows |
| ------ |:-----:|:----:|:-------:|
| [Gerenciador de Armazenamento do Azure](http://storageexplorer.com/) | ✔ | ✔ | ✔ |
| [Cloud Storage Studio 2](http://www.cerebrata.com/Products/CloudStorageStudio/) | | | ✔ |
| [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer) | | | ✔ |
| [Azure Explorer](http://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | | ✔ |
| [Zudio](https://zudio.co/) | ✔ | ✔ | ✔ |
| [Cyberduck](https://cyberduck.io/) | | ✔ | ✔ |

###<a id="storageexplorer"></a>Gerenciador de Armazenamento do Azure

O *Explorador de Armazenamento do Azure* é uma ferramenta útil para inspecionar e alterar os dados nos blobs. É uma ferramenta de software livre que pode ser baixada em [http://storageexplorer.com/](http://storageexplorer.com/). O código-fonte está disponível também neste link.

Para usar a ferramenta, conheça sua chave e seu nome da conta de armazenamento do Azure. Para saber mais sobre como obter essas informações, confira a seção "Como exibir, copiar e regenerar chaves de acesso de armazenamento” em [Criar, gerenciar ou excluir uma conta de armazenamento][azure-create-storage-account].

1. Execute o Azure Storage Explorer. Se esta for a primeira vez que você executou o Explorador de Armazenamento, deverá inserir o ___nome da conta de Armazenamento\___ e a __chave da conta de armazenamento__. Se você tiver o antes, use botão __Adicionar__ para adicionar um novo nome de conta de armazenamento e chave.

    Insira o nome e a chave da conta de armazenamento usada para o seu cluster HDinsight e selecione __Salvar & Abrir__.

	![HDI.AzureStorageExplorer][image-azure-storage-explorer]

5. Na lista de contêineres, clique no nome do contêiner que está associado ao cluster HDInsight. Por padrão, esse é o nome do cluster do HDInsight, mas pode ser diferente se você inseriu um nome específico ao criar o cluster.

6. Na barra de ferramentas, selecione o ícone de upload.

    ![Barra de ferramentas com ícone de upload destacado](./media/hdinsight-upload-data/toolbar.png)

7. Especifique um arquivo para carregar e **Abrir**. Quando solicitado, selecione __Carregar__ para carregar o arquivo para a raiz do contêiner de armazenamento. Se você deseja carregar o arquivo em um caminho específico, digite o caminho no campo __Destino__ e, em seguida, selecione __Carregar__.

    ![Caixa de diálogo de upload do arquivo](./media/hdinsight-upload-data/fileupload.png)
    
    Depois que o arquivo terminar de carregar, você pode usá-lo por meio dos trabalhos no cluster HDInsight.

##Montar o Armazenamento de Blob do Azure como uma unidade Local

Confira [Montar o Armazenamento de Blob do Azure como uma unidade Local](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/09/mount-azure-blob-storage-as-local-drive.aspx).

##Serviços

###Fábrica de dados do Azure

A Fábrica de Dados do Azure é um serviço completamente gerenciado para compor serviços de armazenamento, processamento e movimentação de dados em pipelines de produção de dados simplificada, escalonável e confiável.

O Azure Data Factory pode ser usado para mover dados para o Armazenamento de Blob do Azure, ou para criar pipelines de dados que usam diretamente os recursos do HDInsight como Hive e Pig.

Para obter mais informações, consulte a [Documentação do Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/).

###<a id="sqoop"></a>Apache Sqoop

O Sqoop é uma ferramenta desenvolvida para transferir dados entre bancos de dados relacionais e o Hadoop. Você pode usá-lo para importar dados de um RDBMS (sistema de gerenciamento de banco de dados relacional), como SQL Server, MySQL ou Oracle para o HDFS (Sistema de Arquivos Distribuído) do Hadoop, transformar os dados no Hadoop com o MapReduce ou o Hive e, em seguida, exportar os dados de volta para um RDBMS.

Para obter mais informações, consulte [Usar Sqoop com HDInsight][hdinsight-use-sqoop].

##SDKs de desenvolvimento

O Armazenamento de Blob do Azure também pode ser acessado usando um SDK do Azure com as seguintes linguagens de programação:

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

Para obter mais informações sobre como instalar os SDKs do Azure, consulte [Downloads do Azure](https://azure.microsoft.com/downloads/)


## Próximas etapas
Agora que você compreende como obter dados no HDInsight, leia os seguintes artigos para aprender a executar uma análise:

* [Introdução ao Azure HDInsight][hdinsight-get-started]
* [Enviar trabalhos do Hadoop de forma programática][hdinsight-submit-jobs]
* [Usar o Hive com o HDInsight][hdinsight-use-hive]
* [Usar o Pig com o HDInsight][hdinsight-use-pig]




[azure-management-portal]: https://porta.azure.com
[azure-powershell]: http://msdn.microsoft.com/library/windowsazure/jj152841.aspx

[azure-storage-client-library]: /develop/net/how-to-guides/blob-storage/
[azure-create-storage-account]: ../storage/storage-create-storage-account.md
[azure-azcopy-download]: ../storage/storage-use-azcopy.md
[azure-azcopy]: ../storage/storage-use-azcopy.md

[hdinsight-use-sqoop]: hdinsight-use-sqoop.md

[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-provision]: hdinsight-provision-clusters.md

[sqldatabase-create-configure]: ../sql-database-create-configure.md

[apache-sqoop-guide]: http://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

[Powershell-install-configure]: ../powershell-install-configure.md

[azurecli]: ../xplat-cli-install.md


[image-azure-storage-explorer]: ./media/hdinsight-upload-data/HDI.AzureStorageExplorer.png
[image-ase-addaccount]: ./media/hdinsight-upload-data/HDI.ASEAddAccount.png
[image-ase-blob]: ./media/hdinsight-upload-data/HDI.ASEBlob.png

<!---HONumber=AcomDC_0427_2016-->