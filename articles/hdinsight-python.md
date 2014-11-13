<properties urlDisplayName="Python with HDInsight" pageTitle="Usar o Python com o Hive e o Pig no Azure HDInsight" metaKeywords="" description="Saiba como usar as Fun&ccedil;&otilde;es Definidas pelo Usu&aacute;rio (UDF) do Python com o Hive e o Pig no Azure HDInsight." metaCanonical="" services="hdinsight" documentationCenter="" title="Use o Python com o Hive e o Pig no HDInsight" authors="larryfr" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />

# Use o Python com o Hive e o Pig no HDInsight

Hive e Pig são ótimos para trabalhar com dados no HDInsight, mas algumas vezes você precisa de uma linguagem de propósito mais geral. Tanto o Hive quanto o Pig permitem que você crie Funções Definidas pelo Usuário (UDF) usando uma variedade de linguagens de programação. Neste artigo, você aprenderá como usar uma UDF Python de Hive e Pig.

> [WACOM.NOTE] As etapas neste artigo se aplicam ao cluster do HDInsight versões 2.1, 3.0 e 3.1.

## Sumário

-   [Python no HDInsight][Python no HDInsight]
-   [Hive e Python][Hive e Python]
-   [Pig e Python][Pig e Python]
-   [Executando os exemplos][Executando os exemplos]
-   [Solucionar problemas][Solucionar problemas]
-   [Próximas etapas][Próximas etapas]

## <a name="python"></a>Python no HDInsight

O Python 2.7 é instalado por padrão nos clusters do HDInsight 3.0. A instalação pode ser encontrada na pasta D:\\Python. O Hive pode ser usado com essa versão do Python para processamento de fluxo (os dados são passados entre o Hive e o Python usando STDOUT/STDIN).

O HDInsight também inclui o Jython, que é uma implementação do Python gravada em Java. O Pig compreende como dialogar com o Jython sem precisar utilizar streaming, de modo que é preferível ao utilizar o Pig.

### <a name="hivepython"></a>Hive e Python

O Python pode ser utilizado como uma UDF por meio do Hive por meio da instrução HiveQL **TRANSFORM**. Por exemplo, o HiveQL a seguir chama um script do Python armazenado no arquivo **streaming.py**.

    add file wasb:///streaming.py;

    SELECT TRANSFORM (clientid, devicemake, devicemodel)
      USING 'D:\Python27\python.exe streaming.py' AS
      (clientid string, phoneLable string, phoneHash string)
    FROM hivesampletable
    ORDER BY clientid LIMIT 50;

Aqui está o que este exemplo faz:

1.  A instrução **add file** no início do arquivo acrescenta o arquivo **streaming.py** ao cache distribuído, de modo que ele se torna acessível por todos os nós presentes no cluster.

2.  A instrução **SELECT TRANSFORM ... USING 'D:\\Python27\\python.exe streaming.py'** seleciona dados da **hivesampletable**, e passa as variáveis clientid, devicemake e devicemodel para o script **streaming.py**.

    > [WACOM.NOTE] A cláusula **USING** especifica o caminho completo para o python.exe, já que ele não está no caminho.

3.  A cláusula **AS** descreve os campos retornados como resultado do **streaming.py**

<a name="streamingpy"></a>
Aqui está o arquivo **streaming.py** utilizado pelo exemplo HiveQL.

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

1.  Ler dados por meio do STDIN. Isso é realizado usando `sys.stdin.readline()` neste exemplo.

2.  O último caractere da linha nova é removido usando `string.strip(line, "\n ")`, já que nós apenas desejamos os dados de texto e não o indicador de final de linha.

3.  Ao realizar processamento de fluxo, uma única linha contém todos os valores com um caractere de tabulação entre cada par de valores. Assim, `string.split(line, "\t")` pode ser usado para dividir a entrada em cada guia, retornando somente os campos.

4.  Quando o processamento está concluído, a saída precisa ser gravada em STDOUT como uma linha única, com uma tabulação entre cada par de campos. Isso é realizado utilizando `print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])`.

5.  Tudo isso ocorre dentro de um circuito `while`, que se repetirá até que nenhum `line` seja lido, no ponto em que `break` sai do circuito e o script é encerrado.

Além disso, o script concatena somente os valores de entrada para `devicemake` e `devicemodel`, além de calcular um hash do valor concatenado. É bastante simples, mas ele descreve os princípios básicos de como qualquer script Python disparado por meio do Hive deve funcionar: Em circuito, ler todos os dados de entrada, inserir quebras em cada linha no ponto dos caracteres de tabulação, processar, gravar uma linha única de saída delimitada por caracteres de tabulação.

