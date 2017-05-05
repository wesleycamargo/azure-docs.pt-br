---
title: Carregar dados para trabalhos do Hadoop no HDInsight | Microsoft Docs
description: Saiba como carregar e acessar os dados de trabalhos do Hadoop no HDInsight usando o Azure CLI, Azure Storage Explorer, Azure PowerShell, a linha de comando do Hadoop ou o Sqoop.
services: hdinsight,storage
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 56b913ee-0f9a-4e9f-9eaf-c571f8603dd6
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 0caac0c7ad76df7e7fa657227339d1df8b4a3c77
ms.lasthandoff: 04/27/2017


---
# <a name="upload-data-for-hadoop-jobs-in-hdinsight"></a>Carregar dados para trabalhos do Hadoop no HDInsight
O Azure HDInsight oferece um HDFS (Sistema de Arquivos Distribuído) do Hadoop completo no armazenamento de blob do Azure. Ele foi projetado como uma extensão HDFS para fornecer uma experiência perfeita aos clientes. Ele permite que o conjunto completo de componentes no ecossistema do Hadoop opere diretamente nos dados que ele gerencia. O armazenamento de blob do Azure e o HDFS são sistemas de arquivos distintos otimizados para armazenamento de dados e computação nesses dados. Para ver informações sobre os benefícios de usar o Armazenamento de Blobs do Azure, consulte [Usar o Armazenamento de Blobs do Azure com o HDInsight][hdinsight-storage].

**Pré-requisitos**

Observe os seguintes requisitos antes de começar:

* Um cluster Azure HDInsight. Para obter instruções, consulte [Introdução ao Azure HDInsight][hdinsight-get-started] ou [Provisionar clusters HDInsight][hdinsight-provision].

## <a name="why-blob-storage"></a>Por que o armazenamento de blob?
Os clusters do Azure HDInsight normalmente são implantados para executar trabalhos do MapReduce e são removidos quando esses trabalhos são concluídos. A manutenção dos dados nos clusters HDFS após a conclusão dos cálculos seria uma maneira cara de armazenar esses dados. O armazenamento de Blob do Azure é altamente disponível, escalonável, com alta capacidade, baixo custo e uma opção de armazenamento compartilhável para dados a serem processados usando o HDInsight. O armazenamento de dados em um blob permite que os clusters HDInsight usados para computação sejam liberados sem que ocorra perda de dados.

### <a name="directories"></a>Diretórios
Os contêineres de armazenamento de blob do Azure armazenam dados como pares de chave/valor e não há nenhuma hierarquia de diretório. No entanto, o caractere "/" pode ser usado dentro do nome de chave para parecer que um arquivo está armazenado em uma estrutura de diretório. O HDInsight os vê como se fossem diretórios reais.

Por exemplo, a chave de um blob pode ser *input/log1.txt*. Não existe nenhum diretório de "entrada" real, mas devido à presença do caractere "/" no nome da chave, ele parece um caminho de arquivo.

Por isso, se você usar as ferramentas do Gerenciador do Azure, poderá perceber alguns arquivos de 0 byte. Esses arquivos têm duas finalidades:

* Se houver pastas vazias, eles marcam a existência da pasta. O Armazenamento de blob do Azure é inteligente o suficiente para saber que se existir um blob denominado foo/bar, haverá uma pasta chamada **foo**. Mas se você desejar ter uma pasta vazia chamada **foo** , a única maneira de indicar isso é tendo esse arquivo de 0 byte especial no lugar.
* Elas contêm alguns metadados especiais de que o sistema de arquivos Hadoop precisa, especialmente as permissões e os proprietários das pastas.

## <a name="command-line-utilities"></a>Utilitários de linha de comando
A Microsoft fornece os seguintes utilitários para trabalhar com armazenamento de Blob do Azure:

