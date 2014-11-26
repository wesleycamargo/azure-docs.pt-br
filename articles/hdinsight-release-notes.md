<properties title="HDInsight Release Notes" pageTitle="Notas de versão do HDInsight | Azure" description="HDInsight release notes." metaKeywords="hdinsight, hadoop, hdinsight hadoop, hadoop azure, release notes" services="HDInsight" solutions="" documentationCenter="" editor="cgronlun" manager="paulettm"  authors="bradsev" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bradsev" />


#Notas de versão do Microsoft HDInsight

## Notas para a versão de 15.10.14 ##

A liberação desse hotfix corrige um vazamento de memória no Templeton que afetou os usuários mais ativos do Templeton. Em alguns casos, os usuários que haviam usado o Templeton com frequência viam erros manifestados como códigos de erro 500, pois as solicitações não tinham memória suficiente para serem executadas. A solução alternativa para esse problema era reiniciar o serviço do Templeton. Esse problema foi corrigido agora.


## Notas para a versão de 07.10.14 ##

* Ao usar o ponto de extremidade do Ambari, "https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname}", o campo *host_name* agora retorna o nome de domínio totalmente qualificado (FQDN) do nó ao invés do nome do host apenas. Por exemplo, ao invés de retornar "**headnode0**", você obtém o FQDN "**headnode0.{ClusterDNS}.azurehdinsight.net**". Essa alteração foi necessária para facilitar cenários em que vários tipos de cluster, como HBase e Hadoop, podem ser implementados em uma rede virtual (VNET). Isso acontece, por exemplo, ao usar HBase como uma plataforma de back-end para o Hadoop.

* Fornecemos novas configurações de memória para a implantação padrão do cluster do HDInsight. As configurações de memória padrão anteriores não atentavam adequadamente à instrução quanto ao número de núcleos de CPU implantados. As novas configurações de memória usadas pelo cluster do HDInsight padrão com 4 núcleos de CPU (8 contêineres) são detalhadas na tabela a seguir. (Os valores usados anteriormente a essa versão também são fornecidos entre parênteses). 
 
<table border="1">
<tr><th>Componente</th><th>Alocação de memória</th></tr>
<tr><td> yarn.scheduler.minimum-allocation</td><td>768MB (anteriormente 512MB)</td></tr>
<tr><td> yarn.scheduler.maximum-allocation</td><td>6.144MB (sem alterações)</td></tr>
<tr><td>yarn.nodemanager.resource.memory</td><td>6.144MB (sem alterações)</td></tr>
<tr><td>mapreduce.map.memory</td><td>768MB (anteriormente 512MB)</td></tr>
<tr><td>mapreduce.map.java.opts</td><td>opts=-Xmx512m (anteriormente -Xmx410m)</td></tr>
<tr><td>mapreduce.reduce.memory</td><td>1536MB (anteriormente 1024MB)</td></tr>
<tr><td>mapreduce.reduce.java.opts</td><td>opts=-Xmx1024m (anteriormente -Xmx819m)</td></tr>
<tr><td>yarn.app.mapreduce.am.resource</td><td>768MB (anteriormente 1024MB)</td></tr>
<tr><td>yarn.app.mapreduce.am.command</td><td>opts=-Xmx512m (anteriormente -Xmx819m)</td></tr>
<tr><td>mapreduce.task.io.sort</td><td>256MB (anteriormente 200MB)</td></tr>
<tr><td>tez.am.resource.memory</td><td>1536MB (sem alterações)</td></tr>

</table><br>

Para obter mais informações sobre as definições de configuração de memória usadas por YARN e MapReduce no Hortonworks Data Platform usado pelo HDInsight, consulte [Determinar configurações de memória HDP](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1-latest/bk_installing_manually_book/content/rpm-chap1-11.html). A Hortonworks também oferece uma ferramenta para calcular as configurações adequadas de memória.

Erro do HDInsight PowerShell/SDK: "*O cluster não foi configurado para acessar os Serviços Http*":

