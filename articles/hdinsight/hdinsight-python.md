---
title: Usar o Python com o Hive e o Pig no HDInsight | Microsoft Docs
description: "Saiba como usar UDFs (Funções Definidas pelo Usuário) do Python com o Hive e o Pig no HDInsight, a pilha de tecnologias do Hadoop no Azure."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: c44d6606-28cd-429b-b535-235e8f34a664
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 09/07/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ecd33fdad27cb5bb13931075f4c603f9bd479207


---
# <a name="use-python-with-hive-and-pig-in-hdinsight"></a>Use o Python com o Hive e o Pig no HDInsight
Hive e Pig são ótimos para trabalhar com dados no HDInsight, mas algumas vezes você precisa de uma linguagem de propósito mais geral. Tanto o Hive quanto o Pig permitem que você crie Funções Definidas pelo Usuário (UDF) usando uma variedade de linguagens de programação. Neste artigo, você aprenderá como usar uma UDF Python de Hive e Pig.

## <a name="requirements"></a>Requisitos
* Um cluster HDInsight (Windows ou Linux)
* Um editor de texto
  
  > [!IMPORTANT]
  > Se estiver usando um servidor HDInsight baseado em Linux, mas criando os arquivos de Python em um cliente Windows, você deverá usar um editor que usa LF como uma terminação de linha. Se você não tem certeza se o seu editor usa LF ou CRLF, veja a seção de [Solução de problemas](#troubleshooting) para ver as etapas para remover o caractere CR usando utilitários no cluster HDInsight.
  > 
  > 

## <a name="a-namepythonapython-on-hdinsight"></a><a name="python"></a>Python no HDInsight
O Python 2.7 é instalado por padrão nos clusters do HDInsight 3.0 ou posterior. O Hive pode ser usado com essa versão do Python para processamento de fluxo (os dados são passados entre o Hive e o Python usando STDOUT/STDIN).

O HDInsight também inclui o Jython, que é uma implementação do Python gravada em Java. O Pig compreende como dialogar com o Jython sem precisar utilizar streaming, de modo que é preferível ao utilizar o Pig. No entanto, você também pode usar o Python normal (Python C) com o Pig.

## <a name="a-namehivepythonahive-and-python"></a><a name="hivepython"></a>Hive e Python
O Python pode ser utilizado como uma UDF por meio do Hive por meio da instrução HiveQL **TRANSFORM** . Por exemplo, o HiveQL a seguir chama um script do Python armazenado no arquivo **streaming.py** .

**HDInsight baseado em Linux**

    add file wasbs:///streaming.py;

    SELECT TRANSFORM (clientid, devicemake, devicemodel)
      USING 'python streaming.py' AS
      (clientid string, phoneLable string, phoneHash string)
    FROM hivesampletable
    ORDER BY clientid LIMIT 50;

**HDInsight baseado em Windows**

    add file wasbs:///streaming.py;

    SELECT TRANSFORM (clientid, devicemake, devicemodel)
      USING 'D:\Python27\python.exe streaming.py' AS
      (clientid string, phoneLable string, phoneHash string)
    FROM hivesampletable
    ORDER BY clientid LIMIT 50;

> [!NOTE]
> Em clusters de HDInsight baseados no Windows, a cláusula **USING** cláusula deve especificar o caminho completo para python.exe. Isso é sempre `D:\Python27\python.exe`.
> 
> 

Aqui está o que este exemplo faz:

1. A instrução **add file** no início do arquivo adiciona o arquivo **streaming.py** ao cache distribuído, portanto, é acessível por todos os nós no cluster.
2. A instrução **SELECT TRANSFORM... USING** seleciona os dados de **hivesampletable** e passa clientid, devicemake e devicemodel para o script **streaming.py**.
3. A cláusula **AS** descreve os campos retornados de **streaming.py**

<a name="streamingpy"></a> Aqui está o arquivo **streaming.py** utilizado pelo exemplo HiveQL.

    #!/usr/bin/env python

    import sys
    import string
    import hashlib

    while True:
      line = sys.stdin.readline()
      if not line:
        break

      line = string.strip(line, "\n ")
      clientid, devicemake, devicemodel = string.split(line, "\t")
      phone_label = devicemake + ' ' + devicemodel
      print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])

