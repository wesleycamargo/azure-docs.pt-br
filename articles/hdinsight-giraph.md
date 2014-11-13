<properties title="Como usar o Giraph com o HDInsight" pageTitle="Como usar o Apache Giraph com o Azure HDInsight" description="Saiba como usar o Apache Giraph para realizar o processamento de gr&aacute;ficos com o Azure HDInsight" metaKeywords="Azure HDInsight Apache Giraph, hdinsight giraph, hdinsight graph, hadoop giraph, azure hadoop, hadoop graph" services="hdinsight" solutions="big-data" documentationCenter="" authors="larryfr" videoId="" scriptId="" manager="paulettm" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/14/2014" ms.author="larryfr" />

## Aprenda como utilizar o Apache Giraph com o HDInsight do Azure (Hadoop)

O [Apache Giraph][Apache Giraph] permite que você realize processamento de tabelas usando o Hadoop, além de poder ser utilizado com o HDInsight do Azure.

Relações de modelo de tabelas entre objetos, como as conexões entre roteadores em uma rede grande como a Internet, ou relações entre pessoas em redes sociais (algumas vezes mencionadas como uma tabela social). O processamento de tabelas permite que você faça a análise das relações entre objetos em uma tabela, como:

-   Identificando aliados em potencial com base em seus relacionamentos atuais

-   Identificando a rota mais curta entre dois computadores em uma rede

-   Calculando a classificação de páginas das páginas web

## Você aprenderá como

-   [Compilar e implantar o Apache Giraph em um cluster HDInsight][Compilar e implantar o Apache Giraph em um cluster HDInsight]

-   [Execute o exemplo SimpleShortestPathsComputation][Execute o exemplo SimpleShortestPathsComputation]

    Para uma lista de outros exemplos fornecidos com o Giraph, consulte [Pacote org.apache.giraph.examples][Pacote org.apache.giraph.examples].

-   [Resolva os problemas que você eventualmente encontrar][Resolva os problemas que você eventualmente encontrar]

## Requisitos

-   Um cluster do Azure HDInsight, versão 3.0 ou 3.1

-   [Git][Git]

-   Java 1.6

-   [Maven][Maven] 3 ou posterior

## <span id="build"></span></a>Compilar e implantar o Giraph

O Giraph não é fornecido como parte do cluster HDInsight, portanto, precisa ser compilado por meio da origem. Você pode encontrar mais informações sobre a compilação do Giraph no [repositório do Giraph][repositório do Giraph].

1.  Atualmente (14/07/2014), o Giraph requer um patch para funcionar com o armazenamento de arquivos WASB utilizado pelo HDInsight. O patch foi enviado ao projeto Apache Giraph, mas ainda não foi aceito. Baixe o patch da seção **anexos** do [GIRAPH-930][GIRAPH-930] e salve-o no drive local como **giraph-930.diff**.

2.  por meio de uma linha de comando, utilize o comando Git a seguir para criar um clone do repositório do Giraph.

        git clone https://github.com/apache/giraph.git

3.  Mude os diretórios no diretório **giraph** criado pela operação de clonagem na etapa 2.

        cd giraph

4.  Agregue o patch ao repositório local utilizando o comando a seguir.

        git apply giraph-930.diff

    Substitua **giraph-930.diff** pelo caminho para o arquivo que você criou na Etapa 1.

5.  Compile o Giraph para a sua versão de cluster HDInsight, usando um dos comandos a seguir.

    -   Para **HDInsight 3,0** (Hadoop 2,2)

            mvn package -Phadoop_0.20.203 - DskipTests

    -   Para **HDInsight 3.1** (Hadoop 2.4)

            mvn package -Phadoop_0.23 -DskipTests

    Após a compilação estar concluída, você encontrará o arquivo JAR de exemplo em **\\giraph\\giraph-examples\\target**.

