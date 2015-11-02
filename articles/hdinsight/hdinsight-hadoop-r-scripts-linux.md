<properties
	pageTitle="Instalar R no HDInsight baseado em Linux | Microsoft Azure"
	description="Saiba como instalar e usar R para personalizar os clusters do Hadoop baseados em Linux."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/20/2015"
	ms.author="larryfr"/>

# Instalar e usar R em clusters Hadoop do HDInsight (visualização)

Você pode instalar R em qualquer tipo de cluster no Hadoop no HDInsight usando a personalização de cluster **Ação de Script**. Isso permite que os analistas e cientistas de dados usem R para implantar a eficiente estrutura de programação MapReduce/YARN para processar grandes quantidades de dados em clusters Hadoop implantados no HDInsight.

> [AZURE.NOTE]As etapas deste documento exigem um cluster HDInsight baseado em Linux. Para obter informações sobre como usar o R com um cluster baseado no Windows, veja [Instalar e usar o R em clusters do Hadoop no HDinsight (Windows)](hdinsight-hadoop-r-scripts.md).

## O que é R?

O <a href="http://www.r-project.org/" target="_blank">Projeto R para computação de estatísticas</a> é uma linguagem de software livre e um ambiente de computação de estatísticas. O R fornece centenas de funções estatísticas de compilação e uma linguagem própria de programação que combina aspectos da programação funcional e orientada a objeto. Ele também fornece recursos abrangentes de gráficos. O R é o ambiente de programação preferencial para a maioria dos profissionais estatísticos e cientistas em uma ampla variedade de campos.

Os scripts R podem ser executados em clusters Hadoop no HDInsight que foram personalizados usando a ação de Script quando criado para instalar o ambiente de R. O R é compatível com o armazenamento de Blob do Azure (WASB) para que os dados armazenados nele possam ser processados usando R no HDInsight.

## O que o script faz

A ação de script usada para instalar o R em seu cluster HDInsight instala os seguintes pacotes Ubuntu, que fornecem uma instalação básica do R:

