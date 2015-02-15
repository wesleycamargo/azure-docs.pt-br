<properties 
	pageTitle="Notas de versão do HDInsight | Azure" 
	description="Notas de versão do HDInsight." 
	services="hdinsight" 
	documentationCenter="" 
	editor="cgronlun" 
	manager="paulettm" 
	authors="bradsev"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/30/2015" 
	ms.author="bradsev"/>


#Notas de versão do Microsoft HDInsight

## Notas da versão de 29/01/2015 do HDInsight ##

Os números completos da versão dos clusters HDInsight implantados com essa versão são:

* HDInsight 	2.1.10.455.1309616	(HDP 1.3.9.0-01351 - inalterado)
* HDInsight 	3.0.6.455.1309616	(HDP 2.0.9.0-2097 -  inalterado)
* HDInsight 	3.1.2.455.1309616	(HDP 2.1.9.0-2196 -  inalterado)
* SDK			N/D

Esta versão contém a seguinte atualização.

<table border="1">
<tr>
<th>Título</th>
<th>Descrição</th>
<th>Área afetada 
(por exemplo, serviço, componentes de sistemas operacionais, SDK, PS, AUX)</p></th>
<th>Tipo de cluster afetado (por exemplo, Hadoop, HBase, tempestade, todos)</th>
<th>JIRA (se aplicável)</th>
</tr>


<tr>

<td> Correções de bug</td>
<td>Fizemos algumas correções de bugs importantes que melhoram a confiabilidade dos Clusters HDInsight durante as atualizações do Azure.</td>
<td>Serviços</td>
<td></td>
<td>N/D</td>
</tr>



</table>
<br>

## Notas da versão de 05/01/2015 do HDInsight ##

Os números completos da versão dos clusters HDInsight implantados com essa versão são:

* HDInsight 	2.1.10.420.1246118	(HDP 1.3.9.0-01351 - inalterado)
* HDInsight 	3.0.6.420.1246118	(HDP 2.0.9.0-2097 - inalterado)
* HDInsight 	3.1.2.420.1246118	(HDP 2.1.9.0-2196 - inalterado)


Esta versão contém a seguinte atualização.

<table border="1">
<tr>
<th>Título</th>
<th>Descrição</th>
<th>Componente</th>
<th>Tipo de cluster</th>
<th>JIRA (se aplicável)</th>
</tr>


<tr>
<td>Exemplos para análise de tendência do Twitter e Mahout com base em recomendações de filmes</td>
<td><p>Nesta versão, o console de consulta do HDInsight tem dois exemplos adicionais:</p>

<p><b>Análise de tendência do Twitter</b><br>
APIs públicas fornecidas por sites, como o Twitter, são uma fonte útil de dados para analisar e compreender as tendências populares. Neste tutorial, Aprenda a usar o Hive para obter uma lista dos usuários do Twitter que enviaram a maioria dos tweets que contêm uma palavra específica. </p>

<p><b>Recomendação de filme do Mahout</b><br>
O Apache Mahout é uma biblioteca de aprendizado de máquina de Apache Hadoop. O Mahout contém algoritmos para processamento de dados, como filtragem, classificação e clustering. Neste tutorial, você utilizará um mecanismo de recomendação para gerar recomendações de filmes baseadas nos vídeos que seus amigos assistiram.</p></td>
<td>Console de consulta</td>
<td>Hadoop</td>
<td>N/D</td>
</tr>

<tr>
<td>Mude para o valor padrão na configuração de hive hive.auto.convert.join.noconditionaltask.size</td>
<td><p>Essa configuração de tamanho aplica-se a associações de mapa convertidas automaticamente. O valor representa a soma dos tamanhos das tabelas que podem ser convertidas em hashmaps que cabem na memória. Em uma versão anterior, esse valor aumentava do valor padrão de 10MB para 128MB. No entanto, o novo valor de 128MB estava causando falhas nos trabalhos devido à falta de memória. Esta versão reverte o valor padrão de volta para 10MB. Os clientes ainda podem optar por substituir esse valor durante a criação do cluster considerando suas consultas e tamanhos de tabela. Para obter mais informações sobre essa configuração e como substituí-la, consulte <a href="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.0.2/ds_Hive/optimize-joins.html#JoinOptimization-OptimizeAutoJoinConversion" target="_blank">Otimizar a conversão de associação automática</a> na documentação Hortonworks. </p></td>
<td>Hive</td>
<td>Hadoop, HBase</td>
<td>N/D</td>
</tr>

</table>
<br>

## Notas da versão de 23/12/2014 do HDInsight ##

Os números completos da versão dos clusters HDInsight implantados com essa versão são:

* HDInsight 	2.1.10.420.1246783	(versão HDP inalterada)
* HDInsight 	3.0.6.420.1246783	(versão HDP inalterada)
* HDInsight 	3.1.1.420.1246783	(versão HDP inalterada)

Esta versão contém a seguinte atualização.

