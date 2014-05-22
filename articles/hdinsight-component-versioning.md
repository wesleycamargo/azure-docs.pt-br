<properties linkid="manage-services-hdinsight-version" urlDisplayName="Versão do Hadoop do HDInsight" pageTitle="O que há de novo nas versões de cluster fornecidas pelo HDInsight? | Azure" metaKeywords="hdinsight, hadoop, hadoop do hdinsight, hadoop do azure" description="O HDInsight oferece suporte a várias versões de cluster Hadoop implantáveis a qualquer momento. Consulte as versões de distribuição com suporte do Hadoop e do HortonWorks Data Platform (HDP)." services="HDInsight" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" title="O que há de novo nas versões de cluster fornecidas pelo HDInsight?" authors="bradsev" />


#O que há de novo nas versões de cluster fornecidas pelo HDInsight?

O Azure HDInsight agora oferece suporte ao Hadoop 2.2 com a versão 3.0 do cluster HDInsight e tira total proveito dessa plataforma para fornecer uma variedade de benefícios significativos aos clientes. Esses incluem, principalmente:

- Hive: melhorias na ordem de magnitude dos tempos de resposta de consulta do Hive (até 40x) e da compactação de dados (até 80%) usando o formato Optimized Row Columnar (ORC).

- YARN: uma nova estrutura distribuída de gerenciamento de aplicativos para uso geral, que substituiu a estrutura clássica do Apache Hadoop MapReduce para processamento de dados nos clusters do Hadoop. Ela efetivamente serve como o sistema operacional do Hadoop e leva o Hadoop de uma plataforma de dados de acesso único para processamento em lotes para uma plataforma multiuso que permite processamento em lotes, interativo, online e de fluxo. Essa nova estrutura de gerenciamento melhora a escalabilidade e a utilização do cluster de acordo com critérios, como garantias de capacidade, integridade e contratos de nível de serviço.

- Pig, Sqoop, Qozie, Ambari: atualizações de versões de componentes para o cluster HDInsight versão 3.0 (HDP 2.0) que fornece paridade com o cluster HDInsight versão 2.1 (HDP 1.3). Consulte as tabelas de versão abaixo para obter informações específicas. Observe que o Hbase, o Mahout e o Flume não estão incluídos.

**Implantação**	
A criação dos clusters HDInsight 3.0 no Hadoop 2.2 tem suporte do Portal do Azure, do SDK do HDInsight e do PowerShell do Azure.

**Disponibilidade global**		
Com a versão do Azure HDInsight no Hadoop 2.2, a Microsoft disponibilizou o HDInsight em todas as principais regiões geográficas do Azure com a exceção da Grande China. Especificamente, os data centers da Europa Ocidental e do Sudeste Asiático foram colocados online. Isso permite que os clientes localizem clusters em um data center próximo e possivelmente em uma zona com requisitos de conformidade semelhantes. 

**Alterações de última hora**	
Apenas a sintaxe "wasb://" tem suporte em clusters HDInsight 3.0. A sintaxe antiga "asv://" tem suporte no HDInsight 2.1 e nos clusters 1.6, mas não tem suporte nos clusters HDInsight 3.0 e não terá suporte em versões posteriores. Isso significa que qualquer trabalho enviado para um cluster HDInsight 3.0 que usa explicitamente a sintaxe â€œasv://â€_ irá falhar. Em vez disso, a sintaxe wasb:// deve ser usada. Além disso, trabalhos enviados para qualquer cluster HDInsight 3.0 que foram criados com um metastore existente que contém referências explícitas a recursos usando a sintaxe asv:// irá falhar. Esses metastores precisarão ser recriados usando o wasb:// para endereçar recursos.

##Versões do HDInsight
O HDInsight oferece suporte a várias versões do cluster Hadoop que podem ser implantadas a qualquer momento. Cada opção de versão provisiona uma versão específica da distribuição de HortonWorks Data Platform (HDP) e um conjunto de componentes que são contidos nessa distribuição.

