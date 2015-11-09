<properties
	pageTitle="Instalar e usar o Giraph em clusters HDInsight (Hadoop) baseados em Linux | Microsoft Azure"
	description="Saiba como instalar o Giraph em clusters HDInsight baseados em Linux usando as Ações de Script. Ações de script permitem que você personalize o cluster durante a criação, alterando a configuração do cluster ou instalando serviços e utilitários."
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
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/26/2015"
	ms.author="larryfr"/>

# Instalar o Giraph nos clusters Hadoop do HDInsight e usar o Giraph para processar gráficos em grande escala

Você pode instalar o Giraph em qualquer tipo de cluster em Hadoop no Azure HDInsight usando a **Ação de Script** para personalizar clusters. A Ação de Script permite executar scripts para personalizar um cluster somente quando o cluster está sendo criado. Para obter mais informações, consulte [Personalizar clusters HDInsight usando a Ação de Script](hdinsight-hadoop-customize-cluster-linux.md).

Neste tópico, você aprenderá a instalar o Giraph usando a Ação de Script. Depois de instalar o Giraph, você também aprenderá a usar o Giraph em suas aplicações mais comuns: processar gráficos em grande escala.

> [AZURE.NOTE]As informações neste artigo são específicas de clusters HDInsight baseados em Linux. Para obter informações sobre como trabalhar com clusters baseado no Windows, consulte [Instalar o Giraph em clusters de Hadoop do HDInsight (Windows)](hdinsight-hadoop-giraph-install.md)

## <a name="whatis"></a>O que é o Giraph?

