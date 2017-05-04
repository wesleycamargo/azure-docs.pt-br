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
ms.date: 02/27/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 985f14ce7b8249c8e80deb1851cfee8c89651786
ms.lasthandoff: 04/27/2017

---
# <a name="use-python-user-defined-functions-udf-with-hive-and-pig-in-hdinsight"></a>Usar as UDF (Funções Definidas pelo Usuário) do Python com o Hive e o Pig no HDInsight

O Hive e o Pig são ótimos para trabalhar usando dados no HDInsight, mas às vezes você precisa de uma linguagem de propósito mais geral. Tanto o Hive quanto o Pig permitem que você crie UDFs (Funções Definidas pelo Usuário) usando várias linguagens de programação. Neste artigo, você aprenderá como usar uma UDF do Python com o Hive e o Pig.

## <a name="requirements"></a>Requisitos

* Um cluster HDInsight

  > [!IMPORTANT]
  > O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para saber mais, veja [Substituição do HDInsight no Windows](hdinsight-component-versioning.md#hdi-version-33-nearing-deprecation-date).

* Um editor de texto

## <a name="python"></a>Python no HDInsight

O Python 2.7 é instalado por padrão nos clusters do HDInsight 3.0 ou posterior. O Hive pode ser usado com essa versão do Python para processamento de fluxo (os dados são passados entre o Hive e o Python usando STDOUT/STDIN).

O HDInsight também inclui o Jython, que é uma implementação do Python gravada em Java. O Pig compreende como dialogar com o Jython sem precisar utilizar streaming, de modo que é preferível ao utilizar o Pig. Você também pode usar o Python normal (Python C) com o Pig.

## <a name="hivepython"></a>Hive e Python

O Python pode ser utilizado como uma UDF por meio do Hive por meio da instrução HiveQL **TRANSFORM** . Por exemplo, o HiveQL a seguir chama um script do Python armazenado no arquivo **streaming.py** .

**HDInsight baseado em Linux**

```hiveql
add file wasbs:///streaming.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'python streaming.py' AS
    (clientid string, phoneLable string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

**HDInsight baseado em Windows**

```hiveql
add file wasbs:///streaming.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'D:\Python27\python.exe streaming.py' AS
    (clientid string, phoneLable string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

> [!NOTE]
> Em clusters de HDInsight baseados no Windows, a cláusula **USING** cláusula deve especificar o caminho completo para python.exe.

Aqui está o que este exemplo faz:

1. A instrução **add file** no início do arquivo adiciona o arquivo **streaming.py** ao cache distribuído, portanto, é acessível por todos os nós no cluster.
2. A instrução **SELECT TRANSFORM ... A instrução USING** seleciona dados do **hivesampletable**. Ela também passa os valores clientid, devicemake e devicemodel para o script **streaming.py**.
3. A cláusula **AS** descreve os campos retornados de **streaming.py**

<a name="streamingpy"></a> Aqui está o arquivo **streaming.py** utilizado pelo exemplo HiveQL.

```python
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
```

O script executa as ações a seguir:

1. Ler uma linha de dados do STDIN.
2. O caractere de nova linha é removido usando `string.strip(line, "\n ")`.
3. Ao realizar processamento de fluxo, uma única linha contém todos os valores com um caractere de tabulação entre cada par de valores. Assim, `string.split(line, "\t")` pode ser usado para dividir a entrada em cada guia, retornando somente os campos.
4. Quando o processamento está concluído, a saída precisa ser gravada em STDOUT como uma linha única, com uma tabulação entre cada par de campos. Isso é realizado utilizando `print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])`.
5. O loop `while` é repetido até que nenhum `line` seja lido.

A saída do script é uma concatenação dos valores de entrada para `devicemake` e `devicemodel`, e um hash do valor concatenado.

Consulte [Executando os exemplos](#running) para saber como executar este exemplo em seu cluster HDInsight.

## <a name="pigpython"></a>Pig e Python

Um script Python pode ser utilizado como uma UDF por meio do Pig por meio da instrução **GENERATE** . Você pode executar o script usando o Jython ou o Python C.

A diferença entre eles é que o Jython é executado na JVM e pode ser chamado nativamente do Pig. O Python C é um processo externo para que os dados do Pig no JVM sejam enviados para o script executado em um processo do Python. A saída do script Python é enviada de volta ao Pig.

Para determinar se o Pig usa Jython ou Python C para executar o script, use **registrar** ao referenciar o script Python no Pig Latin. Os exemplos a seguir registram os scripts com o Pig como **myfuncs**:

* **Para usar o Jython**: `register '/path/to/pig_python.py' using jython as myfuncs;`
* **Para usar o Python C**: `register '/path/to/pig_python.py' using streaming_python as myfuncs;`

> [!IMPORTANT]
> Ao usar o Jython, o caminho para o arquivo pig_jython pode ser um caminho local ou um caminho WASB://. No entanto, ao usar o Python C, você deve fazer referência a um arquivo no sistema de arquivos local do nó que está usando para enviar o trabalho de Pig.

Depois do registro, o Pig Latin para o exemplo é o mesmo para ambos:

```pig
LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
LOG = FILTER LOGS by LINE is not null;
DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
DUMP DETAILS;
```

Aqui está o que este exemplo faz:

1. A primeira linha carrega o arquivo de dados de amostra, **sample.log**, em **LOGS**. Ela também define cada registro como uma **matriz de caracteres**.
2. A próxima linha filtra e remove valores nulos, armazenando o resultado da operação no **LOG**.
3. Em seguida, faz a iteração nos registros em **LOG** e usa **GENERATE** para chamar o método **create_structure** contido no script do Python/Jython carregado como **myfuncs**.  **LINE** é usado para passar o registro atual para a função.
4. Por fim, as saídas são depositadas em STDOUT usando o comando **DUMP** . Isso exibe os resultados após a conclusão da operação.

O arquivo de script do Python é semelhante entre o Python C e o Jython. A única diferença é que você deve importar de **pig\_util** ao usar o Python C. Aqui está o script **pig\_python.py**:

<a name="streamingpy"></a>

```python
# Uncomment the following if using C Python
#from pig_util import outputSchema

@outputSchema("log: {(date:chararray, time:chararray, classname:chararray, level:chararray, detail:chararray)}")
def create_structure(input):
    if (input.startswith('java.lang.Exception')):
        input = input[21:len(input)] + ' - java.lang.Exception'
    date, time, classname, level, detail = input.split(' ', 4)
    return date, time, classname, level, detail
```

> [!NOTE]
> 'pig_util' não é algo que você precise se preocupar em instalar; ele fica automaticamente disponível para o script.

Lembre-se de que definimos anteriormente a entrada **LINE** como uma matriz de caracteres porque não havia um esquema consistente para a entrada. O script Python transforma os dados em um esquema consistente para a saída.

1. A instrução **@outputSchema** define o formato dos dados que são retornados ao Pig. Nesse caso, é uma **mala de dados**, que é um tipo de dado do Pig. A mala contém os campos a seguir, todos eles sendo matrizes de caracteres (cadeias de caracteres):

   * date - a data em que a entrada no log foi criada
   * time - o horário em que a entrada de log foi criada
   * classname - o nome da classe para a qual a entrada foi criada
   * level - o nível do log
   * detail - detalhes para a entrada de log

2. Em seguida, o **def create_structure(input)** define a função para a qual o Pig passa os itens da linha.

3. Os dados de exemplo, **sample.log**, na maior parte das vezes estão em conformidade com o esquema de data, horário, nome de classe, nível e detalhe que desejamos retornar. Mas eles também contêm algumas linhas que começam com a cadeia '*java.lang.Exception*‘, que precisam ser modificadas para corresponder ao esquema. A instrução **if** busca essas linhas, então movimenta os dados de entrada para mover a cadeia de caracteres '*java.lang.Exception*' para o final, colocando os dados em linha com o nosso esquema de saída esperado.

4. Em seguida, o comando **split** é utilizado para dividir os dados nos caracteres ocupando os quatro primeiros espaços. A saída é atribuída em **data**, **horário**, **nome de classe**, **nível** e **detalhe**.

5. Por fim, os valores são devolvidos ao Pig.

Quando os dados forem devolvidos ao Pig, eles terão um esquema consistente, como definido na instrução **@outputSchema**.

## <a name="running"></a>Executando os exemplos
Se você estiver usando um cluster HDInsight baseado em Linux, siga as etapas do **SSH**. Se você estiver usando um cluster HDInsight baseado no Windows e um cliente do Windows, use as etapas do **PowerShell** .

### <a name="ssh"></a>SSH

Para saber mais sobre como usar SSH, confira [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

1. Usando os exemplos do Python [streaming.py](#streamingpy) e [pig_python.py](#jythonpy), crie cópias locais dos arquivos em seu computador de desenvolvimento.

2. Use `scp` para copiar os arquivos para seu cluster HDInsight. Por exemplo, o comando a seguir copia os arquivos para um cluster chamado **mycluster**.

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

   ```hive
   add file wasbs:///streaming.py;
   SELECT TRANSFORM (clientid, devicemake, devicemodel)
       USING 'python streaming.py' AS
       (clientid string, phoneLabel string, phoneHash string)
   FROM hivesampletable
   ORDER BY clientid LIMIT 50;
   ```
3. Depois de inserir a última linha, o trabalho deve ser iniciado. Quando o trabalho for concluído, ele retornará uma saída semelhante ao exemplo a seguir:

        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9

#### <a name="pig"></a>Pig

1. Use o comando `pig` para iniciar o shell. Você deve ver um prompt `grunt>` assim que o shell for carregado.

2. No prompt `grunt>`, insira as seguintes instruções:

   ```pig
   Register wasbs:///pig_python.py using jython as myfuncs;
   LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

3. Depois de inserir a linha a seguir, o trabalho será iniciado. Quando o trabalho for concluído, ele retornará uma saída semelhante à seguinte.

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

   ```pig
   Register 'pig_python.py' using streaming_python as myfuncs;
   LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

    Quando o trabalho for concluído, você verá a mesma saída de quando executou o script usando Jython.

### <a name="powershell"></a>PowerShell

Essas etapas usam o PowerShell do Azure. Para obter mais informações sobre como usar o Azure PowerShell, consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

1. Usando os exemplos do Python [streaming.py](#streamingpy) e [pig_python.py](#jythonpy), crie cópias locais dos arquivos em seu computador de desenvolvimento.
2. Use o seguinte script do PowerShell para transferir os arquivos **streaming.py** e **pig\_python.py** para o servidor. Substitua o nome do seu cluster HDInsight do Azure e o caminho para os arquivos **streaming.py** e **pig\_python.py** nas três primeiras linhas do script.

   ```powershell
    # Login to your Azure subscription
    # Is there an active Azure subscription?
    $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Add-AzureRmAccount
    }

    # Get cluster info
    $clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
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
   ```

    Este script obtém informações a partir de seu cluster HDInsight, então, extrai a conta e a chave para a conta de armazenamento padrão, além de carregar os arquivos para a raiz do contêiner.

   > [!NOTE]
   > É possível encontrar outros métodos para carregar os scripts no documento [Carregar dados para trabalhos do Hadoop no HDInsight](hdinsight-upload-data.md) .

Após carregar os arquivos, use os scripts do PowerShell a seguir para iniciar os trabalhos. Quando o trabalho é concluído, a saída deve ser gravada no console do PowerShell.

#### <a name="hive"></a>Hive

O script a seguir executa o script **streaming.py**. Antes da execução, ele solicitará as informações de HTTPs/Conta do administrador do seu cluster HDInsight.

```powershell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Add-AzureRmAccount
}

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$creds=Get-Credential -Message "Enter the login for the cluster"

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
#   -HttpCredential $creds `
#   -DisplayOutputType StandardError
Write-Host "Display the standard output ..." -ForegroundColor Green
Get-AzureRmHDInsightJobOutput `
    -Clustername $clusterName `
    -JobId $job.JobId `
    -HttpCredential $creds
```

A saída para o trabalho do **Hive** deve ser semelhante ao exemplo a seguir:

    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9

#### <a name="pig-jython"></a>Pig (Jython)

O script a seguir usa o script **pig_python.py**, utilizando o interpretador Jython. Antes da execução, ele solicitará as informações de Administrador/HTTPs do cluster HDInsight.

> [!NOTE]
> Ao enviar um trabalho remotamente usando o PowerShell, não é possível usar o Python C como interpretador.

```powershell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Add-AzureRmAccount
}

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$creds=Get-Credential -Message "Enter the login for the cluster"

$PigQuery = "Register wasbs:///pig_python.py using jython as myfuncs;" +
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
#    -Clustername $clusterName `
#    -JobId $job.JobId `
#    -HttpCredential $creds `
#    -DisplayOutputType StandardError
Write-Host "Display the standard output ..." -ForegroundColor Green
Get-AzureRmHDInsightJobOutput `
    -Clustername $clusterName `
    -JobId $job.JobId `
    -HttpCredential $creds
```

A saída para o trabalho **Pig** deve ter aparência similar à seguinte:

    ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
    ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
    ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
    ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
    ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

## <a name="troubleshooting"></a>Solucionar problemas

### <a name="errors-when-running-jobs"></a>Erros durante a execução de trabalhos

Ao executar o trabalho Hive, você pode encontrar um erro semelhante ao seguinte:

    Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.

Esse problema pode ser causado pelas terminações de linha no arquivo streaming.py. Muitos editores Windows usam CRLF como padrão como a terminação de linha, mas aplicativos Linux geralmente esperam LF.

Você pode seguir as seguintes instruções do PowerShell para remover os caracteres CR antes de carregar o arquivo no HDInsight:

```powershell
$original_file ='c:\path\to\streaming.py'
$text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
[IO.File]::WriteAllText($original_file, $text)
```

### <a name="powershell-scripts"></a>Scripts do PowerShell

Ambos os scripts de exemplo do PowerShell usados para executar os exemplos contêm uma linha comentada que exibe a saída de erro do trabalho. Se você não estiver vendo a saída esperada para o trabalho, remova o comentário da linha a seguir e veja se as informações de erro indicam um problema.

```powershell
# Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

As informações de erro (STDERR,) e o resultado do trabalho (STDOUT,) também são registrados em seu armazenamento do HDInsight.

| Para este trabalho. | Veja estes arquivos no contêiner blob |
| --- | --- |
| Hive |/HivePython/stderr<p>/HivePython/stdout |
| Pig |/PigPython/stderr<p>/PigPython/stdout |

## <a name="next"></a>Próximas etapas

Se você precisar carregar módulos do Python que não são fornecidos por padrão, consulte [Como implantar um módulo para o HDInsight do Azure](http://blogs.msdn.com/b/benjguin/archive/2014/03/03/how-to-deploy-a-python-module-to-windows-azure-hdinsight.aspx).

Para obter outras formas de usar o Pig e o Hive e para saber como usar o MapReduce, consulte os documentos a seguir:

* [Usar o Hive com o HDInsight](hdinsight-use-hive.md)
* [Usar o Pig com o HDInsight](hdinsight-use-pig.md)
* [Usar o MapReduce com o HDInsight](hdinsight-use-mapreduce.md)

