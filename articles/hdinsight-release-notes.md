<properties title="HDInsight Release Notes" pageTitle="HDInsight Release Notes | Azure" description="HDInsight release notes." metaKeywords="hdinsight, hadoop, hdinsight hadoop, hadoop azure, release notes" services="HDInsight" solutions="" documentationCenter="" editor="cgronlun" manager="paulettm"  authors="bradsev" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bradsev" />

# Notas de versão do Microsoft HDInsight

## Notas para versão de 21/08/2014

-   Estamos adicionando a nova configuração WebHCat a seguir (HIVE-7155) que define o limite de memória padrão para um trabalho de controlador Templeton como 1 GB (o valor padrão anterior era 512 MB):

    -   templeton.mapper.memory.mb (=1024)
    -   Essa mudança trata do erro a seguir, que determinadas consultas de Hive encontraram devido a limites mais baixos de memória: “O contêiner está sendo executado além dos limites de memória física”.
    -   Para voltar aos padrões antigos, você pode definir o valor dessa configuração para 512 por meio do SDK do PowerShell no momento da criação do cluster, usando o comando a seguir:

        Add-AzureHDInsightConfigValues -Core @{"templeton.mapper.memory.mb"="512";}

-   O nome de host da função zookeeper foi alterado para zookeeper. Isso afeta o modo como o nome é resolvido no cluster, mas não afeta as APIs REST externas. Se você possui componentes que utilizam nome de host de nó zookeeper, você precisará atualizá-los com o novo nome, em vez disso. Os novos nomes para os três nós zookeeper são:

    -   zookeeper0
    -   zookeeper1
    -   zookeeper2
-   A matriz de suporte de versão do HBase é atualizada. Somente a versão HDInsight 3.1 (HBase versão 0.98) é suportada para cargas de trabalho de produção no HBase. A versão 3.0, que estava disponível para visualização, não será suportada se você prosseguir. Durante o período de transição, os clientes ainda podem criar clusters da versão 3.0.

## Notas para a versão de 28/07/2014

-   **O HDInsight está disponível em novas regiões**: Com essa versão, expandimos a presença geográfica do HDInsight para três novas regiões. Os clientes que usam o HDInsight agora podem criar clusters nessas regiões.

    -   Ásia Oriental,
    -   Centro-Norte dos EUA e
    -   Centro-Sul dos EUA
-   Com essa versão, o HDInsight v1.6 (HDP1.1, Hadoop 1.0.3) e o HDInsight v2.1 (HDP1.3, Hadoop 1.2) estão sendo removidos do Portal de Gerenciamento do Azure. Você pode continuar a criar clusters Hadoop para essas versões usando cmdlets do HDInsight PowerShell ([New-AzureHDInsightCluster][New-AzureHDInsightCluster]) ou usando o [SDK do HDInsight][SDK do HDInsight]. Consulte a página [Controle de versão do componente HDInsight][Controle de versão do componente HDInsight] para obter mais informações.
-   Alterações ao Hortonworks Data Platform (HDP) nesta versão:

<table>
<tr><th> HDP               </th><th> Alterações                                                   </th></tr>
<tr><td> HDP 1.3 / HDI 2.1 </td><td> Sem alterações                                               </td></tr>
<tr><td> HDP 2.0 / HDI 3.0 </td><td> Sem alterações                                               </td></tr>
<tr><td> HDP 2.1 / HDI 3.1 </td><td> zookeeper: ['3.4.5.2.1.3.0-1948'] -\> ['3.4.5.2.1.3.2-0002'] </td></tr>
</table>

## Notas para a versão de 24/06/2014

Essa versão contém diversos novos aprimoramentos para o serviço HDInsight:

-   **Disponibilidade do HDP 2.1**: O HDInsight 3.1, que contém o HDP 2.1, agora está disponível de modo geral e é a versão padrão para novos clusters.
-   **HBase – Melhorias no Portal de Gerenciamento do Azure**: Estamos tornando os clusters HBase disponíveis na visualização. Agora você pode criar clusters HBase por meio do portal com três cliques.

![][0]