Consulte [Executando os exemplos][Executando os exemplos] para saber como executar este exemplo em seu cluster HDInsight.

### <a name="pigpython"></a>Pig e Python

Um script Python pode ser utilizado como uma UDF por meio do Pig por meio da instrução **GENERATE**. Por exemplo, o exemplo a seguir usa um script Python armazenado no arquivo **jython.py**.

    Register 'wasb:///jython.py' using jython as myfuncs;
    LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
    LOG = FILTER LOGS by LINE is not null;
    DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
    DUMP DETAILS;

Eis aqui como este exemplo funciona:

1.  Ele registra o arquivo contendo o script Python (**jython.py**), usando **Jython**, então, o expõe ao Pig como **myfuncs**. O Jython é uma implementação do Python em Java e é executado na mesma máquina virtual de Java que o Pig. Isso nos permite tratar o script Python como uma chamada tradicional de função vs. a abordagem de streaming usada com o Hive.

2.  A próxima linha carrega o arquivo de dados de amostra, **sample.log** em **LOGS**. Como esse arquivo de log não tem um esquema consistente, ele também define cada registro (**LINE** neste caso), como uma **matriz de caracteres**. A matriz de caracteres é, essencialmente, uma cadeia de caracteres.

3.  A terceira linha filtra e remove quaisquer valores nulos, armazenando o resultado da operação no **LOG**.

4.  Em seguida, ela faz a iteração pelos registros em **LOG** e usa **GENERATE** para chamar o método **create\_structure** contido no script **jython.py** carregado como **myfuncs**. **LINE** é usado para passar o registro atual para a função.

5.  Por fim, as saídas são depositadas em STDOUT usando o comando **DUMP**. Isso é só para mostrar imediatamente os resultados após a conclusão da operação; em um script real, você normalmente **ARMAZENARIA** os dados em um novo arquivo.

<a name="jythonpy"></a>
Aqui está o arquivo **jython.py** usado pelo exemplo Pig:

    @outputSchema("log: {(date:chararray, time:chararray, classname:chararray, level:chararray, detail:chararray)}")
    def create_structure(input):
      if (input.startswith('java.lang.Exception')):
        input = input[21:len(input)] + ' - java.lang.Exception'
      date, time, classname, level, detail = input.split(' ', 4)
      return date, time, classname, level, detail

Você se lembra que, anteriormente, nós apenas definimos a entrada **LINE** como uma matriz de caracteres porque não havia um esquema consistente para a entrada? O que o **jython.py** faz é transformar os dados em um esquema consistente para a saída. Funciona assim:

1.  A instrução **@outputSchema** define o formato dos dados que serão devolvidos ao Pig. Nesse caso, é uma **mala de dados**, que é um tipo de dado do Pig. A mala contém os campos a seguir, todos eles sendo matrizes de caracteres (cadeias de caracteres):

    -   date - a data em que a entrada no log foi criada
    -   time - o horário em que a entrada de log foi criada
    -   classname - o nome da classe para a qual a entrada foi criada
    -   level - o nível do log
    -   detail - detalhes para a entrada de log

2.  Em seguida, a **def create\_structure(input)** define a função à qual o Pig passará os itens de linha.

3.  Os dados de exemplo, **sample.log**, na maior parte das vezes estão em conformidade com o esquema de data, horário, nome de classe, nível e detalhe que desejamos retornar. Mas eles também contêm algumas linhas que começam com a cadeia '*java.lang.Exception*‘, que precisam ser modificadas para corresponder ao esquema. A instrução **if** busca essas linhas, então movimenta os dados de entrada para mover a cadeia de caracteres '*java.lang.Exception*' para o final, colocando os dados em linha com o nosso esquema de saída esperado.

4.  Em seguida, o comando **split** é utilizado para dividir os dados nos caracteres ocupando os quatro primeiros espaços. Isso resulta em cinco valores, que são atribuídos a **date**, **time**, **classname**, **level** e **detail**.

5.  Por fim, os valores são devolvidos ao Pig.

Quando os dados forem devolvidos para o Pig, ele terá um esquema consistente como definido na instrução **@outputSchema**.

Consulte [Executando os exemplos][Executando os exemplos] para saber como executar este exemplo em seu cluster HDInsight.