Como estamos usando streaming, esse script precisa fazer o seguinte:

1. Ler dados por meio do STDIN. Isso é realizado usando `sys.stdin.readline()` neste exemplo.
2. O último caractere da linha nova é removido usando `string.strip(line, "\n ")`, já que nós apenas desejamos os dados de texto e não o indicador de final de linha.
3. Ao realizar processamento de fluxo, uma única linha contém todos os valores com um caractere de tabulação entre cada par de valores. Assim, `string.split(line, "\t")` pode ser usado para dividir a entrada em cada guia, retornando somente os campos.
4. Quando o processamento está concluído, a saída precisa ser gravada em STDOUT como uma linha única, com uma tabulação entre cada par de campos. Isso é realizado utilizando `print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])`.
5. Tudo isso ocorre dentro de um circuito `while`, que se repetirá até que nenhum `line` seja lido, no ponto em que `break` sai do circuito e o script é encerrado.

Além disso, o script concatena somente os valores de entrada para `devicemake` e `devicemodel`, além de calcular um hash do valor concatenado. Bem simples, mas ele descreve os fundamentos de como qualquer script Python chamado por meio do Hive deve funcionar: executar um loop, ler a entrada até que não haja mais nada, separar cada linha de entrada nas guias, processar, escrever uma única linha de saída delimitado por tabulação.

