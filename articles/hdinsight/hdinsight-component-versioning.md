<properties
	pageTitle="Quais são os diferentes componentes disponíveis em um cluster HDInsight? | Microsoft Azure"
	description="O HDInsight dá suporte a várias versões e componentes de cluster Hadoop implantáveis. Consulte as versões de distribuição com suporte do Hadoop e do HortonWorks Data Platform (HDP)."
	services="hdinsight"
	editor="cgronlun"
	manager="paulettm"
	authors="mumian"
	tags="azure-portal"
	documentationCenter=""/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/29/2016"
	ms.author="jgao"/>


# Quais são os diferentes componentes do Hadoop disponíveis com o HDInsight?

Saiba mais sobre os diferentes níveis de serviço oferecidos pelo HDInsight, bem como as versões dos diferentes componentes do hadoop incluídos com o HDInsight.

## HDInsight Standard e HDInsight Premium

O Azure HDInsight fornece ofertas de nuvem de Big Data em duas categorias: **Standard** e **Premium**. A tabela abaixo da seção lista os recursos disponíveis **somente** como parte da categoria Premium. Recursos que não estão explicitamente indicados na tabela aqui estão disponíveis como parte da categoria Standard.

>[AZURE.NOTE] A oferta HDInsight Premium está em Preview e está disponível somente para clusters do Linux.

| Recurso do HDInsight Premium | Descrição |
|--------------|---------------|
| Microsoft R Server (Preview) | O Microsoft R Server é a plataforma de análise empresarial mais amplamente implantada para R escalonável. A linguagem R dá suporte a uma variedade de recursos de estatísticas de Big Data, modelagem preditiva e aprendizado de máquina. Como parte do HDInsight Premium, agora você pode criar um cluster HDInsight com R Server pronto para uso com grandes conjuntos de dados e modelos. Essa nova funcionalidade oferece aos estatísticos e cientistas de dados uma interface R familiar que pode ser dimensionada sob demanda por meio de HDInsight, sem a sobrecarga de configuração e manutenção de cluster. <br> <br>Para obter mais informações, consulte [Introdução ao R Server no HDInsight](hdinsight-hadoop-r-server-get-started.md).

### Tipos de cluster com suporte para Premium

A tabela a seguir lista o tipo de cluster do HDInsight e a matriz de suporte Premium.

| Tipo de cluster | Standard | Premium |
|--------------|---------------|--------------|
| O Hadoop | Sim | Sim |
| Spark | Sim | Sim |
| HBase | Sim | Não |
| Storm | Sim | Não |

Essa tabela será atualizada conforme mais tipos de cluster forem incluídos no HDInsight Premium.

### Preço e SLA

