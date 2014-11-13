<properties urlDisplayName="Use Hadoop Hive in HDInsight" pageTitle="Usar Hive do Hadoop no HDInsight | Azure" metaKeywords="" description="Saiba como usar o Hive com o HDInsight. Voc&ecirc; usar&aacute; um arquivo de log como entrada em uma tabela do HDInsight e usar&aacute; o HiveQL para consultar as estat&iacute;sticas b&aacute;sicas de dados e relat&oacute;rios." metaCanonical="" services="hdinsight" documentationCenter="" title="Usar o Hive com o HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/25/2014" ms.author="jgao" />

# Usar o Hive com Hadoop no HDInsight

O [Apache Hive][Apache Hive] fornece um meio de executar o trabalho MapReduce por meio de uma linguagem de script semelhante a SQL, chamada *HiveQL*. O Hive é um sistema de armazenamento de dados para Hadoop, que permite o resumo, consulta e análise de grandes volumes de dados. Neste artigo, você usa HiveQL para consultar um arquivo de dados de amostra fornecido como parte do provisionamento de cluster do HDInsight.

**Pré-requisitos:**

-   Você deve ter provisionado um **cluster HDInsight**. Para obter uma explicação sobre como fazer isso com o portal do Azure, consulte [Introdução ao HDInsight][Introdução ao HDInsight]. Para obter instruções sobre as várias outras maneiras nas quais esses clusters podem ser criados, consulte [Provisionar clusters HDInsight][Provisionar clusters HDInsight].

-   Você deve ter instalado o **PowerShell do Azure** em sua estação de trabalho. Para obter instruções sobre como fazer isso, consulte [Instalar e configurar o PowerShell do Azure][Instalar e configurar o PowerShell do Azure].

**Tempo estimado para conclusão:** 30 minutos

## Neste artigo

-   [Caso de uso do Hive][Caso de uso do Hive]
-   [Carregar dados para tabelas Hive][Carregar dados para tabelas Hive]
-   [Executar consultas Hive usando o PowerShell][Executar consultas Hive usando o PowerShell]
-   [Usar o Tez para desempenho aprimorado][Usar o Tez para desempenho aprimorado]
-   [Próximas etapas][Próximas etapas]

## <span id="usage"></span></a>Caso de uso do Hive

![HDI.HIVE.Architecture][HDI.HIVE.Architecture]

O Hive projeta algum tipo de estrutura de um conjunto de dados altamente não estruturado (que é o foco do Hadoop) e, então, permite que você consulte os dados usando HiveQL. O Hive fornece uma camada de abstração sobre a estrutura MapReduce baseada em Java, permitindo aos usuários consultar dados sem conhecimento de Java ou MapReduce. O HiveQL fornece um wrapper simples semelhante ao SQL que permite que as consultas sejam escritas em HiveQL e, em seguida, compiladas no MapReduce para você pelo HDInsight e executadas no cluster. Outros benefícios do Hive são:

-   O Hive permite que os programadores familiarizados com a estrutura do MapReduce conectem mapeadores e redutores personalizados para executar uma análise mais sofisticada que pode não ser compatível com os recursos internos da linguagem HiveQL.
-   O Hive é mais adequado para processamento em lotes de grandes quantidades de dados imutáveis (como logs da web). Não é adequado para aplicativos de transações que precisam de tempos de resposta muito rápidos, como sistemas de gerenciamento de banco de dados.
-   O Hive é otimizado para escalabilidade (mais máquinas podem ser adicionadas dinamicamente ao cluster Hadoop), extensibilidade (dentro da estrutura do MapReduce e com outras interfaces de programação) e tolerância a falhas. A latência não é uma consideração importante no design.

## <span id="uploaddata"></span></a>Carregar dados para tabelas Hive

