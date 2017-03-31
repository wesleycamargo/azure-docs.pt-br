---
title: Desenvolver trabalhos de Python MapReduce com HDInsight | Microsoft Docs
description: Saiba como criar e executar trabalhos Python MapReduce em clusters HDInsight baseados em Linux.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7631d8d9-98ae-42ec-b9ec-ee3cf7e57fb3
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/06/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 6ce490fb903d4ed2177b95145bb98fb3eeb0654f
ms.lasthandoff: 03/25/2017


---
# <a name="develop-python-streaming-programs-for-hdinsight"></a>Desenvolver programas de transmissão do Python para HDInsight

O Hadoop fornece uma API de streaming para o MapReduce que permite que você escreva funções de mapeamento e redução em outras linguagens além do Java. Neste artigo, você aprenderá como usar o Python para executar operações de MapReduce.

Esse artigo se baseia em informações e exemplos publicados por Michael Noll em [Escrevendo um programa de MapReduce do Hadoop em Python](http://www.michael-noll.com/tutorials/writing-an-hadoop-mapreduce-program-in-python/).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas neste artigo, você precisará do seguinte:

* Um Hadoop baseado em Linux no cluster HDInsight

  > [!IMPORTANT]
  > As etapas deste documento exigem um cluster HDInsight que usa Linux. O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para saber mais, veja [Substituição do HDInsight no Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

* Um editor de texto
  
  > [!IMPORTANT]
  > O editor de texto deve usar LF com fim de linha. Se usar CRLF, isso causará erros ao executar o trabalho MapReduce em clusters HDInsight baseados em Linux. Se você não tiver certeza, use a etapa opcional na seção [Executar MapReduce](#run-mapreduce) para converter qualquer CRLF em LF.

* **Familiaridade com o SSH e o SCP**. Para obter mais informações, confira [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="word-count"></a>Contagem de palavras

Para este exemplo, você implementará uma contagem básica de palavras usando um mapeador e um redutor. O mapeador divide frases em palavras individuais e o redutor agrega as palavras e contagens para produzir a saída.

O fluxograma a seguir ilustra o que acontece durante o mapa e reduz as fases.

![ilustração da redução do mapa](./media/hdinsight-hadoop-streaming-python/HDI.WordCountDiagram.png)

## <a name="why-python"></a>Por que Python?

Python é uma linguagem de programação de alto nível para fins gerais que permite expressar conceitos em menos linhas de código que muitas outras linguagens. Ele recentemente se tornou popular com os cientistas de dados como uma linguagem de criação de protótipos, graças à sua natureza interpretada, digitação dinâmica e sintaxe elegante o torna adequado para o método RAD.

Python é instalado em todos os clusters HDInsight.

## <a name="streaming-mapreduce"></a>Transmissão do MapReduce

O Hadoop permite que você especifique um arquivo que contém o mapa e a lógica de redução usada por um trabalho. Os requisitos específicos para o mapa e lógica de redução são:

* **Entrada**- os componentes mapa e reduzir devem ler dados de entrada de STDIN.
* **Saída**- os componentes mapa e reduzir devem gravar os dados de saída para STDOUT.
* **Formato de dados**- os dados consumidos e produzidos devem ser um par chave/valor, separado por um caractere de tabulação.

O Python pode facilmente atender a esses requisitos usando o módulo **sys** para ler do STDIN e usando **print** para imprimir para o STDOUT. O restante é simplesmente formatar os dados com um caractere de tabulação (`\t`) entre a chave e o valor.

## <a name="create-the-mapper-and-reducer"></a>Criar o mapeador e redutor

O mapeador e o redutor são arquivos de texto, nesse caso, **mapper.py** e **reducer.py** (para deixar claro o que cada um faz). Você pode criá-los usando o editor de sua escolha.

### <a name="mapperpy"></a>Mapper.py

Crie um novo arquivo chamado **mapper.py** e use o seguinte código como o conteúdo:

```python
#!/usr/bin/env python

# Use the sys module
import sys

# 'file' in this case is STDIN
def read_input(file):
    # Split each line into words
    for line in file:
        yield line.split()

def main(separator='\t'):
    # Read the data using read_input
    data = read_input(sys.stdin)
    # Process each words returned from read_input
    for words in data:
        # Process each word
        for word in words:
            # Write to STDOUT
            print '%s%s%d' % (word, separator, 1)

if __name__ == "__main__":
    main()
```

Dedique uns momentos para ler o código e entender o que ele faz.

### <a name="reducerpy"></a>reducer.py

Crie um novo arquivo chamado **reducer.py** e use o seguinte código como o conteúdo:

```python
#!/usr/bin/env python

# import modules
from itertools import groupby
from operator import itemgetter
import sys

# 'file' in this case is STDIN
def read_mapper_output(file, separator='\t'):
    # Go through each line
    for line in file:
        # Strip out the separator character
        yield line.rstrip().split(separator, 1)

def main(separator='\t'):
    # Read the data using read_mapper_output
    data = read_mapper_output(sys.stdin, separator=separator)
    # Group words and counts into 'group'
    #   Since MapReduce is a distributed process, each word
    #   may have multiple counts. 'group' will have all counts
    #   which can be retrieved using the word as the key.
    for current_word, group in groupby(data, itemgetter(0)):
        try:
            # For each word, pull the count(s) for the word
            #   from 'group' and create a total count
            total_count = sum(int(count) for current_word, count in group)
            # Write to stdout
            print "%s%s%d" % (current_word, separator, total_count)
        except ValueError:
            # Count was not a number, so do nothing
            pass

if __name__ == "__main__":
    main()
```

## <a name="upload-the-files"></a>Carregar os arquivos

Ambos, **mapper.py** e **reducer.py**, devem estar no nó principal do cluster antes que o cluster possa executá-los. Esta seção traz um exemplo de comando `scp` e um script do Azure PowerShell que pode ser usado para carregar os arquivos para o cluster.

> [!IMPORTANT]
> Há uma diferença importante entre o uso do `scp` e do PowerShell para carregar os arquivos:
>
> * Se usar o `scp`, você vai colocar os arquivos no nó principal do cluster. Isso pressupõe que, posteriormente, você se conectará ao nó principal e executará o trabalho em uma sessão SSH.
> * Se usar o script do PowerShell, você vai colocar os arquivos no armazenamento padrão do cluster. Isso pressupõe, posteriormente, você usará um script do PowerShell para executar o trabalho de um cliente remoto.

### <a name="upload-using-scp"></a>Carregar usando o SCP

No ambiente de desenvolvimento, no mesmo diretório do **mapper.py** e do **reducer.py**, use o comando a seguir. Substitua **nome de usuário** pelo usuário do SSH do cluster, e substitua **nome do cluster** pelo nome do cluster.

`scp mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:`

Isso copiará os arquivos do sistema local para o nó principal.

> [!NOTE]
> Se você usou uma senha para proteger sua conta SSH, você será solicitado pela senha. Se você usou uma chave SSH, talvez precise usar o parâmetro `-i` e o caminho para a chave privada, por exemplo `scp -i /path/to/private/key mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:`.

### <a name="upload-using-powershell"></a>Carregar usando o PowerShell

1. No ambiente de desenvolvimento, crie um novo arquivo chamado `Put-FilesInHDInsight.ps1` e use os itens abaixo como o conteúdo do arquivo:

    ```PowerShell
    param(
        [Parameter(Mandatory = $true)]
        [String]$clusterName,
        [Parameter(Mandatory = $true)]
        [String]$source,
        [Parameter(Mandatory = $true)]
        [String]$destination
    )
    # Login to your Azure subscription
    # Is there an active Azure subscription?
    $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Add-AzureRmAccount
    }

    # Get the cluster info
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $storageInfo = $clusterInfo.DefaultStorageAccount.split('.')

    # What type of default storage are we using?
    switch ($storageInfo[1])
    {
        "blob" {
            # Get the blob storage information for the cluster
            $resourceGroup = $clusterInfo.ResourceGroup
            $storageAccountName=$storageInfo[0]
            $storageContainer=$clusterInfo.DefaultStorageContainer
            $storageAccountKey=(Get-AzureRmStorageAccountKey `
                -Name $storageAccountName `
                -ResourceGroupName $resourceGroup)[0].Value
            # Create a storage context and upload the file
            $context = New-AzureStorageContext `
                -StorageAccountName $storageAccountName `
                -StorageAccountKey $storageAccountKey
            # Upload the file
            Set-AzureStorageBlobContent `
                -File $source `
                -Blob $destination `
                -Container $storageContainer `
                -Context $context
        }
        "azuredatalakestore" {
            # Get the Data Lake Store name
            $dataLakeStoreName=$storageInfo[0]
            # Get the root of the HDInsight cluster azuredatalakestore
            $clusterRoot=$clusterInfo.DefaultStorageRootPath
            # Upload the file. Prepend the destination with the cluster root
            Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $source -Destination "$clusterRoot$destination"
        }
        default {
            Throw "Unknown storage type: $storageInfo[1]"
        }
    }
    ```

2. Para usar esse script para carregar um arquivo, use os itens abaixo em um prompt do Azure PowerShell:

    `.\Put-FilesInHDInsight.ps1 -clusterName <your HDInsight cluster name> -source mapper.py -destination mapper.py`

    Quando necessário, insira as credenciais de logon HTTPS para o cluster.

3. Repita o comando, substituindo `mapper.py` por `reducer.py`. Isso carregará os arquivos do mapeador e do redutor no armazenamento padrão do cluster.

## <a name="run-mapreduce-ssh"></a>Executar MapReduce (SSH)

Use as seguintes etapas para se conectar ao cluster e executar o trabalho de streaming MapReduce em uma sessão SSH.

1. Conecte-se ao cluster usando o SSH:
   
   `ssh username@clustername-ssh.azurehdinsight.net`
   
   > [!NOTE]
   > Se você usou uma senha para proteger sua conta SSH, você será solicitado pela senha. Se você usou uma chave SSH, talvez precise usar o parâmetro `-i` e o caminho para a chave privada, por exemplo `ssh -i /path/to/private/key username@clustername-ssh.azurehdinsight.net`.

2. (Opcional) Se você usou um editor de texto que usa CRLF como fim de linha ao criar os arquivos mapper.py e reducer.py, ou não souber qual fim de linha seu editor usa, use os seguintes comandos para converter as ocorrências de CRLF em mapper.py e reducer.py para LF.
   
    `perl -pi -e 's/\r\n/\n/g' mappery.py`
    `perl -pi -e 's/\r\n/\n/g' reducer.py`

3. Use o seguinte comando para iniciar o trabalho MapReduce.
   
    `yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files mapper.py,reducer.py -mapper mapper.py -reducer reducer.py -input /example/data/gutenberg/davinci.txt -output /example/wordcountout`
   
    Esse comando tem as seguintes partes:
   
   * **hadoop-streaming.jar**: usado ao executar operações de transmissão do MapReduce. Faz interfaces do Hadoop com o código externo do MapReduce fornecido por você.

   * **-files**: informa o Hadoop que os arquivos especificados são necessários para este trabalho do MapReduce e devem ser copiados para todos os nós do trabalho.

   * **-mapper**: informa o Hadoop qual arquivo usar como mapeador.

   * **-reducer**: informa o Hadoop qual arquivo usar como redutor.

   * **-input**: o arquivo de entrada por meio do qual devemos contar palavras.

   * **-output**: o diretório no qual a saída será gravada.
     
     > [!NOTE]
     > Esse diretório será criado pelo trabalho.

Você deverá ver uma série de instruções **INFO** quando o trabalho for iniciado e, por fim, as operações **map** e **reduce** exibidas como porcentagens.

    15/02/05 19:01:04 INFO mapreduce.Job:  map 0% reduce 0%
    15/02/05 19:01:16 INFO mapreduce.Job:  map 100% reduce 0%
    15/02/05 19:01:27 INFO mapreduce.Job:  map 100% reduce 100%

Por fim, você receberá informações de status sobre o trabalho quando ele for concluído.

## <a name="run-mapreduce-powershell"></a>Executar MapReduce (PowerShell)

Use as seguintes etapas para executar o trabalho de streaming MapReduce do PowerShell no seu ambiente de desenvolvimento. O script do PowerShell executa o trabalho conectando o cluster do HDInsight usando o WebHCat.

1. Crie um novo arquivo chamado `Start-HDInsightStreamingPythonJob` e use os itens abaixo como o seu conteúdo:

    ```PowerShell
    param(
        [Parameter(Mandatory = $true)]
        [String]$clusterName,
        [Parameter(Mandatory = $true)]
        [String]$inputPath,
        [Parameter(Mandatory = $true)]
        [String]$outputPath
    )
    # Login to your Azure subscription
    # Is there an active Azure subscription?
    $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Add-AzureRmAccount
    }

    # Get the login (HTTPS) credentials for the cluster
    $creds=Get-Credential -Message "Enter the login for the cluster" -UserName "admin"

    # Create the streaming job definition
    # Note: This assumes that the mapper.py and reducer.py
    #       are in the root of default storage. If you put them in a 
    #       subdirectory, change the -Files parameter to the correct path.
    $jobDefinition = New-AzureRmHDInsightStreamingMapReduceJobDefinition `
        -Files "/mapper.py", "/reducer.py" `
        -Mapper "mapper.py" `
        -Reducer "reducer.py" `
        -InputPath $inputPath `
        -OutputPath $outputPath

    # Start the job
    $job = Start-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $jobDefinition `
        -HttpCredential $creds

    # Wait for the job to complete
    Wait-AzureRmHDInsightJob `
        -JobId $job.JobId `
        -ClusterName $clusterName `
        -HttpCredential $creds
    ```

2. Use os itens abaixo em um prompt do Azure PowerShell para executar o trabalho:

    `.\Start-HDInsightStreamingPythonJob.ps1 -clusterName <your HDInsight cluster name> -inputPath "/example/data/gutenberg/davinci.txt" -outputPath "/example/wordcountout"`

    Isso usará os arquivos `mapper.py` e `reducer.py`, carregados anteriormente para o cluster, para contar as palavras no arquivo `davinci.txt`. A saída fica na pasta `/example/wordcount` do armazenamento padrão do cluster.

    Quando o trabalho for concluído, deverá exibir informações semelhantes às seguintes:

    ```
    Cluster         : myhdicluster
    HttpEndpoint    : myhdicluster.azurehdinsight.net
    State           : SUCCEEDED
    JobId           : job_1486046226168_0004
    ParentId        :
    PercentComplete : map 100% reduce 100%
    ExitValue       : 0
    User            : admin
    Callback        :
    Completed       : done
    StatusFolder    : 2017-02-06T19-14-28-a3dda3ca-f489-4287-afc9-b5e16e2e7c7a
    ```

## <a name="view-the-output"></a>Exibir a saída

A saída do trabalho é armazenada no diretório `/example/wordcountout`. Você pode vê-la em uma sessão SSH ou baixá-la localmente e exibi-la no PowerShell.

Para ver os dados em uma sessão SSH para o cluster, use o seguinte comando:

`hdfs dfs -text /example/wordcountout/part-00000`

Ele exibe uma lista de palavras e ocorrências. A seguir está um exemplo dos dados de saída:

```
wrenching       1
wretched        6
wriggling       1
wrinkled,       1
wrinkles        2
wrinkling       2
```

Para exibir a saída no seu ambiente de desenvolvimento usando o PowerShell, use as seguintes etapas:

1. Crie um novo arquivo chamado `Get-FilesInHDInsight.ps1` e use os itens abaixo como o seu conteúdo:

    ```PowerShell
    param(
        [Parameter(Mandatory = $true)]
        [String]$clusterName,
        [Parameter(Mandatory = $true)]
        [String]$source
    )
    # Login to your Azure subscription
    # Is there an active Azure subscription?
    $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Add-AzureRmAccount
    }

    # Get the cluster info
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $storageInfo = $clusterInfo.DefaultStorageAccount.split('.')

    # What type of default storage are we using?
    switch ($storageInfo[1])
    {
        "blob" {
            # Get the blob storage information for the cluster
            $resourceGroup = $clusterInfo.ResourceGroup
            $storageAccountName=$storageInfo[0]
            $storageContainer=$clusterInfo.DefaultStorageContainer
            $storageAccountKey=(Get-AzureRmStorageAccountKey `
                -Name $storageAccountName `
                -ResourceGroupName $resourceGroup)[0].Value
            # Create a storage context and download the file
            $context = New-AzureStorageContext `
                -StorageAccountName $storageAccountName `
                -StorageAccountKey $storageAccountKey
            # Download the file
            Get-AzureStorageBlobContent `
                -Container $storageContainer `
                -Blob $source `
                -Context $context `
                -Destination "./output.txt"
            # Display the output
            Get-Content "./output.txt"
        }
        "azuredatalakestore" {
            # Get the Data Lake Store name
            $dataLakeStoreName=$storageInfo[0]
            # Get the root of the HDInsight cluster azuredatalakestore
            $clusterRoot=$clusterInfo.DefaultStorageRootPath
            # Download the file. Prepend the destination with the cluster root
            # NOTE: Unlike getting a blob, this just gets the content and no
            #       file is created locally.
            Get-AzureRmDataLakeStoreItemContent -Account $dataLakeStoreName -Path $clusterRoot$source -Confirm
        }
        default {
            Throw "Unknown storage type: $storageInfo[1]"
        }
    }
    ```

2. Em um prompt do Azure PowerShell, use os itens abaixo para executar o script e exibir a saída:

    `Get-FilesInHDInsight.ps1 -clusterName <your HDInsight cluster name> -source "example/wordcountout/part-00000"`

    Ele exibe uma lista de palavras e ocorrências. A seguir está um exemplo dos dados de saída:

    ```
    wrenching       1
    wretched        6
    wriggling       1
    wrinkled,       1
    wrinkles        2
    wrinkling       2
    ```

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a usar a transmissão de trabalhos MapReduce com o HDInsight, use os links abaixo para explorar outras maneiras de trabalhar com o Azure HDInsight.

* [Usar o Hive com o HDInsight](hdinsight-use-hive.md)
* [Usar o Pig com o HDInsight](hdinsight-use-pig.md)
* [Usar trabalhos do MapReduce com o HDInsight](hdinsight-use-mapreduce.md)


