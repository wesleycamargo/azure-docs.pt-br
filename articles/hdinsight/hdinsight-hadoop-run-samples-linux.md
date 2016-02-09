<properties
	pageTitle="Executar amostras de MapReduce do Hadoop no HDInsight baseado em Linux | Microsoft Azure"
	description="Introdução ao uso de exemplos de MapReduce com HDInsight baseado em Linux. Utilize SSH para se conectar ao cluster e use o comando do Hadoop para executar trabalhos de exemplo."
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
	ms.date="12/04/2015"
	ms.author="larryfr"/>




#Executar amostras do Hadoop no HDInsight

[AZURE.INCLUDE [samples-selector](../../includes/hdinsight-run-samples-selector.md)]

Clusters do HDInsight baseados em Linux fornecem um conjunto de exemplos do MapReduce que você pode usar para se familiarizar com a execução de trabalhos de MapReduce do Hadoop. Neste documento, você aprenderá sobre os exemplos disponíveis e verá como é a execução de alguns deles.

##Pré-requisitos

- **Uma assinatura do Azure**: consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)

- **Um cluster do HDInsight baseado em Linux**: consulte [Introdução ao uso do Hadoop com o Hive no HDInsight no Linux](hdinsight-hadoop-linux-tutorial-get-started.md)

