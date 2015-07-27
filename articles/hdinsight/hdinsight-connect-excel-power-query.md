<properties
	pageTitle="Conectar o Excel ao Hadoop com o Power Query | Microsoft Azure"
	description="Saiba como aproveitar os componentes de business intelligence e usar o Power Query para Excel para acessar dados armazenados no Hadoop no HDInsight."
	services="hdinsight"
	documentationCenter=""
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


#Conectar o Excel ao Hadoop usando Power Query

Um dos principais recursos da solução de big data da Microsoft é a integração dos componentes de BI (business intelligence) da Microsoft com os clusters Hadoop do HDInsight do Azure. Um dos principais exemplos dessa integração é a capacidade de conectar o Excel com a conta de Armazenamento do Azure que contém os dados associados ao seu cluster Hadoop usando o complemento Microsoft Power Query para Excel. Este artigo explica como configurar e usar o Power Query para consultar dados associados a um cluster Hadoop gerenciado com o HDInsight.

> [AZURE.NOTE]Embora as etapas deste artigo possam ser usadas com um cluster HDInsight com base no Linux ou Windows, é preciso ter o Windows para a estação de trabalho cliente.

## Pré-requisitos

Antes de começar este artigo, você deve ter o seguinte:

- **Um cluster HDInsight**. Para configurar um, consulte [Introdução ao Azure HDInsight][hdinsight-get-started].
- Um **estação de trabalho** que esteja executando Windows 7, Windows Server 2008 R2 ou um sistema operacional posterior.
- **Office 2013 Professional Plus, Office 365 ProPlus, Excel 2013 Standalone ou Office 2010 Professional Plus**.


## <a id="InstallPowerQuery"></a>Instalar o Microsoft Power Query para Excel

O Power Query pode ser usado para importar dados de uma variedade de origens para o Microsoft Excel, em que ele pode capacitar ferramentas de BI, como o PowerPivot e Power View. Em particular, O Power Query pode importar dados que foram retornados ou que foram gerados por um trabalho Hadoop em execução em um cluster HDInsight.

Baixe o Microsoft Power Query para Excel no [Centro de Download da Microsoft][powerquery-download] e instale-o.

## <a id="ImportData"></a>Importar dados do HDInsight para o Excel

O suplemento do Power Query para Excel facilita a importação de dados de seu cluster HDInsight para o Excel onde ferramentas de BI, como o PowerPivot e o Power Map, podem ser usadas para inspecionar, analisar e apresentar os dados.

**Para importar dados de um cluster HDInsight**

1. Abra o Excel.

2. Crie uma nova pasta de trabalho em branco.

3. Clique no menu **Power Query**, clique em **De Outras Fontes** e, em seguida, clique em **Do Azure HDInsight**.

	![HDI.PowerQuery.SelectHdiSource][image-hdi-powerquery-hdi-source]

	Observação: se você não vir o menu **Power Query**, vá para **Arquivo** > **Opções** > **Suplementos** e selecione **Suplementos COM** na caixa suspensa **Gerenciar** na parte inferior da página. Selecione o botão **Ir...** e verifique se a caixa do suplemento Power Query para Excel está marcada.

3. Para **Nome da Conta**, insira o nome da conta de armazenamento do Blob do Azure associada ao cluster e, em seguida, clique em **OK**.

4. Para **Chave da Conta**, insira a chave para a conta de armazenamento de Blob do Azure e clique em **Salvar**. (Você precisa fazer isso apenas na primeira vez que acessar esse repositório.)

5. No painel **Navegador** à esquerda do Editor de Consulta, clique duas vezes no nome do contêiner de armazenamento de Blob. Por padrão, o nome do contêiner é igual ao nome do cluster.

6. Localize **HiveSampleData.txt** na coluna **Nome** (o caminho da pasta é **../hive/warehouse/hivesampletable/**) e clique em **Binário** à esquerda de HiveSampleData.txt.

	![HDI.PowerQuery.ImportData][image-hdi-powerquery-importdata]

7. Se desejar, você pode renomear os nomes das colunas. Quando estiver pronto, clique em **Aplicar e Fechar**.

	![HDI.PowerQuery.ImportedTable][image-hdi-powerquery-imported-table]

## <a id="NextSteps"></a>Próximas etapas

Neste artigo, você aprendeu como usar o Power Query para recuperar dados do HDInsight para o Excel. Da mesma forma, você pode recuperar dados do HDInsight no banco de dados SQL do Azure. Também é possível carregar dados para o HDInsight. Para saber mais, consulte os seguintes artigos:

* [Conectar o Excel ao HDInsight com o driver ODBC do Microsoft Hive][hdinsight-ODBC]
* [Carregar dados no HDInsight][hdinsight-upload-data]

[hdinsight-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[image-hdi-powerquery-hdi-source]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.SelectHdiSource.png
[image-hdi-powerquery-importdata]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportData.png
[image-hdi-powerquery-imported-table]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportedTable.PNG

[powerquery-download]: http://go.microsoft.com/fwlink/?LinkID=286689
 

<!---HONumber=July15_HO3-->