* [r-base](http://packages.ubuntu.com/precise/r-base): pacote R GNU base
* [r-base-dev](http://packages.ubuntu.com/precise/r-base-dev): pacotes R GNU auxiliares

Os seguintes pacotes RHadoop também são instalados, os quais fornecem integração com o MapReduce e o HDFS:

* [rmr2](https://github.com/RevolutionAnalytics/rmr2): permite que os desenvolvedores do R usem o MapReduce do Hadoop
* [rhdfs](https://github.com/RevolutionAnalytics/rhdfs): permite que desenvolvedores do R usem HDFS do Hadoop (WASB para HDInsight)

Além disso, os seguintes pacotes R são instalados:

| Pacote R | O que ele oferece |
| --------- | ---------------- |
| [rJava](https://cran.r-project.org/web/packages/rJava/index.html) | R de nível baixo para interface Java. |
| [Rcpp](https://cran.r-project.org/web/packages/Rcpp/index.html) | Integração entre R e C++. |
| [RJSONIO](https://cran.r-project.org/web/packages/RJSONIO/index.html) | Serializar/desserializar objetos do R para JSON |
| [bitops](https://cran.r-project.org/web/packages/bitops/index.html) | Funções para operações bit a bit em vetores de número inteiro. |
| [digest](Crie resumos de hash criptográfico de objetos em R) | Crie resumos de hash criptográfico de objetos em R. |
| [funcional](https://cran.r-project.org/web/packages/functional/index.html) | Curry, Compose e outras funções de ordem superior |
| [reshape2](https://cran.r-project.org/web/packages/reshape2/index.html) | Reestrutura de flexibilidade e dados agregados. |
| [stringr](https://cran.r-project.org/web/packages/stringr/index.html) | Invólucros simples e consistentes para operações comuns de cadeia de caracteres. |
| [plyr](https://cran.r-project.org/web/packages/plyr/index.html) | Ferramentas de divisão, aplicação e combinação de dados. |
| [caTools](https://cran.r-project.org/web/packages/caTools/index.html) | Ferramentas de estatísticas de janela em movimento, GIF, Base64, ROC AUC etc. |
| [stringdist](https://cran.r-project.org/web/packages/stringdist/index.html) | Aproximar a correspondência de cadeia de caracteres e funções de distância de cadeia de caracteres. |

## Instalar o R usando ações de script

O script [https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh) é usado para instalar o R em um cluster HDInsight. Esta seção fornece instruções sobre como usar o script ao provisionar o cluster usando o portal do Azure.

> [AZURE.NOTE]Você também pode usar o Azure PowerShell ou o SDK do .NET do HDInsight para criar um cluster usando esse script. Para saber mais sobre como usar esses métodos, consulte [Personalizar clusters HDInsight com ações de script](hdinsight-hadoop-customize-cluster-linux.md).

1. Inicie o provisionamento de um cluster usando as etapas em [Provisionar clusters HDInsight baseados em Linux](hdinsight-hadoop-provision-linux-clusters.md#portal), mas não conclua o provisionamento.

2. Na folha **Configuração opcional**, selecione **Ações de script** e forneça as informações a seguir:

	* __NOME__: insira um nome fácil para a ação de script.
	* __URI do SCRIPT__: https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh
	* __CABEÇALHO__: marque essa opção
	* __TRABALHO__: marque essa opção
	* __ZOOKEEPER__: marque essa opção para instalar no nó Zookeeper.
	* __PARÂMETROS__: deixe esse campo em branco

3. Na parte inferior das **Ações de Script**, use o botão **Selecionar** para salvar a configuração. Por fim, use o botão **Selecionar** na parte inferior da folha **Configuração Opcional** para salvar as informações da configuração opcional.

4. Continue o provisionamento do cluster conforme descrito em [Provisionar clusters HDInsight baseados em Linux](hdinsight-hadoop-provision-linux-clusters.md#portal).

## Executar scripts de R

Após a conclusão do provisionamento do cluster, use as seguintes etapas para usar o R para executar uma operação de MapReduce no cluster.

1. Conecte-se ao cluster HDInsight usando SSH:

		ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

	Para obter mais informações sobre como usar SSH com o HDInsight, consulte o seguinte:

	* [Usar SSH com Hadoop baseado em Linux no HDInsight no Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [Usar SSH com Hadoop baseado em Linux no HDInsight no Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. Do prompt `username@headnode1:~$`, digite o seguinte comando para iniciar uma sessão interativa de R:

		R

3. Digite o seguinte programa R. Isso gera os números de 1 a 100 e multiplica-os por 2.

		library(rmr2)
		ints = to.dfs(1:100)
		calc = mapreduce(input = ints, map = function(k, v) cbind(v, 2*v))

	A primeira linha chama o rmr2 da biblioteca do RHadoop, que é usado para operações de MapReduce.

	A segunda linha gera valores de 1 a 100 e armazena-os no sistema de arquivos do Hadoop usando o `to.dfs`.

	A terceira linha cria um processo MapReduce usando a funcionalidade fornecida pelo rmr2 e começa o processamento. Você verá várias linhas rolarem à medida que o processamento começar.

4. Em seguida, use o seguinte para ver o caminho temporário no qual a saída do MapReduce foi adicionada:

		print(calc())

	Isso deve ser algo semelhante a `/tmp/file5f615d870ad2`. Para exibir a saída real, use o seguinte:

		print(from.dfs(calc))

	O resultado deve ser assim:

		[1,]  1 2
		[2,]  2 4
		.
		.
		.
		[98,]  98 196
		[99,]  99 198
		[100,] 100 200

5. Para sair do R, digite o seguinte:

		q()


## Próximas etapas

- [Instalar e usar a Matiz em clusters do HDInsight](hdinsight-hadoop-hue-linux.md). A Matiz é uma interface da Web que torna mais fácil criar, executar e salvar trabalhos Pig e Hive, bem como procurar o armazenamento padrão do cluster do HDInsight.

- [Instalar e usar Spark em clusters HDInsight][hdinsight-install-spark] para obter instruções sobre como usar o cluster de personalização para instalar e usar o Spark em clusters Hadoop do HDInsight. O Spark é uma estrutura de processamento paralelo de código-fonte aberto que oferece suporte ao processamento de memória para melhorar o desempenho dos aplicativos analíticos de big data.

- [Instalar o Giraph em clusters HDInsight](../hdinsight-hadoop-giraph-install). Use a personalização do cluster para instalar o Giraph em clusters de Hadoop do HDInsight. O Giraph permite que você realize processamento de tabelas usando o Hadoop, além de poder ser utilizado com o HDInsight do Azure.

- [Instalar o Solr em clusters HDInsight](../hdinsight-hadoop-solr-install). Use a personalização do cluster para instalar o Solr em clusters de Hadoop do HDInsight. O Solr permite que você execute operações de pesquisa poderosas nos dados armazenados.

- [Instalar a Matiz em clusters HDInsight](hdinsight-hadoop-hue-linux.md). Use a personalização do cluster para instalar o Hue em clusters de Hadoop do HDInsight. A Matiz é um conjunto de aplicativos da Web usado para interagir com um cluster Hadoop.

[powershell-install-configure]: install-configure-powershell-linux.md
[hdinsight-provision]: hdinsight-provision-clusters-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install-linux.md

<!---HONumber=Oct15_HO4-->