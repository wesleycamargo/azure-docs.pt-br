<properties
	pageTitle="Gerar recomendações usando o Mahout e o Hadoop | Microsoft Azure"
	description="Saiba como usar a biblioteca de aprendizado de máquina do Apache Mahout para gerar recomendações de filmes com o HDInsight para Linux (Hadoop)."
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
	ms.date="09/02/2015"
	ms.author="larryfr"/>

#Gerar recomendações de filmes usando o Apache Mahout com o Hadoop para Linux no HDInsight (visualização)

[AZURE.INCLUDE [mahout-selector](../../includes/hdinsight-selector-mahout.md)]

Saiba como usar a biblioteca de aprendizado de máquina do [Apache Mahout](http://mahout.apache.org) com o Azure HDInsight para gerar recomendações de vídeos.

O Mahout é uma biblioteca de [aprendizado de máquina][ml] para o Apache Hadoop. O Mahout contém algoritmos para processamento de dados, como filtragem, classificação e clustering. Neste artigo, você utilizará um mecanismo de recomendação para gerar recomendações de filmes baseadas nos vídeos que seus amigos assistiram.

> [AZURE.NOTE]As etapas deste documento exigem o Hadoop para Linux no cluster do HDInsight (visualização). Para obter informações sobre como usar o Mahout com um cluster do Windows, consulte [Gerar recomendações de filmes usando o Apache Mahout com o Hadoop para Windows no HDInsight](hdinsight-mahout.md)

##Pré-requisitos

* Um Hadoop baseado em Linux no cluster HDInsight. Para obter informações sobre como criar um, consulte [Introdução ao uso do Hadoop para Linux HDInsight][getstarted].

##<a name="recommendations"></a>Entendendo as recomendações

Uma das funções oferecidas pelo Mahout é um mecanismo de recomendação. Ele aceita dados no formato de `userID`, `itemId` e `prefValue` (os usuários escolhem o item de sua preferência). O Mahout, então, pode realizar análises de coocorrência, para determinar que _usuários que têm preferência por um item também têm preferência por esses outros itens_. O Mahout determinará, então, usuários com preferências de item similares, que podem ser utilizadas para fazer recomendações.

A seguir está um exemplo extremamente simples usando filmes:

* __Coocorrência__: Joe, Alice e Bob, todos gostaram de _Guerra nas Estrelas_, _O Império Contra-ataca_ e _O Retorno de Jedi_. O Mahout determina que usuários que gostam de qualquer um desses filmes também gostam dos outros dois.

* __Coocorrência__: Bob e Alice também gostaram de _A Ameaça Fantasma_, _A Guerra dos Clones_ e _A Vingança dos Sith_. O Mahout determina que usuários que gostam de qualquer um dos três filmes citados anteriormente também gostam destes últimos três.

* __Recomendação por similaridade__: já que Joe gostou dos primeiros três, o Mahout pesquisará os filmes que outros com preferências similares gostaram, mas que Joe não assistiu (curtiu/classificou). Nesse caso, o Mahout recomendaria _A Ameaça Fantasma_, _A Guerra dos Clones_ e _A Vingança dos Sith_.

##Carregar os dados

Convenientemente, a [GroupLens Research][movielens] oferece dados de classificação para filmes em um formato compatível com o Mahout. Use as seguintes etapas para baixar os dados e carregá-los no armazenamento padrão do cluster:

1. Utilize SSH para se conectar ao cluster do HDInsight para Linux. O endereço a ser usado ao conectar-se é `CLUSTERNAME-ssh.azurehdinsight.net` e a porta é `22`.

	Para obter mais informações sobre como usar o SSH para se conectar ao HDInsight, consulte os seguintes documentos:

    * **Clientes Linux, Unix ou OS X**: consulte [Conectar-se a um cluster HDInsight com base no Linux do Linux, OS X ou Unix](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster)

    * **Clientes Windows**: consulte [Conectar-se a um cluster do HDInsight para Linux no Windows](hdinsight-hadoop-linux-use-ssh-windows.md#connect-to-a-linux-based-hdinsight-cluster)

2. Baixe o arquivo MovieLens 100k, que contém 100.000 classificações de 1.700 filmes feitas por 1.000 usuários.

        curl -O http://files.grouplens.org/datasets/movielens/ml-100k.zip

3. Extraia o arquivo usando o seguinte comando:

        unzip ml-100k.zip

    Isso extrairá o conteúdo para uma nova pasta chamada **ml-100k**.

4. Use os seguintes comandos para copiar os dados para o armazenamento do HDInsight:

        cd ml-100k
        hadoop fs -copyFromLocal ml-100k/u.data /example/data/u.data

    Os dados contidos nesse arquivo tem uma estrutura de `userID`, `movieID`, `userRating` e `timestamp`, que informa a classificação que cada usuário deu a um filme. Aqui está um exemplo dos dados:


		196	242	3	881250949
		186	302	3	891717742
		22	377	1	878887116
		244	51	2	880606923
		166	346	1	886397596

##Executar o trabalho

Use o seguinte comando para executar o trabalho de recomendação:

	mahout recommenditembased -s SIMILARITY_COOCCURRENCE --input /example/data/u.data --output /example/data/mahoutout  --tempDir /temp/mahouttemp

> [AZURE.NOTE]O trabalho pode levar vários minutos para ser concluído e pode executar vários trabalhos do MapReduce.

##Exibir a saída

1. Quando o trabalho for concluído, use o seguinte comando para exibir a saída gerada:

		hadoop fs -text /example/data/mahoutout/part-r-00000

	A saída terá a seguinte aparência:

		1	[234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
		2	[282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
		3	[284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
		4	[690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

	A primeira coluna é a `userID`. Os valores contidos em “[” e “]” são `movieId`:`recommendationScore`.

2. Alguns dos outros dados contidos no diretório **ml-100k** podem ser usados para tornar os dados mais simples para o usuário. Primeiro, baixe os dados usando o seguinte comando:

		hadoop fs -copyToLocal /example/data/mahoutout/part-r-00000 recommendations.txt

	Isso copiará os dados de saída em um arquivo chamado **recommendations.txt** no diretório atual.

3. Use o comando a seguir para criar um novo script Python que pesquisará nomes de filmes para os dados na saída de recomendações:

		nano show_recommendations.py

	Quando o editor for aberto, use o seguinte como conteúdo do arquivo:

		#!/usr/bin/env python

		import sys

		if len(sys.argv) != 5:
		        print "Arguments: userId userDataFilename movieFilename recommendationFilename"
		        sys.exit(1)

		userId, userDataFilename, movieFilename, recommendationFilename = sys.argv[1:]

		print "Reading Movies Descriptions"
		movieFile = open(movieFilename)
		movieById = {}
		for line in movieFile:
		        tokens = line.split("|")
		        movieById[tokens[0]] = tokens[1:]
		movieFile.close()

		print "Reading Rated Movies"
		userDataFile = open(userDataFilename)
		ratedMovieIds = []
		for line in userDataFile:
		        tokens = line.split("\t")
		        if tokens[0] == userId:
		                ratedMovieIds.append((tokens[1],tokens[2]))
		userDataFile.close()

		print "Reading Recommendations"
		recommendationFile = open(recommendationFilename)
		recommendations = []
		for line in recommendationFile:
		        tokens = line.split("\t")
		        if tokens[0] == userId:
		                movieIdAndScores = tokens[1].strip("[]\n").split(",")
		                recommendations = [ movieIdAndScore.split(":") for movieIdAndScore in movieIdAndScores ]
		                break
		recommendationFile.close()

		print "Rated Movies"
		print "------------------------"
		for movieId, rating in ratedMovieIds:
		        print "%s, rating=%s" % (movieById[movieId][0], rating)
		print "------------------------"

		print "Recommended Movies"
		print "------------------------"
		for movieId, score in recommendations:
		        print "%s, score=%s" % (movieById[movieId][0], score)
		print "------------------------"

	Pressione **Ctrl-X**, **Y** e finalmente **Enter** para salvar os dados.

3. Use o comando a seguir para tornar o arquivo executável:

		chmod +x show_recommendations.py

4. Execute o script Python:

		./show_recommendations.py 4 ml-100k/u.data ml-100k/u.item recommendations.txt

	Isso examinará as recomendações geradas do ID de usuário 4.

	* O arquivo **u.data** é usado para recuperar filmes que o usuário classificou
	* O arquivo **u.item** é usado para recuperar os nomes dos filmes
	* O arquivo **recommendations.txt** é usado para recuperar as recomendações de filmes deste usuário

	A saída desse comando deve ser semelhante ao seguinte:

		Reading Movies Descriptions
		Reading Rated Movies
		Reading Recommendations
		Rated Movies
		------------------------
		Mimic (1997), rating=3
		Ulee's Gold (1997), rating=5
		Incognito (1997), rating=5
		One Flew Over the Cuckoo's Nest (1975), rating=4
		Event Horizon (1997), rating=4
		Client, The (1994), rating=3
		Liar Liar (1997), rating=5
		Scream (1996), rating=4
		Star Wars (1977), rating=5
		Wedding Singer, The (1998), rating=5
		Starship Troopers (1997), rating=4
		Air Force One (1997), rating=5
		Conspiracy Theory (1997), rating=3
		Contact (1997), rating=5
		Indiana Jones and the Last Crusade (1989), rating=3
		Desperate Measures (1998), rating=5
		Seven (Se7en) (1995), rating=4
		Cop Land (1997), rating=5
		Lost Highway (1997), rating=5
		Assignment, The (1997), rating=5
		Blues Brothers 2000 (1998), rating=5
		Spawn (1997), rating=2
		Wonderland (1997), rating=5
		In & Out (1997), rating=5
		------------------------
		Recommended Movies
		------------------------
		Seven Years in Tibet (1997), score=5.0
		Indiana Jones and the Last Crusade (1989), score=5.0
		Jaws (1975), score=5.0
		Sense and Sensibility (1995), score=5.0
		Independence Day (ID4) (1996), score=5.0
		My Best Friend's Wedding (1997), score=5.0
		Jerry Maguire (1996), score=5.0
		Scream 2 (1997), score=5.0
		Time to Kill, A (1996), score=5.0
		Rock, The (1996), score=5.0
		------------------------

##Excluir dados temporários

Os trabalhos do Mahout não removem dados temporários criados durante o processamento do trabalho. O parâmetro `--tempDir` é especificado no trabalho de exemplo para isolar os arquivos temporários em um caminho específico para fácil exclusão. Para remover os arquivos temporários, use o seguinte comando:

	hadoop fs -rm -f -r wasb:///temp/mahouttemp

> [AZURE.WARNING]Se você não remover os arquivos temporários ou o arquivo de saída, receberá uma mensagem de erro “não é possível substituir o arquivo” se tentar executar o trabalho novamente com o mesmo caminho `--tempDir`.

##Próximas etapas

Agora que você aprendeu como usar o Mahout, descubra outras maneiras de trabalhar com dados no HDInsight:

* [Hive com o HDInsight](../hadoop-use-hive.md)
* [Pig com o HDInsight](../hadoop-use-pig.md)
* [MapReduce com o HDInsight](../hadoop-use-mapreduce.md)

[build]: http://mahout.apache.org/developers/buildingmahout.html
[movielens]: http://grouplens.org/datasets/movielens/
[100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]: hdinsight-hadoop-linux-tutorial-get-started.md
[upload]: hdinsight-upload-data.md
[ml]: http://en.wikipedia.org/wiki/Machine_learning
[forest]: http://en.wikipedia.org/wiki/Random_forest
[management]: https://manage.windowsazure.com/
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools
 

<!---HONumber=September15_HO1-->