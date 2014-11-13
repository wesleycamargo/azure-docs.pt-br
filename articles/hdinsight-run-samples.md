<properties urlDisplayName="Run Hadoop Samples in HDInsight" pageTitle="Executar amostras do Hadoop no HDInsight | Azure" metaKeywords="hdinsight, hdinsight sample,  hadoop, mapreduce" description="Introdu&ccedil;&atilde;o ao uso do servi&ccedil;o Azure HDInsight com os exemplos fornecidos. Use os scripts do PowerShell que executam programas MapReduce em clusters de dados." metaCanonical="" services="hdinsight" documentationCenter="" title="Executar amostras do Hadoop no HDInsight" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bradsev" />

# Executar amostras do Hadoop no HDInsight

Um conjunto de amostras é fornecido para ajudar você a começar a executar trabalhos do MapReduce em clusters do Hadoop usando o Azure HDInsight. Essas amostras são disponibilizadas em cada um dos clusters gerenciados do HDInsight que você cria. Com a execução das amostras, você se familiarizará com os cmdlets do PowerShell do Azure HDInsight para executar trabalhos no Hadoop.

Os programas MapReduce também podem ser executados de forma programática usando a API do Microsoft .NET para HDInsight. Para obter mais informações sobre como usar as APIs do HDInsight para envio de trabalhos, consulte [Enviar trabalhos Hadoop programaticamente][Enviar trabalhos Hadoop programaticamente].

Existe muita documentação adicional na web para tecnologias relacionadas ao Hadoop, como a programação e o streaming de MapReduce com base em Java, bem como documentação sobre o uso de cmdlets usados em scripts do PowerShell. Para obter mais informações sobre esses recursos, consulte a seção final **Recursos para o HDInsight** do tópico [Introdução ao Azure HDInsight][Introdução ao Azure HDInsight].

**O que são esses exemplos**

Esses exemplos têm o objetivo de agilizar sua experiência de como implantar trabalhos do Hadoop e de fornecer uma mesa de testes extensível para trabalhar com os conceitos e procedimentos de scripts usados pelo serviço. Eles fornecem exemplos de tarefas comuns, como a criação e a importação de conjuntos de dados de vários tamanhos, execução de trabalhos e composição de trabalhos sequencialmente e o exame dos resultados de seus trabalhos. Os conjuntos de dados usados podem ter tamanhos variados permitindo que você observe os efeitos que os conjuntos de dados de vários tamanhos têm sobre o desempenho do trabalho.

**Pré-requisitos**:

-   Você deve ter uma conta do Azure. Para obter as opções de como inscrever-se em uma conta, consulte a página [Testar o Azure gratuitamente (a página pode estar em inglês)][Testar o Azure gratuitamente (a página pode estar em inglês)].

-   Você deve ter provisionado um cluster HDInsight. Para obter instruções sobre as várias maneiras pelas quais esses clusters podem ser criados, consulte [Provisionar clusters HDInsight][Provisionar clusters HDInsight].

-   Você deve ter instalado o PowerShell do Azure e o configurado para uso com sua conta. Para obter instruções sobre como fazer isso, consulte [Instalar e configurar o PowerShell do Azure][Instalar e configurar o PowerShell do Azure].

## Os exemplos

O HDInsight é fornecido com os exemplos a seguir.

-   [**O exemplo de estimador de Pi**][**O exemplo de estimador de Pi**] Este tutorial mostra como executar um programa MapReduce com o HDInsight que usa um método estatístico (quasi-Monte Carlo) para estimar o valor de Pi.
-   [**O exemplo WordCount**][**O exemplo WordCount**] Este tutorial mostra como usar um cluster HDInsight para executar um programa MapReduce que conta todas as ocorrências de palavras em um arquivo de texto.
-   [**O exemplo de Graysort de 10 GB**][**O exemplo de Graysort de 10 GB**] Este tutorial mostra como executar um GraySort de finalidade geral em um arquivo de 10 GB usando o HDInsight. Há três trabalhos a serem executados: Teragen para gerar os dados, Terasort para classificar os dados e Teravalidate para confirmar que os dados foram classificados corretamente.
-   [**O exemplo de Streaming do C#**][**O exemplo de Streaming do C#**] Este tutorial mostra como usar o C# para escrever um programa MapReduce que usa a interface de streaming do Hadoop.

## Como executar os exemplos

Os exemplos podem ser executados usando o PowerShell do Azure. São fornecidas instruções sobre como fazer isso para cada um dos exemplos nas páginas vinculadas acima.

## Próximas etapas

Neste artigo e nos artigos sobre cada um dos exemplos, você aprendeu a executar os exemplos incluídos com os clusters de HDInsight usando o PowerShell do Azure. Para obter tutoriais sobre como usar Pig, Hive e MapReduce com o HDInsight, consulte os seguintes tópicos:

-   [Introdução ao Serviço Azure HDInsight][Introdução ao Serviço Azure HDInsight]
-   [Use o Pig com o HDInsight][Use o Pig com o HDInsight]
-   [Use o hive com o HDInsight][Use o hive com o HDInsight]
-   [Enviar trabalhos Hadoop de forma programática][Enviar trabalhos Hadoop programaticamente]
-   [Documentação do SDK do Azure HDInsight][Documentação do SDK do Azure HDInsight]
-   [Depurar o HDInsight: Mensagens de erro][Depurar o HDInsight: Mensagens de erro]

  [Enviar trabalhos Hadoop programaticamente]: ../hdinsight-submit-hadoop-jobs-programmatically/
  [Introdução ao Azure HDInsight]: ../hdinsight-hadoop-introduction/
  [Testar o Azure gratuitamente (a página pode estar em inglês)]: http://azure.microsoft.com/pt-br/pricing/free-trial/
  [Provisionar clusters HDInsight]: ../hdinsight-provision-clusters/
  [Instalar e configurar o PowerShell do Azure]: ../install-configure-powershell/
  [**O exemplo de estimador de Pi**]: ../hdinsight-sample-pi-estimator/
  [**O exemplo WordCount**]: ../hdinsight-sample-wordcount/
  [**O exemplo de Graysort de 10 GB**]: ../hdinsight-sample-10gb-graysort/
  [**O exemplo de Streaming do C#**]: ../hdinsight-sample-csharp-streaming/
  [Introdução ao Serviço Azure HDInsight]: ../hdinsight-get-started/
  [Use o Pig com o HDInsight]: ../hdinsight-use-pig/
  [Use o hive com o HDInsight]: ../hdinsight-use-hive/
  [Documentação do SDK do Azure HDInsight]: http://msdnstage.redmond.corp.microsoft.com/pt-br/library/dn479185.aspx
  [Depurar o HDInsight: Mensagens de erro]: ../hdinsight-debug-jobs/
