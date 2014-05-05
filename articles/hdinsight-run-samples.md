<properties linkid="manage-services-hdinsight-howto-run-samples" urlDisplayName="Executar exemplos do HDInsight" pageTitle="Executar exemplos do HDInsight | Azure" metaKeywords="" description="Introdução ao uso do serviço Azure HDInsight Azure com os exemplos fornecidos. Use os scripts do PowerShell que executam programas MapReduce em clusters de dados." metaCanonical="" services="hdinsight" documentationCenter="" title="Executar exemplos do HDInsight" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />




#Executar os exemplos do HDInsight

Um conjunto de exemplos é fornecido para ajudar você a se familiarizar com o Azure HDInsight. Esses exemplos são disponibilizados em cada um dos clusters HDInsight que você cria. Com a execução desses exemplos, você se familiarizará com os cmdlets do PowerShell do Azure HDInsight.

Os programas MapReduce também podem ser executados de forma programática usando a API do Microsoft .NET para HDInsight. Para obter mais informações sobre como usar as APIs do HDInsight para envio de trabalhos, consulte [Enviar trabalhos Hadoop programaticamente][submit-jobs-programmatically].

Existe muita documentação adicional na web para tecnologias relacionadas ao Hadoop, como a programação e o streaming de MapReduce com base em Java, bem como documentação sobre o uso de cmdlets usados em scripts do PowerShell. Para obter mais informações sobre esses recursos, consulte a seção final **Recursos para o HDInsight** do tópico [Introdução ao Azure HDInsight][hdinsight-resources].

**O que são esses exemplos**

<p>Esses exemplos têm o objetivo de agilizar sua experiência de como implantar trabalhos do Hadoop e de fornecer uma mesa de testes extensível para trabalhar com os conceitos e procedimentos de scripts usados pelo serviço. Eles fornecem exemplos de tarefas comuns, como a criação e a importação de conjuntos de dados de vários tamanhos, execução de trabalhos e composição de trabalhos sequencialmente e o exame dos resultados de seus trabalhos. Os conjuntos de dados usados podem ter tamanhos variados permitindo que você observe os efeitos que os conjuntos de dados de vários tamanhos têm sobre o desempenho do trabalho.</p>


**Pré-requisitos**:	

- Você deve ter uma conta do Azure. Para obter as opções de como inscrever-se em uma conta, consulte a página [Testar o Azure gratuitamente (a página pode estar em inglês)](http://www.windowsazure.com/pt-br/pricing/free-trial/).

- Você deve ter provisionado um cluster HDInsight. Para obter instruções sobre as várias maneiras como esses clusters podem ser criados, consulte [Provisão de clusters HDInsight](/pt-br/gerenciar/services/hdinsight/provisão-hdinsight-clusters /)

- Você deve ter instalado o PowerShell do Azure e tê-lo configurado para uso com sua conta. Para obter instruções sobre como fazer isso, consulte [Instalar e configurar o PowerShell do Azure][powershell-install-configure].

## Os exemplos ##

O HDInsight é fornecido com os exemplos a seguir.

- [**O exemplo de estimador de Pi**][pi-estimator] Este tutorial mostra como executar um programa MapReduce com o HDInsight que usa um método estatístico (quasi-Monte Carlo) para estimar o valor de Pi.
- [**O exemplo WordCount**][wordcount] Este tutorial mostra como usar um cluster HDInsight para executar um programa MapReduce que conta todas as ocorrências de palavras em um arquivo de texto.
- [**O exemplo de Graysort de 10 GB**][10gb-graysort] Este tutorial mostra como executar um GraySort de finalidade geral em um arquivo de 10 GB usando o HDInsight. Existem três trabalhos a serem executados: Teragen para gerar os dados, Terasort para classificar os dados e Teravalidate para confirmar que os dados foram classificados corretamente.
- [**O exemplo de Streaming do C#**][cs-streaming] Este tutorial mostra como usar o C# para escrever um programa MapReduce que usa a interface de streaming do Hadoop. 


## Como executar os exemplos ##

Os exemplos podem ser executados usando o PowerShell do Azure. São fornecidas instruções sobre como fazer isso para cada um dos exemplos nas páginas vinculadas acima.

##Próximas etapas ##

Neste artigo e nos artigos sobre cada um dos exemplos, você aprendeu a executar os exemplos incluídos com os clusters de HDInsight usando o PowerShell do Azure. Para obter tutoriais sobre como usar Pig, Hive e MapReduce com o HDInsight, consulte os seguintes tópicos:

* [Introdução ao Serviço Azure HDInsight][getting-started]
* [Use o Pig com o HDInsight][pig]
* [Use o hive com o HDInsight][hive]
* [Enviar trabalhos Hadoop de forma programática][submit-jobs-programmatically]
* [Documentação do SDK do Azure HDInsight][hdinsight-sdk-documentation]
* [Depuração do HDInsight: Mensagens de erro][hdinsight-debug-error-messages]

[Powershell-install-configure]: /pt-br/documentation/articles/install-configure-powershell/

[hdinsight-debug-error-messages]: /pt-br/manage/services/hdinsight/debug-error-messages/
[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/pt-br/library/dn479185.aspx
[getting-started]: /pt-br/manage/services/hdinsight/get-started-hdinsight/

[hive]: /pt-br/manage/services/hdinsight/using-hive-with-hdinsight/
[pig]: /pt-br/manage/services/hdinsight/using-pig-with-hdinsight/
[pi-estimator]: /pt-br/manage/services/hdinsight/howto-run-samples/sample-pi-estimator/
[10gb-graysort]: /pt-br/manage/services/hdinsight/howto-run-samples/sample-10gb-graysort/
[wordcount]: /pt-br/manage/services/hdinsight/howto-run-samples/sample-wordcount/
[cs-streaming]: /pt-br/manage/services/hdinsight/howto-run-samples/sample-csharp-streaming/

[submit-jobs-programmatically]: /pt-br/manage/services/hdinsight/submit-hadoop-jobs-programmatically/
[hdinsight-resources]: /pt-br/manage/services/hdinsight/introduction-hdinsight/