<table border="1">
<tr>
<th>Título</th>
<th>Descrição</th>
<th>Componente</th>
<th>Tipo de cluster</th>
<th>JIRA (se aplicável)</th>
</tr>


<tr>
<td>Falhas intermitentes de criação de Cluster causadas por excesso de carga</td>
<td><p>Algoritmo aprimorado para baixar os pacotes HDP durante a criação do cluster permite a manipulação mais robusta de falhas causadas por excesso de carga. Espere mais algumas melhorias mais nessa área, permitindo melhor dimensionamento para o aumento de carga relacionado à criação de cluster.</p></td>
<td>Acesso</td>
<td>Hadoop, Hbase, Storm</td>
<td>N/D</td>
</tr>



</table>
<br>

## Notas da versão de 18/12/2014 do HDInsight ##

Esta versão contém as seguintes atualizações de componentes.

<table border="1">
<tr>
<th>Título</th>
<th>Descrição</th>
<th>Componente</th>
<th>Tipo de cluster</th>
<th>JIRA (se aplicável)</th>
</tr>

<tr>
<td><a href = "http://azure.microsoft.com/pt-br/documentation/articles/hdinsight-hadoop-customize-cluster/" target="_blank">GA de personalização de cluster</a></td>
<td><p>Personalização oferece a capacidade de personalizar os clusters de HDInsight do Azure com projetos disponíveis por meio do ecossistema do Apache Hadoop. Com esse novo recurso, agora você pode testar e implantar projetos de Hadoop para o HDInsight do Azure. Isso é habilitado por meio do recurso de <b>Ação de Script</b> que pode modificar clusters Hadoop de modos arbitrários usando scripts personalizados. Esse recurso de personalização está disponível em todos os tipos de clusters do HDInsight, incluindo Hadoop, HBase e Storm. Para demonstrar o poder desse recurso, documentamos o processo para instalar os populares módulos <a href = "http://azure.microsoft.com/pt-br/documentation/articles/hdinsight-hadoop-spark-install/" target="_blank">Spark</a>, <a href = "http://azure.microsoft.com/pt-br/documentation/articles/hdinsight-hadoop-r-scripts/" target="_blank">R</a>, <a href = "http://azure.microsoft.com/pt-br/documentation/articles/hdinsight-hadoop-solr-install/" target="_blank">Solr</a>e <a href = "http://azure.microsoft.com/pt-br/documentation/articles/hdinsight-hadoop-giraph-install/" target="_blank">Giraph</a> . Esta versão também adiciona o recurso para os clientes especificares suas ações de script personalizadas por meio do Portal de Gerenciamento do Azure, fornece diretrizes e práticas recomendadas de como criar ações de script personalizadas usando métodos auxiliares e fornece diretrizes sobre como testar a ação de script. </p></td>
<td>GA do recurso</td>
<td>Todos</td>
<td>N/D</td>
</tr>


</table>
<br>

## Notas da versão de 05/12/2014 do HDInsight ##

Os números completos da versão dos clusters HDInsight implantados com essa versão são:

* HDInsight 	2.1.9.406.1221105	(HDP 1.3.9.0-01351)
* HDInsight 	3.0.5.406.1221105	(HDP 2.0.9.0-2097)
* HDInsight 	3.1.1.406.1221105	(HDP 2.1.9.0-2196)
* SDK do HDInsight N/D

Esta versão contém as seguintes atualizações de componentes.

<table border="1">
<tr>
<th>Título</th>
<th>Descrição</th>
<th>Componente</th>
<th>Tipo de cluster</th>
<th>JIRA (se aplicável)</th>
</tr>

<tr>
<td>Correção de bug: Erro intermitente ao adicionar grande número de partições a uma tabela na DDL do Hive. </td>
<td><p>Se existir um erro de conexão intermitente com o banco de dados do metastore do Hive ao adicionar diversas partições a uma tabela Hive, a DDL do Hive poderá falhar. A seguinte instrução será vista no log de erros do Hive se essa falha ocorrer: </p><p>"ERROR [main]: ql.Driver (SessionState.java:printError(547)) - FAILED: Execution Error, return code 1 from org.apache.hadoop.hive.ql.exec.DDLTask. MetaException(message:java.lang.RuntimeException: commitTransaction was called but openTransactionCalls = 0. This probably indicates that there are unbalanced calls to openTransaction/commitTransaction)"</p></td>
<td>Hive</td>
<td>Hadoop, HBase</td>
<td>HIVE-482 (Este é um JIRA interno de modo que não é possível fazer um orçamento externamente. Mencionado aqui para referência.)</td>
</tr>

<tr>
<td>Correção de bug: Interrupção de funcionamento ocasional no consulta do HDInsight  Console de Consulta</td>
<td>Quando isso acontece, a seguinte instrução pode ser vista no log do WebHCat para o trabalho do inicializador do WebHCat: <p>"org.apache.hive.hcatalog.templeton.CatchallExceptionMapper | org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.yarn.exceptions.YarnRuntimeException): Could not load history file {wasb url to the history file}"</p></td>
<td>WebHCat</td>
<td>Hadoop</td>
<td>HIVE-482 (Este é um JIRA interno de modo que não é possível fazer um orçamento externamente. Mencionado aqui para referência.)</td>
</tr>

