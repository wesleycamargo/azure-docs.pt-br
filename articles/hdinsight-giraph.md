<properties title="How to use Giraph with HDInsight" pageTitle="Como utilizar o Apache Giraph com o Azure HDInsight" description="Aprenda como utilizar o Apache Giraph para realizar processamento de gráficos com o Azure HDInsight" metaKeywords="Azure HDInsight Apache Giraph, hdinsight giraph, hdinsight graph, hadoop giraph, azure hadoop, hadoop graph" services="hdinsight" solutions="big-data" documentationCenter="" authors="larryfr" videoId="" scriptId="" manager="paulettm" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/14/2014" ms.author="larryfr" />

#Aprenda como utilizar o Apache Giraph com o HDInsight do Azure (Hadoop)

O [Apache Giraph][giraph] permite que você realize processamento de gráficos usando o Hadoop, além de poder ser usado com o Azure HDInsight. 

Relações de modelo de gráficos entre objetos, como as conexões entre roteadores em uma rede grande como a Internet, ou relações entre pessoas em redes sociais (algumas vezes mencionadas como uma tabela social). O processamento de gráficos permite que você faça a análise das relações entre objetos em uma gráfico, como:

* Identificando aliados em potencial com base em seus relacionamentos atuais

* Identificando a rota mais curta entre dois computadores em uma rede

* Calculando a classificação de páginas das páginas web

##Você aprenderá como