| Ferramenta | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Interface de Linha de Comando do Azure][azurecli] |✔ |✔ |✔ |
| [Azure PowerShell][azure-powershell] | | |✔ |
| [AzCopy][azure-azcopy] | | |✔ |
| [Comando do Hadoop](#commandline) |✔ |✔ |✔ |

> [!NOTE]
> Enquanto o CLI do Azure, o Azure PowerShell e o AzCopy podem ser usados fora do Azure, o comando Hadoop só está disponível no cluster HDInsight e só permite carregar dados do sistema de arquivos local para o armazenamento de Blob do Azure.
>
>

### <a id="xplatcli"></a>Azure CLI
O Azure CLI é uma ferramenta de plataforma cruzada que permite que você gerencie os serviços do Azure. Use as seguintes etapas para carregar dados no armazenamento de Blob do Azure:

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

1. [Instalar e configurar o Azure CLI para Mac, Linux e Windows](../cli-install-nodejs.md).
2. Abra um prompt de comando, bash ou outro shell e use os dados a seguir para autenticar a sua assinatura do Azure.

        azure login

    Quando solicitado, insira o nome de usuário e senha para a sua assinatura.
3. Use o seguinte comando para listar sites para as contas de armazenamento da sua assinatura:

        azure storage account list
4. Selecione a conta de armazenamento que contém o blob com que você deseja trabalhar e use o seguinte comando para recuperar a chave para esta conta:

        azure storage account keys list <storage-account-name>

    Isso deve retornar as chaves **Primária** e **Secundária**. Copie o valor de chave **Primária** , já que ele será usado nas próximas etapas.
5. Use o seguinte comando para recuperar uma lista de contêineres de blob na conta de armazenamento:

        azure storage container list -a <storage-account-name> -k <primary-key>
6. Use os seguintes comandos para carregar e baixar arquivos para o blob:

   * Para carregar um arquivo:

           azure storage blob upload -a <storage-account-name> -k <primary-key> <source-file> <container-name> <blob-name>
   * Para baixar um arquivo:

           azure storage blob download -a <storage-account-name> -k <primary-key> <container-name> <blob-name> <destination-file>

> [!NOTE]
> Se você for sempre trabalhar com a mesma conta de armazenamento, pode definir as seguintes variáveis de ambiente em vez de especificar a conta e a chave para cada comando:
>
> * **AZURE\_STORAGE\_ACCOUNT**: o nome da conta de armazenamento
> * **AZURE\_STORAGE\_ACCESS\_KEY**: a chave da conta de armazenamento
>
>

### <a id="powershell"></a>PowerShell do Azure
O Azure PowerShell é um ambiente de script que você pode usar para controlar e automatizar a implantação e o gerenciamento de suas cargas de trabalho no Azure. Para saber mais sobre como configurar sua estação de trabalho para executar o PowerShell do Azure, consulte [Instalar e configurar o PowerShell do Azure](/powershell/azure/overview).

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell.md)]

**Para carregar um arquivo local para o Armazenamento de Blob do Azure**

1. Abra o console do PowerShell do Azure conforme instruído em [Instalar e configurar o PowerShell do Azure](/powershell/azure/overview).
2. Defina os valores das cinco primeiras variáveis no script a seguir:

        $resourceGroupName = "<AzureResourceGroupName>"
        $storageAccountName = "<StorageAccountName>"
        $containerName = "<ContainerName>"

        $fileName ="<LocalFileName>"
        $blobName = "<BlobName>"

        # Get the storage account key
        $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName)[0].Value
        # Create the storage context object
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

        # Copy the file from local workstation to the Blob container
        Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob $blobName -context $destContext
3. Cole o script no console do Azure PowerShell para executá-lo para copiar o arquivo.

