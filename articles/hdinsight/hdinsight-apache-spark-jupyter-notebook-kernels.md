<properties 
	pageTitle="Kernels disponíveis com notebooks Jupyter em clusters HDInsight Spark no Linux | Microsoft Azure" 
	description="Saiba mais sobre os kernels do notebook do Jupyter disponíveis com o cluster Spark no HDInsight Linux." 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"
	tags="azure-portal"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/14/2016" 
	ms.author="nitinme"/>


# Kernels disponíveis para notebooks Jupyter com clusters do HDInsight Spark Linux no HDInsight (Preview)

O cluster do Apache Spark no HDInsight (Linux) inclui blocos de anotações do Jupyter que você pode usar para testar seus aplicativos. Um kernel é um programa que é executado e que interpreta seu código. Os clusters HDInsight Spark fornecem dois kernels que você pode usar com o bloco de anotações do Jupyter. Estes são:

1. **PySpark** (para aplicativos escritos em Python)
2. **Spark** (para aplicativos escritos em Scala)

Neste artigo, você aprenderá como usar esses kernels e quais são os benefícios de usá-los.

**Pré-requisitos:**

Você deve ter o seguinte:

- Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Um cluster do Apache Spark no HDInsight no Linux. Para obter instruções, consulte o artigo sobre como [Criar clusters do Apache Spark no Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## Como usar os kernels? 

1. No [Portal do Azure](https://portal.azure.com/), no quadro inicial, clique no bloco do cluster Spark (se você o tiver fixado no quadro inicial). Você também pode navegar até o cluster em **Procurar Tudo** > **Clusters HDInsight**.   

2. Na folha do cluster Spark, clique em **Links Rápidos** e, na folha **Painel do Cluster**, clique em **Notebook do Jupyter**. Se você receber uma solicitação, insira as credenciais de administrador para o cluster.

	> [AZURE.NOTE] Você também pode acessar o Bloco de Notas Jupyter de seu cluster abrindo a seguinte URL no navegador. Substitua __CLUSTERNAME__ pelo nome do cluster:
	>
	> `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Crie um novo notebook com os novos kernels. Clique em **Novo** e, em seguida, clique em **Pyspark** ou **Spark**. Você deve usar o kernel Spark para aplicativos Scala e o kernel PySpark para aplicativos Python.

	![Criar um novo bloco de anotações do Jupyter](./media/hdinsight-apache-spark-jupyter-notebook-kernels/jupyter-kernels.png "Criar um novo bloco de anotações do Jupyter")

3. Isso deve abrir um novo notebook com o kernel selecionado.

## Por que devo usar kernels PySpark ou Spark?

Há alguns benefícios em usar os novos kernels.

1. **Contextos de predefinição**. Com os kernels padrão **PySpark** ou **Spark** que são fornecidos com notebooks Jupyter, você não precisa definir explicitamente contextos do Spark ou do Hive antes de começar a trabalhar com o aplicativo que está em desenvolvimento; eles estão disponíveis por padrão para você. Esses contextos são:

	* **sc** - para o contexto do Spark
	* **sqlContext**: para o contexto Hive


	Portanto, você não precisa executar instruções como as seguintes para definir os contextos:

		###################################################
		# YOU DO NOT NEED TO RUN THIS WITH THE NEW KERNELS
		###################################################
		sc = SparkContext('yarn-client')
		sqlContext = HiveContext(sc)

	Em vez disso, pode usar os contextos predefinidos diretamente em seu aplicativo.
	
2. **A mágica da célula**. O kernel PySpark fornece algumas "palavras mágicas" predefinidas, que são comandos especiais que podem ser chamados com o `%%` (por exemplo, `%%MAGIC` <args>). O comando mágico deve ser a primeira palavra em uma célula do código e de permitir várias linhas de conteúdo. A palavra mágica deve ser a primeira palavra na célula. Adicionar algo antes da palavra mágica, até mesmo comentários, causará um erro. Para saber mais sobre palavras mágicas, consulte [aqui](http://ipython.readthedocs.org/en/stable/interactive/magics.html).

	A tabela a seguir lista os diferentes comandos mágicos disponíveis por meio dos kernels.

	| Mágica | Exemplo | Descrição |
	|-----------|---------------------------------|--------------|
	| ajuda | `%%help` | Gera uma tabela de todos os comandos mágicos disponíveis com exemplo e descrição |
	| informações | `%%info` | Envia informações de sessão para o ponto de extremidade Livy atual |
	| configurar | `%%configure -f`<br>`{"executorMemory": "1000M"`,<br>`"executorCores": 4`} | Configura os parâmetros para a criação de uma sessão. O sinalizador force (-f) será obrigatório se uma sessão já tiver sido criada, e a sessão será descartada e recriada. Veja o [Corpo da Solicitação de sessões/POST do Livy](https://github.com/cloudera/livy#request-body) para obter uma lista de parâmetros válidos. Os parâmetros devem ser passados como uma cadeia de caracteres JSON e devem estar na linha seguinte, logo após a mágica, conforme mostrado na coluna de exemplo. |
	| sql | `%%sql -o <variable name>`<br> `SHOW TABLES` | Executa uma consulta do Hive no dqlContext. Se o parâmetro `-o` for passado, o resultado da consulta será persistido no contexto %%local do Python como um dataframe do [Pandas](http://pandas.pydata.org/). |
	| local | `%%local`<br>`a=1` | Todo o código nas linhas subsequentes será executado localmente. O código deve ser um código Python válido. |
	| logs | `%%logs` | Gera os logs da sessão atual do Livy. |
	| excluir | `%%delete -f -s <session number>` | Exclui uma sessão específica do ponto de extremidade atual do Livy. Observe que você não pode excluir a sessão iniciada para o próprio kernel. |
	| limpeza | `%%cleanup -f` | Exclui todas as sessões do ponto de extremidade atual do Livy, incluindo a sessão deste notebook. O sinalizador de força -f é obrigatório. |

3. **Visualização automática**. O kernel **Pyspark** visualiza automaticamente a saída das consultas do Hive e do SQL. Você tem a opção de escolher entre vários tipos diferentes de visualização, incluindo Tabela, Pizza, Linha, Área, Barra.

## Parâmetros compatíveis com a mágica de %%sql

A mágica %%sql é compatível com diversos parâmetros que podem ser usados para controlar o tipo de saída que você recebe ao executar consultas. A tabela a seguir lista as saídas.

| Parâmetro | Exemplo | Descrição |
|-----------|---------------------------------|--------------|
| -o | `-o <VARIABLE NAME>` | Use esse parâmetro para manter o resultado da consulta, no contexto Python %%local, como um dataframe [Pandas](http://pandas.pydata.org/). O nome da variável dataframe é o nome da variável que você especificar. |
| -q | `-q` | Use esta opção para desativar visualizações da célula. Se não desejar visualizar o conteúdo de uma célula automaticamente, mas apenas capturá-la como um dataframe, use `-q -o <VARIABLE>`. Se desejar desativar as visualizações sem capturar os resultados (por exemplo, para executar uma consulta SQL com efeitos colaterais, como uma instrução `CREATE TABLE`), basta usar `-q` sem especificar um argumento `-o`. |
| -m | `-m <METHOD>` | Em que **METHOD** seja **take** ou **sample** (o padrão é **take**). Se o método for **take**, o kernel selecionará elementos da parte superior do conjunto de dados de resultados especificado por MAXROWS (descrito posteriormente nesta tabela). Se o método for **sample**, o kernel selecionará aleatoriamente elementos de exemplo do conjunto de dados de acordo com o parâmetro `-r`, descrito a seguir nesta tabela. |
| -r | `-r <FRACTION>` | Aqui **FRACTION** é um número de ponto flutuante entre 0,0 e 1,0. Se o método de exemplo para a consulta SQL for `sample`, o kernel recolherá exemplos aleatórios da fração especificada dos elementos do conjunto de resultados para você. Por exemplo, se você executar uma consulta SQL com os argumentos `-m sample -r 0.01`, 1% das linhas de resultado será amostrado aleatoriamente. |
| -n | `-n <MAXROWS>` | **MAXROWS** é um valor inteiro. O kernel limitará o número de linhas de saída para **MAXROWS**. Se **MAXROWS** for um número negativo como **-1**, o número de linhas no conjunto de resultados não será limitado. |

**Exemplo:**

	%%sql -q -m sample -r 0.1 -n 500 -o query2 
	SELECT * FROM hivesampletable

A instrução acima faz o seguinte:

* Seleciona todos os registros de **hivesampletable**.
* Como usamos - q, ele desativa a visualização automática.
* Como usamos `-m sample -r 0.1 -n 500`, ele recolhe um exemplo de 10% das linhas na hivesampletable aleatoriamente e limita o tamanho do conjunto de resultados a 500 linhas.
* Por fim, como usamos `-o query2`, ele também salva a saída em um dataframe chamado **query2**.
	

## Considerações ao usar os novos kernels

Seja qual for o kernel que você use (PySpark ou Spark), deixar os notebooks em execução consumirá os recursos de cluster. Com esses kernels, como os contextos são predefinidos, simplesmente sair dos notebooks não elimina o contexto e, portanto, os recursos de cluster continuarão em uso. Uma boa prática com kernels PySpark e Spark seria usar a opção **Fechar e Interromper** opção do menu **Arquivo** do notebook. Isso elimina o contexto e encerra o notebook.


## Mostre-me alguns exemplos

Quando você abrir um notebook Jupyter, verá duas pastas disponíveis no nível raiz.

* A pasta **PySpark** tem notebooks de exemplo que usam o novo kernel **Python**.
* A pasta **Scala** tem notebooks de amostra que usam o novo kernel **Spark**.

Você pode abrir o notebook **00 - [LEIA-ME PRIMEIRO] Recursos do kernel mágico do Spark** da pasta **PySpark** ou **Spark** para saber mais sobre os diferentes comandos mágicos disponíveis. Você também pode usar outros notebooks de exemplo disponíveis nas duas pastas para saber como obter diferentes cenários usando notebooks Jupyter com clusters Spark HDInsight.

## Onde os blocos de anotações são armazenados?

Os notebooks Jupyter são salvos na conta de armazenamento associada ao cluster na pasta **/HdiNotebooks**. Os blocos de anotações, arquivos de texto e pastas que você criar no Jupyter poderão ser acessados no WASB. Por exemplo, se você usar o Jupyter para criar uma pasta **myfolder** e um notebook **myfolder/mynotebook.ipynb**, poderá acessar o notebook em `wasb:///HdiNotebooks/myfolder/mynotebook.ipynb`. O inverso também é verdadeiro, ou seja, se você carregar um notebook anotações diretamente para sua conta de armazenamento em `/HdiNotebooks/mynotebook1.ipynb`, o notebook também ficará visível no Jupyter. Os logs são mantidos na conta de armazenamento mesmo após a exclusão do cluster.

A forma como os blocos de anotações são salvos na conta de armazenamento é compatível com HDFS. Portanto, se você se conectar por SSH ao cluster, poderá usar comandos de gerenciamento de arquivos como o seguinte:

	hdfs dfs -ls /HdiNotebooks             				  # List everything at the root directory – everything in this directory is visible to Jupyter from the home page
	hdfs dfs –copyToLocal /HdiNotebooks    				# Download the contents of the HdiNotebooks folder
	hdfs dfs –copyFromLocal example.ipynb /HdiNotebooks   # Upload a notebook example.ipynb to the root folder so it’s visible from Jupyter


Caso haja problemas para acessar a conta de armazenamento do cluster, os notebooks também são salvos no `/var/lib/jupyter` do nó principal.

## Navegador com suporte
Os blocos de anotações do Jupyter em execução em clusters HDInsight Spark só têm suporte no Google Chrome.

## Comentários

Os kernels novos estão evoluindo e amadurecerão com o tempo. Isso também pode significar que as APIs podem mudar à medida que esses kernels amadurecem. Agradecemos o envio quaisquer comentários que você tenha ao usar esses novos kernels. Isso será muito útil na formação da versão final desses kernels. Você pode deixar seus comentários/feedback na seção **Comentários** no final deste artigo.


## <a name="seealso"></a>Consulte também


* [Visão geral: Apache Spark no Azure HDInsight](hdinsight-apache-spark-overview.md)

### Cenários

* [Spark com BI: executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](hdinsight-apache-spark-use-bi-tools.md)

* [Spark com Aprendizado de Máquina: usar o Spark no HDInsight para analisar a temperatura de prédios usando dados do sistema HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Spark com Aprendizado de Máquina: usar o Spark no HDInsight para prever resultados da inspeção de alimentos](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Streaming Spark: usar o Spark no HDInsight para a criação de aplicativos de streaming em tempo real](hdinsight-apache-spark-eventhub-streaming.md)

* [Análise de log do site usando o Spark no HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### Criar e executar aplicativos

* [Criar um aplicativo autônomo usando Scala](hdinsight-apache-spark-create-standalone-application.md)

* [Executar trabalhos remotamente em um cluster do Spark usando Livy](hdinsight-apache-spark-livy-rest-interface.md)

### Ferramentas e extensões

* [Usar o plug-in de Ferramentas do HDInsight para IntelliJ IDEA para criar e enviar aplicativos Spark Scala](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Usar blocos de anotações do Zeppelin com um cluster Spark no HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

### Gerenciar recursos

* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

<!---HONumber=AcomDC_0420_2016-->