- **Um cliente SSH**: para obter informações sobre como usar SSH com o HDInsight, consulte os seguintes artigos:

    - [Usar SSH com Hadoop baseado em Linux no HDInsight no Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    - [Usar SSH com Hadoop baseado em Linux no HDInsight no Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

## Os exemplos ##

**Local**: os exemplos estão localizados no cluster HDInsight em **/usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar**

**Conteúdo**: os exemplos a seguir estão contidos neste arquivo:

- **aggregatewordcount**: programa de mapeamento/redução baseado em Agregação que conta as palavras nos arquivos de entrada
- **aggregatewordhist**: programa de mapeamento/redução baseado em Agregação que computa o histograma de palavras nos arquivos de entrada
- **bbp**: um programa de mapeamento/redução que usa Bailey-Borwein-Plouffe para computar os dígitos exatos de Pi
- **dbcount**: um exemplo de trabalho que faz a contagem das exibições de página de um banco de dados
- **distbbp**: um programa de mapeamento/redução que usa uma fórmula do tipo BBP para computar bits exatos de Pi
- **grep**: um programa de mapeamento/redução que conta as correspondências de uma regex na entrada
- **join**: um trabalho que faz a junção de bancos de dados classificados, particionados igualmente
- **multifilewc**: um trabalho que conta palavras de vários arquivos
- **pentomino**: um programa de mapeamento/redução para disposição de blocos voltado a encontrar soluções para problemas de pentamino
- **pi**: um programa de mapeamento/redução que estima Pi usando um método semelhante ao de Monte Carlo
- **randomtextwriter**: um programa de mapeamento/redução que grava 10 GB de dados textuais aleatórios por nó
- **randomwriter**: um programa de mapeamento/redução que grava 10 GB de dados aleatórios por nó
- **secondarysort**: um exemplo que define uma classificação secundária para a redução
- **sort**: um programa de mapeamento/redução que classifica os dados gravados pelo gravador aleatório
- **sudoku**: um solucionador de sudoku
- **teragen**: gera dados para o terasort
- **terasort**: executa o terasort
- **teravalidate**: verifica os resultados do terasort
- **wordcount**: um programa de mapeamento/redução que conta as palavras nos arquivos de entrada
- **wordmean**: um programa de mapeamento/redução que calcula o tamanho médio das palavras nos arquivos de entrada
- **wordmedian**: um programa de mapeamento/redução que calcula a mediana do tamanho das palavras nos arquivos de entrada
- **wordstandarddeviation**: um programa de mapeamento/redução que calcula o desvio padrão do tamanho das palavras nos arquivos de entrada

**Código-fonte**: o código-fonte desses exemplos está incluído no cluster do HDInsight em **/usr/hdp/2.2.4.9-1/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples**

> [AZURE.NOTE] O `2.2.4.9-1` no caminho é a versão do Hortonworks Data Platform para o cluster do HDInsight e pode ser alterado quando o HDInsight for atualizado.

## Como executar os exemplos ##

1. Conecte ao HDInsight usando o SSH conforme descrito nos seguintes artigos:

    - [Usar SSH com Hadoop baseado em Linux no HDInsight no Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    - [Usar SSH com Hadoop baseado em Linux no HDInsight no Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. No prompt `username@#######:~$`, use o comando a seguir para listar os exemplos:

        hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar

    Isso vai gerar a lista de exemplos da seção anterior deste documento.

3. Use o comando a seguir para obter ajuda com um exemplo específico. Neste caso, o exemplo é **wordcount**:

        hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount

    Você deverá receber a seguinte mensagem:

        Usage: wordcount <in> [<in>...] <out>

    Isso indica que você pode fornecer diversos caminhos de entrada para os documentos de origem, e o caminho final é onde a saída (contagem de palavras nos documentos de origem,) ficará localizada.

4. Use o seguinte para contar todas as palavras nos blocos de anotações de Leonardo Da Vinci, que são fornecidos como dados de exemplo com o cluster:

    	hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/davinciwordcount

    A entrada para este trabalho é lida em ****wasb:///example/data/gutenberg/davinci.txt**.

    A saída do exemplo será armazenada em ****wasb:///example/data/davinciwordcount**.

    > [AZURE.NOTE] Conforme observado na ajuda do exemplo wordcount, você também pode especificar vários arquivos de entrada. Por exemplo, `hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/gutenberg/ulysses.txt /example/data/twowordcount` contaria as palavras em davinci.txt e ulysses.txt.

5. Quando o trabalho for concluído, use o seguinte comando para exibir a saída:

        hadoop fs -cat /example/data/davinciwordcount/*

    Isso vai concatenar todos os arquivos de saída produzidos pelo trabalho, além de exibi-los. Para este exemplo básico há apenas um arquivo. No entanto, se houvesse mais, o comando iteraria todos eles.

    Você verá saídas semelhantes à seguinte neste comando:

        zum     1
        zur     1
        zwanzig 1
        zweite  1

    Cada linha representa uma palavra e quantas vezes ela ocorreu nos dados de entrada.

## Sudoku

O exemplo do Sudoku tem instruções de uso que não ajudam muito; "Inclua um quebra-cabeça na linha de comando".

O [Sudoku](https://en.wikipedia.org/wiki/Sudoku) é um quebra-cabeça lógico composto por nove grades de 3x3. Algumas células da grade têm números, enquanto outros estão em branco, e a meta é encontrar os números correspondentes às células em branco. O link acima tem mais informações sobre o quebra-cabeça, mas o propósito deste exemplo é encontrar as respostas para as células em branco. Sendo assim, nossa entrada deve ser um arquivo no seguinte formato:

- Nove linhas de nove colunas

- Cada coluna pode conter um número ou `?` (que indica uma célula em branco)

- As células são separadas por um espaço

Agora, há uma certa forma de construir o quebra-cabeças Sudoku que não permite repetir um número na mesma coluna ou linha. Felizmente, há um exemplo no cluster do HDInsight que está construído corretamente. Ele está localizado em **/usr/hdp/2.2.4.9-1/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta** e contém o seguinte:

    8 5 ? 3 9 ? ? ? ?
    ? ? 2 ? ? ? ? ? ?
    ? ? 6 ? 1 ? ? ? 2
    ? ? 4 ? ? 3 ? 5 9
    ? ? 8 9 ? 1 4 ? ?
    3 2 ? 4 ? ? 8 ? ?
    9 ? ? ? 8 ? 5 ? ?
    ? ? ? ? ? ? 2 ? ?
    ? ? ? ? 4 5 ? 7 8

> [AZURE.NOTE] A porção `2.2.4.9-1` do caminho pode ser alterada conforme são feitas atualizações no cluster do HDInsight.

Para executar o exemplo do Sudoku, use o seguinte comando:

    hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar sudoku /usr/hdp/2.2.4.9-1/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta

Os resultados devem se semelhar ao seguinte:

    8 5 1 3 9 2 6 4 7
    4 3 2 6 7 8 1 9 5
    7 9 6 5 1 4 3 8 2
    6 1 4 8 2 3 7 5 9
    5 7 8 9 6 1 4 2 3
    3 2 9 4 5 7 8 1 6
    9 4 7 2 8 6 5 3 1
    1 8 5 7 3 9 2 6 4
    2 6 3 1 4 5 9 7 8

## Pi (π)

O exemplo do pi usa um método estatístico (quasi-Monte Carlo) para estimar o valor de pi. Os pontos colocados aleatoriamente em um quadrado de unidade também caem dentro de um círculo inscrito nesse quadrado com uma probabilidade igual à área do círculo, pi/4. O valor de pi pode ser estimado do valor de 4R onde R é a proporção do número de pontos que estão dentro do círculo em relação ao número total de pontos que estão dentro do quadrado. Quanto maior a amostra de pontos usados, melhor será a estimativa.

O mapeador para este exemplo gera um número de pontos colocados aleatoriamente em um quadrado de unidade e, em seguida, conta o número dos pontos que estão dentro do círculo.

O redutor acumula os pontos contados pelos mapeadores e, em seguida, estima o valor de pi na fórmula 4R, em que R é a proporção do número de pontos contados dentro do círculo em relação ao número total de pontos que estão dentro do quadrado.

Use o seguinte comando para executar o exemplo. Ele usa 16 mapas com 10.000.000 exemplos cada um para estimar o valor de pi:

    hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar pi 16 10000000

O valor retornado por este deve ser semelhante a **3,14159155000000000000**. Para referência, as 10 primeiras casas decimais de pi são 3,1415926535.

##Greysort de 10GB

GraySort é uma espécie de parâmetro de comparação cuja métrica é a taxa de classificação (TB/minuto) que é obtida durante a classificação de grandes quantidades de dados, geralmente um mínimo de 100 TB.

Este exemplo usa uma quantidade modesta de 10 GB de dados para que possa ser executado de modo relativamente rápido. Ele usa os aplicativos MapReduce desenvolvidos por Owen O'Malley e Arun Murthy que ganharam o parâmetro de comparação anual de classificação de terabytes de finalidade geral ("daytona") em 2009, com uma taxa de 0,578 TB/m (100 TB em 173 minutos). Para obter mais informações sobre esse e outros benchmarks de classificação, consulte o site [Sortbenchmark](http://sortbenchmark.org/).

Este exemplo usa três conjuntos de programas MapReduce:

- **TeraGen**: um programa de MapReduce que gera linhas de dados para classificar

- **TeraSort**: cria amostras dos dados de entrada e usa o MapReduce para classificar os dados em uma ordem total

    O TeraSort é uma espécie de padrão de funções MapReduce, exceto por um particionador personalizado que usa uma lista classificada de chaves de exemplo N-1 que definem o intervalo de chaves para cada redução. Em particular, todas as chaves dessa amostra[i-1] <= chave < amostra[i] são enviadas para reduzir i. Isso garante que as saídas da redução i sejam todas menores do que a saída da redução i+1.

- **TeraValidate**: um programa de MapReduce que valida que a saída é classificada globalmente

    Ele cria um mapa por arquivo no diretório de saída, e cada mapa garante que cada chave seja menor ou igual à anterior. A função de mapa também gera registros das primeiras e das últimas chaves de cada arquivo, e a função de redução garante que a primeira chave do arquivo i seja maior do que a última chave do arquivo i-1. Todos os problemas são relatados como uma saída da redução com as chaves que estão fora de ordem.

Use as seguintes etapas para gerar dados, classificar e validar a saída:

1. Gerar 10 GB de dados, que serão armazenados no armazenamento padrão do cluster do HDInsight em ****wasb:///example/data/10GB-sort-input**:

        hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapred.map.tasks=50 100000000 /example/data/10GB-sort-input

	O `-Dmapred.map.tasks` informa o Hadoop quantas tarefas de mapeamento serão usadas para este trabalho. Os dois parâmetros finais instruem o trabalho a criar 10 GB de dados e armazená-los em ****wasb:///example/data/10GB-sort-input**.

2. Use o comando a seguir para classificar os dados:

		hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-input /example/data/10GB-sort-output

	O `-Dmapred.reduce.tasks` informa o Hadoop quantas tarefas de redução serão usadas para o trabalho. Os dois parâmetros finais são apenas os locais de entrada e saída dos dados.

3. Use o seguinte para validar os dados gerados pela classificação:

		hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-output /example/data/10GB-sort-validate

##Próximas etapas ##

Neste artigo, você aprendeu a executar os exemplos incluídos com os clusters do HDInsight baseados em Linux. Para obter tutoriais sobre como usar o Pig, o Hive e o MapReduce com o HDInsight, consulte os seguintes tópicos:

* [Usar o Pig com Hadoop no HDInsight][hdinsight-use-pig]
* [Usar o Hive com Hadoop no HDInsight][hdinsight-use-hive]
* [Usar o MapReduce com Hadoop no HDInsight][hdinsight-use-mapreduce]



[hdinsight-errors]: hdinsight-debug-jobs.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-sdk-documentation]: https://msdn.microsoft.com/library/azure/dn479185.aspx

[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-introduction]: hdinsight-hadoop-introduction.md



[hdinsight-samples]: hdinsight-run-samples.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

<!---HONumber=AcomDC_0128_2016-->