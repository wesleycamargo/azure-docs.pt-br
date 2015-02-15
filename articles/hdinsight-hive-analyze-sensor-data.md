<properties 
	pageTitle="Analisando dados do sensor usando o Hive e o Microsoft Azure HDInsight (Hadoop)" 
	description="Saiba como usar o Hive e o Excel para analisar e visualizar dados do sensor com o HDInsight (Hadoop)" 
	services="hdinsight" 
	documentationCenter="" 
	authors="blackmist" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/30/2014" 
	ms.author="larryfr"/>

#Analisando dados do sensor usando o Hive com HDInsight

Saiba como analisar dados do sensor usando o Hive com HDInsight (Hadoop) e depois visualize os dados no Microsoft Excel usando o Power View.

Nesta amostra, você usará o Hive para processar dados históricos produzidos por sistemas de aquecimento, ventilação e ar condicionado (HVAC) para identificar sistemas que não conseguem manter uma temperatura determinada de maneira confiável. Você saberá como:

- Criar tabelas do HIVE para consultar dados armazenados em arquivos com valores separados por vírgulas (CSV)
- Criar consultas do HIVE para analisar os dados
- Usar o Microsoft Excel para se conectar ao HDInsight (usando uma conexão ODBC) para recuperar os dados analisados
- Usar o Power View para visualizar os dados

![A diagram of the solution architecture](./media/hdinsight-use-hive-sensor-data-analysis/hvac-architecture.png)

##Pré-requisitos:

* Um cluster do HDInsight (Hadoop) - veja [Provisionar clusters do Hadoop no HDInsight](/pt-br/documentation/articles/hdinsight-provision-clusters/) para obter informações sobre como criar um cluster

* Microsoft Excel 2013

	> [AZURE.NOTE] [WACOM.NOTE] O Microsoft Excel é usado para visualizar dados com o [Power View](https://support.office.com/en-US/Article/Power-View-Explore-visualize-and-present-your-data-98268d31-97e2-42aa-a52b-a68cf460472e?ui=en-US&rs=en-US&ad=US), que atualmente só está disponível no Windows.

* [Driver ODBC do Microsoft Hive](http://www.microsoft.com/pt-br/download/details.aspx?id=40886)

##Para executar a amostra

1. No Portal de Gerenciamento do Azure, clique no cluster no qual deseja executar a amostra e depois clique em **Console de Consulta** na parte inferior. Como alternativa, você pode abrir diretamente o Console de Consulta usando a seguinte URL

	 	https://<clustername>.azurehdinsight.net

	Quando solicitado, faça a autenticação usando o nome de usuário e senha do administrador usados ao provisionar esse cluster.

2. Na página da Web que é aberta, clique na guia **Galeria de introdução** e na categoria **Amostras**, então clique na amostra **Análise de log do Website**.

3. Siga as instruções fornecidas na página da Web para concluir a amostra.

<!--HONumber=42-->