<tr>
<td>Correção de bug: Pico ocasional na latência das consultas do HBase</td>
<td>Se isso acontecer, os usuários notarão um pico ocasional de 3 segundos na latências das consultas do HBase. </td>
<td>Gateway do cluster HDInsight</td>
<td>HBase</td>
<td>N/D</td>
</tr>

<tr>
<td>Mudanças de nomes dos arquivos jar HDP</td>
<td>Para a versão em cluster do HDI 3.0, há algumas mudanças nos arquivos jar internos instalados pelo HDP. jetty-6.1.26.jar
 foi substituído por jetty-6.1.26.hwx.jar. jetty-util-6.1.26.jar foi substituído por jetty-util-6.1.26.hwx.jar. Essas mudanças se aplicam aos projetos do Hadoop, Mahout, WebHCat e Oozie.**</td>
<td>Hadoop, Mahout, WebHCat, Oozie</td>
<td>Hadoop, HBase</td>
<td>N/D</td>
</tr>

</table>
<br>


## Notas da versão de 21/11/2014 do HDInsight ##

Os números completos da versão dos clusters HDInsight implantados com essa versão são:

* HDInsight 2.1.9.382.1169709 (sem alteração desde 14/11/2014)
* HDInsight 3.0.5.382.1169709 (sem alteração desde 14/11/2014)
* HDInsight 3.1.1.382.1169709 (sem alteração desde 14/11/2014)
* SDK do HDINsight 1.4.0

Esta versão contém as seguintes atualizações de componentes.

<table border="1">
<tr>
<th>Título</th>
<th>Descrição</th>
<th>Componente</th>
<th>Tipo de cluster</th>
<th>JIRA (se aplicável)</th>
</tr>

<tr>
<td>Acesso a logs de aplicativos</td>
<td>Capacidade para enumerar de modo programático aplicativos que foram executados em seus clusters e para baixar logs de aplicativos relevantes ou de contêineres específicos para ajudar a depurar aplicativos problemáticos.</td>
<td>.</td>
<td>Hadoop</td>
<td>N/D</td>
</tr>

<tr>
<td>Capacidade para especificar o nome da região no IHdInsightClient.DeleteCluster </td>
<td>O SDK do Azure HDInsight agora fornece a capacidade de especificar um nome de região ao usar o **DeleteCluster**. Isso ajuda a desbloquear clientes com 2 recursos de mesmo nome em regiões diferentes que não conseguiam excluir nenhum deles.</td>
<td>.</td>
<td>Todos</td>
<td>N/D</td>
</tr>

<tr>
<td>ClusterDetails.DeploymentId</td>
<td>O objeto **ClusterDetails** agora retorna um campo **DeploymentID** que representa um identificador exclusivo do cluster. É garantida a exclusividade nas tentativas de criação de clusters com os mesmos nomes.</td>
<td>.</td>
<td>Todos</td>
<td>N/D</td>
</tr>
</table>
<br>

## Notas da versão de 14/11/2014 do HDInsight ##

Os números completos da versão dos clusters HDInsight implantados com essa versão são:

* HDInsight 2.1.9.382.1169709
* HDInsight 3.0.5.382.1169709
* HDInsight 3.1.1.382.1169709

Esta versão contém os seguintes novos recursos, atualizações de componentes e correções de bug.

<table border="1">
<tr>
<th>Título</th>
<th>Descrição</th>
<th>Componente</th>
<th>Tipo de cluster</th>
<th>JIRA (se aplicável)</th>
</tr>

<tr>
<td>Ação de script (visualização)</td>
<td>A visualização do recurso de personalização de cluster possibilita a modificação dos clusters Hadoop de maneiras arbitrárias usando scripts personalizados. Com esse novo recurso, os usuários podem experimentar e implantar projetos disponíveis no ecossistema do Apache Hadoop nos clusters do Azure HDInsight. O recurso de personalização está disponível em todos os tipos de clusters do HDInsight, incluindo Hadoop, HBase e Storm.</td>
<td>New recurso</td>
<td>Todos</td>
<td>N/D</td>
</tr>

<tr>
<td>Trabalhos pré-compilados para sites do Azure e análise de logs de armazenamento</td>
<td>O Console de Consulta do HDInsight tem uma galeria de introdução que dá suporte a soluções que funcionam com seus dados ou com dados de exemplo.
<p>Soluções que funcionam com seus dados:<br>
Criamos trabalhos para alguns dos cenários de análise de dados mais comuns para fornecer um ponto de partida para você criar as suas próprias soluções. Você pode usar seus próprios dados com o trabalho para ver como funciona. Em seguida, quando estiver pronto, use o que aprendeu para criar sua própria solução com base no trabalho pré-compilado.</p>
<p>Soluções que funcionam com dados de exemplo:<br>
Aprenda como trabalhar com o HDInsight ao analisar alguns cenários básicos, como analisar los da Web e dados de sensores. Além de aprender como usar o HDInsight para analisar tais dados, você também verá como é possível conectar outros aplicativos e serviços a esses dados. Visualizar dados ao conectar ao Microsoft Excel fornecerá um exemplo da eficiência dessa abordagem.</p></td>
<td>Console de consulta</td>
<td>Hadoop</td>
<td>N/D</td>
</tr>

