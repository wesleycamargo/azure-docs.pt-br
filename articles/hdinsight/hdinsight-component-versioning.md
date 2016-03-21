<properties
	pageTitle="O que há de novo nas versões de cluster Hadoop do HDInsight? | Microsoft Azure"
	description="O HDInsight dá suporte a várias versões de cluster Hadoop implantáveis. Consulte as versões de distribuição com suporte do Hadoop e do HortonWorks Data Platform (HDP)."
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
	ms.date="02/29/2016"
	ms.author="jgao"/>


#O que há de novo nas versões de cluster Hadoop fornecidas pelo HDInsight?

##Versões do HDInsight e componentes do Hadoop
O HDInsight do Azure dá suporte a várias versões do cluster Hadoop que podem ser implantadas a qualquer momento. Cada opção de versão cria uma versão específica da distribuição de HDP (Hortonworks Data Platform) e um conjunto de componentes contidos nessa distribuição. As versões do componente associadas às versões do cluster HDInsight são detalhadas na tabela a seguir. Observe que a versão padrão do cluster usada pelo Azure HDInsight atualmente é a 3.2 e, a partir de 03/12/2015, baseada em HDP 2.2.


Componente|HDInsight versão 3.3 | HDInsight versão 3.2 (padrão)|HDInsight versão 3.1 |HDInsight versão 3,0|
---|---|---|---|---
Hortonworks Data Platform|2\.3|2\.2|2\.1.7|2,0|
Apache Hadoop e YARN|2\.7.1|2\.6.0|2\.4.0|2\.2.0|
Apache Tez|0\.7.0 | 0\.5.2|0\.4.0||
Apache Pig|0\.15.0|0\.14.0|0\.12.1|0\.12.0|
Apache Hive e HCatalog|1\.2.1|0\.14.0|0\.13.1|0\.12.0|
HBase no Apache |1\.1.1|0\.98.4|0\.98.0||
Apache Sqoop|1\.4.6|1\.4.5|1\.4.4|1\.4.4|1\.4.3
Apache Oozie|4\.2.0|4\.1.0|4\.0.0|4\.0.0|
O Apache Zookeeper|3\.4.6|3\.4.6|3\.4.5|3\.4.5|
Apache Storm|0\.10.0|0\.9.3|0\.9.1||
O Apache Mahout|0\.9.0+|0\.9.0|0\.9.0||
Apache Phoenix|4\.4.0|4\.2.0|4\.0.0.2.1.7.0-2162||
Apache Spark|1\.5.2 (somente Linux/Compilação experimental)|1\.3.1 (somente Windows)|||


**Obter informações atuais sobre a versão do componente**

As versões de componente associadas às versões do cluster HDInsight podem ser alteradas em futuras atualizações no HDInsight. Uma maneira de determinar os componentes disponíveis e verificar quais versões estão sendo usadas para um cluster é usar a API REST do Ambari. O comando **GetComponentInformation** pode ser usado para recuperar informações sobre um componente de serviço. Para obter detalhes, consulte a [Documentação do Ambari][ambari-docs]. Outra maneira de obter essas informações é fazer logon em um cluster usando uma área de trabalho remota e examinar diretamente o conteúdo do diretório "C:\\apps\\dist".


**Notas de versão**

Consulte [Notas de versão do HDInsight](hdinsight-release-notes.md) para ver notas de versão adicionais sobre as versões mais recentes do HDInsight.

### Selecionar uma versão ao criar um cluster HDInsight

Ao criar um cluster por meio dos cmdlets do Windows PowerShell do HDInsight ou do SDK do .NET do HDInsight, você poderá escolher a versão para o cluster Hadoop HDInsight usando o menu suspenso **Versão do HDInsight** da folha **Configuração Opcional** no Portal do Azure.

##Destaques do recurso
Alguns dos principais recursos da plataforma HDInsight incluem:

- **Spark** - o Apache Spark é uma estrutura de processamento paralelo de código-fonte aberto que dá suporte ao processamento de memória para melhorar o desempenho dos aplicativos de análise de big data. Recursos de computação na memória do Spark fazem dele uma boa escolha para algoritmos iterativos em cálculos de aprendizado e gráfico de máquina.

	O Spark também podem ser usado para executar o processamento de dados baseados em disco convencional. O Spark melhora a estrutura MapReduce tradicional, evitando gravações em disco nos estágios intermediários. Além disso, o Spark é compatível com o armazenamento de Blob do Azure e com HDFS (Sistema de Arquivos Distribuído Hadoop) para que os dados existentes possam ser processados facilmente por meio do Spark.

	O Spark também pode ser adicionado usando a Ação de Script. A Ação de Script adiciona o Spark 1.2.0 ao cluster do HDInsight 3.2 ou o Spark 1.0.2 ao cluster do HDInsight 3.1. Para obter mais informações, confira [Instalar e usar o Spark em clusters Hadoop HDInsight](hdinsight-hadoop-spark-install.md).


