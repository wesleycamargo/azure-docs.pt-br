<properties
	pageTitle="Explorar dados em tabelas do Hive com consultas do Hive | Microsoft Azure."
	description="Explorar dados em tabelas do Hive usando consultas do Hive."
	services="machine-learning"
	documentationCenter=""
	authors="bradsev"
	manager="paulettm" 
	editor="cgronlun"  />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/08/2016"
	ms.author="hangzh;bradsev" />

# Explorar dados em tabelas do Hive com consultas do Hive. 

## Introdução

Este documento fornece scripts de Hive de exemplo usados para explorar os dados em tabelas do Hive.

O **menu** abaixo leva a tópicos que descrevem como usar ferramentas para explorar dados de vários ambientes de armazenamento. Esta tarefa é uma etapa no Processo de Análise da Cortana (CAP).

[AZURE.INCLUDE [cap-explore-data-selector](../../includes/cap-explore-data-selector.md)]

## Pré-requisitos
Este artigo supõe que você:

* Criou uma conta de armazenamento do Azure. Se precisar de instruções, consulte [Criar uma conta de Armazenamento do Azure](../hdinsight-get-started.md#storage)
* Provisionou um cluster do Hadoop personalizado com o serviço HDInsight. Se precisar de instruções, consulte [Personalizar os clusters do Hadoop do Azure HDInsight para análise avançada](machine-learning-data-science-customize-hadoop-cluster.md).
* Os dados foram carregados para tabelas Hive em clusters do Hadoop do Azure HDInsight. Se não, siga as instruções de [Criar e carregar dados nas tabelas Hive](machine-learning-data-science-move-hive-tables.md) para carregar dados nas tabelas Hive primeiro.
* Habilitou o acesso remoto ao cluster. Se precisar de instruções, consulte [Acessar o nó principal do Cluster do Hadoop](machine-learning-data-science-customize-hadoop-cluster.md#headnode).
* Se você precisar de instruções sobre como enviar consultas do Hive, consulte [Como enviar consultas do Hive](machine-learning-data-science-move-hive-tables.md#submit)

## Exemplo de scripts de consulta do Hive para exploração de dados

1. Obter a contagem de observações por partição `SELECT <partitionfieldname>, count(*) from <databasename>.<tablename> group by <partitionfieldname>;`

2. Obter a contagem de observações por dia `SELECT to_date(<date_columnname>), count(*) from <databasename>.<tablename> group by to_date(<date_columnname>);`

3. Obter os níveis em uma coluna categórica `SELECT  distinct <column_name> from <databasename>.<tablename>`

4. Obter o número de níveis na combinação de duas colunas categóricas `SELECT <column_a>, <column_b>, count(*) from <databasename>.<tablename> group by <column_a>, <column_b>`

5. Obter a distribuição de colunas numéricas `SELECT <column_name>, count(*) from <databasename>.<tablename> group by <column_name>`

6. Extrair registros de associação de duas tabelas

	    SELECT
			a.<common_columnname1> as <new_name1>,
			a.<common_columnname2> as <new_name2>,
    		a.<a_column_name1> as <new_name3>,
    		a.<a_column_name2> as <new_name4>,
    		b.<b_column_name1> as <new_name5>,
    		b.<b_column_name2> as <new_name6>
    	FROM
    		(
    		SELECT <common_columnname1>,
    			<common_columnname2>,
				<a_column_name1>,
				<a_column_name2>,
			FROM <databasename>.<tablename1>
			) a
			join
			(
			SELECT <common_columnname1>,
    			<common_columnname2>,
				<b_column_name1>,
				<b_column_name2>,
			FROM <databasename>.<tablename2>
			) b
			ON a.<common_columnname1>=b.<common_columnname1> and a.<common_columnname2>=b.<common_columnname2>

## Scripts de consulta adicionais para cenários de dados de viagem de táxi

Exemplos de consultas que são específicas para cenários de [Dados de Viagens de Táxi em NYC](http://chriswhong.com/open-data/foil_nyc_taxi/) também são fornecidos no [repositório Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Essas consultas já tem o esquema de dados especificado e estão prontas para ser enviadas para execução.

 

<!---HONumber=AcomDC_0211_2016-->