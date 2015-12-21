<properties
	pageTitle="Use o Python com o Hive e o Pig no HDInsight | Microsoft Azure"
	description="Saiba como usar UDFs (Funções Definidas pelo Usuário) do Python com o Hive e o Pig no HDInsight, a pilha de tecnologias do Hadoop no Azure."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="article"
	ms.date="12/04/2015" 
	ms.author="larryfr"/>

#Use o Python com o Hive e o Pig no HDInsight

Hive e Pig são ótimos para trabalhar com dados no HDInsight, mas algumas vezes você precisa de uma linguagem de propósito mais geral. Tanto o Hive quanto o Pig permitem que você crie Funções Definidas pelo Usuário (UDF) usando uma variedade de linguagens de programação. Neste artigo, você aprenderá como usar uma UDF Python de Hive e Pig.

> [AZURE.NOTE]As etapas neste artigo se aplicam ao cluster do HDInsight versões 2.1, 3.0, 3.1 e 3.2.


##<a name="python"></a>Python no HDInsight

O Python 2.7 é instalado por padrão nos clusters do HDInsight 3.0 ou posterior. O Hive pode ser usado com essa versão do Python para processamento de fluxo (os dados são passados entre o Hive e o Python usando STDOUT/STDIN).

O HDInsight também inclui o Jython, que é uma implementação do Python gravada em Java. O Pig compreende como dialogar com o Jython sem precisar utilizar streaming, de modo que é preferível ao utilizar o Pig.

###<a name="hivepython"></a>Hive e Python

O Python pode ser utilizado como uma UDF por meio do Hive por meio da instrução HiveQL **TRANSFORM**. Por exemplo, o HiveQL a seguir chama um script do Python armazenado no arquivo **streaming.py**.

**HDInsight baseado em Linux**

	add file wasb:///streaming.py;

	SELECT TRANSFORM (clientid, devicemake, devicemodel)
	  USING 'streaming.py' AS
	  (clientid string, phoneLable string, phoneHash string)
	FROM hivesampletable
	ORDER BY clientid LIMIT 50;

**HDInsight baseado em Windows**

	add file wasb:///streaming.py;

	SELECT TRANSFORM (clientid, devicemake, devicemodel)
	  USING 'D:\Python27\python.exe streaming.py' AS
	  (clientid string, phoneLable string, phoneHash string)
	FROM hivesampletable
	ORDER BY clientid LIMIT 50;

> [AZURE.NOTE]Em clusters de HDInsight baseados no Windows, a cláusula **USING** cláusula deve especificar o caminho completo para python.exe. Isso é sempre `D:\Python27\python.exe`.

Aqui está o que este exemplo faz:

1. A instrução **add file** no início do arquivo acrescenta o arquivo **streaming.py** ao cache distribuído, de modo que ele se torna acessível por todos os nós presentes no cluster.

2. A instrução **SELECT TRANSFORM ... A instrução USING** seleciona dados da **hivesampletable** e passa as variáveis clientid, devicemake e devicemodel para o script **streaming.py**.

3. A cláusula **AS** descreve os campos retornados como resultado do **streaming.py**

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

2. Ao realizar processamento de fluxo, uma única linha contém todos os valores com um caractere de tabulação entre cada par de valores. Assim, `string.split(line, "\t")` pode ser usado para dividir a entrada em cada guia, retornando somente os campos.

3. Quando o processamento está concluído, a saída precisa ser gravada em STDOUT como uma linha única, com uma tabulação entre cada par de campos. Isso é realizado utilizando `print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])`.

4. Tudo isso ocorre dentro de um circuito `while`, que se repetirá até que nenhum `line` seja lido, no ponto em que `break` sai do circuito e o script é encerrado.

Além disso, o script concatena somente os valores de entrada para `devicemake` e `devicemodel`, além de calcular um hash do valor concatenado. Bem simples, mas ele descreve os fundamentos de como qualquer script Python chamado por meio do Hive deve funcionar: executar um loop, ler a entrada até que não haja mais nada, separar cada linha de entrada nas guias, processar, escrever uma única linha de saída delimitado por tabulação.