Com o HBase, você pode criar uma variedade de cargas de trabalho em tempo real no HDInsight, por meio de sites interativos que funcionem com conjuntos de dados grandes para dados para o sensor de armazenamento de serviços e dados de telemetria de milhões de pontos de extremidade. A próxima etapa seria analisar os dados nessas cargas de trabalho com trabalhos do Hadoop e isso é possível de imediato no HDInsight, por meio das experiências fornecidas como o painel de cluster do Hive e do PowerShell.

### Agora, o Apache Mahout é pré-instalado no HDInsight 3.1

O [Mahout][Mahout] é pré-instalado nos clusters do Hadoop do HDInsight 3.1. Desse modo, você pode executar trabalhos do Mahout sem a necessidade de alguma configuração de cluster adicional. Por exemplo, você pode acessar remotamente um cluster do Hadoop usando o Protocolo de Área de trabalho remota (RDP) e, sem etapas adicionais, executar o comando Hello world do Mahout:

        mahout org.apache.mahout.classifier.df.tools.Describe -p /user/hdp/glass.data -f /user/hdp/glass.info -d I 9 N L  

        mahout org.apache.mahout.classifier.df.BreimanExample -d /user/hdp/glass.data -ds /user/hdp/glass.info -i 10 -t 100

Para obter uma explicação mais completa sobre este procedimento, consulte a documentação do [Exemplo Breiman][Exemplo Breiman] no site do Apache Mahout.

### Consultas de Hive podem usar o Tez no HDinsight 3.1

O Hive 0.13 agora está disponível no HDInsight 3.1 e é capaz de realizar consultas usando o Tez, o que pode ser aproveitado para melhorias significativas de desempenho.
O Tez não está habilitado por padrão para consultas do Hive. Para utilizá-lo, você precisa optar por isso. Você pode habilitar o Tez executando o trecho de código a seguir:

        set hive.execution.engine=tez;
        select sc_status, count(*), histogram_numeric(sc_bytes,5) from website_logs_orc_local group by sc_status;

A Hortonworks publicou uma demonstração detalhada das melhorias nas consultas do Hive com o Tez, conforme fornecido em medições de desempenho padrão. Para obter mais informações, consulte [Medindo o desempenho do Hive 13 Apache para Hadoop Enterprise][Medindo o desempenho do Hive 13 Apache para Hadoop Enterprise].

Para obter mais detalhes sobre como usar o Hive com o Tez, confira a [página wiki Hive em Tez][página wiki Hive em Tez].

### Disponibilidade global

Com a versão do Azure HDInsight no Hadoop 2.2, a Microsoft disponibilizou o HDInsight em todas as principais regiões geográficas do Azure. Especificamente, os data centers da Europa Ocidental e do Sudeste Asiático foram colocados online. Isso permite que os clientes localizem clusters em um data center próximo e possivelmente em uma zona com requisitos de conformidade semelhantes.

### Alterações de última hora

**Sintaxe de prefixo**:
Apenas a sintaxe "wasb://" tem suporte em clusters HDInsight 3.0 e 3.1. A antiga sintaxe "asv://" tem suporte em clusters HDInsight 2.1 e 1.6, mas não é suportada em clusters HDInsight 3.0 ou em versões posteriores. Isso significa que qualquer trabalho enviado para um cluster HDInsight 3.0 ou 3.1 que usa explicitamente a sintaxe “asv://” falhará. Em vez disso, a sintaxe wasb:// deve ser usada. Além disso, os trabalhos enviados para qualquer cluster HDInsight 3.0 ou 3.1, que foram criados com um metastore existente que contenha referências explícitas a recursos usando a sintaxe asv://, falharão. Esses metastores precisarão ser recriados usando o wasb:// para endereçar recursos.

**Portas**: As portas usadas pelo serviço HDInsight foram alteradas. Os números de porta que estavam sendo usados estavam dentro do intervalo de portas efêmero do SO Windows. As portas são alocadas automaticamente por meio de um intervalo efêmero, para comunicações baseadas em protocolo de Internet de curta duração. O novo conjunto de números de porta de serviço Hortonworks Data Platform (HDP) permitido está agora fora desse intervalo para evitar conflitos que poderiam surgir com as portas usadas pelos serviços que estão sendo executados no headnode. Os novos números de porta não devem causar nenhuma mudança de última hora. Os números usados agora são conforme descrito a seguir:

**HDInsight 1.6 (HDP 1.1)**

<table border="1">

<tr>
<th>
Nome

</th>
<th>
Valor

</th>
</tr>

<tr>
<td>
dfs.http.address

</td>
<td>
namenodehost:30070

</td>
</tr>

<tr>
<td>
dfs.datanode.address

</td>
<td>
0.0.0.0:30010

</td>
</tr>

<tr>
<td>
dfs.datanode.http.address

</td>
<td>
0.0.0.0:30075

</td>
</tr>

<tr>
<td>
dfs.datanode.ipc.address

</td>
<td>
0.0.0.0:30020

</td>
</tr>

<tr>
<td>
dfs.secondary.http.address

</td>
<td>
0.0.0.0:30090

</td>
</tr>

<tr>
<td>
mapred.job.tracker.http.address

</td>
<td>
jobtrackerhost:30030

</td>
</tr>

<tr>
<td>
mapred.task.tracker.http.address

</td>
<td>
0.0.0.0:30060

</td>
</tr>

<tr>
<td>
mapreduce.history.server.http.address

</td>
<td>
0.0.0.0:31111

</td>
</tr>

<tr>
<td>
templeton.port

</td>
<td>
30111

</td>
</tr>

</table>

</p>
**HDInsight 3.0 e 3.1 (HDP 2.0 e 2.1)**

<table border="1">

<tr>
<th>
Nome

</th>
<th>
Valor

</th>
</tr>

<tr>
<td>
dfs.namenode.http-address

</td>
<td>
namenodehost:30070

</td>
</tr>

<tr>
<td>
dfs.namenode.https-address

</td>
<td>
headnodehost:30470

</td>
</tr>

<tr>
<td>
dfs.datanode.address

</td>
<td>
0.0.0.0:30010

</td>
</tr>

<tr>
<td>
dfs.datanode.http.address

</td>
<td>
0.0.0.0:30075

</td>
</tr>

<tr>
<td>
dfs.datanode.ipc.address

</td>
<td>
0.0.0.0:30020

</td>
</tr>

<tr>
<td>
dfs.namenode.secondary.http-address

</td>
<td>
0.0.0.0:30090

</td>
</tr>

<tr>
<td>
yarn.nodemanager.webapp.address

</td>
<td>
0.0.0.0:30060

</td>
</tr>

<tr>
<td>
templeton.port

</td>
<td>
30111

</td>
</tr>

</table>

</p>
### Dependências

As seguintes dependências foram acrescentadas ao HDInsight 3.x (HDP2.x):

-   guice-servlet
-   optiq-core
-   javax.inject
-   activation
-   jsr305
-   geronimo-jaspic\_1.0\_spec
-   jul-to-slf4j
-   java-xmlbuilder
-   ant
-   commons-compiler
-   jdo-api
-   commons-math3
-   paranamer
-   jaxb-impl
-   stringtemplate
-   eigenbase-xom
-   jersey-servlet
-   commons-exec
-   jaxb-api
-   jetty-all-server
-   janino
-   xercesImpl
-   optiq-avatica
-   jta
-   eigenbase-properties
-   groovy-all
-   hamcrest-core
-   mail
-   linq4j
-   jpam
-   jersey-client
-   aopalliance
-   geronimo-annotation\_1.0\_spec
-   ant-launcher
-   jersey-guice
-   xml-apis
-   stax-api
-   asm-commons
-   asm-tree
-   wadl
-   geronimo-jta\_1.1\_spec
-   guice
-   leveldbjni-all
-   velocity
-   jettison
-   snappy-java
-   jetty-all
-   commons-dbcp

As seguintes dependências não existem mais no HDInsight 3.x (HDP2.x):

-   jdeb
-   kfs
-   sqlline
-   ivy
-   aspectjrt
-   json
-   core
-   jdo2-api
-   avro-mapred
-   datanucleus-enhancer
-   jsp
-   commons-logging-api
-   commons-math
-   JavaEWAH
-   aspectjtools
-   javolution
-   hdfsproxy
-   hbase
-   snappy

### Mudanças de versão

As mudanças de versão a seguir foram feitas entre o HDInsight 2.x (HDP1.x) e o HDInsight 3.x (HDP2.x):