6.  Carregue o arquivo JAR de exemplo no armazenamento primário para seu cluster HDInsight usando o [PowerShell do Azure][PowerShell do Azure] e o [HDInsight-Tools][HDInsight-Tools].

        Add-HDInsightFile giraph-examples-1.1.0-SNAPSHOT-for-hadoop-0.23.1-jar-with-dependencies.jar example/jars/giraph.jar clustername

    Substitua **giraph-examples-1.1.0-SNAPSHOT-for-hadoop-0.23.1-jar-with-dependencies.jar** pelo caminho e o nome do arquivo JAR produzido na etapa anterior, e **clustername** pelo nome de seu cluster HDInsight. Por exemplo, se você compilar o pacote com o parâmetro `-Phadoop_0.20.203`, o nome de arquivo do JAR incluirá **hadoop-0.20.203**.

    Assim que o comando estiver concluído, o arquivo JAR terá sido carregado para wasb:///example/jars/giraph.jar.

    > [WACOM.NOTE] Para obter uma lista de ferramentas que podem ser utilizadas para carregar arquivos para o HDInsight, consulte [Carregar dados para trabalhos do Hadoop no HDInsight][Carregar dados para trabalhos do Hadoop no HDInsight].

## <span id="run"></span></a>Execute o exemplo

A SimpleShortestPathsComputation demonstra a implementação do [Pregel][Pregel] básico para encontrar a rota mais curta entre objetos em uma tabela. Use as etapas a seguir para carregar os dados amostrais, execute um trabalho usando o exemplo SimpleShortestPathsComputation e, então, visualize os resultados.

> [WACOM.NOTE] A origem para esse e outros exemplos está disponível na [ramificação versão-1.1][ramificação versão-1.1] do [repositório GitHub][repositório do Giraph].

1.  Crie um novo arquivo chamado **tiny\_graph.txt**. Ele deve conter as linhas a seguir.

        [0,0,[[1,1],[3,3]]]
        [1,0,[[0,1],[2,2],[3,1]]]
        [2,0,[[1,2],[4,4]]]
        [3,0,[[0,3],[1,1],[4,4]]]
        [4,0,[[3,4],[2,4]]]

    Esses dados descrevem uma relação entre objetos em uma [tabela direcionada][tabela direcionada], usando o formato `[source_id,source_value,[[dest_id], [edge_value],...]]`. Cada linha representa uma relação entre um objeto **source\_id** e um ou mais objetos **dest\_id**. O **edge\_value** (ou peso), pode ser considerado como a força ou distância da conexão entre o **source\_id** e o **dest\_id**.

    Desenhado e utilizando o valor (ou peso) como distância entre os objetos, os dados acima podem se parecer com os demonstrados aqui.

    ![tiny\_graph.txt Desenhado como círculos com linhas de distância variável entre][tiny\_graph.txt Desenhado como círculos com linhas de distância variável entre]

2.  Carregue o arquivo **tiny\_graph.txt** no armazenamento primário para o seu cluster HDInsight usando o [PowerShell do Azure][PowerShell do Azure] e as [HDInsight-Tools][HDInsight-Tools].

        Add-HDInsightFile tiny_graph.txt example/data/tiny_graph.txt clustername

    Substitua o nome do cluster pelo nome do seu cluster HDInsight.

