---
title: Gerar recomendações usando o Mahout e o HDInsight baseado em Linux | Microsoft Docs
description: Saiba como usar a biblioteca de aprendizado de máquina do Apache Mahout para gerar recomendações de filmes com o HDInsight para Linux (Hadoop).
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal

ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2016
ms.author: larryfr

---
# Gerar recomendações de filmes usando o Apache Mahout com o Hadoop para Linux no HDInsight
[!INCLUDE [mahout-selector](../../includes/hdinsight-selector-mahout.md)]

Saiba como usar a biblioteca de aprendizado de máquina do [Apache Mahout](http://mahout.apache.org) com o Azure HDInsight para gerar recomendações de vídeos.

O Mahout é uma biblioteca de [aprendizado de máquina][ml] para o Apache Hadoop. O Mahout contém algoritmos para processamento de dados, como filtragem, classificação e clustering. Neste artigo, você utilizará um mecanismo de recomendação para gerar recomendações de filmes baseadas nos vídeos que seus amigos assistiram.

> [!NOTE]
> As etapas deste documento exigem o Hadoop para Linux no cluster do HDInsight. Para obter informações sobre como usar o Mahout com um cluster do Windows, consulte [Gerar recomendações de filmes usando o Apache Mahout com o Hadoop para Windows no HDInsight](hdinsight-mahout.md)
> 
> 

## Pré-requisitos
* Um cluster do Hadoop no HDInsight baseado em Linux. Para obter informações sobre como criar um, consulte [Introdução ao uso do Hadoop para Linux HDInsight][getstarted].

## Versões do Mahout
Para obter mais informações sobre a versão do Mahout incluída com o cluster HDInsight, consulte [Versões do HDInsight e componentes do Hadoop](hdinsight-component-versioning.md).

> [!WARNING]
> Embora seja possível carregar uma versão diferente do Mahout no cluster HDInsight, há suporte somente aos componentes fornecidos com o cluster HDInsight e o Suporte da Microsoft ajudará a isolar e resolver problemas relacionados a esses componentes.
> 
> Componentes personalizados recebem suporte comercialmente razoável para ajudá-lo a solucionar o problema. Isso pode resultar na resolução do problema ou na solicitação de você buscar nos canais disponíveis as tecnologias de código-fonte aberto, onde é possível encontrar conhecimento aprofundado sobre essa tecnologia. Por exemplo, há muitos sites de comunidades que podem ser usados, como o [Fórum do MSDN para o HDInsight](https://social.msdn.microsoft.com/Forums/azure/pt-BR/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Além disso, projetos Apache têm sites de projetos em [http://apache.org](http://apache.org), por exemplo: [Hadoop](http://hadoop.apache.org/) e [Spark](http://spark.apache.org/).
> 
> 

## <a name="recommendations"></a>Entendendo as recomendações
Uma das funções oferecidas pelo Mahout é um mecanismo de recomendação. Ele aceita dados no formato de `userID`, `itemId` e `prefValue` (os usuários escolhem o item de sua preferência). O Mahout, então, pode realizar análises de coocorrência, para determinar que *usuários que têm preferência por um item também têm preferência por esses outros itens*. O Mahout determinará, então, usuários com preferências de item similares, que podem ser utilizadas para fazer recomendações.

A seguir está um exemplo extremamente simples usando filmes:

* **Coocorrência**: Joe, Alice e Bob, todos gostaram de *Guerra nas Estrelas*, *O Império Contra-ataca* e *O Retorno de Jedi*. O Mahout determina que usuários que gostam de qualquer um desses filmes também gostam dos outros dois.
* **Coocorrência**: Bob e Alice também gostaram de *A Ameaça Fantasma*, *A Guerra dos Clones* e *A Vingança dos Sith*. O Mahout determina que usuários que gostam de qualquer um dos três filmes citados anteriormente também gostam destes últimos três.
* **Recomendação por similaridade**: já que Joe gostou dos primeiros três, o Mahout pesquisará os filmes que outros com preferências similares gostaram, mas que Joe não assistiu (curtiu/classificou). Nesse caso, o Mahout recomendaria *A Ameaça Fantasma*, *A Guerra dos Clones* e *A Vingança dos Sith*.

### Compreendendo os dados
Convenientemente, a [GroupLens Research][movielens] oferece dados de classificação para filmes em um formato compatível com o Mahout. Esses dados estão disponíveis no armazenamento padrão do cluster em `/HdiSamples/HdiSamples/MahoutMovieData`.

Existem dois arquivos, `moviedb.txt` (informações sobre filmes) e `user-ratings.txt`. O arquivo ratings.txt do usuário é usado durante a análise, enquanto moviedb.txt é usado para fornecer informações de texto amigável ao exibir os resultados da análise.

Os dados contidos em user-ratings.txt têm uma estrutura de `userID`, `movieID`, `userRating` e `timestamp`, que informa a classificação que cada usuário deu a um filme. Aqui está um exemplo dos dados:

    196    242    3    881250949
    186    302    3    891717742
    22    377    1    878887116
    244    51    2    880606923
    166    346    1    886397596

## Executar a análise
Use o seguinte comando para executar o trabalho de recomendação:

    mahout recommenditembased -s SIMILARITY_COOCCURRENCE -i /HdiSamples/HdiSamples/MahoutMovieData/user-ratings.txt -o /example/data/mahoutout --tempDir /temp/mahouttemp

> [!NOTE]
> O trabalho pode levar vários minutos para ser concluído e pode executar vários trabalhos do MapReduce.
> 
> 

## Exibir a saída
1. Quando o trabalho for concluído, use o seguinte comando para exibir a saída gerada:
   
        hdfs dfs -text /example/data/mahoutout/part-r-00000
   
    A saída terá a seguinte aparência:
   
        1    [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
        2    [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
        3    [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
        4    [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]
   
    A primeira coluna é a `userID`. Os valores contidos em “[” e “]” são `movieId`:`recommendationScore`.
2. Você pode usar a saída, juntamente com o moviedb.txt, para exibir mais informações amigáveis. Primeiro, é necessário copiar os arquivos localmente usando os seguintes comandos:
   
        hdfs dfs -get /example/data/mahoutout/part-r-00000 recommendations.txt
        hdfs dfs -get /HdiSamples/HdiSamples/MahoutMovieData/* .
   
    Isso copiará os dados de saída em um arquivo chamado **recommendations.txt** no diretório atual, juntamente com os aquivos de dados de filme.
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
4. Use o comando a seguir para tornar o arquivo executável:
   
        chmod +x show_recommendations.py
5. Execute o script Python. O exemplo a seguir pressupõe que você esteja no diretório em que todos os arquivos foram baixados:
   
        ./show_recommendations.py 4 user-ratings.txt moviedb.txt recommendations.txt
   
    Isso examinará as recomendações geradas do ID de usuário 4.
   
   * O arquivo **user-ratings.txt** é usado para recuperar filmes que o usuário classificou
   * O arquivo **user-ratings.txt** é usado para recuperar os nomes dos filmes
   * O arquivo **recommendations.txt** é usado para recuperar as recomendações de filmes deste usuário
     
     A saída desse comando deve ser semelhante ao seguinte:
     
       Reading Movies Descriptions
       Reading Rated Movies
       Reading Recommendations
     
     ##   Rated Movies
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
     
     ##   In & Out (1997), rating=5
     ##   Recommended Movies
       Seven Years in Tibet (1997), score=5.0
       Indiana Jones and the Last Crusade (1989), score=5.0
       Jaws (1975), score=5.0
       Sense and Sensibility (1995), score=5.0
       Independence Day (ID4) (1996), score=5.0
       My Best Friend's Wedding (1997), score=5.0
       Jerry Maguire (1996), score=5.0
       Scream 2 (1997), score=5.0
       Time to Kill, A (1996), score=5.0
     
     ##   Rock, The (1996), score=5.0

## Excluir dados temporários
Os trabalhos do Mahout não removem dados temporários criados durante o processamento do trabalho. O parâmetro `--tempDir` é especificado no trabalho de exemplo para isolar os arquivos temporários em um caminho específico para fácil exclusão. Para remover os arquivos temporários, use o seguinte comando:

    hdfs dfs -rm -f -r /temp/mahouttemp

> [!WARNING]
> Se você quiser executar o comando novamente, você também deve excluir o diretório de saída. Use o seguinte para excluir esse diretório:
> 
> ```hdfs dfs -rm -f -r /example/data/mahoutout```
> 
> 

## Próximas etapas
Agora que você aprendeu como usar o Mahout, descubra outras maneiras de trabalhar com dados no HDInsight:

* [Hive com o HDInsight](hdinsight-use-hive.md)
* [Pig com o HDInsight](hdinsight-use-pig.md)
* [MapReduce com o HDInsight](hdinsight-use-mapreduce.md)

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


<!---HONumber=AcomDC_0914_2016-->