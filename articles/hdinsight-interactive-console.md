<properties linkid="manage-services-hdinsight-howto-work-with-the-interactive-console" urlDisplayName="Console interativo" pageTitle="Como trabalhar com o console interativo no HDInsight | Azure" metaKeywords="" description="Neste guia, você aprenderá a executar tarefas comuns, como carregar arquivos, executar trabalhos e visualizar dados usando o console interativo do Serviço HDInsight." metaCanonical="" services="hdinsight" documentationCenter="" title="Consoles interativos JavaScript e Hive do HDInsight" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />




# Consoles interativos JavaScript e Hive do HDInsight

O Serviço Microsoft Azure HDInsight Service é fornecido com consoles interativos para JavaScript e Hive. Esses consoles fornecem uma experiência REPL (loops de leitura-avaliação-impressão) simples e interativa, onde os usuários podem inserir expressões, avaliá-las e, em seguida, consultar e exibir os resultados de um trabalho MapReduce imediatamente. O console JavaScript executa instruções Pig Latin. O console Hive avalia instruções Hive Query Language (Hive QL). Os dois tipos de instruções são compilados em programas MapReduce. O gerenciamento de trabalhos Hadoop nesses consoles é muito mais simples do que conectar-se remotamente com o nó principal do cluster Hadoop e trabalhar com programas MapReduce diretamente.

**O console JavaScript**: um shell de comando que fornece uma interface fluente do ecossistema do Hadoop. Uma interface fluente usa um método de encadeamento de instruções que retransmite o contexto de uma chamada em uma sequência para a chamada subsequente nessa sequência. O console JavaScript fornece:
		
- Acesso ao cluster Hadoop, seus recursos e aos comandos do HDFS (Sistema de Arquivos Distribuídos) do Hadoop.
- Gerenciamento e manipulação de dados de entrada e de saída do cluster Hadoop.
- Uma interface fluente que avalia as instruções de Pig Latin e JavaScript para definir uma série de programas MapReduce para criar fluxos de trabalho de processamento de dados.

**O console Hive**: o Hive é uma estrutura de data warehouse, baseada no Hadoop, que fornece gerenciamento, consultas e análise de dados. O Hive usa o HiveQL, um dialeto do SQL, para consultar dados armazenados em um cluster Hadoop. O console Hive fornece:
			
- Acesso ao cluster Hadoop, seus recursos e aos comandos do HDFS.		
- Uma implementação da estrutura do Hive que pode executar instruções HiveQL em um cluster Hadoop.	
- Um modelo de banco de dados relacional para HDFS que permite interagir com os dados armazenados no sistema de arquivos distribuídos, como se os dados estivessem armazenados em tabelas.		
O consoleJavaScript usa o Pig Latin, uma linguagem de fluxo de dados, e o console Hive usa o HiveQL, uma linguagem de consulta. 	

O Pig (e o console JavaScript) terão preferência para usuários que estão mais familiarizados com uma abordagem de script, onde uma sequência de transformações encadeadas (ou fluentes) é usada para definir o fluxo de trabalho de processamento de dados. Também será uma boa opção se você tiver dados seriamente não estruturados.	

O Hive (e seu console) terá a preferência dos usuários que estão mais familiarizados com o SQL e com um ambiente de banco de dados relacional. O uso de esquema e de uma tabela de abstração no Hive significa que a experiência é muito semelhante a que é normalmente encontrada em um RDBMS.

O Pig e o Hive fornecem linguagens de nível superior que são compiladas em programas MapReduce escritos em Java e executados no HDFS. Se você realmente precisar de controle ou de alto desempenho, você precisará escrever programas MapReduce diretamente.


##Neste tutorial