Consulte [Executando os exemplos](#running) para saber como executar este exemplo em seu cluster HDInsight.

## <a name="a-namepigpythonapig-and-python"></a><a name="pigpython"></a>Pig e Python
Um script Python pode ser utilizado como uma UDF por meio do Pig por meio da instrução **GENERATE** . Há duas maneiras de fazer isso: usando Jython (Python implementado na máquina virtual Java) e Python C (Python normal).

A principal diferença entre eles é que Jython é executado na JVM e pode ser chamado nativamente do Pig (também executado na JVM). O Python C é um processo externo (escrito em C). Portanto, os dados do Pig na JVM são enviados para o script em execução em um processo de Python e a saída disso é enviada para o Pig.

Para determinar se o Pig usa Jython ou Python C para executar o script, use **registrar** ao referenciar o script Python no Pig Latin. Isso informa ao Pig o intérprete a ser usado e o alias para criar o script. Os exemplos a seguir registram os scripts com o Pig como **myfuncs**:

* **Para usar o Jython**: `register '/path/to/pig_python.py' using jython as myfuncs;`
* **Para usar o Python C**: `register '/path/to/pig_python.py' using streaming_python as myfuncs;`

> [!IMPORTANT]
> Ao usar o Jython, o caminho para o arquivo pig_jython pode ser um caminho local ou um caminho WASB://. No entanto, ao usar o Python C, você deve fazer referência a um arquivo no sistema de arquivos local do nó que está usando para enviar o trabalho de Pig.
> 
> 

Depois do registro, o Pig Latin para o exemplo é o mesmo para ambos:

    LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
    LOG = FILTER LOGS by LINE is not null;
    DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
    DUMP DETAILS;

Aqui está o que este exemplo faz:

1. A primeira linha carrega o arquivo de dados de amostra, **sample.log**, em **LOGS**. Como esse arquivo de log não tem um esquema consistente, ele também define cada registro (**LINE** nesse caso) como uma **matriz de caracteres**. A matriz de caracteres é, essencialmente, uma cadeia de caracteres.
2. A próxima linha filtra e remove valores nulos, armazenando o resultado da operação no **LOG**.
3. Em seguida, faz a iteração nos registros em **LOG** e usa **GENERATE** para chamar o método **create_structure** contido no script do Python/Jython carregado como **myfuncs**.  **LINE** é usado para passar o registro atual para a função.
4. Por fim, as saídas são depositadas em STDOUT usando o comando **DUMP** . Isso é só para mostrar imediatamente os resultados após a conclusão da operação; em um script real, você normalmente **ARMAZENARIA** os dados em um novo arquivo.

O arquivo de script do Python real também é semelhante entro e Python C e o Jython. A única diferença é que você deve importar de **pig\_util** ao usar o Python C. Aqui está o script **pig\_python.py**:

<a name="streamingpy"></a>

    # Uncomment the following if using C Python
    #from pig_util import outputSchema

    @outputSchema("log: {(date:chararray, time:chararray, classname:chararray, level:chararray, detail:chararray)}")
    def create_structure(input):
    if (input.startswith('java.lang.Exception')):
        input = input[21:len(input)] + ' - java.lang.Exception'
    date, time, classname, level, detail = input.split(' ', 4)
    return date, time, classname, level, detail

> [!NOTE]
> 'pig_util' não é algo que você precise se preocupar em instalar; ele fica automaticamente disponível para o script.
> 
> 

Você se lembra que, anteriormente, nós apenas definimos a entrada **LINE** como uma matriz de caracteres porque não havia um esquema consistente para a entrada? O que o script Python faz é transformar os dados em um esquema consistente para a saída. Funciona assim:

1. A instrução **@outputSchema** define o formato dos dados que serão retornados ao Pig. Nesse caso, é uma **mala de dados**, que é um tipo de dado do Pig. A mala contém os campos a seguir, todos eles sendo matrizes de caracteres (cadeias de caracteres):
   
   * date - a data em que a entrada no log foi criada
   * time - o horário em que a entrada de log foi criada
   * classname - o nome da classe para a qual a entrada foi criada
   * level - o nível do log
   * detail - detalhes para a entrada de log
2. Em seguida, **def create_structure(input)** define a função para a qual o Pig passará os itens da linha.
3. Os dados de exemplo, **sample.log**, na maior parte das vezes estão em conformidade com o esquema de data, horário, nome de classe, nível e detalhe que desejamos retornar. Mas eles também contêm algumas linhas que começam com a cadeia '*java.lang.Exception*‘, que precisam ser modificadas para corresponder ao esquema. A instrução **if** busca essas linhas, então movimenta os dados de entrada para mover a cadeia de caracteres '*java.lang.Exception*' para o final, colocando os dados em linha com o nosso esquema de saída esperado.
4. Em seguida, o comando **split** é utilizado para dividir os dados nos caracteres ocupando os quatro primeiros espaços. Isso resulta em cinco valores, que são atribuídos à **data**, **hora**, **classname**, **nível** e **detalhe**.
5. Por fim, os valores são devolvidos ao Pig.

Quando os dados forem devolvidos ao Pig, eles terão um esquema consistente conforme definido na instrução **@outputSchema** .

## <a name="a-namerunningarunning-the-examples"></a><a name="running"></a>Executando os exemplos
Se você estiver usando um cluster HDInsight baseados em Linux, use as etapas do **SSH** abaixo. Se você estiver usando um cluster HDInsight baseado no Windows e um cliente do Windows, use as etapas do **PowerShell** .

### <a name="ssh"></a>SSH
Para obter mais informações sobre como usar o SSH, consulte [Usar SSH com Hadoop baseado em Linux no HDInsight por meio do Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md) ou [Usar SSH com Hadoop baseado em Linux no HDInsight por meio do Windows](hdinsight-hadoop-linux-use-ssh-windows.md).

1. Usando os exemplos do Python [streaming.py](#streamingpy) e [pig_python.py](#jythonpy), crie cópias locais dos arquivos em seu computador de desenvolvimento.
2. Use `scp` para copiar os arquivos para seu cluster HDInsight. Por exemplo, o comando seguinte copiaria os arquivos para um cluster chamado **mycluster**.
   
        scp streaming.py pig_python.py myuser@mycluster-ssh.azurehdinsight.net:
3. Use SSH para conectar-se ao cluster. Por exemplo, o seguinte conectaria um cluster denominado **mycluster** como o usuário **myuser**.
   
        ssh myuser@mycluster-ssh.azurehdinsight.net
4. Na sessão de SSH, adicione ao cluster os arquivos de python carregados no armazenamento WASB anteriormente.
   
        hdfs dfs -put streaming.py /streaming.py
        hdfs dfs -put pig_python.py /pig_python.py

Após carregar os arquivos, use as etapas a seguir para executar os trabalhos de Hive e Pig.

#### <a name="hive"></a>Hive
1. Use o comando `hive` para iniciar o shell do hive. Você deve ver um prompt `hive>` assim que o shell for carregado.
2. No prompt `hive>` , insira o seguinte:
   
        add file wasbs:///streaming.py;
        SELECT TRANSFORM (clientid, devicemake, devicemodel)
          USING 'python streaming.py' AS
          (clientid string, phoneLabel string, phoneHash string)
        FROM hivesampletable
        ORDER BY clientid LIMIT 50;
3. Depois de inserir a última linha, o trabalho deve ser iniciado. Eventualmente, ele retornará uma saída semelhante à seguinte.
   
        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9

#### <a name="pig"></a>Pig
1. Use o comando `pig` para iniciar o shell. Você deve ver um prompt `grunt>` assim que o shell for carregado.
2. Insira as seguintes instruções no prompt `grunt>` executar o script de Python usando o interpretador Jython.
   
        Register wasbs:///pig_python.py using jython as myfuncs;
        LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
        LOG = FILTER LOGS by LINE is not null;
        DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
        DUMP DETAILS;
3. Depois de inserir a linha a seguir, o trabalho deve ser iniciado. Eventualmente, ele retornará uma saída semelhante à seguinte.
   
        ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
        ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
        ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
        ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
        ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))
