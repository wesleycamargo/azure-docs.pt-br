---
title: "Componentes e versões do Hadoop - Azure HDInsight | Microsoft Docs"
description: "Conheça os componentes e versões do Hadoop no HDInsight, e os níveis de serviço disponíveis na distribuição de nuvem do HortonWorks Data Platform."
keywords: "versões do hadoop, componentes do ecossistema do hadoop, componentes do hadoop, como verificar a versão do hadoop"
services: hdinsight
editor: cgronlun
manager: asadk
author: bprakash
tags: azure-portal
documentationcenter: 
ms.assetid: 367b3f4a-f7d3-4e59-abd0-5dc59576f1ff
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/14/2017
ms.author: bprakash
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: f6f50906766408226ad3ab9fdfd67b4707961b9b
ms.contentlocale: pt-br
ms.lasthandoff: 05/17/2017

 

---
# <a name="what-are-the-hadoop-components-and-versions-available-with-hdinsight"></a>Quais são os componentes e versões do Hadoop disponíveis com o HDInsight?

Saiba mais sobre as versões e componentes do ecossistema Hadoop no Azure HDInsight, bem como os níveis de serviço Standard e Premium. Além disso, saiba como verificar as versões dos componentes do Hadoop no HDInsight. 

Cada versão do HDInsight é uma distribuição de nuvem de uma versão do Hortonworks Data Platform (HDP).

## <a name="hadoop-components-available-with-different-hdinsight-versions"></a>Componentes do Hadoop disponíveis com diferentes versões do HDInsight
O HDInsight do Azure dá suporte a várias versões do cluster Hadoop que podem ser implantadas a qualquer momento. Cada opção de versão cria uma versão específica da distribuição de HDP (Hortonworks Data Platform) e um conjunto de componentes contidos nessa distribuição. A versão padrão do cluster usada pelo Azure HDInsight atualmente é a 3.5 e, a partir de 17 de fevereiro de 2017, baseada em HDP 2.5.

As versões do componente associadas às versões do cluster HDInsight são detalhadas na tabela a seguir: 

> [!NOTE]
> A versão padrão do serviço pode ser mudar sem aviso prévio. Se você tiver uma dependência de versão, recomendamos a especificação da versão ao criar clusters usando o .NET SDK/Azure PowerShell e a CLI do Azure. 
>
>


| Componente | HDInsight versão 3.6 | HDInsight versão 3.5 (padrão) | HDInsight versão 3.4 | HDInsight versão 3.3 | HDInsight versão 3.2 | HDInsight versão 3.1 | HDInsight versão 3,0 |
| --- | --- | --- | --- | --- | --- | --- |--- |
| Hortonworks Data Platform |2.6 |2.5 |2.4 |2.3 |2.2 |2.1.7 |2,0 |
| Apache Hadoop e YARN |2.7.3 |2.7.3 |2.7.1 |2.7.1 |2.6.0 |2.4.0 |2.2.0 |
| Apache Tez |0.7.0 |0.7.0 |0.7.0 |0.7.0 |0.5.2 |0.4.0 |-|
| Apache Pig |0.16.0 |0.16.0 |0.15.0 |0.15.0 |0.14.0 |0.12.1 |0.12.0 |
| Apache Hive e HCatalog |1.2.1 |1.2.1 |1.2.1 |1.2.1 |0.14.0 |0.13.1 |0.12.0 |
| Apache Hive2 | 2.1.0 |-|-|-|-|-|-|
| Apache Tez-Hive2 | 0.8.4 |-|-|-|-|-|-|
| Apache Ranger | 0.7.0 |0.6.0 |-|-|-|-|-|
| HBase no Apache |1.1.2 |1.1.2 |1.1.2 |1.1.1 |0.98.4 |0.98.0 |-|
| Apache Sqoop |1.4.6 |1.4.6 |1.4.6 |1.4.6 |1.4.5 |1.4.4 |1.4.4 |
| Apache Oozie |4.2.0 |4.2.0 |4.2.0 |4.2.0 |4.1.0 |4.0.0 |4.0.0 |
| O Apache Zookeeper |3.4.6 |3.4.6 |3.4.6 |3.4.6 |3.4.6 |3.4.5 |3.4.5 |
| Apache Storm |1.1.0 |1.0.1 |0.10.0 |0.10.0 |0.9.3 |0.9.1 |-|
| O Apache Mahout |0.9.0+ |0.9.0+ |0.9.0+ |0.9.0+ |0.9.0 |0.9.0 |-|
| Apache Phoenix |4.7.0 |4.7.0 |4.4.0 |4.4.0 |4.2.0 |4.0.0.2.1.7.0-2162 |-|
| Apache Spark |2.1.0 (somente Linux) |1.6.2 + 2.0 (somente Linux) |1.6.0 (somente Linux) |1.5.2 (somente Linux/Compilação experimental) |1.3.1 (somente Windows) |-|-|
| Apache Kafka | 0.10.0 | 0.10.0 | 0.9.0 |-|-|-|-|
| Apache Ambari | 2.5.0 | 2.4.0 | 2.2.1 | 2.1.0 |-|-|-|
| Apache Zeppelin | 0.7.0 |-|-|-|-|-|-|
| Mono |4.2.1 |4.2.1 |3.2.8 |-|-|-|


