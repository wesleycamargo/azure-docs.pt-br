<properties
	pageTitle="Analisando dados do sensor usando Hive e Hadoop | Microsoft Azure"
	description="Saiba como analisar dados do sensor usando o Console de consulta Hive com HDInsight (Hadoop) e depois visualizar os dados no Microsoft Excel usando o PowerView."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/06/2015" 
	ms.author="larryfr"/>

#Analisar dados de sensor usando o Console de consulta Hive no Hadoop HDInsight

Saiba como analisar dados do sensor usando o Console de consulta Hive com HDInsight (Hadoop) e depois visualizar os dados no Microsoft Excel usando o Power View.

> [AZURE.NOTE]As etapas neste documento só funcionam com clusters HDInsight baseados no Windows.

Nesta amostra, você usará o Hive para processar dados históricos produzidos por sistemas de aquecimento, ventilação e ar condicionado (HVAC) para identificar sistemas que não conseguem manter uma temperatura determinada de maneira confiável. Você saberá como:

- Criar tabelas do HIVE para consultar dados armazenados em arquivos com valores separados por vírgulas (CSV).
- Criar consultas do HIVE para analisar os dados.
- Use o Microsoft Excel para conectar-se ao HDInsight (usando ODBC, conectividade de banco de dados aberta) para recuperar os dados analisados.
- Use o Power View para visualizar os dados.

![Um diagrama da arquitetura da solução](./media/hdinsight-hive-analyze-sensor-data/hvac-architecture.png)

##Pré-requisitos

* Um cluster do HDInsight (Hadoop): veja [Provisionar clusters Hadoop no HDInsight](hdinsight-provision-clusters.md) para obter informações sobre como criar um cluster.

* Microsoft Excel 2013

	> [AZURE.NOTE]O Microsoft Excel é usado para visualização de dados no [Power View](https://support.office.com/Article/Power-View-Explore-visualize-and-present-your-data-98268d31-97e2-42aa-a52b-a68cf460472e?ui=en-US&rs=en-US&ad=US).

* [Driver ODBC do Microsoft Hive](http://www.microsoft.com/download/details.aspx?id=40886)

##Para executar a amostra

1. No Portal do Azure, clique no cluster no qual deseja executar a amostra e depois clique em **Console de Consulta** na parte inferior. Como alternativa, você pode abrir diretamente o Console de Consulta usando a seguinte URL:

	 	https://<clustername>.azurehdinsight.net

	Quando solicitado, faça a autenticação usando o nome de usuário e senha do administrador usados ao provisionar esse cluster.

2. Na página da Web que é aberta, clique na guia **Galeria de introdução** e na categoria **Soluções com dados de amostra**, clique na amostra **Análise de dados do sensor**.

3. Siga as instruções fornecidas na página da Web para concluir a amostra.

<!---HONumber=July15_HO2-->