<tr>
<td>Correção de perda de memória no Templeton</td>
<td>Foi tratada a perda de memória no Templeton que afetava os clientes que tinham um cluster em execução há tempos ou que estavam enviando 100s de solicitações de trabalhos por segundo. O problema se manifestava como erros 5xx do Templeton e a solução alternativa era reiniciar o serviço. A solução alternativa não é mais necessária.</td>
<td>Templeton</td>
<td>Todos</td>
<td>https://issues.apache.org/jira/browse/HADOOP-11248</td>
</tr>
</table>
<br>


Observação: Para demonstrar os novos recursos disponíveis pela personalização de clusters, os procedimentos que usam as Ações de script para instalar ps módulos Spark e R em um cluster foram documentados. Para obter mais informações, consulte:

* [Instalar e usar o Spark 1.0 em clusters HDInsight (visualização)][hdinsight-install-spark]
* [Instalar e usar R em clusters Hadoop do HDInsight (visualização)][hdinsight-r-scripts]




## Notas da versão de 07/11/2014 do HDInsight ##

Os números completos da versão dos clusters HDInsight implantados com essa versão são:

* HDInsight 2.1	2.1.9.374.1153876
* HDInsight 3.0	3.0.5.374.1153876
* HDInsight 3.1	3.1.1.374.1153876

Esta versão contém as seguintes atualizações de componentes.

<table border="1">
<tr>
<th>Título</th>
<th>Descrição</th>
<th>Componente</th>
<th>Tipo de cluster</th>
<th>JIRA (se aplicável)</th>
</tr>

<tr>
<td>HDP 2.1.7</td>
<td>Esta versão é baseada no Hortonworks Data Platform (HDP) 2.1.7. As notas de versão do HDP 2.1.7 estão disponíveis no site da Hortonworks no endereço http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html.</td>
<td>HDP</td>
<td>Todos</td>
<td>N/D</td>
</tr>

<tr>
<td>YARN Timeline Server</td>
<td>O YARN Timeline Server (também conhecido como Generic Application History Server) foi habilitado por padrão. O Timeline Server fornece informações genéricas sobre aplicativos concluídos, como ID do aplicativo, nome do aplicativo, status do aplicativo, hora de envio do aplicativo e hora de conclusão do aplicativo. <p>Essas informações do aplicativo podem ser recuperadas do headnode tanto ao acessar o URI http://headnodehost:8188 ou ao executar o comando YARN: yarn application -list -appStates ALL.</p> 
<p>Essas informações também podem ser recuperadas remotamente pela API REST em https://{ClusterDnsName}. azurehdinsight.net/ws/v1/applicationhistory/.</p> 
<p>Informações mais detalhadas sobre o Timeline Server estão disponíveis em http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html.</p></td>
<td>Serviço, YARN</td>
<td>Hadoop, HBase</td>
<td>N/D</td>
</tr>

<tr>
<td>ID de Implantação de Cluster</td>
<td>Iniciando pela versão mais recente do SDK 1.3.3.1.5426.29232, os usuários podem acessar o HDInsight um uma ID exclusiva emitida para cada cluster. Isso permite que os clientes tomem conhecimento das instâncias exclusivas de clusters quando um dnsname está sendo reutilizado entre cenários de criação/cancelamento.</td>
<td>.</td>
<td>Todos</td>
<td>N/D</td>
</tr>
</table>
<br>

* Observe que o bug que não permitia a versão completa # ser mostrada no portal ou ser retornada pelo SDK ou pelo PowerShell foi corrigida nesta versão. 

## Notas da versão de 15/10/2014 ##

A liberação desse hotfix corrige um vazamento de memória no Templeton que afetou os usuários mais ativos do Templeton. Em alguns casos, os usuários que haviam usado o Templeton com frequência viam erros manifestados como códigos de erro 500, pois as solicitações não tinham memória suficiente para serem executadas. A solução alternativa para esse problema era reiniciar o serviço do Templeton. Esse problema foi corrigido agora.


## Notas da versão de 07/10/2014 ##

* Ao usar o ponto de extremidade do Ambari, "https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname}", o campo *host_name* agora retorna o nome de domínio totalmente qualificado (FQDN) do nó em vez de apenas o nome do host. Por exemplo, ao invés de retornar "**headnode0**", você obtém o FQDN "**headnode0.{ClusterDNS}.azurehdinsight.net**". Essa alteração foi necessária para facilitar cenários em que vários tipos de cluster, como HBase e Hadoop, podem ser implantados em uma rede virtual (VNET). Isso acontece, por exemplo, ao usar HBase como uma plataforma de back-end para o Hadoop.

