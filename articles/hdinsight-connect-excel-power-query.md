<properties linkid="manage-services-hdinsight-connect-excel-with-power-query" urlDisplayName="HDInsight and Excel" pageTitle="Connect Excel to Hadoop with Power Query | Azure" metaKeywords="hdinsight, excel, data explorer, hive excel, hdinsight excel, power query" description="Learn how to take advantage of business intelligence components and use Excel to access data stored in Azure HDInsight using Power Query." metaCanonical="" services="hdinsight" documentationCenter="" title="Connect Excel to Hadoop with Power Query" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bradsev"></tags>

# Conectar o Excel ao Hadoop com o Power Query

Um dos principais recursos da solução de big data da Microsoft é a integração dos componentes do Microsoft Business Intelligence (BI) com os clusters Hadoop do HDInsight. Um dos principais exemplos dessa integração é a capacidade de conectar o Excel com a conta de Armazenamento do Azure que contém os dados associados ao seu cluster Hadoop usando o Microsoft Power Query para Excel. Este artigo explica como configurar e usar o Power Query no Excel para consultar dados associados a um cluster Hadoop gerenciado com o HDInsight.

**Pré-requisitos**

Antes de começar este artigo, você deve ter o seguinte:

-   Um cluster HDInsight. Para configurar um, consulte [Introdução ao Azure HDInsight][].
-   Um computador que esteja executando o Windows 8, Windows 7, Windows Server 2012 ou Windows Server 2008 R2.
-   Office 2013 Professional Plus, Office 365 Pro Plus, Excel 2013 Standalone ou Office 2010 Professional Plus.

## Neste artigo

-   [Instalar o Microsoft Power Query para Excel][]
-   [Importar dados no Excel][]
-   [Próximas etapas][]

## <span id="InstallPowerQuery"></span></a>Instalar o Microsoft Power Query para Excel

O Power Query pode ser usado para importar dados de uma variedade de origens para o Microsoft Excel, onde ele pode capacitar ferramentas de BI (Business Intelligence), como o PowerPivot e Power View. Em particular, O Power Query pode importar dados que foram retornados ou que foram gerados por um trabalho Hadoop em execução em um cluster HDInsight.

Baixe o Microsoft Power Query para Excel no [Centro de Download da Microsoft][] e instale-o.

## <span id="ImportData"></span></a>Importar dados do HDInsight para o Excel

O suplemento do Power Query para Excel facilita a importação de dados de seu cluster HDInsight para o Excel onde ferramentas de business intelligence, como o PowerPivot e o Power Map, podem ser usadas para inspecionar, analisar e apresentar os dados.

**Para importar dados de um cluster HDInsight**

1.  Abra o Excel.

2.  Crie uma nova pasta de trabalho em branco.

3.  Clique no menu **Power Query**, clique em **De Outras Fontes** e, em seguida, clique em **Do Azure HDInsight**.

    ![HDI.PowerQuery.SelectHdiSource][]

    Observação: Se você o menu do **Power Query** não aparecer, vá para **Arquivo** \> **Opções** \> **Suplementos** e selecione **Suplementos COM** na caixa suspensa **Gerenciador** na parte inferior da página. Selecione o botão **Ir...** e verifique se a caixa do suplemento Microsoft Office Power Query para Excel está marcada.

4.  Digite o **Nome da Conta** de armazenamento de Blob do Azure associada ao cluster e clique em **OK**.

5.  Digite a **Chave da Conta** da de armazenamento de Blob do Azure e clique em **Salvar**. (Você precisa fazer isso apenas na primeira vez que acessar esse repositório.)

6.  No painel **Navegador** à esquerda do **Editor de Consulta**, clique duas vezes no nome do contêiner de armazenamento de Blob. Por padrão, o nome do contêiner é igual ao nome do cluster.

7.  Localize **HiveSampleData.txt** na coluna **Nome** (o caminho da pasta é **../hive/warehouse/hivesampletable/**) e clique em **Binário** à esquerda de HiveSampleData.txt.

    ![HDI.PowerQuery.ImportData][]

8.  Se desejar, você pode renomear os nomes das colunas. Quando estiver pronto, clique em **Aplicar e Fechar**.

    ![HDI.PowerQuery.ImportedTable][]

## <span id="NextSteps"></span></a>Próximas etapas

Neste artigo, você aprendeu como usar o Power Query para recuperar dados do HDInsight para o Excel. Da mesma forma, você pode recuperar dados do HDInsight no SQL Azure. Também é possível carregar dados para o HDInsight. Para saber mais, consulte os seguintes artigos:

-   [Conectar o Excel ao HDInsight com o driver ODBC do Microsoft Hive][]
-   [Carregar dados no HDInsight][].

  [Introdução ao Azure HDInsight]: ../hdinsight-get-started/
  [Instalar o Microsoft Power Query para Excel]: #InstallPowerQuery
  [Importar dados no Excel]: #ImportData
  [Próximas etapas]: #NextSteps
  [Centro de Download da Microsoft]: http://go.microsoft.com/fwlink/?LinkID=286689
  [HDI.PowerQuery.SelectHdiSource]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.SelectHdiSource.png
  [HDI.PowerQuery.ImportData]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportData.png
  [HDI.PowerQuery.ImportedTable]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportedTable.PNG
  [Conectar o Excel ao HDInsight com o driver ODBC do Microsoft Hive]: ../hdinsight-connect-excel-hive-ODBC-driver/
  [Carregar dados no HDInsight]: ../hdinsight-upload-data/
