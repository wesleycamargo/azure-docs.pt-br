<properties
	pageTitle="Executar amostras do Hadoop no HDInsight | Microsoft Azure"
	description="Introdução ao uso do serviço Azure HDInsight com os exemplos fornecidos. Use os scripts do PowerShell que executam programas MapReduce em clusters de dados."
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/09/2015"
	ms.author="jgao"/>




#Executar amostras do Hadoop no HDInsight

[AZURE.INCLUDE [samples-selector](../../includes/hdinsight-run-samples-selector.md)]

Um conjunto de amostras é fornecido para ajudar você a começar a executar trabalhos do MapReduce em clusters do Hadoop usando o Azure HDInsight. Essas amostras são disponibilizadas em cada um dos clusters gerenciados do HDInsight que você cria. A execução dessas amostras fará você se familiarizar com o uso dos cmdlets do PowerShell do Azure para executar trabalhos em clusters do Hadoop.

Os programas MapReduce também podem ser executados programaticamente de um aplicativo usando a API do Microsoft .NET para HDInsight. Para obter mais informações sobre o uso das APIs do HDInsight para envio de trabalhos, consulte [Enviar Trabalhos do Hadoop no HDInsight][hdinsight-submit-jobs].

Existe muita documentação adicional na Web para tecnologias relacionadas ao Hadoop, como a programação e o streaming de MapReduce baseado em Java, bem como documentação sobre o uso de cmdlets usados em scripts do Windows PowerShell. Para obter mais informações sobre esses recursos, consulte a seção final **Recursos para o HDInsight** do tópico [Introdução ao HDInsight do Azure][hdinsight-introduction].

**O que são estas amostras**

<p>Esses exemplos têm o objetivo de agilizar sua experiência de como implantar trabalhos do Hadoop e de fornecer uma bancada de testes extensível para trabalhar com os conceitos e procedimentos de scripts usados pelo serviço. Eles fornecem exemplos de tarefas comuns, como a criação e a importação de conjuntos de dados de vários tamanhos, execução e composição de trabalhos sequencialmente e o exame dos resultados de seus trabalhos. Os conjuntos de dados usados podem ter tamanhos variados permitindo que você observe os efeitos que os conjuntos de dados de vários tamanhos têm sobre o desempenho do trabalho.</p>


**Pré-requisitos**:

- **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **um cluster do HDInsight**. Para obter instruções sobre as várias maneiras pelas quais esses clusters podem ser criados, consulte [[Provisionar clusters HDInsight](hdinsight-provision-clusters.md).
- **Uma estação de trabalho com o PowerShell do Azure.** Consulte [Instalar e usar o PowerShell do Azure](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).



## Os exemplos ##

O HDInsight é fornecido com estas amostras:

- [**A amostra do Hadoop do estimador de Pi**][hdinsight-sample-pi-estimator]\: mostra como executar um programa MapReduce com o HDInsight que usa um método estatístico (quasi-Monte Carlo) para estimar o valor de pi.
- [**Executar um exemplo de contagem de palavras do MapReduce em um cluster do Hadoop**][hdinsight-sample-wordcount]\: mostra como usar um cluster HDInsight para executar um programa MapReduce que conta todas as ocorrências de palavras em um arquivo de texto.
- [**A amostra do Hadoop Graysort de 10 GB**][hdinsight-sample-10gb-graysort]\: mostra como executar um GraySort de finalidade geral em um arquivo de 10 GB usando o HDInsight. Existem três trabalhos a serem executados: Teragen, para gerar os dados, Terasort, para classificar os dados e Teravalidate, para confirmar que os dados foram classificados corretamente.
- [**A amostra do MapReduce de contagem de streaming do C# no Hadoop**][hdinsight-sample-csharp-streaming]\: mostra como usar o C# para escrever um programa MapReduce que usa a interface de streaming do Hadoop.


## Como executar os exemplos ##

As amostras podem ser executadas usando o PowerShell do Azure. São fornecidas instruções sobre como fazer isso para cada uma das amostras anteriores.

##Próximas etapas ##

Neste artigo e nos artigos sobre cada uma das amostras, você aprendeu a executar as amostras incluídas com os clusters do HDInsight usando o PowerShell do Azure. Para obter tutoriais sobre como usar o Pig, o Hive e o MapReduce com o HDInsight, consulte os seguintes tópicos:

* [Introdução ao uso do Hadoop com o Hive no HDInsight para analisar o uso de fone de celular][hdinsight-get-started]
* [Usar o Pig com o Hadoop no HDInsight][hdinsight-use-pig]
* [Usar o Hive com o Hadoop no HDInsight][hdinsight-use-hive]
* [Enviar trabalhos do Hadoop no HDInsight][hdinsight-submit-jobs]
* [Documentação do SDK do Azure HDInsight][hdinsight-sdk-documentation]
* [Depurar o Hadoop no HDInsight: mensagens de erro][hdinsight-errors]


[hdinsight-errors]: hdinsight-debug-jobs.md

[hdinsight-sdk-documentation]: https://msdn.microsoft.com/library/azure/dn479185.aspx

[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-introduction]: hdinsight-hadoop-introduction.md


[powershell-install-configure]: ../install-configure-powershell.md

[hdinsight-get-started]: ../hdinsight-get-started.md

[hdinsight-samples]: hdinsight-run-samples.md
[hdinsight-sample-10gb-graysort]: hdinsight-sample-10gb-graysort.md
[hdinsight-sample-csharp-streaming]: hdinsight-sample-csharp-streaming.md
[hdinsight-sample-pi-estimator]: hdinsight-sample-pi-estimator.md
[hdinsight-sample-wordcount]: hdinsight-sample-wordcount.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

<!---HONumber=Oct15_HO3-->