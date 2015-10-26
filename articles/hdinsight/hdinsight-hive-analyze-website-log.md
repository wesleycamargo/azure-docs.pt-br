<properties 
	pageTitle="Usar o Hive com Hadoop para análise de log do Website | Microsoft Azure" 
	description="Saiba como usar o Hive com o HDInsight para analisar os logs do Website. Você usará um arquivo de log como entrada em uma tabela do HDInsight e usará o HiveQL para consultar os dados." 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"
	tags="azure-portal"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/07/2015" 
	ms.author="nitinme"/>

# Usar o Hive com HDInsight para analisar os logs de Websites

Saiba como usar o HiveQL com HDInsight para analisar os logs de um Website. A análise de log do Website pode ser usada para segmentar seu público com base em atividades semelhantes, categorizar os visitantes do site por demografia e descobrir o conteúdo que eles veem, seus Websites de origem e assim por diante.

Neste exemplo, você utilizará um cluster do HDInsight para analisar arquivos de log do Website para obter informações sobre a frequência de visitas ao Website por meio de Websites externos em um dia. Você também vai gerar um resumo dos erros de site que os usuários enfrentam. Você saberá como:

- Conectar-se a um armazenamento de Blob do Azure, que contém os arquivos de log do site.
- Criar tabelas do HIVE para consultar esses logs.
- Criar consultas do HIVE para analisar os dados.
- Usar o Microsoft Excel para conectar-se ao HDInsight (usando ODBC, conectividade de banco de dados aberta) para recuperar os dados analisados.

![HDI.Samples.Website.Log.Analysis][img-hdi-weblogs-sample]

##Pré-requisitos

- Você deve ter provisionado um cluster Hadoop no Azure HDInsight. Para obter instruções, veja [Provisionar clusters do HDInsight][hdinsight-provision]. 
- Você deve ter o Microsoft Excel 2013 ou Microsoft Excel 2010 instalado.
- Você deve ter o [Driver ODBC do Microsoft Hive](http://www.microsoft.com/download/details.aspx?id=40886) para importar dados do Hive no Excel.


##Para executar a amostra

1. Do [Portal de Visualização do Azure](https://ms.portal.azure.com/), no Quadro Inicial (caso você tenha fixado o cluster ali), clique no bloco do cluster no qual você deseja executar a amostra.

2. Na folha do cluster, em **Links Rápidos**, clique em **Painel do Cluster** e, na folha **Painel do Cluster**, clique em **Painel do Cluster HDInsight**. Como alternativa, você pode abrir diretamente o painel usando a seguinte URL:

	 	https://<clustername>.azurehdinsight.net
	
	Quando solicitado, faça a autenticação usando o nome de usuário e senha do administrador usados ao provisionar o cluster.
  
2. Na página da Web que é aberta, clique na guia **Galeria de introdução** e na categoria **Amostras**; clique na amostra **Análise de log do Website**.

3. Siga as instruções fornecidas na página da Web para concluir a amostra.

##Próximas etapas
Experimente o exemplo a seguir: [Analisando dados do sensor usando o Hive com HDInsight](hdinsight-hive-analyze-sensor-data.md).


[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-sensor-data-sample]: ../hdinsight-use-hive-sensor-data-analysis.md

[img-hdi-weblogs-sample]: ./media/hdinsight-hive-analyze-website-log/hdinsight-weblogs-sample.png
 

<!---HONumber=Oct15_HO3-->