* Esse erro é um [problema de compatibilidade](https://social.msdn.microsoft.com/Forums/azure/en-US/a7de016d-8de1-4385-b89e-d2e7a1a9d927/hdinsight-powershellsdk-error-cluster-is-not-configured-for-http-services-access?forum=hdinsight) Conhecido que pode ocorrer devido a uma diferença entre a versão do SDK/PowerShell e a versão do cluster. Clusters criados em 15 de agosto, ou posteriormente, oferecem suporte ao novo recurso de provisionamento em Redes Virtuais. Mas esse recurso não é interpretado corretamente por versões mais antigas do SDK/PowerShell. O resultado é uma falha em algumas operações de envio de trabalho. Se você usar APIs do SDK ou cmdlets do PowerShell para enviar trabalhos (**Use-AzureHDInsightCluster**, **Invoke-Hive**), essas operações podem falhar com a mensagem de erro "*O cluster clustername> não está configurado para acessar Serviços Http*" ou, dependendo da operação, outras mensagens de erro, como "*Não é possível se conectar ao cluster*".

* Esses problemas de compatibilidade são resolvidos nas versões mais recentes do HDInsight SDK e do Azure PowerShell. Recomendamos atualizar o HDInsight SDK para a versão 1.3.1.6 ou posterior e o Azure PowerShell Tools para a versão 0.8.8 ou posterior. Você pode obter acesso ao SDK do HDInsight mais recente do [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started) e o Azure PowerShell Tools em [Como instalar e configurar o PowerShell do Azure](http://azure.microsoft.com/pt-br/documentation/articles/install-configure-powershell/).

* Pode-se esperar que o SDK e o PowerShell continuem a funcionar com as novas atualizações dos clusters, contanto que a versão do cluster permaneça a mesma. Por exemplo, a versão de clusters 3.1 sempre será compatível com a versão atual do SDK/PowerShell 1.3.1.6 e 0.8.8.


## Observações para a versão de 12/9/2014 do HDinsight 3.1##

* Esta versão se baseia no Hortonworks Data Platform (HDP) 2.1.5. Para obter uma lista de bugs corrigidos nesta versão, consulte a página [Corrigido nesta versão](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.5/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.5-fixed.html) no site do Hortonworks.
* Na pasta de bibliotecas do Pig, o arquivo "avro-mapred-1.7.4.jar" foi alterado para avro-mapred-1.7.4-hadoop2.jar. Esse arquivo contém uma pequena correção de erro que é não separável. Recomenda-se que os clientes não assumam uma dependência direta no nome do arquivo JAR em si para evitar separações quando os arquivos forem renomeados.


## Notas para a versão de 21.08.14 ##

* Estamos adicionando a nova configuração WebHCat a seguir (HIVE-7155) que define o limite de memória padrão para um trabalho de controlador Templeton como 1 GB (o valor padrão anterior era 512 MB):
	
	* templeton.mapper.memory.mb (=1024)
	* Essa mudança trata do erro a seguir, que determinadas consultas de Hive encontraram devido a limites mais baixos de memória: "O contêiner está sendo executado além dos limites de memória física".
	* Para voltar aos padrões antigos, você pode definir o valor dessa configuração para 512 por meio do SDK do PowerShell no momento da criação do cluster, usando o comando a seguir:
	
		Add-AzureHDInsightConfigValues -Core @{"templeton.mapper.memory.mb"="512";}


* O nome de host da função zookeeper foi alterado para zookeeper. Isso afeta o modo como o nome é resolvido no cluster, mas não afeta as APIs REST externas. Se você possui componentes que utilizam nome de host de nó zookeeper, você precisará atualizá-los com o novo nome, em vez disso. Os novos nomes para os três nós zookeeper são: 
	* zookeeper0 
	* zookeeper1 
	* zookeeper2 
* A matriz de suporte de versão do HBase é atualizada. Somente a versão HDInsight 3.1 (HBase versão 0.98) é suportada para cargas de trabalho de produção no HBase. A versão 3.0, que estava disponível para visualização, não será suportada se você prosseguir. Durante o período de transição, os clientes ainda podem criar clusters da versão 3.0. 

## Observações sobre os clusters criados antes de 15/8/2014 ##

Um erro do HDInsight PowerShell/SDK com a mensagem "O cluster <clustername> não está configurado para acessar Serviços Http" (ou, dependendo da operação, outras mensagens de erro, como: "Não é possível se conectar ao cluster") poderá ser encontrado devido a uma diferença de versões entre o SDK/PowerShell e um cluster. Clusters criados em 15 de agosto, ou posteriormente, oferecem suporte ao novo recurso de provisionamento em Redes Virtuais. Esse recurso não é interpretado corretamente por versões mais antigas do SDK/PowerShell, o que resulta em falhas de operações de envio de trabalhos. Se você usar APIs do SDK ou cmdlets do PowerShell para enviar trabalhos, como Use-AzureHDInsightCluster ou Invoke-AzureHDInsightHiveJob, essas operações podem falhar com uma das mensagens de erro descritas acima.

Esses problemas de compatibilidade são resolvidos nas versões mais recentes do SDK e do Azure PowerShell. Recomendamos atualizar o HDInsight SDK para a versão 1.3.1.6 ou posterior e o Azure PowerShell Tools para a versão 0.8.8 ou posterior. Você pode obter acesso ao SDK do HDInsight mais recente do [nuget](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.HDInsight/) e o Azure PowerShell Tools usando [Microsoft Web PI](http://go.microsoft.com/?linkid=9811175&clcid=0x409).

Pode-se esperar que o SDK e o PowerShell continuem a funcionar com as novas atualizações dos clusters, contanto que a versão do cluster permaneça a mesma. Por exemplo, a versão de clusters 3.1 sempre será compatível com a versão atual do SDK/PowerShell 1.3.1.6 e 0.8.8.


## Notas para a versão de 28/07/2014 ##

* **O HDInsight está disponível em novas regiões**: Com essa versão, expandimos a presença geográfica do HDInsight para três novas regiões. Os clientes que usam o HDInsight agora podem criar clusters nessas regiões. 
	* Ásia Oriental, 
	* Centro-Norte dos EUA e 
	* Centro-Sul dos EUA 
* Com essa versão, o HDInsight v1.6 (HDP1.1, Hadoop 1.0.3) e o HDInsight v2.1 (HDP1.3, Hadoop 1.2) estão sendo removidos do Portal de Gerenciamento do Azure. Você pode continuar a criar clusters de Hadoop para essas versões usando cmdlets do PowerShell do HDInsight ([New-AzureHDInsightCluster](http://msdn.microsoft.com/pt-br/library/dn593744.aspx)) ou usando o [SDK do HDInsight](http://msdn.microsoft.com/pt-br/library/azure/dn469975.aspx). Consulte a página [Controle de versão de componente de HDInsight](http://azure.microsoft.com/pt-br/documentation/articles/hdinsight-component-versioning/) para obter mais informações.
* Alterações ao Hortonworks Data Platform (HDP) nesta versão: 

<table border="1">
<tr><th>HDP</th><th>Alterações</th></tr>
<tr><td>HDP 1,3 / HDI 2,1</td><td>Sem alterações</td></tr>
<tr><td>HDP 2.0 / HDI 3.0</td><td>Sem alterações</td></tr>
<tr><td>HDP 2,1 / HDI 3,1</td><td>zookeeper: ['3.4.5.2.1.3.0-1948'] -> ['3.4.5.2.1.3.2-0002']</td></tr>


</table><br>

## Notas para a versão de 24.06.14 ##

Essa versão contém diversos novos aprimoramentos para o serviço HDInsight: 

* **Disponibilidade do HDP 2.1**: O HDInsight 3.1, que contém o HDP 2.1, agora está disponível de modo geral e é a versão padrão para novos clusters.
* **HBase - Melhorias no Portal de Gerenciamento do Azure**: Estamos tornando os clusters HBase disponíveis na visualização. Agora você pode criar clusters HBase por meio do portal com três cliques.

![](http://i.imgur.com/cmOl5fM.png)

Com o HBase, você pode criar uma variedade de cargas de trabalho em tempo real no HDInsight, por meio de sites interativos que funcionem com conjuntos de dados grandes para dados para o sensor de armazenamento de serviços e dados de telemetria de milhões de pontos de extremidade. A próxima etapa seria analisar os dados nessas cargas de trabalho com trabalhos do Hadoop e isso é possível de imediato no HDInsight, por meio das experiências fornecidas como o painel de cluster do Hive e do PowerShell.

### Agora, o Apache Mahout é pré-instalado no HDInsight 3.1 ###

 [Mahout](http://hortonworks.com/hadoop/mahout/) é pré-instalado nos clusters do Hadoop do HDInsight 3.1. Desse modo, você pode executar trabalhos do Mahout sem a necessidade de alguma configuração de cluster adicional. Por exemplo, você pode acessar remotamente um cluster do Hadoop usando o Protocolo de Área de trabalho remota (RDP) e, sem etapas adicionais, executar o comando Hello world do Mahout:

		mahout org.apache.mahout.classifier.df.tools.Describe -p /user/hdp/glass.data -f /user/hdp/glass.info -d I 9 N L  

		mahout org.apache.mahout.classifier.df.BreimanExample -d /user/hdp/glass.data -ds /user/hdp/glass.info -i 10 -t 100

Para obter uma explicação mais completa deste procedimento, consulte a documentação de [Exemplo de Breiman](https://mahout.apache.org/users/classification/breiman-example.html) no site da Apache Mahout. 


### Consultas de Hive podem usar o Tez no HDinsight 3.1 ###

O Hive 0.13 agora está disponível no HDInsight 3.1 e é capaz de executar consultas usando Tez, que pode ser utilizado para melhorias de desempenho significativas. 
O Tez não está habilitado por padrão para consultas de Hive. Para utilizá-lo, você precisa optar por isso. Você pode habilitar o Tez executando o trecho de código a seguir:

		set hive.execution.engine=tez;
		select sc_status, count(*), histogram_numeric(sc_bytes,5) from website_logs_orc_local group by sc_status;

A Hortonworks publicou uma demonstração detalhada das melhorias nas consultas do Hive com o Tez, conforme fornecido em medições de desempenho padrão. Para obter mais informações, consulte [Benchmark Apache Hive 13 para Enterprise Hadoop](http://hortonworks.com/blog/benchmarking-apache-hive-13-enterprise-hadoop/). 

Para obter mais detalhes sobre como usar o Hive com Tez, confira a página [wiki Hive sobre o Tez](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez).

###Disponibilidade global
Com a versão do Azure HDInsight no Hadoop 2.2, a Microsoft disponibilizou o HDInsight em todas as principais regiões geográficas do Azure. Especificamente, os data centers da Europa Ocidental e do Sudeste Asiático foram colocados online. Isso permite que os clientes localizem clusters em um data center próximo e possivelmente em uma zona com requisitos de conformidade semelhantes. 


###Prós e contras entre as versões de clusters

**Os metastores do Oozie usados com um cluster do HDInsight 3.1 não são compatíveis com as versões anteriores dos clusters do HDInsight 2.1 e não podem ser usados com essa versão anterior**

Um banco de dados do metastore do Oozie personalizado implantado com um cluster do HDInsight 3.1 não pode ser reutilizado com um cluster do HDInsight 2.1. Isso acontece mesmo se o metastore tiver sido originado com um cluster 2.1. Esse cenário não é suportado, já que o esquema do metastore é atualizado quando utilizado com um cluster 3.1 e, portanto, não é mais compatível com o metastore exigido pelos clusters 2.1. Qualquer tentativa de reutilizar um metastore do Oozie que tenha sido usado com um cluster do HDInsight 3.1 fará com que o cluster 2.1 seja inutilizado. 

**Os metastores do Oozie não podem ser compartilhadas em clusters**
Uma observação mais geral e um pouco ortogonal, metastores do Oozie estão associados a clusters específicos e não podem ser compartilhados entre clusters.

###Alterações de última hora

**Sintaxe de prefixo**:
Apenas a sintaxe "wasb://" tem suporte em clusters HDInsight 3.0.  e 3.1. A antiga sintaxe "asv://" tem suporte em clusters HDInsight 2.1 e 1.6, mas não é suportada em clusters HDInsight 3.0 ou em versões posteriores. Isso significa que todos os trabalhos enviados para um cluster HDInsight 3.0  ou 3.1 que usar explicitamente a sintaxe "asv: / /" falharão. Em vez disso, a sintaxe wasb:// deve ser usada. Além disso, os trabalhos enviados para qualquer cluster HDInsight 3.0 ou 3.1, que foram criados com um metastore existente que contenha referências explícitas a recursos usando a sintaxe asv://, falharão. Esses metastores precisarão ser recriados usando o wasb:// para endereçar recursos. 


**Portas**: As portas usadas pelo serviço HDInsight foram alteradas. Os números de porta que estavam sendo usados estavam dentro do intervalo de portas efêmero do SO Windows. As portas são alocadas automaticamente por meio de um intervalo efêmero, para comunicações baseadas em protocolo de Internet de curta duração. O novo conjunto de números de porta de serviço Hortonworks Data Platform (HDP) permitido está agora fora desse intervalo para evitar conflitos que poderiam surgir com as portas usadas pelos serviços que estão sendo executados no headnode. Os novos números de porta não devem causar nenhuma mudança de última hora. Os números usados agora são conforme descrito a seguir:

 **HDInsight 1,6 (HDP 1,1)**
<table border="1">
<tr><th>Nome</th><th>Valor</th></tr>
<tr><td>dfs.http.address</td><td>namenodehost:30070</td></tr>
<tr><td>dfs.datanode.address</td><td>0.0.0.0:30010</td></tr>
<tr><td>dfs.datanode.http.address</td><td>0.0.0.0:30075</td></tr>
<tr><td>dfs.datanode.ipc.address</td><td>0.0.0.0:30020</td></tr>
<tr><td>dfs.secondary.http.address</td><td>0.0.0.0:30090</td></tr>
<tr><td>mapred.job.tracker.http.address</td><td>jobtrackerhost:30030</td></tr>
<tr><td>mapred.task.tracker.http.address</td><td>0.0.0.0:30060</td></tr>
<tr><td>mapreduce.history.server.http.address</td><td>0.0.0.0:31111</td></tr>
<tr><td>templeton.port</td><td>30111</td></tr>
</table><br>

 **HDInsight 3.0 e 3.1 (HDP 2.0 e 2.1)**
<table border="1">
<tr><th>Nome</th><th>Valor</th></tr>
<tr><td>dfs.namenode.http-address</td><td>namenodehost:30070</td></tr>
<tr><td>dfs.namenode.https-address</td><td>headnodehost:30470</td></tr>
<tr><td>dfs.datanode.address</td><td>0.0.0.0:30010</td></tr>
<tr><td>dfs.datanode.http.address</td><td>0.0.0.0:30075</td></tr>
<tr><td>dfs.datanode.ipc.address</td><td>0.0.0.0:30020</td></tr>
<tr><td>dfs.namenode.secondary.http-address</td><td>0.0.0.0:30090</td></tr>
<tr><td>yarn.nodemanager.webapp.address</td><td>0.0.0.0:30060</td></tr>
<tr><td>templeton.port</td><td>30111</td></tr>
</table><br>

###Dependências 

As seguintes dependências foram acrescentadas ao HDInsight 3.x (HDP2.x):

* guice-servlet
* optiq-core
* javax.inject
* activation
* jsr305
* geronimo-jaspic_1.0_spec
* jul-to-slf4j
* java-xmlbuilder
* ant
* commons-compiler
* jdo-api
* commons-math3
* paranamer
* jaxb-impl
* stringtemplate
* eigenbase-xom
* jersey-servlet
* commons-exec
* jaxb-api
* jetty-all-server
* janino
* xercesImpl
* optiq-avatica
* jta
* eigenbase-properties
* groovy-all
* hamcrest-core
* mail
* linq4j
* jpam
* jersey-client
* aopalliance
* geronimo-annotation_1.0_spec
* ant-launcher
* jersey-guice
* xml-apis
* stax-api
* asm-commons
* asm-tree
* wadl
* geronimo-jta_1.1_spec
* guice
* leveldbjni-all
* velocity
* jettison
* snappy-java
* jetty-all
* commons-dbcp

As seguintes dependências não existem mais no HDInsight 3.x (HDP2.x):

* jdeb
* kfs
* sqlline
* ivy
* aspectjrt
* json
* core
* jdo2-api
* avro-mapred
* datanucleus-enhancer
* jsp
* commons-logging-api
* commons-math
* JavaEWAH
* aspectjtools
* javolution
* hdfsproxy
* hbase
* snappy

###Mudanças de versão 

As mudanças de versão a seguir foram feitas entre o HDInsight 2.x (HDP1.x) e o HDInsight 3.x (HDP2.x):

* metrics-core: ['2.1.2'] -> ['3.0.0']
* derbynet: ['10.4.2.0'] -> ['10.10.1.1']
* datanucleus: ['rdbms-3.0.8'] -> ['rdbms-3,20,9']
* jasper-compiler: ['5.5.12'] -> ['5.5.23']
* log4j: ['1.2.15', '1.2.16'] -> ['1.2.16', '1.2.17']
* derbyclient: ['10.4.2.0'] -> ['10.10.1.1']
* httpcore: ['4.2.4'] -> ['4.2.5']
* hsqldb: ['1.8.0.10'] -> ['2.0.0']
* jets3t: ['0.6.1'] -> ['0.9.0']
* protobuf-java: ['2.4.1'] -> ['2.5.0']
* derby: ['10.4.2.0'] -> ['10.10.1.1']
* jasper: ['runtime-5.5.12'] -> ['runtime-5.50,23']
* commons-daemon: ['1.0.1'] -> ['1.0.13']
* datanucleus-core: ['3.0.9'] -> ['3.2.10']
* datanucleus-api-jdo: ['3.0.7'] -> ['3.2.6']
* zookeeper: ['3.4.5.1.3.9.0-01320'] -> ['3.4.5.2.1.3.0-1948']
* bonecp: ['0.7.1.RELEASE'] -> ['0.8.0.RELEASE']


###Drivers
O driver JDBC do SQL Server é usado internamente pelo HDInsight e não é usado para operações externas. Se você desejar se conectar ao HDInsight usando o ODBC, use o driver ODBC do Microsoft Hive. Para obter mais informações sobre como usar o ODBC do Hive, [Conectar o Excel ao HDInsight com o driver ODBC do Microsoft Hive][connect-excel-with-hive-ODBC].


### Correções de bug ###

Com esta versão, atualizamos o HDInsight a seguir  (Hortonworks Data Platform - HDP) versões com várias correções de bug:

* HDInsight 2,1 (HDP 1,3)
* HDInsight 3,0 (HDP 2,0)
* HDInsight 3.1 (HDP 2.1)

## Notas de versão Hortonworks ##

As notas de versão para os HDPs que são usados pelas versões do cluster HDInsight estão disponíveis nos locais listados a seguir.

* O cluster HDInsight versão 3.1 usa uma distribuição de Hadoop que se baseia no [Hortonworks Data Platform 2.1][hdp-2-1-1]. (Esse é o cluster do Hadoop padrão criado durante o uso do portal Azure HDInsight.)

* O cluster HDInsight versão 3,0 usa uma distribuição de Hadoop que se baseia no [Hortonworks Data Platform 2.0][hdp-2-0-8].

* O cluster HDInsight versão 2.1 usa uma distribuição de Hadoop que se baseia no [Hortonworks Data Platform 1.3][hdp-1-3-0]. 

* O cluster HDInsight versão 1.6 usa uma distribuição de Hadoop que se baseia no [Hortonworks Data Platform 1.1][hdp-1-1-0]. 




[hdp-2-1-1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html