* [Compilar e implantar o Apache Giraph em um cluster HDInsight](#build)

* [Executar o exemplo SimpleShortestPathsComputation](#run)

	Para uma lista de outros exemplos fornecidos com o Giraph, consulte [Pacote org.apache.giraph.examples](https://giraph.apache.org/apidocs/org/apache/giraph/examples/package-summary.html).

* [Resolva os problemas que você eventualmente encontrar](#tshoot)

##Requisitos

* Um cluster do Azure HDInsight, versão 3.0 ou 3.1

* [Git](http://git-scm.com/)

* Java 1.6

* [Maven](http://maven.apache.org/) 3 ou superior

##<a id="build"></a>Compilar e implantar o Giraph

O Giraph não é fornecido como parte do cluster HDInsight, portanto, precisa ser compilado por meio da origem.  Você pode encontrar mais informações sobre a compilação do Giraph no [repositório do Giraph](https://github.com/apache/giraph).

1. Atualmente (14/07/2014), o Giraph requer um patch para funcionar com o armazenamento de arquivos WASB utilizado pelo HDInsight. O patch foi enviado ao projeto Apache Giraph, mas ainda não foi aceito. Baixe o patch da seção __attachments__ de [GIRAPH-930](https://issues.apache.org/jira/browse/GIRAPH-930) e salve-o na unidade local __giraph-930.diff__.

1. Por meio de uma linha de comando, utilize o comando Git a seguir para criar um clone do repositório do Giraph.

		git clone https://github.com/apache/giraph.git

2. Mude os diretórios no diretório __giraph__ criado pela operação de clonagem na etapa 2.

		cd giraph

3. Agregue o patch ao repositório local utilizando o comando a seguir.

		git apply giraph-930.diff

	Substitua __giraph-930.diff__ pelo caminho para o arquivo que você criou na etapa 1.

3. Compile o Giraph para a sua versão de cluster HDInsight, usando um dos comandos a seguir.

	* Para __HDInsight 3.0__ (Hadoop 2.2)

			mvn package -Phadoop_0.20.203 - DskipTests

	* Para __HDInsight 3.1__ (Hadoop 2.4)

			mvn package -Phadoop_0.23 -DskipTests

	Após a compilação estar concluída, você encontrará o arquivo JAR de exemplo em __\\giraph\\giraph-examples\\target__.

4. Carregue o arquivo JAR de exemplo no armazenamento primário de seu cluster HDInsight usando o [PowerShell do Azure][aps] e as [HDInsight-Tools][tools].

		Add-HDInsightFile giraph-examples-1.1.0-SNAPSHOT-for-hadoop-0.23.1-jar-with-dependencies.jar example/jars/giraph.jar clustername

	Substitua __giraph-examples-1.1.0-SNAPSHOT-for-hadoop-0.23.1-jar-with-dependencies.jar__ pelo caminho e o nome do arquivo JAR produzido na etapa anterior, e __clustername__ pelo nome de seu cluster HDInsight. Por exemplo, se você compilar o pacote com o parâmetro `-Phadoop_0.20.203`, o nome de arquivo do JAR incluirá __hadoop-0.20.203__.

	Assim que o comando estiver concluído, o arquivo JAR terá sido carregado para wasb:///example/jars/giraph.jar.

	> [WACOM.NOTE] Para obter uma lista de utilitários que podem ser usados para carregar arquivos no HDInsight, consulte [Carregar dados para trabalhos do Hadoop no HDInsight](http://azure.microsoft.com/pt-br/documentation/articles/hdinsight-upload-data/).

##<a id="run"></a>Execute o exemplo

O SimpleShortestPathsComputation demonstra a implementação básica do [Pregel](http://people.apache.org/~edwardyoon/documents/pregel.pdf) para encontrar o caminho mais curto entre objetos em um gráfico. Use as etapas a seguir para carregar os dados amostrais, execute um trabalho usando o exemplo SimpleShortestPathsComputation e, então, visualize os resultados.

> [WACOM.NOTE] A origem para este e outros exemplos está disponível na [ramificação da versão 1.1](https://github.com/apache/giraph/tree/release-1.1) do [repositório GitHub](https://github.com/apache/giraph).

1. Crie um novo arquivo chamado __tiny\_graph.txt__. Ele deve conter as linhas a seguir.

		[0,0,[[1,1],[3,3]]]
		[1,0,[[0,1],[2,2],[3,1]]]
		[2,0,[[1,2],[4,4]]]
		[3,0,[[0,3],[1,1],[4,4]]]
		[4,0,[[3,4],[2,4]]]

	Esses dados descrevem uma relação entre objetos em um[gráfico direcionado](http://en.wikipedia.org/wiki/Directed_graph), usando o formato `[source_id,source_value,[[dest_id], [edge_value],...]]`. Cada linha representa uma relação entre um objeto __source\_id__ e um ou mais objetos __dest\_id__. O __edge\_value__ pode ser considerado como a força ou distância da conexão entre o __source\_id__ e o __dest\_id__. 

	Desenhado e utilizando o valor (ou peso) como distância entre os objetos, os dados acima podem se parecer com os demonstrados aqui.

	![tiny_graph.txt drawn as circles with lines of varying distance between](.\media\hdinsight-giraph\giraph-graph.png)


2. Carregue o arquivo __tiny\_graph.txt__ no armazenamento primário no seu cluster HDInsight usando o [PowerShell do Azure][aps] e as [HDInsight-Tools][tools].

		Add-HDInsightFile tiny_graph.txt example/data/tiny_graph.txt clustername

	Substitua o nome do cluster pelo nome do seu cluster HDInsight.

3. Use o PowerShell a seguir para executar o exemplo __SimpleShortstPathsComputation__, usando o arquivo __tiny_graph.txt__ como entrada. Isso exige que você tenha instalado e configurado o [PowerShell do Azure][aps].

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

	No exemplo acima, substitua __clustername__ pelo nome do seu cluster HDInsight.

###Exibir os resultados

Assim que o trabalho tiver sido concluído, os resultados serão armazenados na pasta __wasb:///example/out/shotestpaths__ como arquivos __part-m-#####__. Use o [PowerShell do Azure][aps] e as [HDInsight-Tools][tools] para baixar os arquivos de saída.

	Find-HDInsightFile example/output/shortestpaths/part* clustername | foreach-object { Get-HDInsightFile $_.name .  itsfullofstorage }
	Cat example/output/shortestpaths/part*

Isso criará a estrutura de diretórios __example/output/shortestpaths__ no diretório atual e baixará os arquivos começando por __part__. O cmdlet __Cat__ exibirá os conteúdos dos arquivos, que devem ser semelhantes ao exibido a seguir.

	0	1.0
	4	5.0
	2	2.0
	1	0.0
	3	1.0

O exemplo SimpleShortestPathComputation é programado em código fixo para começar com a ID de objeto 1 e encontrar o caminho mais curto para outros objetos. Portanto, a saída deve ser lida como `destination_id distance`, em que a distância é o valor (ou peso) dos limites entre os quais se percorre entre a ID de objeto 1 e a ID de destino.

Visualizando isso, você pode verificar os resultados percorrendo os caminhos mais curtos entre a ID 1 e todos os outros objetos. Observe que o caminho mais curto entre a ID 1 e a ID 4 é 5. Essa é a distância padrão entra a <span style="color:orange">ID 1 e 3</span>e entre a <span style="color:red">ID 3 e 4</span>.

![Drawing of objects as circles with shortest paths drawn between](.\media\hdinsight-giraph\giraph-graph-out.png)

##<a id="tshoot"></a>Solução de problemas

###Diretório de saída já existe

Os trabalhos do Giraph criam o diretório de saída especificado no tempo de execução. Se o diretório já existir, ocorrerá um erro afirmando que o diretório de saída já existe.

Se você deseja executar um trabalho múltiplas vezes, é preciso remover o diretório de saída entre trabalhos ou então especificar um diretório de saída diferente para cada trabalho.

###<a id="cmd"></a>Usando a linha de comando do Hadoop

Embora este artigo demonstre como executar um trabalho Giraph via PowerShell, você também pode executar o trabalho usando a linha de comando do Hadoop.

> [WACOM.NOTE] A linha de comando do Hadoop só está disponível ao se conectar ao cluster HDInsight, usando a área de trabalho remota.
> 
> As sessões de área de trabalho remota para recursos de computador do Azure, como o cluster HDInsight, só podem funcionar por meio de clientes de área de trabalho remota baseados em Windows.

Para se conectar ao cluster HDInsight, realize as etapas a seguir:

1. Usando o [Portal de Gerenciamento do Azure](https://manage.windowsazure.com), selecione seu cluster HDInsight e selecione __Configuração__.

2. Na parte inferior da página, selecione __Habilitar Modo Remoto__ e forneça o nome de usuário, senha e data de vencimento para a conexão de área de trabalho remota.

3. Após a solicitação para habilitar a área de trabalho remota ser processada, uma nova entrada para se __Conectar__ aparecerá na parte inferior da página. Selecione essa entrada para baixar o arquivo .RDP para a sessão de área de trabalho remota.

4. O arquivo .RDP pode ser salvo ou aberto imediatamente para executar o cliente de área de trabalho remota. Durante o processo de conexão, será solicitado que você forneça o nome de usuário e a senha utilizados por você ao habilitar a conexão de área de trabalho remota.

5. Uma vez conectado, use o ícone __Linha de comando do Hadoop__ na área de trabalho para iniciar a linha de comando do Hadoop.

6. O exemplo a seguir demonstra como copiar o arquivo __giraph.jar__ para o headnode do cluster e executar o trabalho usando a linha de comando do Hadoop.

		hadoop fs -copyToLocal wasb:///example/jar/giraph.jar
		hadoop jar giraph.jar org.apache.giraph.GiraphRunner org.apache.giraph.examples.SimpleShortestPathsComputation -ca "mapred.job.tracker=headnodehost:9010" -vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat -vip wasb:///example/data/tinygraph.txt -vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat -op wasb:///example/output/shortestpaths -w 2


###Versões mais antigas do HDInsight

Se você deseja usar o Giraph com versões mais antigas do HDInsight, é preciso compilá-lo para a versão específica do Hadoop com suporte por essa versão.  Consulte [O que há de novo nas versões de cluster HDInsight](http://azure.microsoft.com/pt-br/documentation/articles/hdinsight-component-versioning/) para determinar qual versão corresponde à versão do HDInsight.

Além disso, versões anteriores do HDInsight podem exigir que você execute o trabalho do Giraph por meio da linha de comando do Hadoop. Se você recebe mensagens de erro ao executar o trabalho por meio do PowerShell, tente executar o trabalho na [Linha de comando do Hadoop](#cmd).

##Próximas etapas

Agora que você aprendeu como usar o Giraph com o HDInsight, experimente o [Pig][] e [Hive][] com o HDInsight.

[giraph]: http://giraph.apache.org
[tools]: https://github.com/Blackmist/hdinsight-tools
[aps]: http://azure.microsoft.com/pt-br/documentation/articles/install-configure-powershell/
[pig]: http://azure.microsoft.com/pt-br/documentation/articles/hdinsight-use-pig/
[hive]: http://azure.microsoft.com/pt-br/documentation/articles/hdinsight-use-hive/

<!--HONumber=35.1-->