O HDInsight usa o contêiner do armazenamento de Blob do Azure como o sistema de arquivos padrão para clusters do Hadoop. Alguns arquivos de dados de amostra são adicionados ao armazenamento de blob como parte do provisionamento de cluster. Você pode usar esses arquivos de dados de amostra para executar consultas do Hive no cluster. Se desejar, também é possível carregar seu próprio arquivo de dados para a conta de armazenamento de blob associada ao cluster. Consulte [Carregar dados para o HDInsight][Carregar dados para o HDInsight] para obter instruções. Para obter mais informações sobre como o armazenamento de Blob do Azure é usado com HDInsight, consulte [Usar armazenamento de blob do Azure com HDInsight][Usar armazenamento de blob do Azure com HDInsight].

Este artigo usa um arquivo de amostra *log4j* distribuído com o cluster HDInsight e armazenado no *\\example\\data\\sample.log* sob seu contêiner de armazenamento de blob. É possível também gerar seus próprios arquivos log4j usando o utilitário de registro em log [Apache Log4j][Apache Log4j] e, então, carregar para o contêiner de blob. Cada log dentro do arquivo consiste em uma linha de campos que contém um campo `[LOG LEVEL]` para mostrar o tipo e a gravidade. Por exemplo:

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937 

No exemplo acima, o nível de log é ERRO.

Você pode acessar os arquivos usando a seguinte sintaxe de dentro do seu aplicativo:

    wasb://<containerName>@<AzureStorageName>.blob.core.windows.net

Assim, para acessar o arquivo sample.log, é possível usar a seguinte sintaxe:

    wasb://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log

Substitua *mycontainer* pelo nome do contêiner de Blob do Azure e *mystorage* pelo nome da conta de armazenamento do Azure.

Como o arquivo é armazenado no sistema de arquivos padrão, você também pode acessar o arquivo usando o seguinte:

    wasb:///example/data/sample.log
    /example/data/sample.log

## <span id="runhivequeries"></span></a> Executar consultas Hive usando o PowerShell

Na última seção, você identificou um arquivo *sample.log* que será usado para executar consultas do Hive. Nesta seção, você executará o HiveQL para criar uma tabela hive, carregar os dados de amostra na tabela hive e, em seguida, consultar os dados para descobrir a quantidade de logs de erro presentes no arquivo.

Este artigo fornece instruções para usar os cmdlets do PowerShell do Azure para executar uma consulta Hive. Antes de continuar com esta seção, você precisa primeiro configurar o ambiente local e configurar a conexão ao Azure, conforme explicado na seção **Pré-requisitos** no início deste tópico.

As consultas Hive podem ser executadas no PowerShell usando o cmdlet **Start-AzureHDInsightJob** ou o cmdlet **Invoke-Hive**.

**Para executar as consultas Hive usando Start-AzureHDInsightJob**

1.  Abra uma janela de console do PowerShell do Azure. As instruções podem ser encontradas em [Instalar e configurar o PowerShell do Azure][Instalar e configurar o PowerShell do Azure].
2.  Execute o seguinte comando para conectar-se à sua assinatura do Azure:

        Add-AzureAccount

    Será solicitado que você insira suas credenciais de conta do Azure.

3.  Defina as variáveis no script a seguir e execute-o:

        # Provide Azure subscription name, and the Azure Storage account and container that is used for the default HDInsight file system.
        $subscriptionName = "<SubscriptionName>"
        $storageAccountName = "<AzureStorageAccountName>"
        $containerName = "<AzureStorageContainerName>"

        # Provide HDInsight cluster name Where you want to run the Hive job
        $clusterName = "<HDInsightClusterName>"

4.  Execute o seguinte script para definir as consultas HiveQL. Você pode escolher criar uma tabela do Hive *interna* ou *externa*.

    -   Com tabelas internas, os dados de amostra usados são movidos do local existente para a pasta \\hive\\warehouse\\\<*tablename\>*. Assim, ao eliminar uma tabela interna, os dados associados também são excluídos. Se você usar as tabelas internas e desejar executar o script novamente, deverá carregar o arquivo *sample.log* novamente no armazenamento de blob.
    -   Com tabelas externas, os dados não são movidos do local original. Você também pode usar a tabela externa quando o arquivo de dados estiver localizado em outro contêiner ou conta de armazenamento.

            # HiveQL queries
            # Use the internal table option. 
            $queryString = "DROP TABLE log4jLogs;" +
                           "CREATE TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ';" +
                           "LOAD DATA INPATH 'wasb://$containerName@$storageAccountName.blob.core.windows.net/example/data/sample.log' OVERWRITE INTO TABLE log4jLogs;" +
                           "SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;"

            # Use the external table option. 
            $queryString = "DROP TABLE log4jLogs;" +
                            "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasb://$containerName@$storageAccountName.blob.core.windows.net/example/data/';" +
                            "SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;"

    O comando DROP TABLE exclui a tabela e o arquivo de dados, caso a tabela já exista. O comando LOAD DATA HiveQL move o arquivo de dados de \\example\\data para a pasta \\hive\\warehouse\\\<*tablename\>*.