-   metrics-core: ['2.1.2'] -\> ['3.0.0']
-   derbynet: ['10.4.2.0'] -\> ['10.10.1.1']
-   datanucleus: ['rdbms-3.0.8'] -\> ['rdbms-3.2.9']
-   jasper-compiler: ['5.5.12'] -\> ['5.5.23']
-   log4j: ['1.2.15', '1.2.16'] -\> ['1.2.16', '1.2.17']
-   derbyclient: ['10.4.2.0'] -\> ['10.10.1.1']
-   httpcore: ['4.2.4'] -\> ['4.2.5']
-   hsqldb: ['1.8.0.10'] -\> ['2.0.0']
-   jets3t: ['0.6.1'] -\> ['0.9.0']
-   protobuf-java: ['2.4.1'] -\> ['2.5.0']
-   derby: ['10.4.2.0'] -\> ['10.10.1.1']
-   jasper: ['runtime-5.5.12'] -\> ['runtime-5.5.23']
-   commons-daemon: ['1.0.1'] -\> ['1.0.13']
-   datanucleus-core: ['3.0.9'] -\> ['3.2.10']
-   datanucleus-api-jdo: ['3.0.7'] -\> ['3.2.6']
-   zookeeper: ['3.4.5.1.3.9.0-01320'] -\> ['3.4.5.2.1.3.0-1948']
-   bonecp: ['0.7.1.RELEASE'] -\> ['0.8.0.RELEASE']

### Drivers

O driver JDBC do SQL Server é usado internamente pelo HDInsight e não é usado para operações externas. Se você desejar se conectar ao HDInsight usando o ODBC, use o driver ODBC do Microsoft Hive. Para obter mais informações sobre como usar o ODBC do Hive, consulte [Conectar o Excel ao HDInsight com o driver ODBC do Microsoft Hive][connect-excel-with-hive-ODBC].

### Correções de bug

Com essa versão, atualizamos as seguintes versões do HDInsight (Hortonworks Data Platform - HDP) com diversas correções de bug:

-   HDInsight 2.1 (HDP 1.3)
-   HDInsight 3.0 (HDP 2.0)
-   HDInsight 3.1 (HDP 2.1)

## Notas de versão Hortonworks

As notas de versão para os HDPs que são usados pelas versões do cluster HDInsight estão disponíveis nos locais listados a seguir.

-   O cluster HDInsight versão 3.1 usa uma distribuição do Hadoop baseada no [Hortonworks Data Platform 2.1][Hortonworks Data Platform 2.1].(Este é o cluster Hadoop padrão criado ao usar o portal do Azure HDInsight.)

-   O cluster HDInsight versão 3.0 usa uma distribuição do Hadoop baseada no [Hortonworks Data Platform 2.0][Hortonworks Data Platform 2.0].

-   O cluster HDInsight versão 2.1 usa uma distribuição do Hadoop baseada no [Hortonworks Data Platform 1.3][Hortonworks Data Platform 1.3].

-   O cluster HDInsight versão 1.6 usa uma distribuição do Hadoop baseada no [Hortonworks Data Platform 1.1][Hortonworks Data Platform 1.1].

  [New-AzureHDInsightCluster]: http://msdn.microsoft.com/pt-br/library/dn593744.aspx
  [SDK do HDInsight]: http://msdn.microsoft.com/pt-br/library/azure/dn469975.aspx
  [Controle de versão do componente HDInsight]: http://azure.microsoft.com/pt-br/documentation/articles/hdinsight-component-versioning/
  [0]: http://i.imgur.com/cmOl5fM.png
  [Mahout]: http://hortonworks.com/hadoop/mahout/
  [Exemplo Breiman]: https://mahout.apache.org/users/classification/breiman-example.html
  [Medindo o desempenho do Hive 13 Apache para Hadoop Enterprise]: http://hortonworks.com/blog/benchmarking-apache-hive-13-enterprise-hadoop/
  [página wiki Hive em Tez]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
  [Hortonworks Data Platform 2.1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html
  [Hortonworks Data Platform 2.0]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html
  [Hortonworks Data Platform 1.3]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html
  [Hortonworks Data Platform 1.1]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html