###Versão 3.0 do cluster

O Azure HDInsight agora oferece suporte ao Hadoop 2.2. Ele se baseia no Hortonworks Data Platform versão 2.0 e fornece serviços do Hadoop com as versões do componente detalhadas na tabela a seguir:

<table border="1">
<tr><th>Componente</th><th>Versão</th></tr>
<tr><td>Apache Hadoop</td><td>2.2.0</td></tr>
<tr><td>Apache Hive</td><td>0.12.0</td></tr>
<tr><td>Apache Pig</td><td>0.12</td></tr>
<tr><td>Apache Sqoop</td><td>1.4.4</td></tr>
<tr><td>Apache Oozie</td><td>4.0.0</td></tr>
<tr><td>Apache HCatalog</td><td>Mesclado com o Hive</td></tr>
<tr><td>Apache Templeton</td><td>Mesclado com o Hive</td></tr>
<tr><td>Ambari</td><td>API v1.0</td></tr>
</table>

###Versão 2.1 do cluster

A versão padrão do cluster usada pelo [Azure HDInsight (a página pode estar em inglês)](http://go.microsoft.com/fwlink/?LinkID=285601) é a 2.1. Ele se baseia no Hortonworks Data Platform versão 1.3.0 e fornece serviços do Hadoop com as versões do componente detalhadas na tabela a seguir:

<table border="1">
<tr><th>Componente</th><th>Versão</th></tr>
<tr><td>Apache Hadoop</td><td>1.2.0</td></tr>
<tr><td>Apache Hive</td><td>0.11.0</td></tr>
<tr><td>Apache Pig</td><td>0.11</td></tr>
<tr><td>Apache Sqoop</td><td>1.4.3</td></tr>
<tr><td>Apache Oozie</td><td>3.2.2</td></tr>
<tr><td>Apache HCatalog</td><td>Mesclado com o Hive</td></tr>
<tr><td>Apache Templeton</td><td>Mesclado com o Hive</td></tr>
<tr><td>Ambari</td><td>API v1.0</td></tr>
</table>


###Versão 1.6 do cluster

A versão 1.6 do cluster [Azure HDInsight (a página pode estar em inglês)](http://go.microsoft.com/fwlink/?LinkID=285601) também está disponível. Ela se baseia no Hortonworks Data Platform versão 1.1.0 e fornece serviços do Hadoop com as versões de componente detalhadas na tabela a seguir:

<table border="1">
<tr><th>Componente</th><th>Versão</th></tr>
<tr><td>Apache Hadoop</td><td>1.0.3</td></tr>
<tr><td>Apache Hive</td><td>0.9.0</td></tr>
<tr><td>Apache Pig</td><td>0.9.3</td></tr>
<tr><td>Apache Sqoop</td><td>1.4.2</td></tr>
<tr><td>Apache Oozie</td><td>3.2.0</td></tr>
<tr><td>Apache HCatalog</td><td>0.4.1</td></tr>
<tr><td>Apache Templeton</td><td>0.1.4</td></tr>
<tr><td>Driver do SQL Server JDBC</td><td>3.0</td></tr>
</table>


### Selecione uma versão ao provisionar um cluster HDInsight

Ao criar um cluster por meio dos Cmdlets do PowerShell do HDInsight ou do SDK do .NET do HDInsight, você pode escolher a versão para o cluster Hadoop HDInsight usando o parâmetro "Version".

Se você usar a opção **Criação Rápida**, por padrão, você irá obter a versão 2.1 do cluster Hadoop HDInsight. Se usar a opção **Criação Personalizada** do Portal do Azure, poderá escolher a versão do cluster que você implantará da lista suspensa **Versão do HDInsight** na página **Detalhes do Cluster**. A versão 3.0 do cluster Hadoop HDInsight só está disponível como uma opção no assistente de **Criação Personalizada**.

![HDI.Versioning.VersionScreen][image-hdi-versioning-versionscreen]


## Versões com suporte
A tabela a seguir lista as versões do HDInsight disponíveis no momento, as versões correspondentes do Hortonworks Data Platform (HDP) que elas usam e suas datas de lançamento. Quando conhecida, suas datas de reprovação também serão fornecidas.

<table border="1">
<tr><th>Versão do HDInsight</th><th>versão do HDP</a></th><th>Data de lançamento</th></tr>
<tr><td>HDI 3.0</td><td>HDP 2.0</td><td>11/02/2014</td></tr>
<tr><td>HDI 2.1</td><td>HDP 1.3</td><td>28/10/2013</td></tr>
<tr><td>HDI 1.6</td><td>HDP 1.1</td><td>28/10/2013</td></tr>
</table><br/>


### O SLA (Contrato de Nível de Serviço) para versões do cluster HDInsight 
O SLA é definido em termos de uma "Janela de Suporte". Uma janela de suporte refere-se ao período de tempo que uma versão do cluster HDInsight é suportada pelo Atendimento ao Cliente da Microsoft.  Um cluster HDInsight estará fora da janela de suporte se sua versão tiver uma data de expiração de suporte após a data atual.  Uma lista das versões com suporte do cluster HDInsight pode ser encontrada na tabela acima.  A data de expiração de suporte de uma determinada versão do HDInsight (denotada como versão X) é calculada como a mais posterior de:  

- Fórmula 1: adicionar 180 dias à data em que o cluster HDInsight versão X foi lançado.
- Fórmula 2: adicionar 90 dias à data em que o cluster HDInsight versão X+1 (a versão subsequente após X) foi disponibilizado no Portal de Gerenciamento do Azure.

> [WACOM.NOTE] O HDInsight 2.1 e o 3.0 usam a versão de 64 bits do Windows 2008 R2 SP1 com o .NET Framework 4.0. 

**Observações adicionais e informações sobre controle de versão**	

* O Driver JDBC do SQL Server é usado internamente pelo HDInsight e não é usado para operações externas. Se você desejar se conectar ao HDInsight usando o ODBC, use o driver ODBC do Microsoft Hive. Para obter mais informações sobre como usar o ODBC do Hive, consulte [Conectar o Excel ao HDInsight com o driver ODBC do Microsoft Hive][connect-excel-with-hive-ODBC].

* O cluster HDInsight versão 3.0 usa uma distribuição do Hadoop que é baseada no [Hortonworks Data Platform 2.0][hdp-2-0-8].

* O cluster HDInsight versão 2.1 usa uma distribuição do Hadoop que é baseada no [Hortonworks Data Platform 1.3][hdp-1-3-0]. Esse é o cluster do Hadoop padrão criado durante o uso do portal Azure HDInsight.

* O cluster HDInsight versão 1.6 usa uma distribuição do Hadoop que é baseada no [Hortonworks Data Platform 1.1][hdp-1-1-0]. 

* As versões de componente associadas com as versões do cluster HDInsight podem mudar em futuras atualizações do HDInsight. Uma maneira de determinar os componentes disponíveis e de verificar quais versões estão sendo usadas para um cluster para usar a API REST do Ambari. O comando GetComponentInformation pode ser usado para recuperar informações sobre um componente de serviço. Para obter detalhes, consulte a [Documentação do Ambari][ambari-docs]. Outra maneira de obter essas informações é efetuar logon em um cluster usando uma área de trabalho remota e examinar o conteúdo do diretório "C:\apps\dist\" diretamente.

[image-hdi-versioning-versionscreen]: ./media/hdinsight-component-versioning/hdi-versioning-version-screen.png

[wa-forums]: http://www.windowsazure.com/pt-br/support/forums/

[connect-excel-with-hive-ODBC]: /pt-br/documentation/articles/hdinsight-connect-excel-hive-ODBC-driver

[hdp-2-0-8]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html

[ambari-docs]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