- **Storm** - O Storm no Azure HDInsight agora está disponível ao público geral, proporcionando uma maneira rápida e fácil de implantar análise em tempo real em apenas alguns cliques e dentro de minutos. O Apache Storm no HDInsight do Azure é um projeto de código-fonte aberto no ecossistema do Apache Hadoop que fornece acesso a uma plataforma de análise capaz de processar milhões de eventos de maneira confiável. Agora os usuários de Hadoop podem obter insights sobre como os eventos ocorrem, além de insights de eventos passados. A Microsoft também fornece integração interna com o Visual Studio, facilitando a interação do desenvolvedor com o Storm. Agora você pode desenvolver, implantar e depurar topologias Storm de dentro do Visual Studio.

- **HDInsight no Linux** - O HDInsight do Azure fornece a opção de criar clusters do Hadoop que sejam executados em máquinas virtuais (VMs) Linux (Ubuntu). Você pode usar essa opção se estiver familiarizado com o Linux ou Unix, estiver migrando de uma solução Hadoop baseada em Linux existente ou desejar fácil integração com componentes do ecossistema do Hadoop criados para o Linux. Você pode criar um cluster HDInsight no Linux por meio de um computador cliente executando o Windows ou Linux usando o Portal do Azure, a CLI do Azure ou o SDK do .NET do HDInsight (somente Windows).

- **Tamanhos de VM adicionais** - clusters do HDInsight agora estão disponíveis em mais tipos e tamanhos de VM. Os clusters do HDInsight agora podem utilizar tamanhos de A2 a A7 criados para fins gerais; os nós da Série D têm SSDs (unidades de estado sólido) e processadores 60% mais rápidos; e tamanhos A8 e A9 com suporte para InfiniBand para rede rápida. Clientes Apache HBase no Azure HDInsight podem se beneficiar das configurações de memória maiores da série de D para aumentar o desempenho. Clientes do Storm Apache no Azure HDInsight também podem se beneficiar de memória adicional para carregar conjuntos de dados de referência maiores, bem como CPUs mais rápidas para taxa de transferência mais alta.

- **Dimensionamento do cluster** - Dimensionamento de cluster permite alterar o número de nós de um cluster HDInsight em execução sem precisar excluí-lo ou recriá-lo. Atualmente, somente Hadoop Query e Apache Storm têm essa capacidade, mas o Apache HBase logo deverá ter também.

- **Ação do Script** - Esse recurso de personalização de cluster habilita a modificação dos clusters Hadoop de maneiras arbitrárias usando scripts personalizados. Com esse novo recurso, os usuários podem experimentar e implantar projetos disponíveis no ecossistema do Apache Hadoop nos clusters do Azure HDInsight. O recurso de personalização está disponível em todos os tipos de clusters do HDInsight, incluindo Hadoop, HBase e Storm.

- **HBase** - O HBase é um banco de dados NoSQL de baixa latência que permite o processamento transacional online de Big data. Ele é oferecido como um cluster gerenciado integrado no ambiente do Azure. Os clusters são configurados para armazenar dados diretamente no armazenamento de blobs do Azure, que proporciona baixa latência e maior flexibilidade para escolhas relacionadas a desempenho/custos. Isso permite que os clientes criem sites interativos que trabalham com grandes conjuntos de dados, de modo a criar serviços que armazenam dados de sensor e telemetria de milhões de pontos de extremidade e analisar esses dados por meio de trabalhos do Hadoop.

- **Apache Phoenix** -Apache Phoenix é uma camada de consulta SQL (Structured Query Language) em HBase. Ele dá suporte a um subconjunto limitado da especificação de linguagem de consulta SQL, incluindo suporte a índices secundários. É fornecido como um driver JDBC (Java Database Connectivity) integrado ao cliente que é destinado a consultas de baixa latência sobre dados do HBase. Apache Phoenix usa a consulta SQL, compila em uma série de chamadas de verificações e coprocessadores HBase e produz regulares conjuntos de resultados do JDBC. O Apache Phoenix é uma camada de banco de dados relacional em HBase. É fornecido como um driver JDBC integrado ao cliente que é destinado a consultas de baixa latência sobre dados do HBase. O Apache Phoenix usa a sua consulta SQL, compila-a em uma série de verificações do HBase e orquestra a execução dessas verificações para produzir conjuntos resultados JDBC regulares.

- **Cluster Dashboard** - um novo aplicativo Web que é implantado no seu cluster HDInsight. Use-o para executar consultas do Hive, verificar logs de trabalho e procurar armazenamento de Blob do Azure. A URL usada para acessar o aplicativo Web é <*NomeCluster*>.azurehdinsight.net.