5.  Execute o script a seguir para criar uma definição de trabalho Hive:

        # Create a Hive job definition 
        $hiveJobDefinition = New-AzureHDInsightHiveJobDefinition -Query $queryString 

    Você também pode usar a opção -File para especificar um arquivo script HiveQL no HDFS.

6.  Execute o script a seguir para enviar o trabalho Hive:

        # Submit the job to the cluster 
        Select-AzureSubscription $subscriptionName
        $hiveJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $hiveJobDefinition

7.  Execute o script a seguir para aguardar a conclusão do trabalho Hive:

        # Wait for the Hive job to complete
        Wait-AzureHDInsightJob -Job $hiveJob -WaitTimeoutInSeconds 3600

8.  Execute o script a seguir para imprimir a saída padrão:

        # Print the standard error and the standard output of the Hive job.
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardOutput

    ![HDI.HIVE.PowerShell][HDI.HIVE.PowerShell]

    O resultado é:

        [ERROR] 3

    isso significa que há três instâncias de logs de ERROR no arquivo *sample.log*.

Se necessário, também é possível importar a saída das suas consultas para o Microsoft Excel para mais análise. Para instruções, consulte [Conectar o Excel ao Hadoop com o Power Query][Conectar o Excel ao Hadoop com o Power Query].

**Para enviar consultas Hive usando Invoke-Hive**

1.  Abra uma janela de console do PowerShell do Azure.
2.  Execute o seguinte comando para conectar-se à sua assinatura do Azure:

        Add-AzureAccount

    Será solicitado que você insira suas credenciais de conta do Azure.

3.  Defina as variáveis no script a seguir e execute-o:

        # Provide Azure subscription name, Azure Storage account, and container.
        $subscriptionName = "<SubscriptionName>"
        $storageAccountName = "<AzureStorageAccountName>"
        $containerName = "<AzureStorageContainerName>"

        # Provide HDInsight cluster name Where you want to run the Hive job
        $clusterName = "<HDInsightClusterName>"

4.  Conecte-se ao cluster HDInsight.

        # Connect to the cluster
        Use-AzureHDInsightCluster $clusterName

5.  Execute o script a seguir para invocar as consultas HiveQL:

        # Run the query to create an internal Hive table, load sample data
        $response = Invoke-Hive -Query @"
        >> DROP TABLE log4jLogs;
        >> CREATE TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW   
            FORMAT DELIMITED FIELDS TERMINATED BY ' ';
        >> LOAD DATA INPATH 'wasb://$containerName@$storageAccountName.blob.core.windows.net/example/data/
            sample.log' OVERWRITE INTO TABLE log4jLogs;
        >> SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4; 
        >> "@

6.  Imprima a saída no console.

        # print the output on the console
        Write-Host $response

    A saída se parece com o seguinte:

    ![Saída de Invoke-Hive do PowerShell][Saída de Invoke-Hive do PowerShell]

    Para consultas HiveQL mais longas, é possível usar Here-Strings do PowerShell ou arquivos de script HiveQL. Os fragmentos a seguir mostram como usar o cmdlet *Invoke-Hive* para executar um arquivo de script HiveQL. O arquivo de script HiveQL deve ser carregado no WASB.

        Invoke-Hive -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"

    Para obter mais informações sobre Here-Strings, consulte [Usando Here-Strings do Windows PowerShell][Usando Here-Strings do Windows PowerShell].