## <a name="running"></a>Executando os exemplos

Essas etapas usam o Microsoft Azure PowerShell. Se ele ainda não estiver instalado e configurado em sua máquina de desenvolvimento, consulte [Como instalar e configurar o PowerShell do Azure][Como instalar e configurar o PowerShell do Azure] antes de usar as etapas a seguir.

1.  Usando os exemplos do Python [streaming.py][streaming.py] e [jython.py][jython.py], crie cópias locais dos arquivos em seu computador do desenvolvimento.

2.  Use o script PowerShell a seguir para carregar os arquivos **streaming.py** e **jython.py** no servidor. Substitua o nome do seu cluster HDInsight do Azure e o caminho para os arquivos **streaming.py** e **jython.py** nas três primeiras linhas do script.

        $clusterName = YourHDIClusterName
        $pathToStreamingFile = "C:\path\to\streaming.py"
        $pathToJythonFile = "C:\path\to\jython.py"

        $hdiStore = get-azurehdinsightcluster -name $clusterName
        $storageAccountName = $hdiStore.DefaultStorageAccount.StorageAccountName.Split(".",2)[0]
        $storageAccountKey = $hdiStore.defaultstorageaccount.storageaccountkey
        $defaultContainer = $hdiStore.DefaultStorageAccount.StorageContainerName

        $destContext = new-azurestoragecontext -storageaccountname $storageAccountName -storageaccountkey $storageAccountKey
        set-azurestorageblobcontent -file $pathToStreamingFile -Container $defaultContainer -Blob "streaming.py" -context $destContext
        set-azurestorageblobcontent -file $pathToJythonFile -Container $defaultContainer -Blob "jython.py" -context $destContext

    Este script obtém informações a partir de seu cluster HDInsight, então, extrai a conta e a chave para a conta de armazenamento padrão, além de carregar os arquivos para a raiz do contêiner.

    > [WACOM.NOTE] É possível encontrar outros métodos para carregar os scripts no documento [Carregar dados para trabalhos do Hadoop no HDInsight][Carregar dados para trabalhos do Hadoop no HDInsight].

### Usando o painel do Hive (somente exemplo do Hive)

1.  Depois de fazer o upload do arquivo, abra um navegador e navegue até https://YourClusterName.azurehdinsight.net/. Quando for solicitado a informar as credenciais, digite o nome de usuário e a senha de administrador para seu cluster.

    > [WACOM.NOTE] Você também pode usar o link **Gerenciar Cluster** na parte inferior do **Painel** do HDInsight no portal de gerenciamento do Azure para executar o painel do Hive.

2.  Usando o **Editor do Hive**, substitua a linha `select * from hivesampletable` pelo HiveQL a seguir.

        add file wasb:///streaming.py;
        SELECT TRANSFORM (clientid, devicemake, devicemodel)
          USING 'D:\Python27\python.exe streaming.py' AS
          (clientid string, phoneLable string, phoneHash string)
        FROM hivesampletable
        ORDER BY clientid LIMIT 50;

3.  Clique no botão **Enviar** para enviar o trabalho. Dependendo da versão de cluster do HDInsight, você pode ser redirecionado à página de Detalhes do Trabalho. Caso contrário, selecione **Visualizar Detalhes** na área **Sessão do Trabalho** na parte inferior da página.

4.  A página **Detalhes do Trabalho** será atualizada até que o trabalho seja concluído. Uma vez concluído, as informações sobre o trabalho, assim como a saída, serão exibidas.

### Usando o PowerShell (exemplos de Hive e Pig)

Após carregar os arquivos, use os scripts do PowerShell a seguir para iniciar os trabalhos. Quando o trabalho é concluído, a saída deve ser gravada no console do PowerShell.

**Para executar o Trabalho do Hive**

    # Replace 'YourHDIClusterName' with the name of your cluster
    $clusterName = YourHDIClusterName

    $HiveQuery = "add file wasb:///streaming.py;" +
                 "SELECT TRANSFORM (clientid, devicemake, devicemodel) " +
                   "USING 'D:\Python27\python.exe streaming.py' AS " +
                   "(clientid string, phoneLable string, phoneHash string) " +
                 "FROM hivesampletable " +
                 "ORDER BY clientid LIMIT 50;"

    $jobDefinition = New-AzureHDInsightHiveJobDefinition -Query $HiveQuery -StatusFolder '/hivepython'

    $job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
    Write-Host "Wait for the Hive job to complete ..." -ForegroundColor Green
    Wait-AzureHDInsightJob -Job $job
    # Uncomment the following to see stderr output
    # Get-AzureHDInsightJobOutput -StandardError -JobId $job.JobId -Cluster $clusterName
    Write-Host "Display the standard output ..." -ForegroundColor Green
    Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput

A saída para o trabalho do **Hive** deve ter aparência similar à seguinte:

    100041  RIM 9650    d476f3687700442549a83fac4560c51c
    100041  RIM 9650    d476f3687700442549a83fac4560c51c
    100042  Apple iPhone 4.2.x  375ad9a0ddc4351536804f1d5d0ea9b9
    100042  Apple iPhone 4.2.x  375ad9a0ddc4351536804f1d5d0ea9b9
    100042  Apple iPhone 4.2.x  375ad9a0ddc4351536804f1d5d0ea9b9

**Para executar o trabalho Pig**

    # Replace 'YourHDIClusterName' with the name of your cluster
    $clusterName = YourHDIClusterName

    $PigQuery = "Register wasb:///jython.py using jython as myfuncs;" +
                "LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);" +
                "LOG = FILTER LOGS by LINE is not null;" +
                "DETAILS = foreach LOG generate myfuncs.create_structure(LINE);" +
                "DUMP DETAILS;"

    $jobDefinition = New-AzureHDInsightPigJobDefinition -Query $PigQuery -StatusFolder '/pigpython'

    $job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
    Write-Host "Wait for the Pig job to complete ..." -ForegroundColor Green
    Wait-AzureHDInsightJob -Job $job
    # Uncomment the following to see stderr output
    # Get-AzureHDInsightJobOutput -StandardError -JobId $job.JobId -Cluster $clusterName
    Write-Host "Display the standard output ..." -ForegroundColor Green
    Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput

A saída para o trabalho **Pig** deve ter aparência similar à seguinte:

    ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
    ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
    ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
    ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
    ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

## <a name="troubleshooting"></a> Solucionar problemas

Ambos os scripts de Powershell de exemplo utilizados para executar os exemplos contêm uma linha comentada que exibirá a saída de erro para o trabalho. Se você não estiver vendo a saída esperada para o trabalho, remova o comentário da linha a seguir e veja se as informações de erro indicam um problema.

    # Get-AzureHDInsightJobOutput -StandardError -JobId $job.JobId -Cluster $clusterName

As informações de erro (STDERR) e o resultado do trabalho (STDOUT) também são registrados para o contêiner blob padrão para seus clusters nos locais a seguir.

<table>
<tr>
<td>
Para este trabalho.

</td>
<td>
Veja estes arquivos no contêiner blob

</td>
</tr>
<td>
Hive

</td>
<td>
/HivePython/stderr</br>/HivePython/stdout

</td>
</tr>
<td>
Pig

</td>
<td>
/PigPython/stderr</br>/PigPython/stdout

</td>
</tr>
</table>
## <a name="next"></a>Próximas etapas

Se você precisa carregar módulos Python que não são fornecidos por padrão, consulte [Como implantar um módulo para o HDInsight do Azure][Como implantar um módulo para o HDInsight do Azure] para ver um exemplo de como fazer isso.

Se você gostaria de executar trabalhos no HDInsight remotamente sem usar o PowerShell, consulte [Como usar o HDInsight do Azure em Linux][Como usar o HDInsight do Azure em Linux] para obter um exemplo de uso do Python para executar trabalhos via API REST do WebHCat.

  [Python no HDInsight]: #python
  [Hive e Python]: #hivepython
  [Pig e Python]: #pigpython
  [Executando os exemplos]: #running
  [Solucionar problemas]: #troubleshooting
  [Próximas etapas]: #next
  [Como instalar e configurar o PowerShell do Azure]: http://azure.microsoft.com/pt-br/documentation/articles/install-configure-powershell/
  [streaming.py]: #streamingpy
  [jython.py]: #jythonpy
  [Carregar dados para trabalhos do Hadoop no HDInsight]: /pt-br/documentation/articles/hdinsight-upload-data/
  [Como implantar um módulo para o HDInsight do Azure]: http://blogs.msdn.com/b/benjguin/archive/2014/03/03/how-to-deploy-a-python-module-to-windows-azure-hdinsight.aspx
  [Como usar o HDInsight do Azure em Linux]: http://blogs.msdn.com/b/benjguin/archive/2014/02/18/how-to-use-hdinsight-from-linux.aspx