* Fornecemos novas configurações de memória para a implantação padrão do cluster do HDInsight. As configurações de memória padrão anteriores não atentavam adequadamente à instrução quanto ao número de núcleos de CPU implantados. Essas novas configurações de memória devem fornecer melhores padrões, de acordo com as recomendações da Hortonworks. Para alterá-las, consulte a documentação de referência do SDK sobre alteração da configuração de clusters. As novas configurações de memória usadas pelo cluster do HDInsight padrão com 4 núcleos de CPU (8 contêineres) são detalhadas na tabela a seguir. (Os valores usados anteriormente a essa versão também são fornecidos entre parênteses). 
 
<table border="1">
<tr><th>Componente</th><th>Alocação de memória</th></tr>
<tr><td> yarn.scheduler.minimum-allocation</td><td>768MB (anteriormente 512MB)</td></tr>
<tr><td> yarn.scheduler.maximum-allocation</td><td>6144MB (sem alterações)</td></tr>
<tr><td>yarn.nodemanager.resource.memory</td><td>6144MB (sem alterações)</td></tr>
<tr><td>mapreduce.map.memory</td><td>768MB (anteriormente 512MB)</td></tr>
<tr><td>mapreduce.map.java.opts</td><td>opts=-Xmx512m (anteriormente -Xmx410m)</td></tr>
<tr><td>mapreduce.reduce.memory</td><td>1536MB (anteriormente 1024MB)</td></tr>
<tr><td>mapreduce.reduce.java.opts</td><td>opts=-Xmx1024m (anteriormente -Xmx819m)</td></tr>
<tr><td>yarn.app.mapreduce.am.resource</td><td>768MB (anteriormente 1024MB)</td></tr>
<tr><td>yarn.app.mapreduce.am.command</td><td>opts=-Xmx512m (anteriormente -Xmx819m)</td></tr>
<tr><td>mapreduce.task.io.sort</td><td>256MB (anteriormente 200MB)</td></tr>
<tr><td>tez.am.resource.memory</td><td>1536MB (sem alterações)</td></tr>

</table><br>

Para obter mais informações sobre as configurações de memória usadas pelo YARN e pelo MapReduce no Hortonworks Data Platform usado pelo HDInsight, consulte [Determinar as configurações de memória de HDP](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1-latest/bk_installing_manually_book/content/rpm-chap1-11.html). A Hortonworks também oferece uma ferramenta para calcular as configurações adequadas de memória.

Erro do PowerShell do HDInsight/SDK: "*O cluster não foi configurado para acessar os Serviços Http*":

