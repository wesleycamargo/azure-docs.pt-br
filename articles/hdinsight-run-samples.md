<properties urlDisplayName="Run Hadoop Samples in HDInsight" pageTitle="Executar amostras do Hadoop no HDInsight | Azure" metaKeywords="hdinsight, hdinsight sample,  hadoop, mapreduce" description="Introdução ao uso do serviço Azure HDInsight com os exemplos fornecidos. Use os scripts do PowerShell que executam programas MapReduce em clusters de dados." metaCanonical="" services="hdinsight" documentationCenter="" title="Run the Hadoop samples in HDInsight" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/10/2014" ms.author="bradsev" />




#Executar amostras do Hadoop no HDInsight

Um conjunto de amostras é fornecido para ajudar você a começar a executar trabalhos do MapReduce em clusters do Hadoop usando o Azure HDInsight. Essas amostras são disponibilizadas em cada um dos clusters gerenciados do HDInsight que você cria. Com a execução das amostras, você se familiarizará com os cmdlets do PowerShell do Azure HDInsight para executar trabalhos no Hadoop.

Os programas MapReduce também podem ser executados de forma programática usando a API do Microsoft .NET para HDInsight. Para obter mais informações sobre como usar as APIs do HDInsight para envio de trabalhos, consulte [Enviar trabalhos do Hadoop de forma programática][hdinsight-submit-jobs].

Existe muita documentação adicional na web para tecnologias relacionadas ao Hadoop, como a programação e o streaming de MapReduce com base em Java, bem como documentação sobre o uso de cmdlets usados em scripts do PowerShell. Para obter mais informações sobre esses recursos, consulte a seção final **Recursos para o HDInsight** do tópico [Introdução ao Azure HDInsight][hdinsight-introduction].

**O que são esses exemplos**

<p>Esses exemplos têm o objetivo de agilizar sua experiência de como implantar trabalhos do Hadoop e de fornecer uma mesa de testes extensível para trabalhar com os conceitos e procedimentos de scripts usados pelo serviço. Eles fornecem exemplos de tarefas comuns, como a criação e a importação de conjuntos de dados de vários tamanhos, execução de trabalhos e composição de trabalhos sequencialmente e o exame dos resultados de seus trabalhos. Os conjuntos de dados usados podem ter tamanhos variados permitindo que você observe os efeitos que os conjuntos de dados de vários tamanhos têm sobre o desempenho do trabalho.</p>


**Pré-requisitos**:	

- Você deve ter uma conta do Azure. Para obter as opções de como se inscrever em uma conta, consulte a página [Testar o Azure gratuitamente](http://azure.microsoft.com/pt-br/pricing/free-trial/) .

- Você deve ter provisionado um cluster HDInsight. Para obter instruções sobre as várias maneiras pelas quais esses clusters podem ser criados, consulte [Provisionar clusters HDInsight](../hdinsight-provision-clusters/)

- Você deve ter instalado o PowerShell do Azure e o configurado para uso com sua conta. Para obter instruções sobre como fazer isso, consulte[Instalar e configurar o PowerShell do Azure][powershell-install-configure].

## Os exemplos ##

O HDInsight é fornecido com os exemplos a seguir.

- [**O exemplo de estimador de Pi**][hdinsight-sample-pi-estimator] Este tutorial mostra como executar um programa MapReduce com o HDInsight que usa um método estatístico (quasi-Monte Carlo) para estimar o valor de Pi.
- [**O exemplo WordCount**][hdinsight-sample-wordcount] Este tutorial mostra como usar um cluster HDInsight para executar um programa MapReduce que conta todas as ocorrências de palavras em um arquivo de texto.
- [**O exemplo de Graysort de 10 GB**][hdinsight-sample-10gb-graysort] Este tutorial mostra como executar um GraySort de finalidade geral em um arquivo de 10 GB usando o HDInsight. Há três trabalhos a serem executados:Teragen para gerar os dados, Terasort para classificar os dados e Teravalidate para confirmar que os dados foram classificados corretamente.
- [**O exemplo de Streaming do C#**][hdinsight-sample-csharp-streaming] Este tutorial mostra como usar o C# para escrever um programa MapReduce que usa a interface de streaming do Hadoop. 


## Como executar os exemplos ##

Os exemplos podem ser executados usando o PowerShell do Azure. São fornecidas instruções sobre como fazer isso para cada um dos exemplos nas páginas vinculadas acima.

##Próximas Etapas ##

Neste artigo e nos artigos sobre cada um dos exemplos, você aprendeu a executar os exemplos incluídos com os clusters de HDInsight usando o PowerShell do Azure. Para obter tutoriais sobre como usar Pig, Hive e MapReduce com o HDInsight, consulte os seguintes tópicos:

* [Introdução ao Serviço Azure HDInsight][hdinsight-get-started]
* [Usar o Pig com o HDInsight][hdinsight-use-pig]
* [Usar o Hive com o HDInsight][hdinsight-use-hive]
* [Enviar trabalhos do Hadoop de forma programática][hdinsight-submit-jobs]
* [Documentação do SDK do Azure HDInsight][hdinsight-sdk-documentation]
* [Depurar o HDInsight: Mensagens de erro][hdinsight-errors]


[hdinsight-errors]: ../hdinsight-debug-jobs/

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/pt-br/library/dn479185.aspx

[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-introduction]: ../hdinsight-hadoop-introduction/



[Powershell-install-configure]: ../install-configure-powershell/

[hdinsight-get-started]: ../hdinsight-get-started/

[hdinsight-samples]: ../hdinsight-run-samples/
[hdinsight-sample-10gb-graysort]: ../hdinsight-sample-10gb-graysort/
[hdinsight-sample-csharp-streaming]: ../hdinsight-sample-csharp-streaming/
[hdinsight-sample-pi-estimator]: ../hdinsight-sample-pi-estimator/
[hdinsight-sample-wordcount]: ../hdinsight-sample-wordcount/

[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/


<!--HONumber=35.1-->