Por exemplo, os scripts do PowerShell criados para funcionar com o HDInsight, consulte [Ferramentas HDInsight](https://github.com/blackmist/hdinsight-tools).

### <a id="azcopy"></a>AzCopy
O AzCopy é uma ferramenta de linha de comando destinada a simplificar a tarefa de transferir dados de e para uma conta de Armazenamento do Azure. Você pode usá-lo como uma ferramenta independente ou incorporar essa ferramenta em um aplicativo existente. [Baixar o AzCopy][azure-azcopy-download].

A sintaxe do AzCopy é:

    AzCopy <Source> <Destination> [filePattern [filePattern...]] [Options]

Para saber mais, consulte [AzCopy – Carregando/baixando arquivos para Blobs do Azure][azure-azcopy].

### <a id="commandline"></a>Linha de comando do Hadoop
A linha de comando do Hadoop só é útil para armazenar dados no armazenamento de blob quando os dados já estão presentes no nó principal do cluster.

Para usar o comando Hadoop, primeiro você deve se conectar ao nó de cabeçalho usando um dos seguintes métodos:

* **HDInsight baseado em Windows**: [conecte-se usando a área de trabalho remota](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp)
* **HDInsight baseado em Linux**: conecte-se usando SSH ([o comando SSH](hdinsight-hadoop-linux-use-ssh-unix.md) ou [PuTTY](hdinsight-hadoop-linux-use-ssh-windows.md))

Uma vez conectado, você pode usar a seguinte sintaxe para carregar um arquivo no armazenamento.

    hadoop -copyFromLocal <localFilePath> <storageFilePath>

Por exemplo, `hadoop fs -copyFromLocal data.txt /example/data/data.txt`

Como o sistema de arquivos padrão para o HDInsight está no armazenamento de Blob do Azure, /example/data.txt está, na verdade, no armazenamento de Blob do Azure. Você também pode fazer referência ao arquivo como:

    wasbs:///example/data/data.txt

ou o

    wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

Para obter uma lista dos outros comandos Hadoop que trabalham com os arquivos, consulte [http://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html](http://hadoop.apache.org/docs/r2.7.0/hadoop-project-dist/hadoop-common/FileSystemShell.html)

> [!WARNING]
> Em clusters HBase, o tamanho do bloco padrão usado na gravação de dados é de 256 KB. Embora isso funcione bem com APIs HBase ou APIs REST, usar os comandos `hadoop` ou `hdfs dfs` para gravar dados com mais de, aproximadamente, 12 GB resulta em um erro. Confira a seção abaixo [Exceção de armazenamento para gravar no blob](#storageexception) para obter mais informações.
>
>

## <a name="graphical-clients"></a>Clientes gráficos
Também há vários aplicativos que fornecem uma interface gráfica para trabalhar com o Armazenamento do Azure. Segue uma lista de alguns desses aplicativos:

| Cliente | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Ferramentas do Microsoft Visual Studio para HDInsight](hdinsight-hadoop-visual-studio-tools-get-started.md#navigate-the-linked-resources) |✔ |✔ |✔ |
| [Gerenciador de Armazenamento do Azure](http://storageexplorer.com/) |✔ |✔ |✔ |
| [Cloud Storage Studio 2](http://www.cerebrata.com/Products/CloudStorageStudio/) | | |✔ |
| [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer) | | |✔ |
| [Azure Explorer](http://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | |✔ |
| [Cyberduck](https://cyberduck.io/) | |✔ |✔ |

### <a name="visual-studio-tools-for-hdinsight"></a>Ferramentas do Visual Studio para HDInsight
Para saber mais, veja [Navegar nos recursos vinculados](hdinsight-hadoop-visual-studio-tools-get-started.md#navigate-the-linked-resources).

### <a id="storageexplorer"></a>Gerenciador de Armazenamento do Azure
*Gerenciador de Armazenamento do Azure* é uma ferramenta útil para inspecionar e alterar os dados nos blobs. É uma ferramenta de software livre que pode ser baixada em [http://storageexplorer.com/](http://storageexplorer.com/). O código-fonte está disponível também neste link.

Para usar a ferramenta, conheça sua chave e seu nome da conta de armazenamento do Azure. Para saber mais sobre como obter essas informações, confira a seção “Como exibir, copiar e regenerar chaves de acesso de armazenamento” em [Criar, gerenciar ou excluir uma conta de armazenamento][azure-create-storage-account].

1. Execute o Azure Storage Explorer. Se esta for a primeira vez que você executou o Gerenciador de Armazenamento, será solicitado que você insira o **_Nome da conta de armazenamento** e a **Chave da conta de armazenamento**. Caso o tenha executado antes, use botão **Adicionar** para adicionar um novo nome e chave de conta de armazenamento.

    Insira o nome e a chave da conta de armazenamento usados pelo cluster HDInsight e selecione **SALVAR E ABRIR**.

    ![HDI.AzureStorageExplorer][image-azure-storage-explorer]
2. Na lista de contêineres, clique no nome do contêiner que está associado ao cluster HDInsight. Por padrão, esse é o nome do cluster do HDInsight, mas pode ser diferente se você inseriu um nome específico ao criar o cluster.
3. Na barra de ferramentas, selecione o ícone de upload.

    ![Barra de ferramentas com ícone de upload destacado](./media/hdinsight-upload-data/toolbar.png)
4. Especifique um arquivo para carregar e **Abrir**. Quando solicitado, selecione **Carregar** para carregar o arquivo para a raiz do contêiner de armazenamento. Se você desejar carregar o arquivo em um caminho específico, digite o caminho no campo **Destino** e selecione **Carregar**.

    ![Caixa de diálogo de upload do arquivo](./media/hdinsight-upload-data/fileupload.png)

    Depois que o arquivo terminar de carregar, você pode usá-lo por meio dos trabalhos no cluster HDInsight.

## <a name="mount-azure-blob-storage-as-local-drive"></a>Montar o Armazenamento de Blob do Azure como uma unidade Local
Confira [Montar o Armazenamento de Blob do Azure como uma unidade Local](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/09/mount-azure-blob-storage-as-local-drive.aspx).

## <a name="services"></a>Serviços
### <a name="azure-data-factory"></a>Fábrica de dados do Azure
A Fábrica de Dados do Azure é um serviço completamente gerenciado para compor serviços de armazenamento, processamento e movimentação de dados em pipelines de produção de dados simplificada, escalonável e confiável.

O Azure Data Factory pode ser usado para mover dados para o Armazenamento de Blob do Azure, ou para criar pipelines de dados que usam diretamente os recursos do HDInsight como Hive e Pig.

Para obter mais informações, consulte a [Documentação do Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/).

### <a id="sqoop"></a>Apache Sqoop
O Sqoop é uma ferramenta desenvolvida para transferir dados entre bancos de dados relacionais e o Hadoop. Você pode usá-lo para importar dados de um RDBMS (sistema de gerenciamento de banco de dados relacional), como SQL Server, MySQL ou Oracle para o HDFS (Sistema de Arquivos Distribuído) do Hadoop, transformar os dados no Hadoop com o MapReduce ou o Hive e, em seguida, exportar os dados de volta para um RDBMS.

Para obter mais informações, consulte [Usar Sqoop com HDInsight][hdinsight-use-sqoop].

## <a name="development-sdks"></a>SDKs de desenvolvimento
O Armazenamento de Blob do Azure também pode ser acessado usando um SDK do Azure com as seguintes linguagens de programação:

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

Para obter mais informações sobre como instalar os SDKs do Azure, consulte [Downloads do Azure](https://azure.microsoft.com/downloads/)

## <a name="troubleshooting"></a>Solucionar problemas
### <a id="storageexception"></a>Exceção de armazenamento para gravar no blob
**Sintomas**: ao usar os comandos `hadoop` ou `hdfs dfs` para gravar arquivos que tenham aproximadamente 12 GB ou mais em um cluster HBase, você pode encontrar o seguinte erro:

    ERROR azure.NativeAzureFileSystem: Encountered Storage Exception for write on Blob : example/test_large_file.bin._COPYING_ Exception details: null Error Code : RequestBodyTooLarge
    copyFromLocal: java.io.IOException
            at com.microsoft.azure.storage.core.Utility.initIOException(Utility.java:661)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:366)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:350)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:471)
            at java.util.concurrent.FutureTask.run(FutureTask.java:262)
            at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
            at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
            at java.lang.Thread.run(Thread.java:745)
    Caused by: com.microsoft.azure.storage.StorageException: The request body is too large and exceeds the maximum permissible limit.
            at com.microsoft.azure.storage.StorageException.translateException(StorageException.java:89)
            at com.microsoft.azure.storage.core.StorageRequest.materializeException(StorageRequest.java:307)
            at com.microsoft.azure.storage.core.ExecutionEngine.executeWithRetry(ExecutionEngine.java:182)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlockInternal(CloudBlockBlob.java:816)
            at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlock(CloudBlockBlob.java:788)
            at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:354)
            ... 7 more

**Causa**: os clusters HBase no HDInsight são padronizados para um tamanho de bloco de 256 KB na gravação no armazenamento do Azure. Embora isso funcione para APIs HBase ou APIs REST, resultará em erro no uso de utilitários de linha comando `hadoop` ou `hdfs dfs`.

**Resolução**: use `fs.azure.write.request.size` para especificar um tamanho de bloco maior. Isso pode ser feito a cada uso com o parâmetro `-D`. Veja a seguir um exemplo de uso desse parâmetro com o comando `hadoop`:

    hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data

Você também pode aumentar o valor de `fs.azure.write.request.size` globalmente usando Ambari. As etapas a seguir podem ser usadas para alterar o valor na interface de usuário do Ambari Web:

1. No navegador, acesse a interface de usuário do Ambari Web para seu cluster. Isto é, https://CLUSTERNAME.azurehdinsight.net, onde **CLUSTERNAME** é o nome do seu cluster.

    Quando solicitado, insira o nome de administrador e a senha de administrador para o cluster.
2. No lado esquerdo da tela, escolha **HDFS** e selecione a guia **Configurações**.
3. No campo **Filtrar...**, insira `fs.azure.write.request.size`. Isso exibirá o campo e o valor atual no meio da página.
4. Altere o valor de 262144 (256 KB) para o novo valor. Por exemplo, 4194304 (4 MB).

![Imagem de alteração de valor por meio da interface de usuário do Ambari Web](./media/hdinsight-upload-data/hbase-change-block-write-size.png)

Para saber mais sobre como usar o Ambari, confira [Gerenciar clusters HDInsight interface de usuário do Ambari Web](hdinsight-hadoop-manage-ambari.md).

## <a name="next-steps"></a>Próximas etapas
Agora que você compreende como obter dados no HDInsight, leia os seguintes artigos para aprender a executar uma análise:

* [Introdução ao Azure HDInsight][hdinsight-get-started]
* [Enviar trabalhos Hadoop de forma programática][hdinsight-submit-jobs]
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
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md

[sqldatabase-create-configure]: ../sql-database-create-configure.md

[apache-sqoop-guide]: http://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs

[azurecli]: ../cli-install-nodejs.md


[image-azure-storage-explorer]: ./media/hdinsight-upload-data/HDI.AzureStorageExplorer.png
[image-ase-addaccount]: ./media/hdinsight-upload-data/HDI.ASEAddAccount.png
[image-ase-blob]: ./media/hdinsight-upload-data/HDI.ASEBlob.png

