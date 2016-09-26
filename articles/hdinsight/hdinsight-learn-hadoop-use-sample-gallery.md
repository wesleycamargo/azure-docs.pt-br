<properties
   pageTitle="Saiba mais sobre o Hadoop no HDInsight usando a Galeria de Exemplos | Microsoft Azure"
   description="Aprenda Hadoop rapidamente executando exemplos de aplicativos na galeria de introdução do HDInsight. Use dados de exemplo ou forneça seus próprios dados."
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.workload="big-data"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="07/25/2016"
   ms.author="jgao"/>

# Saiba mais sobre o Hadoop usando a Galeria de Introdução do HDInsight do Azure

A Galeria de Introdução só está disponível para clusters HDInsight baseados em Windows. A galeria fornece uma maneira fácil e rápida de aprender a trabalhar com o Hadoop, executando aplicativos de exemplo no HDInsight. Alguns dos exemplos vêm com dados de exemplo. Você pode fornecer seus próprios dados para o restante dos exemplos. Atualmente, há os seguintes exemplos seis exemplos (com mais a caminho):

- Soluções com os dados do Azure
	- Análise de log de site do Microsoft Azure
	- Análise de armazenamento do Microsoft Azure
- Soluções com dados de exemplo
	- Análise de dados de sensor
	- Análise de tendência do Twitter
	- Análise de log do Website
	- Recomendação de filme do Mahout

![Soluções de Hadoop do HDInsight, Storm e Galeria de Introdução ao HBase, incluindo dados de exemplo.][hdinsight.sample.gallery]

O vídeo a seguir mostra como executar o exemplo de análise de tendência do Twitter:

<center><a href="https://www.youtube.com/embed/7ePbHot1SN4">https://www.youtube.com/embed/7ePbHot1SN4></a></center>

Você pode acessar o Painel navegando até http://<YourHDInsightClusterName>.azurehdinsight.net/ ou no Portal do Azure.

**Para executar um exemplo da Galeria de Introdução**

1. Entre no [Portal do Azure][azure.portal].
2. Clique em **Procurar** no menu à esquerda, clique em **Clusters HDInsight** e clique no nome do cluster.
3. Clique em **Painel** no menu superior.
4. Insira o nome de usuário e a senha para o usuário HTTP (também chamado de usuário de cluster).
6. Clique em **Galeria de Introdução** no topo da página.
7. Clique em um dos exemplos. Cada exemplo fornece etapas detalhadas para executá-lo. A imagem a seguir mostra o exemplo de análise de tendência do Twitter:

	![Exemplo de análise de tendência de Twitter do HDInsight][hdinsight.twitter.sample]

## Próximas etapas
Outras maneiras de saber mais sobre o HDInsight incluem:

- [Mapa de aprendizado do HDInsight][hdinsight.learn.map]
- [Infográfico do HDInsight][hdinsight.infographic]

<!--Image references-->
[hdinsight.sample.gallery]: ./media/hdinsight-learn-hadoop-use-sample-gallery/HDInsight-Getting-Started-Gallery.png
[hdinsight.twitter.sample]: ./media/hdinsight-learn-hadoop-use-sample-gallery/HDInsight-Twitter-Trend-Analysis-sample.png

<!--Link references-->
[hdinsight.learn.map]: https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/
[hdinsight.infographic]: http://go.microsoft.com/fwlink/?linkid=523960
[azure.portal]: https://portal.azure.com

<!---HONumber=AcomDC_0914_2016-->