Para obter informações sobre preços e SLA para o HDInsight Premium, consulte [preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## Componentes do Hadoop disponíveis com diferentes versões do HDInsight

O HDInsight do Azure dá suporte a várias versões do cluster Hadoop que podem ser implantadas a qualquer momento. Cada opção de versão cria uma versão específica da distribuição de HDP (Hortonworks Data Platform) e um conjunto de componentes contidos nessa distribuição. As versões do componente associadas às versões do cluster HDInsight são detalhadas na tabela a seguir. Observe que a versão padrão do cluster usada pelo Azure HDInsight atualmente é a 3.2 e, a partir de 03/12/2015, baseada em HDP 2.2.


Componente|HDInsight versão 3.4 | HDInsight versão 3.3 | HDInsight versão 3.2 (padrão)|HDInsight versão 3.1 |HDInsight versão 3,0|
---|---|---|---|---|---
Hortonworks Data Platform|2\.4|2\.3|2\.2|2\.1.7|2,0|
Apache Hadoop e YARN|2\.7.1|2\.7.1|2\.6.0|2\.4.0|2\.2.0|
Apache Tez|0\.7.0|0\.7.0 | 0\.5.2|0\.4.0||
Apache Pig|0\.15.0|0\.15.0|0\.14.0|0\.12.1|0\.12.0|
Apache Hive e HCatalog|1\.2.1|1\.2.1|0\.14.0|0\.13.1|0\.12.0|
HBase no Apache |1\.1.2|1\.1.1|0\.98.4|0\.98.0||
Apache Sqoop|1\.4.6|1\.4.6|1\.4.5|1\.4.4|1\.4.4|1\.4.3
Apache Oozie|4\.2.0|4\.2.0|4\.1.0|4\.0.0|4\.0.0|
O Apache Zookeeper|3\.4.6|3\.4.6|3\.4.6|3\.4.5|3\.4.5|
Apache Storm|0\.10.0|0\.10.0|0\.9.3|0\.9.1||
O Apache Mahout|0\.9.0+|0\.9.0+|0\.9.0|0\.9.0||
Apache Phoenix|4\.4.0|4\.4.0|4\.2.0|4\.0.0.2.1.7.0-2162||
Apache Spark|1\.6.0 (somente Linux)|1\.5.2 (somente Linux/Compilação experimental)|1\.3.1 (somente Windows)|||


**Obter informações atuais sobre a versão do componente**

As versões de componente associadas às versões do cluster HDInsight podem ser alteradas em futuras atualizações no HDInsight. Uma maneira de determinar os componentes disponíveis e verificar quais versões estão sendo usadas para um cluster é usar a API REST do Ambari. O comando **GetComponentInformation** pode ser usado para recuperar informações sobre um componente de serviço. Para obter detalhes, consulte a [Documentação do Ambari][ambari-docs]. Outra maneira de obter essas informações é fazer logon em um cluster usando uma área de trabalho remota e examinar diretamente o conteúdo do diretório "C:\\apps\\dist".


**Notas de versão**

Consulte [Notas de versão do HDInsight](hdinsight-release-notes.md) para ver notas de versão adicionais sobre as versões mais recentes do HDInsight.


## Versões do HDInsight com suporte
A tabela a seguir lista as versões do HDInsight disponíveis no momento, as versões correspondentes do Hortonworks Data Platform que elas usam e suas datas de lançamento. Quando conhecidas, suas datas de vencimento e reprovação também são fornecidas. Observe o seguinte:

* Clusters altamente disponíveis com dois headnodes são implantados por padrão para clusters HDInsight 2.1 e superiores. Eles não estão disponíveis para clusters HDInsight 1.6.
* Depois que o suporte expirar para uma determinada versão, ele poderá não estar disponível pelo Portal do Azure. A tabela a seguir indica quais versões estão disponíveis no Portal Clássico do Azure. As versões do cluster continuarão disponíveis usando o parâmetro `Version` no comando do Windows PowerShell [New-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619331.aspx) e o SDK .NET até sua data de substituição.

Versão do HDInsight|Versão do HDP|Alta Disponibilidade|Data do lançamento|Disponível no Portal do Azure|Data de validade do suporte|Data de reprovação
---|---|---|---|---|---|---
HDI 3.4|HDP 2.4|Sim|29/03/2016|Sim||
HDI 3.3|HDP 2.3|Sim|02/12/2015|Sim||
HDI 3.2|HDP 2.2|Sim|18/02/2015|Sim||
HDI 3,1|HDP 2,1|Sim|24/06/2014|Sim||
HDI 3,0|HDP 2,0|Sim|11/02/2014|Sim|17/09/2014|30/06/2015
HDI 2,1|HDP 1,3|Sim|28/10/2013|Sim|12/05/2014|31/05/2015
HDI 1.6|HDP 1.1|Não|28/10/2013|Sim|26/04/2014|31/05/2015

**Implantação de clusters não padrão**

### O contrato de nível de serviço para versões do cluster HDInsight

O SLA é definido em termos de uma "Janela de Suporte". Uma janela de suporte refere-se ao período de tempo que uma versão do cluster HDInsight tem suporte pelo Atendimento e Suporte ao Cliente da Microsoft. Um cluster HDInsight ficará fora da janela de suporte se sua versão tiver uma **Data de Validade de Suporte** após a data atual. Uma lista das versões com suporte do cluster HDInsight pode ser encontrada na tabela acima. A data de validade de suporte de determinada versão do HDInsight (assim que uma versão X+1 mais nova estiver disponível) é calculada como a mais posterior de:

- Fórmula 1: adicionar 180 dias à data em que o cluster HDInsight versão X foi lançado.
- Fórmula 2: adicionar 90 dias à data em que o cluster HDInsight versão X+1 (a versão subsequente após X) foi disponibilizado no Portal.

A **Data de Reprovação** é a data após a qual a versão do cluster não pode ser criada no HDInsight.

> [AZURE.NOTE] Os clusters HDInsight 2.1 e 3.0 são executados no SO Convidado do Azure [Família 4](../cloud-services/cloud-services-guestos-update-matrix.md), que usa a versão de 64 bits do Windows Server 2012 R2 e dá suporte ao .NET Framework 4.0, 4.5. e 4.5.1.

## Notas de versão do Hortonworks associadas a versões do HDInsight##

* O cluster HDInsight versão 3.4 usa uma distribuição do Hadoop baseada em [Hortonworks Data Platform 2.4](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.4.0/bk_HDP_RelNotes/content/ch_relnotes_v240.html).

* O cluster HDInsight versão 3.3 usa uma distribuição do Hadoop baseada em [Hortonworks Data Platform 2.3](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_HDP_RelNotes/content/ch_relnotes_v230.html).
	* As notas de versão do Apache Storm estão disponíveis [aqui](https://storm.apache.org/2015/11/05/storm0100-released.html).
	* As notas de versão do Apache Hive estão disponíveis [aqui](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12332384&styleName=Text&projectId=12310843).

* O cluster HDInsight versão 3.2 usa uma distribuição do Hadoop baseada em [Hortonworks Data Platform 2.2][hdp-2-2]. Esse é o cluster Hadoop **padrão** criado ao usar o portal.

	* Notas de versão para componentes específicos do Apache - [Hive 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310843&version=12326450), [Pig 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310730&version=12326954), [HBase 0.98.4](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310753&version=12326810), [Phoenix 4.2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12315120&version=12327581), [M/R 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310941&version=12327180), [HDFS 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310942&version=12327181), [YARN 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12313722&version=12327197), [Common](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310240&version=12327179), [Tez 0.5.2](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314426&version=12328742), [Ambari 2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12312020&version=12327486), [Storm 0.9.3](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314820&version=12327112), [Oozie 4.1.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12324960&projectId=12311620).


* O cluster HDInsight versão 3.1 usa uma distribuição do Hadoop baseada em [Hortonworks Data Platform 2.1.7][hdp-2-1-7]. Clusters do HDInsight 3.1 criados antes de 07/11/2014 eram baseados em [Hortonworks Data Platform 2.1.1][hdp-2-1-1].

* O cluster HDInsight versão 3.0 usa uma distribuição do Hadoop baseada em [Hortonworks Data Platform 2.0][hdp-2-0-8].

* O cluster HDInsight versão 2.1 usa uma distribuição do Hadoop baseada em [Hortonworks Data Platform 1.3][hdp-1-3-0].

* O cluster HDInsight versão 1.6 usa uma distribuição do Hadoop baseada em [Hortonworks Data Platform 1.1][hdp-1-1-0].


[image-hdi-versioning-versionscreen]: ./media/hdinsight-component-versioning/hdi-versioning-version-screen.png

[wa-forums]: http://azure.microsoft.com/support/forums/

[connect-excel-with-hive-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md

[hdp-2-2]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.2.0/HDP_2.2.0_Release_Notes_20141202_version/index.html

[hdp-2-1-7]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html

[ambari-docs]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[zookeeper]: http://zookeeper.apache.org/

<!-----------HONumber=AcomDC_0330_2016-->