* Esse erro é um [problema de compatibilidade](https://social.msdn.microsoft.com/Forums/azure/en-US/a7de016d-8de1-4385-b89e-d2e7a1a9d927/hdinsight-powershellsdk-error-cluster-is-not-configured-for-http-services-access?forum=hdinsight) conhecido que pode ocorrer devido a uma diferença na versão do SDK/PowerShell e na versão do cluster. Clusters criados em 15 de agosto, ou posteriormente, dão suporte à nova funcionalidade de provisionamento em Redes Virtuais. Mas essa funcionalidade não é interpretada corretamente por versões mais antigas do SDK/PowerShell. O resultado é uma falha em algumas operações de envio de trabalho. Se você usar APIs do SDK ou cmdlets do PowerShell para enviar trabalhos (**Use-AzureHDInsightCluster**, **Invoke-Hive**), essas operações podem falhar com a mensagem de erro "*O cluster <nome_cluster> não está configurado para acessar os Serviços Http*" ou, dependendo da operação, com outras mensagens de erro, como "*Não é possível se conectar ao cluster*".

* Esses problemas de compatibilidade são resolvidos nas versões mais recentes do HDInsight SDK e do Azure PowerShell. Recomendamos atualizar o HDInsight SDK para a versão 1.3.1.6 ou posterior e o Azure PowerShell Tools para a versão 0.8.8 ou posterior. Você pode obter acesso ao HDInsight SDK mais recente no [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started) e ao Azure PowerShell Tools em [Como instalar e configurar o Azure PowerShell](http://azure.microsoft.com/pt-br/documentation/articles/install-configure-powershell/).

* Pode-se esperar que o SDK e o PowerShell continuem a funcionar com as novas atualizações dos clusters, contanto que a versão do cluster permaneça a mesma. Por exemplo, a versão de clusters 3.1 sempre será compatível com a versão atual do SDK/PowerShell 1.3.1.6 e 0.8.8.


## Notas da versão de 12/09/2014 do HDInsight 3.1##

* Esta versão é baseada no Hortonworks Data Platform (HDP) 2.1.5. Para obter uma lista de bugs corrigidos nesta versão, consulte a página [Corrigido nesta versão](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.5/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.5-fixed.html) no site da Hortonworks.
* Na pasta de bibliotecas do Pig, o arquivo "avro-mapred-1.7.4.jar" foi alterado para avro-mapred-1.7.4-hadoop2.jar. Esse arquivo contém uma pequena correção de bug que é não separável. Recomenda-se que os clientes não assumam uma dependência direta no nome do arquivo JAR em si para evitar separações quando os arquivos forem renomeados.


## Notas da versão de 21/08/2014 ##

* Estamos adicionando a nova configuração WebHCat a seguir (HIVE-7155) que define o limite de memória padrão para um trabalho de controlador Templeton como 1 GB (o valor padrão anterior era 512 MB):
	
	* templeton.mapper.memory.mb (=1024)
	* Essa mudança trata do erro a seguir, que determinadas consultas de Hive encontraram devido a limites mais baixos de memória: "O contêiner está sendo executado além dos limites de memória física".
	* Para voltar aos padrões antigos, você pode definir o valor dessa configuração para 512 por meio do SDK do PowerShell no momento da criação do cluster, usando o comando a seguir:
	
		Add-AzureHDInsightConfigValues -Core @{"templeton.mapper.memory.mb"="512";}


* O nome de host da função zookeeper foi alterado para zookeeper. Isso afeta o modo como o nome é resolvido no cluster, mas não afeta as APIs REST externas. Se você possui componentes que utilizam nome de host de nó zookeeper, você precisará atualizá-los com o novo nome, em vez disso. Os novos nomes para os três nós zookeeper são: 
	* zookeeper0 
	* zookeeper1 
	* zookeeper2 
* A matriz de suporte de versão do HBase é atualizada. Somente a versão HDInsight 3.1 (HBase versão 0.98) tem suporte para cargas de trabalho de produção no HBase. A versão 3.0, que estava disponível para visualização, não terá suporte se você prosseguir. Durante o período de transição, os clientes ainda podem criar clusters da versão 3.0. 

## Observações sobre os clusters criados antes de 15/8/2014 ##

Um erro do PowerShell do HDInsight/SDK com a mensagem "O cluster <nome_cluster> não está configurado para acessar Serviços Http" (ou, dependendo da operação, outras mensagens de erro, como: "Não é possível se conectar ao cluster") poderá ser encontrado devido a uma diferença de versões entre o SDK/PowerShell e um cluster. Clusters criados em 15 de agosto, ou posteriormente, dão suporte à nova funcionalidade de provisionamento em Redes Virtuais. Essa funcionalidade não é interpretada corretamente por versões mais antigas do SDK/PowerShell, o que resulta em falhas de operações de envio de trabalhos. Se você usar APIs do SDK ou cmdlets do PowerShell para enviar trabalhos, como Use-AzureHDInsightCluster ou Invoke-AzureHDInsightHiveJob, essas operações podem falhar com uma das mensagens de erro descritas acima.

Esses problemas de compatibilidade são resolvidos nas versões mais recentes do SDK e do Azure PowerShell. Recomendamos atualizar o HDInsight SDK para a versão 1.3.1.6 ou posterior e o Azure PowerShell Tools para a versão 0.8.8 ou posterior. Você pode obter acesso ao HDInsight SDK mais recente no [nuget](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.HDInsight/) e ao Azure PowerShell Tools usando o [Microsoft Web PI](http://go.microsoft.com/?linkid=9811175&clcid=0x409).

Pode-se esperar que o SDK e o PowerShell continuem a funcionar com as novas atualizações dos clusters, contanto que a versão do cluster permaneça a mesma. Por exemplo, a versão de clusters 3.1 sempre será compatível com a versão atual do SDK/PowerShell 1.3.1.6 e 0.8.8.


## Notas da versão de 28/07/2014 ##

* **O HDInsight está disponível em novas regiões**: Com essa versão, expandimos a presença geográfica do HDInsight para três novas regiões. Os clientes que usam o HDInsight agora podem criar clusters nessas regiões. 
	* Ásia Oriental, 
	* Centro-Norte dos EUA e 
	* Centro-Sul dos EUA 
* Com essa versão, o HDInsight v1.6 (HDP1.1, Hadoop 1.0.3) e o HDInsight v2.1 (HDP1.3, Hadoop 1.2) estão sendo removidos do Portal de Gerenciamento do Azure. Você pode continuar a criar clusters Hadoop para essas versões usando cmdlets do HDInsight PowerShell ([New-AzureHDInsightCluster](http://msdn.microsoft.com/pt-br/library/dn593744.aspx)) ou usando o [SDK do HDInsight](http://msdn.microsoft.com/pt-br/library/azure/dn469975.aspx). Consulte a página [Controle de versão do componente HDInsight](http://azure.microsoft.com/pt-br/documentation/articles/hdinsight-component-versioning/) para obter mais informações.
* Alterações ao Hortonworks Data Platform (HDP) nesta versão: 

<table border="1">
<tr><th>HDP</th><th>Alterações</th></tr>
<tr><td>HDP 1.3 / HDI 2.1</td><td>Sem alterações</td></tr>
<tr><td>HDP 2.0 / HDI 3.0</td><td>Sem alterações</td></tr>
<tr><td>HDP 2.1 / HDI 3.1</td><td>zookeeper: ['3.4.5.2.1.3.0-1948'] -> ['3.4.5.2.1.3.2-0002']</td></tr>


</table><br>

## Notas da versão de 24/06/2014 ##

Essa versão contém diversos novos aprimoramentos para o serviço HDInsight: 

* **Disponibilidade do HDP 2.1**: O HDInsight 3.1, que contém o HDP 2.1, agora está disponível de modo geral e é a versão padrão para novos clusters.
* **HBase - Melhorias no Portal de Gerenciamento do Azure**: Estamos tornando os clusters HBase disponíveis na visualização. Agora você pode criar clusters HBase por meio do portal com três cliques.

![](http://i.imgur.com/cmOl5fM.png)

Com o HBase, você pode criar uma variedade de cargas de trabalho em tempo real no HDInsight, por meio de sites interativos que funcionem com conjuntos de dados grandes para dados para o sensor de armazenamento de serviços e dados de telemetria de milhões de pontos de extremidade. A próxima etapa seria analisar os dados nessas cargas de trabalho com trabalhos do Hadoop e isso é possível de imediato no HDInsight, por meio das experiências fornecidas como o painel de cluster do Hive e do PowerShell.

### Agora, o Apache Mahout é pré-instalado no HDInsight 3.1 ###

 [O Mahout](http://hortonworks.com/hadoop/mahout/) é pré-instalado nos clusters do Hadoop do HDInsight 3.1. Desse modo, você pode executar trabalhos do Mahout sem a necessidade de alguma configuração de cluster adicional. Por exemplo, você pode acessar remotamente um cluster do Hadoop usando o Protocolo de Área de trabalho remota (RDP) e, sem etapas adicionais, executar o comando Hello world do Mahout:

		mahout org.apache.mahout.classifier.df.tools.Describe -p /user/hdp/glass.data -f /user/hdp/glass.info -d I 9 N L  

		mahout org.apache.mahout.classifier.df.BreimanExample -d /user/hdp/glass.data -ds /user/hdp/glass.info -i 10 -t 100

Para obter uma explicação mais completa sobre este procedimento, consulte a documentação do [Exemplo Breiman](https://mahout.apache.org/users/classification/breiman-example.html) no site do Apache Mahout. 


### Consultas de Hive podem usar o Tez no HDinsight 3.1 ###

O Hive 0.13 agora está disponível no HDInsight 3.1 e é capaz de executar consultas usando Tez, que pode ser utilizado para melhorias de desempenho significativas. 
O Tez não está habilitado por padrão para consultas de Hive. Para utilizá-lo, você precisa optar por isso. Você pode habilitar o Tez executando o trecho de código a seguir:

		set hive.execution.engine=tez;
		select sc_status, count(*), histogram_numeric(sc_bytes,5) from website_logs_orc_local group by sc_status;

A Hortonworks publicou uma demonstração detalhada das melhorias nas consultas do Hive com o Tez, conforme fornecido em medições de desempenho padrão. Para obter mais informações, consulte [Medindo o desempenho do Hive 13 Apache para Hadoop Enterprise](http://hortonworks.com/blog/benchmarking-apache-hive-13-enterprise-hadoop/). 

Para obter mais detalhes sobre como usar o Hive com o Tez, confira a [página wiki Hive em Tez](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez).

###Disponibilidade global
Com a versão do Azure HDInsight no Hadoop 2.2, a Microsoft disponibilizou o HDInsight em todas as principais regiões geográficas do Azure. Especificamente, os data centers da Europa Ocidental e do Sudeste Asiático foram colocados online. Isso permite que os clientes localizem clusters em um data center próximo e possivelmente em uma zona com requisitos de conformidade semelhantes. 


###Prós e contras entre as versões de clusters

**Os metastores do Oozie usados com um cluster HDInsight 3.1 não são compatíveis com as versões anteriores dos clusters HDInsight 2.1 e não podem ser usados com essa versão anterior**

Um banco de dados do metastore do Oozie personalizado implantado com um cluster do HDInsight 3.1 não pode ser reutilizado com um cluster do HDInsight 2.1. Isso acontece mesmo se o metastore tiver sido originado com um cluster 2.1. Esse cenário não tem suporte, já que o esquema do metastore é atualizado quando utilizado com um cluster 3.1 e, portanto, não é mais compatível com o metastore exigido pelos clusters 2.1. Qualquer tentativa de reutilizar um metastore do Oozie que tenha sido usado com um cluster do HDInsight 3.1 fará com que o cluster 2.1 seja inutilizado. 

**Os metastores do Oozie não podem ser compartilhados entre clusters**
Uma observação mais geral e um pouco ortogonal, metastores do Oozie estão associados a clusters específicos e não podem ser compartilhados entre clusters.

###Alterações de última hora

**Sintaxe de prefixo**:
Apenas a sintaxe "wasb://" tem suporte em clusters HDInsight 3.0 e 3.1. A antiga sintaxe "asv://" tem suporte em clusters HDInsight 2.1 e 1.6, mas não tem suporte em clusters HDInsight 3.0 ou em versões posteriores. Isso significa que todos os trabalhos enviados para um cluster HDInsight 3.0  ou 3.1 que use explicitamente a sintaxe "asv: / /" falhará. Em vez disso, a sintaxe wasb:// deve ser usada. Além disso, os trabalhos enviados para qualquer cluster HDInsight 3.0 ou 3.1, que foram criados com um metastore existente que contenha referências explícitas a recursos usando a sintaxe asv://, falharão. Esses metastores precisarão ser recriados usando o wasb:// para endereçar recursos. 


**Portas**: As portas usadas pelo serviço HDInsight foram alteradas. Os números de porta que estavam sendo usados estavam dentro do intervalo de portas efêmero do SO Windows. As portas são alocadas automaticamente por meio de um intervalo efêmero, para comunicações baseadas em protocolo de Internet de curta duração. O novo conjunto de números de porta de serviço Hortonworks Data Platform (HDP) permitido está agora fora desse intervalo para evitar conflitos que poderiam surgir com as portas usadas pelos serviços que estão sendo executados no headnode. Os novos números de porta não devem causar nenhuma mudança de última hora. Os números usados agora são conforme descrito a seguir:

 **HDInsight 1.6 (HDP 1.1)**
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
* datanucleus: ['rdbms-3.0.8'] -> ['rdbms-3.2.9']
* jasper-compiler: ['5.5.12'] -> ['5.5.23']
* log4j: ['1.2.15', '1.2.16'] -> ['1.2.16', '1.2.17']
* derbyclient: ['10.4.2.0'] -> ['10.10.1.1']
* httpcore: ['4.2.4'] -> ['4.2.5']
* hsqldb: ['1.8.0.10'] -> ['2.0.0']
* jets3t: ['0.6.1'] -> ['0.9.0']
* protobuf-java: ['2.4.1'] -> ['2.5.0']
* derby: ['10.4.2.0'] -> ['10.10.1.1']
* jasper: ['runtime-5.5.12'] -> ['runtime-5.5.23']
* commons-daemon: ['1.0.1'] -> ['1.0.13']
* datanucleus-core: ['3.0.9'] -> ['3.2.10']
* datanucleus-api-jdo: ['3.0.7'] -> ['3.2.6']
* zookeeper: ['3.4.5.1.3.9.0-01320'] -> ['3.4.5.2.1.3.0-1948']
* bonecp: ['0.7.1.RELEASE'] -> ['0.8.0.RELEASE']


###Drivers
O driver JDBC do SQL Server é usado internamente pelo HDInsight e não é usado para operações externas. Se você desejar se conectar ao HDInsight usando o ODBC, use o driver ODBC do Microsoft Hive. Para obter mais informações sobre como usar o ODBC do Hive, consulte [Conectar o Excel ao HDInsight com o driver ODBC do Microsoft Hive][connect-excel-with-hive-ODBC].


### Correções de bug ###

Com esta versão, podemos ter atualizado HDInsight a seguir  (Hortonworks Data Platform - HDP) versões com diversas correções de bugs:

* HDInsight 2.1 (HDP 1.3)
* HDInsight 3.0 (HDP 2.0)
* HDInsight 3.1 (HDP 2.1)


## Notas de versão Hortonworks ##

As notas de versão para os HDPs que são usados pelas versões do cluster HDInsight estão disponíveis nos locais listados a seguir.

* O cluster HDInsight versão 3.1 usa uma distribuição do Hadoop baseada na [Hortonworks Data Platform 2.1.7][hdp-2-1-7]. Esse será o cluster Hadoop padrão criado ao usar o portal do Azure HDInsight após 07/11/2014. Os clusters do HDInsight 3.1 criados antes de 07/11/2014 eram baseados no [Hortonworks Data Platform 2.1.1][hdp-2-1-1] 

* O cluster HDInsight versão 3.0 usa uma distribuição do Hadoop que é baseada no [Hortonworks Data Platform 2.0][hdp-2-0-8].

* O cluster HDInsight versão 2.1 usa uma distribuição do Hadoop que é baseada no [Hortonworks Data Platform 1.3][hdp-1-3-0]. 

* O cluster HDInsight versão 1.6 usa uma distribuição do Hadoop que é baseada no [Hortonworks Data Platform 1.1][hdp-1-1-0]. 

[hdp-2-1-7]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html


[hdinsight-install-spark]: ../hdinsight-hadoop-spark-install/
[hdinsight-r-scripts]: ../hdinsight-hadoop-r-scripts/




<!--HONumber=42-->