* [Usar o console JavaScript para executar um trabalho MapReduce](#runjob)
* [Usar o console JavaScript para exibir os resultados graficamente](#displayresults)
* [Usar o console Hive para exportar os resultados para uma tabela Hive](#createhivetable)
* [Usar o console Hive para consultar os dados da tabela Hive](#queryhivetable)


##<a name="runjob"></a>Usar o console JavaScript para executar um trabalho MapReduce

Nesta seção, você pode usar o console JavaScript para executar o exemplo de WordCount que é fornecido com o Serviço HDInsight. A consulta que o JavaScript executa aqui usa a API fluente sobreposta no Pig que é fornecida pelo Console Interativo.  O arquivo de texto analisado aqui é a edição do eBook do Project Gutenberg de *The Notebooks of Leonardo Da Vinci*. Um filtro é especificado para que os resultados do trabalho MapReduce contenham somente as dez palavras que ocorrem com mais frequência. 

1. Entre no [Portal de Gerenciamento](https://manage.windowsazure.com).
2. Clique em **HDINSIGHT**. Você verá uma lista de clusters Hadoop implantados.
3. Clique no nome do cluster HDInsight ao qual você deseja conectar-se.
4. Clique em **Gerenciar Cluster**.
5. Digite suas credenciais e, em seguida, clique em **Fazer logon**.
6. No portal de HDInsight, clique em **Exemplos**.

	![HDI.Tiles.Samples][hdi-tiles-samples]

7. Na **Galeria de Exemplos do Hadoop** , clique no título **WordCount**. 
8. Clique em **WordCount.js** no canto superior direito e salve o arquivo em um diretório local, por exemplo, na pasta ../downloads.

	![HDI.JsConsole.WordCountDownloads][hdi-jsconsole-wordcount]

9. Clique em **Azure HDInsight** no canto superior esquerdo para voltar à página do painel de cluster.
10. Clique em **Cluster Interativo** para abrir o console JavaScript. 
	
	![HDI.Tiles.InteractiveConsole][hdi-tiles-interactive-console]
11. Clique em **JavaScript** no canto superior direito.
12. Execute o seguinte comando:

	fs.put()

13. Insira os seguintes parâmetros na janela **Carregar um arquivo**:  

	- **Origem:** _..\downloads\Wordcount.js
 	- **Destino:** ./WordCount.js/ 	

	![HDI.JsConsole.UploadJs][hdi-jsconsole-upload]

	Procure o local do arquivo WordCount.js. O caminho local completo será necessário. O único ponto no início do caminho de destino é necessário como parte do endereço relativo no HDFS.


14. Clique no botão **Carregar**.

15. Execute os seguintes comandos para listar o arquivo e exibir o conteúdo:

		#ls
		#cat WordCount.js

	![HDI.JsConsole.JsCode][hdi-jsconsole-jscode]

	Observe que a função de mapa JavaScript remove letras maiúsculas do texto usando o método "toLowerCase()" antes de contar o número de ocorrências de uma palavra na função de redução. 
	
16. Execute o seguinte comando para listar o arquivo de dados que será processado pelo trabalho MapReduce de WordCount: 

 		#ls /example/data/gutenberg
	
17. Execute o comando a seguir para executar o programa MapReduce: 

		pig.from("/example/data/gutenberg/davinci.txt").mapReduce("/user/admin/WordCount.js", "word, count:long").orderBy("count DESC").take(10).to("DaVinciTop10Words")
	
	Substitua admin pelo nome do usuário do logon atual.

	Observe como as instruções são "encadeadas" usando o operador ponto, e o arquivo de saída é chamado *DaVinciTop10Words*. Na próxima seção, você irá acessar o arquivo de saída. 

	Depois de concluído, você deverá ver o seguinte:

		pig.from("/example/data/gutenberg/davinci.txt").mapReduce("/user/admin/WordCount.js", "word, count:long").orderBy("count DESC").take(10).to("DaVinciTop10Words")
		2013-04-25 18:54:28,116 [main] INFO  org.apache.pig.backend.hadoop.executionengine.mapReduceLayer.MapReduceLauncher - Success! (View Log)

18. Role para a direita e, em seguida, clique em **Exibir Log** se você desejar observar o andamento do trabalho. Esse log também fornecerá diagnóstico se o trabalho não for concluído. Quando o trabalho for concluído, você verá a seguinte mensagem no final do log:

		[main] INFO org.apache.pig.backend.hadoop.executionengine.mapReduceLayer.MapReduceLauncher - Success! followed by a link to the log file.
	
19. Execute o comando a seguir para listar o arquivo de saída:

		#ls

	Observe que uma pasta DavinciTop10Words é criada.

##<a name="displayresults"></a>Usar o console JavaScript para exibir os resultados graficamente
Na última seção, você executou um trabalho MapReduce para recuperar as 10 palavras que mais ocorrem em um arquivo de texto. O arquivo de saída é ./DaVinciTop10Words.

1. Execute o comando a seguir para exibir os resultados no diretório DaVinciTop10Words:

		file = fs.read("DaVinciTop10Words")

	O resultado é semelhante a:

		js> file=fs.read("DaVinciTop10Words")
		the	22966
		of	11228
		and	8428
		in	5737
		to	5296
		a	4791
		is	4261
		it	3073
		that	2903
		which	2544
	
2. Execute o seguinte comando para analisar o conteúdo do arquivo em um arquivo de dados:

		data = parse(file.data, "word, count:long")
		
3. Execute o comando a seguir para plotar os dados:

		graph.bar(data)
		
	![HDI.JsConsole.BarGraphTop10Words][hdi-jsconsole-bargraph-top10words]


##<a name="createhivetable"></a>Usar o console Hive para exportar os resultados para uma tabela Hive

Esta seção apresenta o console interativo Hive. Você irá criar uma tabela Hive na saída do trabalho MapReduce. A próxima seção mostra como consultar os dados nesta tabela. 

**Criar a tabela Hive**
	
1. Clique em Hive no canto superior direito para abrir o console Hive.
	
2. Digite o seguinte comando para criar uma tabela de duas colunas denominada _DaVinciWordCountTable_ na saída do WordCount de exemplo que foi salva na pasta "DaVinciTop10Words":
		
		CREATE EXTERNAL TABLE DaVinciWordCountTable		
		(word STRING,
		count INT)	
		ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'	
		STORED AS TEXTFILE LOCATION '/user/admin/DaVinciTop10Words';	

	Substitua admin pelo nome do usuário do logon. 

	Observe que a tabela é criada como uma tabela EXTERNA para manter a pasta de destino independente da tabela. Além disso, observe que você precisa apenas especificar a pasta na qual o arquivo de saída está localizado, não o próprio nome do arquivo.

3. Clique em **AVALIAR** na parte inferior esquerda. 		

4. Digite os seguintes comandos para confirmar se a tabela de duas colunas foi criada:

		SHOW TABLES;
		DESCRIBE DaVinciWordCountTable;

5. Clique em **AVALIAR**.
	
	![HDI.Hive.ShowDescribeTable][hdi-hive-showdescribetable]

##<a name="queryhivetable"></a>Usar o console Hive para consultar os dados da tabela Hive

1. Execute o seguinte comando para consultar as 10 palavras com o maior número de ocorrências:

		SELECT word, count
		FROM DaVinciWordCountTable
		ORDER BY count DESC LIMIT 10

	Os resultados dessa consulta são:			

		SELECT word, count FROM DaVinciWordCountTable ORDER BY count DESC LIMIT 10
		the 22966
		of 11228
		and 8428
		in 5737
		to 5296
		a 4791
		is 4261
		it 3073
		that 2903
		which 2544

## Próximas etapas

Você viu como executar um trabalho Hadoop no console interativo JavaScript e como inspecionar os resultados de um trabalho usando esse console. Você também viu como o console interativo Hive pode ser usado para inspecionar e processar os resultados de um trabalho Hadoop criando e consultando uma tabela que contém a saída de um programa MapReduce. Também viu exemplos de instruções de Pig Latin e Hive QL sendo usadas nos consoles. Finalmente, você viu como a natureza interativa de REPL dos consoles JavaScript e Hive simplifica o uso de um cluster Hadoop. Para saber mais, consulte os seguintes artigos:

* [Usando o Pig com o HDInsight][hdinsight-pig] 
* [Usando o Hive com o HDInsight][hdinsight-hive]
* [Usando o MapReduce com o HDInsight][hdinsight-mapreduce]

[hdinsight-pig]: /pt-br/manage/services/hdinsight/using-pig-with-hdinsight/
[hdinsight-hive]: /pt-br/manage/services/hdinsight/using-hive-with-hdinsight/
[hdinsight-mapreduce]: /pt-br/manage/services/hdinsight/using-mapreduce-with-hdinsight/

[hdi-tiles-samples]: ./media/hdinsight-interactive-console/HDI.TileSamples.PNG
[hdi-jsconsole-wordcount]: ./media/hdinsight-interactive-console/HDI.JsConsole.WordCountDownloads.PNG
[hdi-tiles-interactive-console]: ./media/hdinsight-interactive-console/HDI.TileInteractiveConsole.PNG
[hdi-jsconsole-upload]: ./media/hdinsight-interactive-console/HDI.JsConsole.UploadJs.PNG
[hdi-jsconsole-jscode]: ./media/hdinsight-interactive-console/HDI.JsConsole.JsCode.PNG
[hdi-jsconsole-bargraph-top10words]: ./media/hdinsight-interactive-console/HDI.JsConsole.BarGraphTop10Words.PNG
[hdi-hive-showdescribetable]: ./media/hdinsight-interactive-console/HDI.Hive.ShowDescribeTable.PNG "Confirmação da tabela Hive")