Se necessário, também é possível importar a saída das suas consultas para o Microsoft Excel para mais análise. Para instruções, consulte [Conectar o Excel ao Hadoop com o Power Query][Conectar o Excel ao Hadoop com o Power Query].

## <span id="usetez"></span></a>Usando Tez para desempenho aprimorado

[Apache Tez][Apache Tez] é uma estrutura que permite que aplicativos com uso intenso de dados como o Hive executem de maneira muito mais eficiente em escala. Na versão mais recente do HDInsight, o Hive agora tem suporte para a execução no Tez. No momento, isso está desativado por padrão e deve ser habilitado. Em futuras versões de cluster, esse recurso estará ativado por padrão. Para aproveitar o Tez, o seguinte valor deve ser definido para uma consulta Hive:

        set hive.execution.engine=tez;
        

Isso pode ser enviado por consulta colocando-o no início da sua consulta. Também é possível definir esse recurso para estar ativado por padrão definindo o valor de configuração no momento da criação do cluster. Você pode encontrar mais detalhes em [Provisionamento de clusters do HDInsight][Provisionar clusters HDInsight].

Os [documentos de design do Hive no Tez][documentos de design do Hive no Tez] contêm vários detalhes das escolhas de implantação e configurações de ajuste.

## <span id="nextsteps"></span></a>Próximas etapas

Embora o Hive facilite consultar dados usando uma linguagem de consulta semelhante ao SQL, outros componentes disponíveis com o HDInsight fornecem funcionalidade complementar, como a movimentação e a transformação de dados. Para saber mais, consulte os seguintes artigos:

-   [Usar o Oozie com o HDInsight][Usar o Oozie com o HDInsight]
-   [Enviar trabalhos Hadoop de forma programática][Enviar trabalhos Hadoop de forma programática]
-   [Usando o Pig com o HDInsight][Usando o Pig com o HDInsight]
-   [Analisar dados de atraso de voo usando o HDInsight][Analisar dados de atraso de voo usando o HDInsight]
-   [Documentação do SDK do Azure HDInsight][Documentação do SDK do Azure HDInsight]
-   [Carregar dados no HDInsight][Carregar dados para o HDInsight]
-   [Introdução ao Azure HDInsight][Introdução ao HDInsight]

  [Apache Hive]: http://hive.apache.org/
  [Introdução ao HDInsight]: ../hdinsight-get-started/
  [Provisionar clusters HDInsight]: ../hdinsight-provision-clusters/
  [Instalar e configurar o PowerShell do Azure]: ../install-configure-powershell/
  [Caso de uso do Hive]: #usage
  [Carregar dados para tabelas Hive]: #uploaddata
  [Executar consultas Hive usando o PowerShell]: #runhivequeries
  [Usar o Tez para desempenho aprimorado]: #usetez
  [Próximas etapas]: #nextsteps
  [HDI.HIVE.Architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png
  [Carregar dados para o HDInsight]: ../hdinsight-upload-data/
  [Usar armazenamento de blob do Azure com HDInsight]: ../hdinsight-use-blob-storage
  [Apache Log4j]: http://en.wikipedia.org/wiki/Log4j
  [HDI.HIVE.PowerShell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
  [Conectar o Excel ao Hadoop com o Power Query]: http://azure.microsoft.com/pt-br/documentation/articles/hdinsight-connect-excel-power-query/
  [Saída de Invoke-Hive do PowerShell]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
  [Usando Here-Strings do Windows PowerShell]: http://technet.microsoft.com/pt-br/library/ee692792.aspx
  [Apache Tez]: http://tez.apache.org
  [documentos de design do Hive no Tez]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
  [Usar o Oozie com o HDInsight]: ../hdinsight-use-oozie/
  [Enviar trabalhos Hadoop de forma programática]: ../hdinsight-submit-hadoop-jobs-programmatically/
  [Usando o Pig com o HDInsight]: ../hdinsight-use-pig/
  [Analisar dados de atraso de voo usando o HDInsight]: ../hdinsight-analyze-flight-delay-data/
  [Documentação do SDK do Azure HDInsight]: http://msdnstage.redmond.corp.microsoft.com/pt-br/library/dn479185.aspx