O [Apache Giraph](http://giraph.apache.org/) permite executar processamento de gráficos usando o Hadoop e pode ser usado com o Azure HDInsight. Os gráficos modelam as relações entre objetos, como as conexões entre roteadores em uma rede grande, como a Internet, ou as relações entre as pessoas nas redes sociais (às vezes chamados de gráficos sociais). O processamento de tabelas permite que você faça a análise das relações entre objetos em uma tabela, como:

- Identificar possíveis amigos com base em suas relações atuais.
- Identificar a menor rota entre dois computadores em uma rede.
- Calcular a ordem de classificação de página da Web.

##O que o script faz

O script executa as ações a seguir:

* Instala o Giraph no `/usr/hdp/current/giraph`
* Copia o arquivo `giraph-examples.jar` para o armazenamento padrão (WASB) para o seu cluster: `/example/jars/giraph-examples.jar`

## <a name="install"></a>Instalar o Giraph usando Ações de Script

Um script de exemplo para instalar o Giraph em um cluster HDInsight está disponível em um blob de armazenamento do Azure somente leitura em [https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh). Esta seção fornece instruções sobre como usar o exemplo de script durante a criação do cluster usando o Portal do Azure.

> [AZURE.NOTE]Você também pode usar o Azure PowerShell ou o SDK do .NET do HDInsight para criar um cluster usando esse script. Para obter mais informações sobre como usar esses métodos, consulte [Personalizar clusters HDInsight com Ações de Script](hdinsight-hadoop-customize-cluster-linux.md).

1. Inicie a criação de um cluster usando as etapas em [Criar clusters de HDInsight baseados em Linux](hdinsight-provision-linux-clusters.md#portal), mas não conclua a criação.

2. Na folha **Configuração Opcional**, selecione **Ações de Script** e forneça as informações a seguir:

	* __NOME__: insira um nome amigável para a ação de script.
	* __URI DO SCRIPT__: https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
	* __CABEÇALHO__: marque esta opção
	* __TRABALHO__: marque esta opção
	* __ZOOKEEPER__: marque esta opção para instalar no nó Zookeeper.
	* __PARÂMETROS__: deixe este campo em branco

3. Na parte inferior das **Ações de Script**, use o botão **Selecionar** para salvar a configuração. Por fim, use o botão **Selecionar** na parte inferior da folha **Configuração Opcional** para salvar as informações de configuração opcional.

4. Continue a criação do cluster conforme descrito em [Criar clusters HDInsight baseados em Linux](hdinsight-provision-linux-clusters.md#portal).

## <a name="usegiraph"></a>Como usar o Giraph no HDInsight?

Quando a criação do cluster tiver terminado, use as etapas a seguir para executar o exemplo SimpleShortestPathsComputation incluído com o Giraph. Isso realiza a implementação do <a href = "http://people.apache.org/~edwardyoon/documents/pregel.pdf">Pregel</a> básico para encontrar o caminho mais curto entre objetos em um gráfico.

1. Conecte-se ao cluster HDInsight usando SSH:

		ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

	Para obter mais informações sobre como usar SSH com o HDInsight, consulte o seguinte:

	* [Usar SSH com Hadoop baseado em Linux no HDInsight no Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [Usar SSH com Hadoop baseado em Linux no HDInsight no Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

1. Use o seguinte para criar um novo arquivo chamado __tiny\_graph.txt__:

		nano tiny_graph.txt

	Use o seguinte como conteúdo deste arquivo:

		[0,0,[[1,1],[3,3]]]
		[1,0,[[0,1],[2,2],[3,1]]]
		[2,0,[[1,2],[4,4]]]
		[3,0,[[0,3],[1,1],[4,4]]]
		[4,0,[[3,4],[2,4]]]

	Esses dados descrevem uma relação entre objetos em um gráfico direcionado, usando o formato [source\_id, source\_value,[[dest\_id], [edge\_value],...]]. Cada linha representa uma relação entre um objeto **source\_id** e um ou mais objetos **dest\_id**. O **edge\_value** (ou peso) pode ser pensado como a força ou a distância da conexão entre **source\_id** e **dest\_id**.

	Desenhado e utilizando o valor (ou peso) como distância entre os objetos, os dados acima podem se parecer com os demonstrados aqui:

	![tiny\_graph.txt Desenhado como círculos com linhas de distância variável entre](./media/hdinsight-hadoop-giraph-install-linux/giraph-graph.png)

2. Para salvar o arquivo, use __Ctrl + X__, em seguida, __Y__ e, finalmente, __Enter__ para aceitar o nome de arquivo.

3. Use o seguinte para armazenar os dados no armazenamento primário para o cluster HDInsight:

		hadoop fs -copyFromLocal tiny_graph.txt /example/data/tiny_graph.txt

3. Use o seguinte para obter o FQDN (Nome de Domínio Totalmente Qualificado) do nó de cabeçalho do cluster:

        hostname -f
        
4. Execute o exemplo SimpleShortestPathsComputation usando o comando a seguir. Substitua __HEADNODE__ pelo FQDN retornado da etapa anterior:

		 hadoop jar /usr/hdp/current/giraph/giraph-examples.jar org.apache.giraph.GiraphRunner org.apache.giraph.examples.SimpleShortestPathsComputation -ca mapred.job.tracker=HEADNODE:9010 -vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat -vip /example/data/tiny_graph.txt -vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat -op /example/output/shortestpaths -w 2

	Os parâmetros usados com este comando são descritos na tabela a seguir.

	| Parâmetro | O que faz |
	| --------- | ------------ |
	| `jar /usr/hdp/current/giraph/giraph-examples.jar` | O arquivo jar que contém os exemplos. |
	| `org.apache.giraph.GiraphRunner` | A classe usada para iniciar os exemplos. |
	| `org.apache.giraph.examples.SimpleShortestPathsCoputation` | O exemplo que será executado. Nesse caso, ele calculará o caminho mais curto entre a ID 1 e todas as outras IDs no gráfico. |
	| `-ca mapred.job.tracker=HEADNODE:9010` | O nó de cabeçalho do cluster. |
	| `-vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFromat` | O formato de entrada a ser usado para os dados de entrada. |
	| `-vip /example/data/tiny_graph.txt` | O arquivo de dados de entrada. |
	| `-vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat` | O formato de saída. Nesse caso, a ID e o valor como texto sem formatação. |
	| `-op /example/output/shortestpaths` | O local de saída. |
	| `-w 2` | O número de trabalhos a usar. Nesse caso, 2. |

	Para obter mais informações sobre esses e outros parâmetros usados com exemplos do Giraph, consulte o [Guia de início rápido do Giraph](http://giraph.apache.org/quick_start.html).

5. Concluído o trabalho, os resultados serão armazenados em dois arquivos de saída no diretório \_\___wasb:///example/out/shotestpaths__. Os arquivos criados começarão com __part-m-__ e terminarão com um número indicando a classificação ordinal do arquivo – primeiro, segundo, etc. Para exibir a saída, use o que é mostrado a seguir:

		hadoop fs -text /example/output/shortestpaths/*

	A saída deve ter aparência similar à exibida a seguir:

		0	1.0
		4	5.0
		2	2.0
		1	0.0
		3	1.0

	O exemplo SimpleShortestPathComputation está codificado para começar com a ID do objeto 1 e localizar o caminho mais curto para os outros objetos. Logo, a saída deve ser lida como `destination_id distance`, em que a distância é o valor (ou peso) dos perímetros percorridos entre a ID do objeto 1 e a ID de destino.

	Visualizando isso, você pode verificar os resultados percorrendo os caminhos mais curtos entre a ID 1 e todos os outros objetos. Observe que o caminho mais curto entre a ID 1 e a ID 4 é 5. Essa é a distância total entre <span style="color:orange">ID 1 e 3</span> e, em seguida, entre <span style="color:red">ID 3 e 4</span>.

	![Desenho dos objetos como círculos com os percursos mais curtos entre](./media/hdinsight-hadoop-giraph-install-linux/giraph-graph-out.png)


## Próximas etapas

- [Instalar e usar matiz em clusters HDInsight](hdinsight-hadoop-hue-linux.md). O Hue é uma interface do usuário da Web que torna mais fácil criar, executar e salvar trabalhos Pig e Hive, bem como procurar o armazenamento padrão do cluster HDInsight.

- [Instalar e usar o Spark em clusters HDInsight](hdinsight-hadoop-spark-install-linux.md): instruções de como usar personalização de cluster para instalar e usar o Spark em clusters Hadoop HDInsight. O Spark é uma estrutura de processamento paralelo de software livre que dá suporte a processamento na memória para melhorar o desempenho de aplicativos analíticos de Big Data.

- [Instalar o R em clusters HDInsight](hdinsight-hadoop-r-scripts-linux.md): instruções sobre como usar personalização do cluster para instalar e usar o R em clusters Hadoop HDInsight. R é uma linguagem e ambiente de software livre para computação estatística. Ele fornece centenas de funções estatísticas internas e sua própria linguagem de programação, que combina aspectos de programação funcional e de programação orientada a objetos. Ele também fornece recursos abrangentes de gráficos.

- [Instalar o Solr em clusters HDInsight](hdinsight-hadoop-solr-install-linux.md). Use a personalização do cluster para instalar o Solr em clusters de Hadoop do HDInsight. O Solr permite que você execute operações poderosas nos dados armazenados.

<!---HONumber=Nov15_HO1-->