- **Biblioteca Microsoft Avro** - Essa biblioteca implementa o sistema de serialização de dados Apache Avro para o ambiente Microsoft .NET. O Apache Avro fornece um formato de intercâmbio de dados binários compactos para serialização. Ele usa a JSON (JavaScript Object Notation) para definir um esquema independente de linguagem que garanta a interoperabilidade de linguagem. Os dados serializados em uma linguagem podem ser lidos em outra diferente. Atualmente, há suporte para C, C++, C#, Java, PHP, Python e Ruby. O formato de serialização Apache Avro é amplamente usado no Azure HDInsight para representar estruturas de dados complexas em um trabalho MapReduce do Hadoop.

- **YARN** - uma nova estrutura distribuída de gerenciamento de aplicativos para uso geral, que substituiu a estrutura clássica do Apache Hadoop MapReduce para processamento de dados nos clusters do Hadoop. Ela efetivamente serve como o sistema operacional do Hadoop e leva o Hadoop de uma plataforma de dados de acesso único para processamento em lotes para uma plataforma multiuso que permite processamento em lotes, interativo, online e de fluxo. Essa nova estrutura de gerenciamento melhora a escalabilidade e a utilização do cluster de acordo com critérios, como garantias de capacidade, integridade e SLAs (contratos de nível de serviço).

- **Tez (HDInsight 3.1 e superiores apenas)** - Uma estrutura personalizável e de uso geral que cria tarefas simplificadas de processamento de dados em cargas de trabalho de pequena e grande escala no Hadoop. Ela oferece a capacidade de executar um DAG (gráfico acíclico direcionado) complexo de tarefas para um único trabalho, de modo que os projetos no ecossistema do Apache Hadoop, como Apache Hive e Apache Pig, possam atender aos requisitos de tempos de resposta interativa humana e extrema produtividade em uma escala de petabytes. Observe que o Hive 0.13 permite que consultas do Hive sejam executadas parte superior do Tez, em vez de no MapReduce.

- **Alta disponibilidade** - Um segundo nó de cabeçalho foi adicionado aos clusters Hadoop implantados pelo HDInsight para aumentar a disponibilidade do serviço. Implementações padrão de clusters Hadoop normalmente têm um único nó principal. O HDInsight remove esse ponto único de falha com a adição de um nó de cabeçalho secundário. A mudança para uma nova configuração de cluster de alta disponibilidade não altera o preço do cluster, a menos que os clientes criarem clusters com um nó de cabeçalho extragrande, em vez do nó de tamanho grande padrão.

- **Desempenho do Hive** - melhorias na ordem de magnitude dos tempos de resposta de consulta do Hive (até 40x) e da compactação de dados (até 80%) usando o formato ORC **(Optimized Row Columnar)**.

- **Pig, Sqoop, Oozie, Ambari** - Atualizações da versão do componente para o cluster HDInsight versão 3.0 (HDP 2.0/Hadoop 2.2) que fornece paridade com o cluster HDInsight versão 2.1 (HDP 1.3/Hadoop 1.2). Consulte a tabela de versão abaixo para obter informações específicas.

- **Mahout** - essa biblioteca de algoritmos de aprendizado de máquina escalonáveis está pré-instalada nos clusters Hadoop HDInsight 3.1 (e superiores). Desse modo, você pode executar trabalhos do Mahout sem a necessidade de alguma configuração de cluster adicional.

- **Suporte a rede virtual** - Os clusters do HDInsight podem ser usados com a Rede Virtual do Azure para dar suporte ao isolamento de recursos de nuvem ou a cenários híbridos que vinculam os recursos de nuvem àqueles em seu datacenter.


## Versões com suporte
A tabela a seguir lista as versões do HDInsight disponíveis no momento, as versões correspondentes do Hortonworks Data Platform que elas usam e suas datas de lançamento. Quando conhecidas, suas datas de vencimento e reprovação também são fornecidas. Observe o seguinte:

* Clusters altamente disponíveis com dois headnodes são implantados por padrão para clusters HDInsight 2.1 e superiores. Eles não estão disponíveis para clusters HDInsight 1.6.
* Depois que o suporte expirar para uma determinada versão, ele poderá não estar disponível pelo Portal do Azure. A tabela a seguir indica quais versões estão disponíveis no Portal Clássico do Azure. As versões do cluster continuarão disponíveis usando o parâmetro `Version` no comando do Windows PowerShell [New-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619331.aspx) e o SDK .NET até sua data de substituição.

Versão do HDInsight|Versão do HDP|Alta Disponibilidade|Data do lançamento|Disponível no Portal do Azure|Data de validade do suporte|Data de reprovação
---|---|---|---|---|---|---
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

<!---HONumber=AcomDC_0309_2016-->