3.  Use o PowerShell a seguir para executar o exemplo **SimpleShortstPathsComputation**, usando o arquivo **tiny\_graph.txt** como entrada. Isso requer que você tenha instalado e configurado o [PowerShell do Azure][PowerShell do Azure].

        $clusterName = "clustername"
        # Giraph examples JAR
        $jarFile = "wasb:///example/jars/giraph.jar"
        # Arguments for this job
        $jobArguments = "org.apache.giraph.examples.SimpleShortestPathsComputation", `
                        "-ca", "mapred.job.tracker=headnodehost:9010", `
                        "-vif", "org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat", `
                        "-vip", "wasb:///example/data/tinygraph.txt", `
                        "-vof", "org.apache.giraph.io.formats.IdWithValueTextOutputFormat", `
                        "-op",  "wasb:///example/output/shortestpaths", `
                        "-w", "2"
        # Create the definition
        $jobDefinition = New-AzureHDInsightMapReduceJobDefinition `
          -JarFile $jarFile `
          -ClassName "org.apache.giraph.GiraphRunner" `
          -Arguments $jobArguments

        # Run the job, write output to the PowerShell window
        $job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
        Write-Host "Wait for the job to complete ..." -ForegroundColor Green
        Wait-AzureHDInsightJob -Job $job
        Write-Host "STDERR"
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardError
        Write-Host "Display the standard output ..." -ForegroundColor Green
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput

    No exemplo acima, substitua **clustername** pelo nome do seu cluster HDInsight.

### Exibir os resultados

Assim que o trabalho tiver sido concluído, os resultados serão armazenados na pasta **wasb:///example/out/shotestpaths** como arquivos **part-m-\#\#\#\#\#**. Use o [PowerShell do Azure][PowerShell do Azure] e as [HDInsight-Tools][HDInsight-Tools] para baixar os arquivos de saída.

    Find-HDInsightFile example/output/shortestpaths/part* clustername | foreach-object { Get-HDInsightFile $_.name .  itsfullofstorage }
    Cat example/output/shortestpaths/part*

Isso criará a estrutura de diretórios **example/output/shortestpaths** no diretório atual, e baixará os arquivos começando pela **parte**. O cmdlet **Cat** exibirá, então, os conteúdos dos arquivos, que devem ser semelhantes ao exibido a seguir.

    0   1.0
    4   5.0
    2   2.0
    1   0.0
    3   1.0

O exemplo SimpleShortestPathComputation é programado em código fixo para começar com a ID de objeto 1 e encontrar o caminho mais curto para outros objetos. Portanto, a saída deve ser lida como `destination_id distance`, onde a distância é o valor (ou peso) dos limites entre os quais se percorre entre a ID de objeto 1 e a ID de destino.

Visualizando isso, você pode verificar os resultados percorrendo os caminhos mais curtos entre a ID 1 e todos os outros objetos. Observe que o caminho mais curto entre a ID 1 e a ID 4 é o 5. Essa é a distância total entre as <span style="color:orange">IDs 1 e 3</span>, depois entre as <span style="color:red">IDs 3 e 4</span>.

![Desenho dos objetos como círculos com os percursos mais curtos entre][Desenho dos objetos como círculos com os percursos mais curtos entre]

## <span id="tshoot"></span></a> Solucionar problemas

### Diretório de saída já existe

Os trabalhos do Giraph criam o diretório de saída especificado no tempo de execução. Se o diretório já existir, ocorrerá um erro afirmando que o diretório de saída já existe.

Se você deseja executar um trabalho múltiplas vezes, é preciso remover o diretório de saída entre trabalhos ou então especificar um diretório de saída diferente para cada trabalho.

### <span id="cmd"></span></a>Usando a linha de comando do Hadoop

Embora este artigo demonstre como executar um trabalho Giraph via PowerShell, você também pode executar o trabalho usando a linha de comando do Hadoop.

> [WACOM.NOTE] A linha de comando do Hadoop só está disponível ao se conectar ao cluster HDInsight, usando a área de trabalho remota.
>
> As sessões de desktop remoto para recursos de computador do Azure, como o cluster HDInsight, só podem funcionar por meio de clientes de desktop remotos baseados em Windows.

Para se conectar ao cluster HDInsight, realize as etapas a seguir:

1.  Utilize o [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure], selecione seu cluster HDInsight e, então, selecione **Configuração**.

2.  Na parte inferior da página, selecione **Habilitar Modo Remoto** e forneça o nome de usuário, senha e data de vencimento para a conexão via área de trabalho remota.

3.  Após a solicitação para habilitar a área de trabalho remota ser processada, uma nova entrada para se **Conectar** aparecerá na parte inferior da página. Selecione essa entrada para baixar o arquivo .RDP para a sessão de área de trabalho remota.

4.  O arquivo .RDP pode ser salvo ou aberto imediatamente para executar o cliente de área de trabalho remota. Durante o processo de conexão, será solicitado que você forneça o nome de usuário e a senha utilizados por você ao habilitar a conexão via área de trabalho remota.

5.  Uma vez conectado, use o ícone **Linha de comando do Hadoop** na área de trabalho para iniciar a linha de comando do Hadoop.

6.  O exemplo a seguir demonstra como copiar o arquivo **giraph.jar** para o headnode do cluster, depois executar o trabalho usando a linha de comando do Hadoop.

        hadoop fs -copyToLocal wasb:///example/jar/giraph.jar
        hadoop jar giraph.jar org.apache.giraph.GiraphRunner org.apache.giraph.examples.SimpleShortestPathsComputation -ca "mapred.job.tracker=headnodehost:9010" -vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat -vip wasb:///example/data/tinygraph.txt -vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat -op wasb:///example/output/shortestpaths -w 2

### Versões mais antigas do HDInsight

Se você deseja usar o Giraph com versões mais antigas do HDInsight, é preciso compilá-lo para a versão específica do Hadoop suportada por essa versão. Consulte [Novidades em versões de cluster HDInsight][Novidades em versões de cluster HDInsight] para determinar qual versão do Hadoop corresponde à sua versão do HDInsight.

Além disso, versões anteriores do HDInsight podem exigir que você execute o trabalho do Giraph por meio da linha de comando do Hadoop. Se você recebe mensagens de erro ao executar o trabalho por meio do PowerShell, tente executar o trabalho por meio da [Linha de comando do Hadoop][Linha de comando do Hadoop].

## Próximas etapas

Agora que você aprendeu como usar o Giraph com o HDInsight, tente fazer [Pig][Pig] e [Hive][Hive] com o HDInsight.

  [Apache Giraph]: http://giraph.apache.org
  [Compilar e implantar o Apache Giraph em um cluster HDInsight]: #build
  [Execute o exemplo SimpleShortestPathsComputation]: #run
  [Pacote org.apache.giraph.examples]: https://giraph.apache.org/apidocs/org/apache/giraph/examples/package-summary.html
  [Resolva os problemas que você eventualmente encontrar]: #tshoot
  [Git]: http://git-scm.com/
  [Maven]: http://maven.apache.org/
  [repositório do Giraph]: https://github.com/apache/giraph
  [GIRAPH-930]: https://issues.apache.org/jira/browse/GIRAPH-930
  [PowerShell do Azure]: http://azure.microsoft.com/pt-br/documentation/articles/install-configure-powershell/
  [HDInsight-Tools]: https://github.com/Blackmist/hdinsight-tools
  [Carregar dados para trabalhos do Hadoop no HDInsight]: http://azure.microsoft.com/pt-br/documentation/articles/hdinsight-upload-data/
  [Pregel]: http://people.apache.org/~edwardyoon/documents/pregel.pdf
  [ramificação versão-1.1]: https://github.com/apache/giraph/tree/release-1.1
  [tabela direcionada]: http://en.wikipedia.org/wiki/Directed_graph
  [tiny\_graph.txt Desenhado como círculos com linhas de distância variável entre]: .\media\hdinsight-giraph\giraph-graph.png
  [Desenho dos objetos como círculos com os percursos mais curtos entre]: .\media\hdinsight-giraph\giraph-graph-out.png
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com
  [Novidades em versões de cluster HDInsight]: http://azure.microsoft.com/pt-br/documentation/articles/hdinsight-component-versioning/
  [Linha de comando do Hadoop]: #cmd
  [Pig]: http://azure.microsoft.com/pt-br/documentation/articles/hdinsight-use-pig/
  [Hive]: http://azure.microsoft.com/pt-br/documentation/articles/hdinsight-use-hive/