Consulte [Executando os exemplos](#running) para saber como executar este exemplo em seu cluster HDInsight.

###<a name="pigpython"></a>Pig e Python

Um script Python pode ser utilizado como uma UDF por meio do Pig por meio da instrução **GENERATE**. Por exemplo, o exemplo a seguir usa um script Python armazenado no arquivo **jython.py**.

	Register 'wasb:///jython.py' using jython as myfuncs;
    LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
    LOG = FILTER LOGS by LINE is not null;
    DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
    DUMP DETAILS;

Eis aqui como este exemplo funciona:

1. Ele registra o arquivo contendo o script Python (**jython.py**), usando **Jython**, então, o expõe ao Pig como **myfuncs**. O Jython é uma implementação do Python em Java e é executado na mesma máquina virtual de Java que o Pig. Isso nos permite tratar o script Python como uma chamada tradicional de função vs. a abordagem de streaming usada com o Hive.

2. A próxima linha carrega o arquivo de dados de amostra, **sample.log** em **LOGS**. Como esse arquivo de log não tem um esquema consistente, ele também define cada registro (**LINE** neste caso), como uma **matriz de caracteres**. A matriz de caracteres é, essencialmente, uma cadeia de caracteres.

3. A terceira linha filtra e remove quaisquer valores nulos, armazenando o resultado da operação no **LOG**.

4. Em seguida, ela faz a iteração pelos registros em **LOG** e usa **GENERATE** para chamar o método **create\_structure** contido no script **jython.py** carregado como **myfuncs**. **LINE** é usado para passar o registro atual para a função.

5. Por fim, as saídas são depositadas em STDOUT usando o comando **DUMP**. Isso é só para mostrar imediatamente os resultados após a conclusão da operação; em um script real, você normalmente **ARMAZENARIA** os dados em um novo arquivo.

<a name="jythonpy"></a> Aqui está o arquivo **jython.py** usado pelo exemplo Pig:

	@outputSchema("log: {(date:chararray, time:chararray, classname:chararray, level:chararray, detail:chararray)}")
	def create_structure(input):
	  if (input.startswith('java.lang.Exception')):
	    input = input[21:len(input)] + ' - java.lang.Exception'
	  date, time, classname, level, detail = input.split(' ', 4)
	  return date, time, classname, level, detail

Você se lembra que, anteriormente, nós apenas definimos a entrada **LINE** como uma matriz de caracteres porque não havia um esquema consistente para a entrada? O que o **jython.py** faz é transformar os dados em um esquema consistente para a saída. Funciona assim:

1. A instrução **@outputSchema** define o formato dos dados que serão retornados ao Pig. Nesse caso, é uma **mala de dados**, que é um tipo de dado do Pig. A mala contém os campos a seguir, todos eles sendo matrizes de caracteres (cadeias de caracteres):

	* date - a data em que a entrada no log foi criada
	* time - o horário em que a entrada de log foi criada
	* classname - o nome da classe para a qual a entrada foi criada
	* level - o nível do log
	* detail - detalhes para a entrada de log

2. Em seguida, a **def create\_structure(input)** define a função à qual o Pig passará os itens de linha.

3. Os dados de exemplo, **sample.log**, na maior parte das vezes estão em conformidade com o esquema de data, horário, nome de classe, nível e detalhe que desejamos retornar. Mas eles também contêm algumas linhas que começam com a cadeia '*java.lang.Exception*‘, que precisam ser modificadas para corresponder ao esquema. A instrução **if** busca essas linhas, então movimenta os dados de entrada para mover a cadeia de caracteres '*java.lang.Exception*' para o final, colocando os dados em linha com o nosso esquema de saída esperado.

4. Em seguida, o comando **split** é utilizado para dividir os dados nos caracteres ocupando os quatro primeiros espaços. Isso resulta em cinco valores, que são atribuídos a **date**, **time**, **classname**, **level** e **detail**.

5. Por fim, os valores são devolvidos ao Pig.

Quando os dados forem devolvidos ao Pig, eles terão um esquema consistente conforme definido na instrução **@outputSchema**.

##<a name="running"></a>Executando os exemplos

Se você estiver usando um cluster HDInsight baseados em Linux, use as etapas do **SSH** abaixo. Se você estiver usando um cluster HDInsight baseado no Windows e um cliente do Windows, use as etapas do **PowerShell**.

###SSH

Para obter mais informações sobre como usar o SSH, consulte <a href="../hdinsight-hadoop-linux-use-ssh-unix/" target="_blank">Usar SSH com Hadoop baseado em Linux no HDInsight por meio do Linux, Unix ou OS X</a> ou <a href="../hdinsight-hadoop-linux-use-ssh-windows/" target="_blank">Usar SSH com Hadoop baseado em Linux no HDInsight por meio do Windows</a>.

1. Usando os exemplos do Python [streaming.py](#streamingpy) e [jython.py](#jythonpy), crie cópias locais dos arquivos em seu computador do desenvolvimento.

2. Use `scp` para copiar os arquivos para seu cluster HDInsight. Por exemplo, o comando seguinte copiaria os arquivos para um cluster chamado **mycluster**.

		scp streaming.py jython.py myuser@mycluster-ssh.azurehdinsight.net:

3. Use SSH para conectar-se ao cluster. Por exemplo, o seguinte deve se conectar a um cluster chamado **mycluster** como o usuário **myuser**.

		ssh myuser@mycluster-ssh.azurehdinsight.net

4. Na sessão de SSH, adicione ao cluster os arquivos de python carregados no armazenamento WASB anteriormente.

		hadoop fs -copyFromLocal streaming.py /streaming.py
		hadoop fs -copyFromLocal jython.py /jython.py

Após carregar os arquivos, use as etapas a seguir para executar os trabalhos de Hive e Pig.

####Hive

1. Use o comando `hive` para iniciar o shell do hive. Você deve ver um prompt `hive>` assim que o shell for carregado.

2. No prompt `hive>`, insira o seguinte:

		add file wasb:///streaming.py;
		SELECT TRANSFORM (clientid, devicemake, devicemodel)
		  USING 'streaming.py' AS
		  (clientid string, phoneLabel string, phoneHash string)
		FROM hivesampletable
		ORDER BY clientid LIMIT 50;

3. Depois de inserir a última linha, o trabalho deve ser iniciado. Eventualmente, ele retornará uma saída semelhante à seguinte.

		100041	RIM 9650	d476f3687700442549a83fac4560c51c
		100041	RIM 9650	d476f3687700442549a83fac4560c51c
		100042	Apple iPhone 4.2.x	375ad9a0ddc4351536804f1d5d0ea9b9
		100042	Apple iPhone 4.2.x	375ad9a0ddc4351536804f1d5d0ea9b9
		100042	Apple iPhone 4.2.x	375ad9a0ddc4351536804f1d5d0ea9b9

####Pig

1. Use o comando `pig` para iniciar o shell. Você deve ver um prompt `grunt>` assim que o shell for carregado.

2. No prompt `grunt>`, insira as instruções a seguir.

		Register wasb:///jython.py using jython as myfuncs;
	    LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
	    LOG = FILTER LOGS by LINE is not null;
	    DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
	    DUMP DETAILS;

3. Depois de inserir a linha a seguir, o trabalho deve ser iniciado. Eventualmente, ele retornará uma saída semelhante à seguinte.

		((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
		((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
		((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
		((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
		((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

###PowerShell

Essas etapas usam o PowerShell do Azure. Se ele ainda não estiver instalado e configurado em sua máquina de desenvolvimento, consulte [Como instalar e configurar o PowerShell do Azure](../install-configure-powershell.md) antes de usar as etapas a seguir.

1. Usando os exemplos do Python [streaming.py](#streamingpy) e [jython.py](#jythonpy), crie cópias locais dos arquivos em seu computador do desenvolvimento.

2. Use o script PowerShell a seguir para carregar os arquivos **streaming.py** e **jython.py** no servidor. Substitua o nome do seu cluster HDInsight do Azure e o caminho para os arquivos **streaming.py** e **jython.py** nas três primeiras linhas do script.

		$clusterName = YourHDIClusterName
		$pathToStreamingFile = "C:\path\to\streaming.py"
		$pathToJythonFile = "C:\path\to\jython.py"

		$clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
            -ResourceGroupName $resourceGroup `
            | %{ $_.Key1 }

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
            -Blob "jython.py" `
            -Container $container `
            -Context $context

	Este script obtém informações a partir de seu cluster HDInsight, então, extrai a conta e a chave para a conta de armazenamento padrão, além de carregar os arquivos para a raiz do contêiner.

	> [AZURE.NOTE]É possível encontrar outros métodos para carregar os scripts no documento [Carregar dados para trabalhos do Hadoop no HDInsight](hdinsight-upload-data.md).

Após carregar os arquivos, use os scripts do PowerShell a seguir para iniciar os trabalhos. Quando o trabalho é concluído, a saída deve ser gravada no console do PowerShell.

####Hive

O script a seguir executará o script __streaming.py__. Antes da execução, ele solicitará as informações da conta de Administrador/HTTPs para o cluster HDInsight.

    # Replace 'YourHDIClusterName' with the name of your cluster
	$clusterName = YourHDIClusterName
    $creds=Get-Credential
    #Get the cluster info so we can get the resource group, storage, etc.
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroup = $clusterInfo.ResourceGroup
    $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
    $container=$clusterInfo.DefaultStorageContainer
    $storageAccountKey=Get-AzureRmStorageAccountKey `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup `
        | %{ $_.Key1 }
    #Create a storage content and upload the file
    $context = New-AzureStorageContext `
        -StorageAccountName $storageAccountName `
        -StorageAccountKey $storageAccountKey
            
	$HiveQuery = "add file wasb:///streaming.py;" +
	             "SELECT TRANSFORM (clientid, devicemake, devicemodel) " +
	               "USING 'D:\Python27\python.exe streaming.py' AS " +
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

A saída para o trabalho do **Hive** deve ter aparência similar à seguinte:

	100041	RIM 9650	d476f3687700442549a83fac4560c51c
	100041	RIM 9650	d476f3687700442549a83fac4560c51c
	100042	Apple iPhone 4.2.x	375ad9a0ddc4351536804f1d5d0ea9b9
	100042	Apple iPhone 4.2.x	375ad9a0ddc4351536804f1d5d0ea9b9
	100042	Apple iPhone 4.2.x	375ad9a0ddc4351536804f1d5d0ea9b9

####Pig

O exemplo a seguir usará o script __jython.py__. Antes da execução, ele solicitará as informações de Administrador/HTTPs do cluster HDInsight.

	# Replace 'YourHDIClusterName' with the name of your cluster
	$clusterName = YourHDIClusterName

    $creds = Get-Credential
    #Get the cluster info so we can get the resource group, storage, etc.
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroup = $clusterInfo.ResourceGroup
    $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
    $container=$clusterInfo.DefaultStorageContainer
    $storageAccountKey=Get-AzureRmStorageAccountKey `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup `
        | %{ $_.Key1 }
    
    #Create a storage content and upload the file
    $context = New-AzureStorageContext `
        -StorageAccountName $storageAccountName `
        -StorageAccountKey $storageAccountKey
            
	$PigQuery = "Register wasb:///jython.py using jython as myfuncs;" +
	            "LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);" +
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

##<a name="troubleshooting"></a>Solucionar problemas

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

Para este trabalho.|Veja estes arquivos no contêiner blob
---|---
Hive|/HivePython/stderr<p>/HivePython/stdout
Pig|/PigPython/stderr<p>/PigPython/stdout

##<a name="next"></a>Próximas etapas

Se você precisa carregar módulos Python que não são fornecidos por padrão, consulte [Como implantar um módulo para o HDInsight do Azure](http://blogs.msdn.com/b/benjguin/archive/2014/03/03/how-to-deploy-a-python-module-to-windows-azure-hdinsight.aspx) para ver um exemplo de como fazer isso.

Para outras maneiras de usar o Pig e o Hive e para saber como usar o MapReduce, consulte os tutoriais a seguir.

* [Usar o Hive com o HDInsight](hdinsight-use-hive.md)

* [Usar o Pig com o HDInsight](hdinsight-use-pig.md)

* [Usar o MapReduce com o HDInsight](hdinsight-use-mapreduce.md)

<!---HONumber=AcomDC_1210_2015-->