4. Use `quit` para sair do shell do Grunt e use o seguinte para editar o arquivo pig_python.py no sistema de arquivos local:
   
    nano pig_python.py
5. No editor, remova a seguinte linha removendo o caractere `#` do início da linha:
   
        #from pig_util import outputSchema
   
    Depois que a alteração for feita, use Ctrl+X para sair do editor. Selecione Y e Enter para salvar as alterações.
6. Use o comando `pig` para iniciar o shell novamente. No prompt `grunt>` , use o que segue para executar o script de Python usando o interpretador de Python C.
   
        Register 'pig_python.py' using streaming_python as myfuncs;
        LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
        LOG = FILTER LOGS by LINE is not null;
        DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
        DUMP DETAILS;
   
    Quando o trabalho for concluído, você verá a mesma saída de quando executou o script usando Jython.

### <a name="powershell"></a>PowerShell
Essas etapas usam o PowerShell do Azure. Se ele ainda não estiver instalado e configurado em sua máquina de desenvolvimento, consulte [Como instalar e configurar o PowerShell do Azure](../powershell-install-configure.md) antes de usar as etapas a seguir.

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

1. Usando os exemplos do Python [streaming.py](#streamingpy) e [pig_python.py](#jythonpy), crie cópias locais dos arquivos em seu computador de desenvolvimento.
2. Use o seguinte script do PowerShell para transferir os arquivos **streaming.py** e **pig\_python.py** para o servidor. Substitua o nome do seu cluster HDInsight do Azure e o caminho para os arquivos **streaming.py** e **pig\_python.py** nas três primeiras linhas do script.
   
        $clusterName = YourHDIClusterName
        $pathToStreamingFile = "C:\path\to\streaming.py"
        $pathToJythonFile = "C:\path\to\pig_python.py"
   
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
   
        #Create a storage content and upload the file
        $context = New-AzureStorageContext `
            -StorageAccountName $storageAccountName `
            -StorageAccountKey $storageAccountKey
   
        Set-AzureStorageBlobContent `
            -File $pathToStreamingFile `
            -Blob "streaming.py" `
            -Container $container `
            -Context $context
   
        Set-AzureStorageBlobContent `
            -File $pathToJythonFile `
            -Blob "pig_python.py" `
            -Container $container `
            -Context $context
   
    Este script obtém informações a partir de seu cluster HDInsight, então, extrai a conta e a chave para a conta de armazenamento padrão, além de carregar os arquivos para a raiz do contêiner.
   
   > [!NOTE]
   > É possível encontrar outros métodos para carregar os scripts no documento [Carregar dados para trabalhos do Hadoop no HDInsight](hdinsight-upload-data.md) .
   > 
   > 

Após carregar os arquivos, use os scripts do PowerShell a seguir para iniciar os trabalhos. Quando o trabalho é concluído, a saída deve ser gravada no console do PowerShell.

#### <a name="hive"></a>Hive
O script a seguir executará o script **streaming.py**. Antes da execução, ele solicitará as informações da conta de Administrador/HTTPs para o cluster HDInsight.

    # Replace 'YourHDIClusterName' with the name of your cluster
    $clusterName = YourHDIClusterName
    $creds=Get-Credential
    #Get the cluster info so we can get the resource group, storage, etc.
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroup = $clusterInfo.ResourceGroup
    $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
    $container=$clusterInfo.DefaultStorageContainer
    $storageAccountKey=(Get-AzureRmStorageAccountKey `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
    #Create a storage content and upload the file
    $context = New-AzureStorageContext `
        -StorageAccountName $storageAccountName `
        -StorageAccountKey $storageAccountKey

    # If using a Windows-based HDInsight cluster, change the USING statement to:
    # "USING 'D:\Python27\python.exe streaming.py' AS " +
    $HiveQuery = "add file wasbs:///streaming.py;" +
                 "SELECT TRANSFORM (clientid, devicemake, devicemodel) " +
                   "USING 'python streaming.py' AS " +
                   "(clientid string, phoneLabel string, phoneHash string) " +
                 "FROM hivesampletable " +
                 "ORDER BY clientid LIMIT 50;"

    $jobDefinition = New-AzureRmHDInsightHiveJobDefinition `
        -Query $HiveQuery

    $job = Start-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $jobDefinition `
        -HttpCredential $creds
    Write-Host "Wait for the Hive job to complete ..." -ForegroundColor Green
    Wait-AzureRmHDInsightJob `
        -JobId $job.JobId `
        -ClusterName $clusterName `
        -HttpCredential $creds
    # Uncomment the following to see stderr output
    # Get-AzureRmHDInsightJobOutput `
    #   -Clustername $clusterName `
    #   -JobId $job.JobId `
    #   -DefaultContainer $container `
    #   -DefaultStorageAccountName $storageAccountName `
    #   -DefaultStorageAccountKey $storageAccountKey `
    #   -HttpCredential $creds `
    #   -DisplayOutputType StandardError
    Write-Host "Display the standard output ..." -ForegroundColor Green
    Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -DefaultContainer $container `
        -DefaultStorageAccountName $storageAccountName `
        -DefaultStorageAccountKey $storageAccountKey `
        -HttpCredential $creds

A saída para o trabalho do **Hive** deve ter aparência similar à seguinte:

    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9

#### <a name="pig-jython"></a>Pig (Jython)
O seguinte usará o script **pig_python.py**, usando o interpretador Jython. Antes da execução, ele solicitará as informações de Administrador/HTTPs do cluster HDInsight.

> [!NOTE]
> Ao enviar um trabalho remotamente usando o PowerShell, não é possível usar o Python C como interpretador.
> 
> 

    # Replace 'YourHDIClusterName' with the name of your cluster
    $clusterName = YourHDIClusterName

    $creds = Get-Credential
    #Get the cluster info so we can get the resource group, storage, etc.
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroup = $clusterInfo.ResourceGroup
    $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
    $container=$clusterInfo.DefaultStorageContainer
    $storageAccountKey=(Get-AzureRmStorageAccountKey `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value

    #Create a storage content and upload the file
    $context = New-AzureStorageContext `
        -StorageAccountName $storageAccountName `
        -StorageAccountKey $storageAccountKey

    $PigQuery = "Register wasbs:///jython.py using jython as myfuncs;" +
                "LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);" +
                "LOG = FILTER LOGS by LINE is not null;" +
                "DETAILS = foreach LOG generate myfuncs.create_structure(LINE);" +
                "DUMP DETAILS;"

    $jobDefinition = New-AzureRmHDInsightPigJobDefinition -Query $PigQuery

    $job = Start-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $jobDefinition `
        -HttpCredential $creds

    Write-Host "Wait for the Pig job to complete ..." -ForegroundColor Green
    Wait-AzureRmHDInsightJob `
        -Job $job.JobId `
        -ClusterName $clusterName `
        -HttpCredential $creds
    # Uncomment the following to see stderr output
    # Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -DefaultContainer $container `
        -DefaultStorageAccountName $storageAccountName `
        -DefaultStorageAccountKey $storageAccountKey `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
    Write-Host "Display the standard output ..." -ForegroundColor Green
    Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -DefaultContainer $container `
        -DefaultStorageAccountName $storageAccountName `
        -DefaultStorageAccountKey $storageAccountKey `
        -HttpCredential $creds

A saída para o trabalho **Pig** deve ter aparência similar à seguinte:

    ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
    ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
    ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
    ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
    ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

## <a name="a-nametroubleshootingatroubleshooting"></a><a name="troubleshooting"></a>Solucionar problemas
### <a name="errors-when-running-jobs"></a>Erros durante a execução de trabalhos
Ao executar o trabalho Hive, você pode encontrar um erro semelhante ao seguinte:

    Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.

Esse problema pode ser causado pelas terminações de linha no arquivo streaming.py. Muitos editores Windows usam CRLF como padrão como a terminação de linha, mas aplicativos Linux geralmente esperam LF.

Se você está usando um editor que não pode criar terminações de linha em LF ou não tem certeza de quais terminações de linha estão sendo usadas, use as seguintes instruções do PowerShell para remover os caracteres CR antes de carregar o arquivo no HDInsight:

    $original_file ='c:\path\to\streaming.py'
    $text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
    [IO.File]::WriteAllText($original_file, $text)

### <a name="powershell-scripts"></a>Scripts do PowerShell
Ambos os scripts de Powershell de exemplo utilizados para executar os exemplos contêm uma linha comentada que exibirá a saída de erro para o trabalho. Se você não estiver vendo a saída esperada para o trabalho, remova o comentário da linha a seguir e veja se as informações de erro indicam um problema.

    # Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $job.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

As informações de erro (STDERR) e o resultado do trabalho (STDOUT) também são registrados para o contêiner blob padrão para seus clusters nos locais a seguir.

| Para este trabalho. | Veja estes arquivos no contêiner blob |
| --- | --- |
| Hive |/HivePython/stderr<p>/HivePython/stdout |
| Pig |/PigPython/stderr<p>/PigPython/stdout |

## <a name="a-namenextanext-steps"></a><a name="next"></a>Próximas etapas
Se você precisa carregar módulos Python que não são fornecidos por padrão, consulte [Como implantar um módulo para o HDInsight do Azure](http://blogs.msdn.com/b/benjguin/archive/2014/03/03/how-to-deploy-a-python-module-to-windows-azure-hdinsight.aspx) para ver um exemplo de como fazer isso.

Para outras maneiras de usar o Pig e o Hive e para saber como usar o MapReduce, consulte os tutoriais a seguir.

* [Usar o Hive com o HDInsight](hdinsight-use-hive.md)
* [Usar o Pig com o HDInsight](hdinsight-use-pig.md)
* [Usar o MapReduce com o HDInsight](hdinsight-use-mapreduce.md)




<!--HONumber=Nov16_HO3-->