## <a name="how-to-check-current-hadoop-component-version-information"></a>Como verificar informações atuais de versão do componente do Hadoop

As versões de componente do ecossistema Hadoop associadas às versões do cluster HDInsight podem ser alteradas com atualizações ao HDInsight. Para verificar os componentes do Hadoop e verificar quais versões estão sendo usadas para um cluster é usar a API REST do Ambari. O comando **GetComponentInformation** recupera informações sobre componentes de serviço. Para obter detalhes, consulte a [Documentação do Ambari][ambari-docs].

Somente para clusters baseados em Windows: outra maneira de obter versões de componente é fazer logon em um cluster usando uma área de trabalho remota e examinar diretamente o conteúdo do diretório "C:\apps\dist\".

> [!IMPORTANT]
> O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para obter mais informações, veja [Substituição do HDInsight 3.3](#hdi-version-33-nearing-deprecation-date). 

### <a name="release-notes"></a>Notas de versão

Consulte [Notas de versão do HDInsight](hdinsight-release-notes.md) para ver notas de versão adicionais sobre as versões mais recentes do HDInsight.

## <a name="supported-hdinsight-versions"></a>Versões do HDInsight com suporte
A tabela a seguir lista as versões do HDInsight disponíveis no momento, as versões correspondentes do Hortonworks Data Platform que elas usam e suas datas de lançamento. Quando conhecidas, suas datas de vencimento e reprovação também são fornecidas. Observe as seguintes informações de versão:

* Clusters altamente disponíveis com dois headnodes são implantados por padrão para clusters HDInsight 2.1 e superiores. Eles não estão disponíveis para clusters HDInsight 1.6.
* Depois que o suporte expirar para uma determinada versão, ele poderá não estar disponível pelo portal do Azure. A tabela a seguir indica quais versões estão disponíveis no Portal Clássico do Azure. As versões do cluster continuarão disponíveis usando o parâmetro `Version` no comando do Windows PowerShell [New-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619331.aspx) e o SDK .NET até sua data de substituição.

| Versão do HDInsight | Versão do HDP | SO da VM | Alta disponibilidade | Data do lançamento | Disponível no Portal do Azure | Data de validade do suporte | Data de reprovação |
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDI 3.6 |HDP 2.6 |Ubuntu 16 |Sim |04/06/2017 |Sim | | |
| HDI 3.5 |HDP 2.5 |Ubuntu 16 |Sim |9/30/2016 |Sim |07/05/2017 |05/31/2018 |
| HDI 3.4 |HDP 2.4 |Ubuntu 14.0.4 LTS |Sim |29/03/2016 |Sim |12/29/2016 |1/9/2018 |
| HDI 3.3 |HDP 2.3 |Ubuntu 14.0.4 2012R2 LTS ou Windows Server |Sim |02/12/2015 |Sim |06/27/2016 |07/31/2017 |
| HDI 3.2 |HDP 2.2 |Ubuntu 12.04 LTS ou Windows Server 2012R2 |Sim |18/02/2015 |Não |3/1/2016 |04/01/2017 |
| HDI 3,1 |HDP 2,1 |Windows Server 2012R2 |Sim |24/06/2014 |Não |05/18/2015 |06/30/2016 |
| HDI 3,0 |HDP 2,0 |Windows Server 2012R2 |Sim |11/02/2014 |Não |17/09/2014 |30/06/2015 |
| HDI 2,1 |HDP 1,3 |Windows Server 2012R2 |Sim |28/10/2013 |Não |12/05/2014 |31/05/2015 |
| HDI 1.6 |HDP 1.1 | |Não |28/10/2013 |Não |26/04/2014 |31/05/2015 |

## <a name="hdi-version-33-nearing-deprecation-date"></a>O HDI versão 3.3 está se aproximando da data de substituição
O suporte para o cluster do HDI 3.3 expirou em 27/06/2016 e ele será preterido em 31/07/2017. Se você tiver um cluster do HDI 3.3, atualize seu cluster para o HDI 3.5 ou 3.6 em breve. Os cronogramas de substituição para HDI 3.3 Windows podem variar por região. Se a data de reprovação planejada da região for diferente, você receberá uma notificação separadamente.

### <a name="the-service-level-agreement-for-hdinsight-cluster-versions"></a>O contrato de nível de serviço para versões do cluster HDInsight
O SLA é definido em termos de uma **Janela de Suporte**. Uma janela de suporte refere-se ao período de tempo que uma versão do cluster HDInsight tem suporte pelo Atendimento e Suporte ao Cliente da Microsoft. Um cluster HDInsight ficará fora da janela de suporte se sua versão tiver uma **Data de Validade de Suporte** após a data atual. Uma lista das versões com suporte do cluster HDInsight pode ser encontrada na tabela acima. A data de validade de suporte de determinada versão do HDInsight (assim que uma versão X+1 mais nova estiver disponível) é calculada como a mais posterior de:  

* Fórmula 1: adicionar 180 dias à data em que o cluster HDInsight versão X foi lançado.
* Fórmula 2: adicionar 90 dias à data em que o cluster HDInsight versão X+1 (a versão subsequente após X) foi disponibilizado no Portal.

A **Data de Reprovação** é a data após a qual a versão do cluster não pode ser criada no HDInsight. A partir de 31 de julho de 2017, você não poderá redimensionar um cluster após a data de substituição. 

> [!NOTE]
> Os clusters HDInsight baseados no Windows (incluindo a versão 2.1, 3.0, 3.1, 3.2 e 3.3) são executados na Família 4 de SOs Convidados do Azure, que usa a versão de 64 bits do Windows Server 2012 R2 e dá suporte ao .NET Framework 4.0, 4.5, 4.5.1 e 4.5.2.
>
>

## <a name="hortonworks-release-notes-associated-with-hdinsight-versions"></a>Notas de versão do Hortonworks associadas a versões do HDInsight
* O cluster do HDInsight versão 3.6 usa uma distribuição do Hadoop baseada no [Hortonworks Data Platform 2.6](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.0/bk_release-notes/content/ch_relnotes.html). 
* O cluster do HDInsight versão 3.5 usa uma distribuição do Hadoop baseada no [Hortonworks Data Platform 2.5](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.5.0/bk_release-notes/content/ch_relnotes_v250.html). Esse é o cluster Hadoop **padrão** criado ao usar o portal.
* O cluster HDInsight versão 3.4 usa uma distribuição do Hadoop baseada em [Hortonworks Data Platform 2.4](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.4.0/bk_HDP_RelNotes/content/ch_relnotes_v240.html). 
* O cluster HDInsight versão 3.3 usa uma distribuição do Hadoop baseada em [Hortonworks Data Platform 2.3](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_HDP_RelNotes/content/ch_relnotes_v230.html).

  * As notas de versão do Apache Storm estão disponíveis [aqui](https://storm.apache.org/2015/11/05/storm0100-released.html).
  * As notas de versão do Apache Hive estão disponíveis [aqui](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12332384&styleName=Text&projectId=12310843).
* O cluster do HDInsight versão 3.2 usa uma distribuição do Hadoop baseada no [Hortonworks Data Platform 2.2][hdp-2-2].  

  * Notas de versão para componentes específicos do Apache – [Hive 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310843&version=12326450), [Pig 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310730&version=12326954), [HBase 0.98.4](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310753&version=12326810), [Phoenix 4.2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12315120&version=12327581), [M/R 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310941&version=12327180), [HDFS 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310942&version=12327181), [YARN 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12313722&version=12327197), [Common](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310240&version=12327179), [Tez 0.5.2](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314426&version=12328742), [Ambari 2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12312020&version=12327486), [Storm 0.9.3](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314820&version=12327112), [Oozie 4.1.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12324960&projectId=12311620).
* O cluster do HDInsight versão 3.1 usa uma distribuição do Hadoop baseada no [Hortonworks Data Platform 2.1.7][hdp-2-1-7]. Clusters do HDInsight 3.1 criados antes de 07/11/2014 eram baseados no [Hortonworks Data Platform 2.1.1][hdp-2-1-1].
* O cluster do HDInsight versão 3.0 usa uma distribuição do Hadoop baseada no [Hortonworks Data Platform 2.0][hdp-2-0-8].
* O cluster do HDInsight versão 2.1 usa uma distribuição do Hadoop baseada no [Hortonworks Data Platform 1.3][hdp-1-3-0].
* O cluster do HDInsight versão 1.6 usa uma distribuição do Hadoop baseada no [Hortonworks Data Platform 1.1][hdp-1-1-0].

## <a name="hdinsight-standard-and-hdinsight-premium"></a>HDInsight Standard e HDInsight Premium

O Azure HDInsight oferece ofertas de nuvem de Big Data em duas categorias: **Standard** e **Premium**. A tabela abaixo da seção lista os recursos disponíveis **somente como parte da categoria Premium**. Recursos que não estão explicitamente indicados na tabela aqui estão disponíveis como parte da categoria Standard.

> [!NOTE]
> A oferta HDInsight Premium está em Preview e está disponível somente para clusters do Linux.
>
>

| Recurso do HDInsight Premium | Descrição |
| --- | --- |
| Clusters HDInsight ingressados no domínio |Ingresse clusters HDInsight em domínios do AAD (Azure Active Directory) para segurança de nível corporativo. Agora você pode configurar uma lista de funcionários de sua empresa que podem se autenticar através do Azure Active Directory para fazer logon no cluster HDInsight. O administrador corporativo também pode configurar o controle de acesso baseado em função de segurança do Hive utilizando o [Apache Ranger](http://hortonworks.com/apache/ranger/), restringindo assim o acesso a dados apenas tanto quanto necessário. Por fim, o administrador pode auditar o os dados acessados por funcionários e as alterações feitas às políticas de controle de acesso, atingindo assim um alto grau de controle de seus recursos corporativos. Para obter mais informações, consulte [Configurar clusters HDInsight ingressados em domínio](hdinsight-domain-joined-configure.md). |

### <a name="cluster-types-supported-for-hdinsight-premium"></a>Tipos de cluster com suporte para o HDInsight Premium
A tabela a seguir lista o tipo de cluster do HDInsight e a matriz de suporte Premium.

| Tipo de cluster | Standard | Premium (Visualização) |
| --- | --- | --- |
| O Hadoop |Sim |Sim (somente HDInsight 3.5 e 3.6) |
| Spark |Sim |Não |
| HBase |Sim |Não |
| Storm |Sim |Não |
| Servidor R  |Sim |Não |
| Hive Interativo (versão prévia) |Sim |Não |
| Kafka (Versão prévia)|Sim|Não| 

Essa tabela é atualizada conforme mais tipos de cluster forem incluídos no HDInsight Premium.

### <a name="features-not-supported-for-hdinsight-premium"></a>Recursos sem suporte para o HDInsight Premium

Atualmente, os recursos a seguir não têm suporte para clusters HDInsight Premium.

* **Não há suporte para o Azure Data Lake Store como armazenamento primário**. Você ainda pode usar o Azure Data Lake Store como armazenamento de complemento com clusters HDInsight Premium.

### <a name="pricing-and-sla"></a>Preço e SLA
Para obter informações sobre preços e SLA para o HDInsight Premium, consulte [preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).


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

