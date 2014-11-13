<properties urlDisplayName="Monitor Hadoop clusters  in HDInsight using the Ambari API" pageTitle="Monitorar clusters do Hadoop no HDInsight usando a API do Ambari | Azure" metaKeywords="" description="Use as APIs do Apache Ambari para provisionar, gerenciar e monitorar clusters do Hadoop. As APIs e ferramentas intuitivas do operador do Ambari ocultam a complexidade do Hadoop." services="hdinsight" documentationCenter="" title="Monitorar clusters Hadoop no HDInsight usando a API da Ambari" umbracoNaviHide="0" disqusComments="1" authors="jgao" editor="cgronlun" manager="paulettm" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />

# Monitorar clusters Hadoop no HDInsight usando a API da Ambari

Saiba como monitorar os clusters do HDInsight versões 3.1 e 2.1 usando APIs do Ambari.

**Tempo estimado para conclusão:** 15 minutos

## Neste artigo

-   [O que é Ambari?][O que é Ambari?]
-   [Pré-requisitos][Pré-requisitos]
-   [Iniciar rapidamente][Iniciar rapidamente]
-   [APIs de monitoramento da Ambari][APIs de monitoramento da Ambari]
-   [Próximas etapas][Próximas etapas]

## <span id="whatisambari"></span></a> O que é Ambari?

[Apache Ambari][Apache Ambari] para provisionar, gerenciar e monitorar clusters do Apache Hadoop. Inclui uma coleção de ferramentas intuitivas para operador e um conjunto abrangente de APIs que ocultam a complexidade do Hadoop, simplificando a operação de clusters. Para obter mais informações sobre as APIs, consulte [referência de API do Ambari][referência de API do Ambari].

Atualmente, o HDInsight oferece suporte apenas ao recurso de monitoramento da Ambari. A API da Ambari v1.0 tem suporte do cluster HDInsight versão 2.1 e 3.0. Este artigo aborda o acesso às APIs do Ambari em clusters do HDInsight versões 3.1 e 2.1. A principal diferença entre os dois é que alguns dos componentes foram alterados com a introdução de novos recursos (como o Servidor de histórico de trabalho).

## <span id="prerequisites"></span></a>Pré-requisitos

Antes de começar este tutorial, você deve ter o seguinte:

-   **Uma estação de trabalho** com o PowerShell do Azure instalado e configurado. Para obter instruções, consulte [Instalar e configurar o PowerShell do Azure][Instalar e configurar o PowerShell do Azure]. Para executar scripts do PowerShell, você deve executar o PowerShell do Azure como administrador e configurar a política de execução como *RemoteSigned*. Consulte [Executar scripts do Windows PowerShell][Executar scripts do Windows PowerShell].

    [Curl][Curl] é opcional. Pode ser instalado [aqui][aqui].

    > [WACOM.NOTE] Quando usar o comando curl no Windows, use aspas duplas em vez de aspas simples para os valores de opção.

-   **Um cluster Azure HDInsight**. Para obter informações sobre como provisionar um cluster, consulte [Introdução ao HDInsight][Introdução ao HDInsight] ou [Provisionar clusters HDInsight][Provisionar clusters HDInsight]. Você precisará dos seguintes dados para percorrer o tutorial:

    <table>
    <colgroup>
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">Propriedade do cluster</th>
    <th align="left">Nome de variável do PowerShell</th>
    <th align="left">Valor</th>
    <th align="left">Descrição</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left">Nome do cluster HDInsight</td>
    <td align="left">$clusterName</td>
    <td align="left"></td>
    <td align="left">O nome do seu cluster HDInsight.</td>
    </tr>
    <tr class="even">
    <td align="left">Nome de usuário do cluster</td>
    <td align="left">$clusterUsername</td>
    <td align="left"></td>
    <td align="left">Nome de usuário do cluster especificado na provisão.</td>
    </tr>
    <tr class="odd">
    <td align="left">Senha do cluster</td>
    <td align="left">$clusterPassword</td>
    <td align="left"></td>
    <td align="left">Senha do usuário do cluster.</td>
    </tr>
    </tbody>
    </table>

    > [WACOM.NOTE] Preencha os valores nas tabelas. Isso poderá ser útil para percorrer este tutorial.

## <span id="jumpstart"></span></a>Iniciar rapidamente

Há várias maneiras de usar a Ambari para monitorar os clusters HDInsight.

**Usar PowerShell do Azure**

Segue abaixo um script do PowerShell para obter as informações sobre o jobtracker do MapReduce *em um cluster 3.1.* A principal diferença é que esses detalhes agora serão extraídos do serviço YARN (e não do MapReduce).

    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterPassword>"

    $ambariUri = "https://$clusterName.azurehdinsight.net:443/ambari"
    $uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName.azurehdinsight.net/services/yarn/components/resourcemanager"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    $response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus 

    $response.metrics.'yarn.queueMetrics'

Segue abaixo um script do PowerShell para obter as informações sobre o jobtracker do MapReduce *em um cluster 2.1*:

    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterPassword>"

    $ambariUri = "https://$clusterName.azurehdinsight.net:443/ambari"
    $uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName.azurehdinsight.net/services/mapreduce/components/jobtracker"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    $response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus 

    $response.metrics.'mapred.JobTracker'

A saída é:

![Saída de jobtracker][Saída de jobtracker]

**Usar curl**

A seguir está um exemplo para obter informações sobre cluster usando Curl:

    curl -u <username>:<password> -k https://<ClusterName>.azurehdinsight.net:443/ambari/api/v1/clusters/<ClusterName>.azurehdinsight.net

A saída é:

    {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/",
     "Clusters":{"cluster_name":"hdi0211v2.azurehdinsight.net","version":"2.1.3.0.432823"},
     "services"[
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/services/hdfs",
        "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.net","service_name":"hdfs"}},
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/services/mapreduce",
        "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.net","service_name":"mapreduce"}}],
     "hosts":[
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/hosts/headnode0",
        "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.net",
                 "host_name":"headnode0"}},
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/hosts/workernode0",
        "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.net",
                 "host_name":"headnode0.{ClusterDNS}.azurehdinsight.net"}}]}

Nota para o lançamento de 08/10/2014:
Ao usar o ponto de extremidade do Ambari, "https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname}", agora, o campo *host\_name* retorna o nome de domínio totalmente qualificado (FQDN) do nó em vez do nome do host. Antes da versão de 8/10/2014, esse exemplo retornava simplesmente "**headnode0**". Após 8/10/2014, o FQDN “**headnode0.{ClusterDNS}.azurehdinsight.net**” passou a ser retornado, como mostrado no exemplo acima. Essa alteração foi necessária para facilitar cenários em que vários tipos de cluster, como HBase e Hadoop, podem ser implementados em uma rede virtual (VNET). Isso acontece, por exemplo, ao usar HBase como uma plataforma de back-end para o Hadoop.

## <span id="monitor"></span></a>APIs de monitoramento da Ambari

A tabela a seguir lista algumas das chamadas de API para monitoramento da Ambari. Para obter mais informações sobre a API, consulte [referência de API do Ambari][referência de API do Ambari].

<table border="1">
<tr>
<th>
Monitorar a chamada de API

</th>
<th>
URI

</th>
<th>
Descrição

</th>
</tr>
<tr>
<td>
Obter clusters

</td>
<td>
`/api/v1/clusters`

</td>
<td>
</td>
</tr>
<tr>
<td>
Obter informações do cluster.

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net`

</td>
<td>
clusters, serviços, hosts

</td>
</tr>
<tr>
<td>
Obter serviços

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services`

</td>
<td>
Os serviços incluem: hdfs, mapreduce

</td>
</tr>
<tr>
<td>
Obter informações dos serviços.

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>`

</td>
<td>
</td>
</tr>
<tr>
<td>
Obter componentes do serviço

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>/components`

</td>
<td>
HDFS: namenode, datanode
MapReduce: jobtracker; tasktracker

</td>
</tr>
<tr>
<td>
Obter informações do componente.

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>/components/<ComponentName>`

</td>
<td>
ServiceComponentInfo, host-components, métricas

</td>
</tr>
<tr>
<td>
Obter hosts

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts`

</td>
<td>
headnode0, workernode0

</td>
</tr>
<tr>
<td>
Obter informações do host.

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName> `

<td>
</td>
</tr>
<tr>
<td>
Obter componentes do host

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>/host_components`

</td>
<td>
namenode, resourcemanager

</td>
</tr>
<tr>
<td>
Obter informações de componente do host.

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>/host_components/<ComponentName>`

</td>
<td>
HostRoles, componente, host, métrica

</td>
</tr>
<tr>
<td>
Obter configurações

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net/configurations `

</td>
<td>
Tipos de configuração: core-site, hdfs-site, mapred-site, hive-site

</td>
</tr>
<tr>
<td>
Obter informações de configuração.

</td>
<td>
`/api/v1/clusters/<ClusterName>.azurehdinsight.net/configurations?type=<ConfigType>&tag=<VersionName> `

</td>
<td>
Tipos de configuração: core-site, hdfs-site, mapred-site, hive-site

</td>
</tr>
</table>
## <span id="nextsteps"></span></a>Próximas etapas

Você aprendeu como usar as chamadas de API para monitoramento da Ambari. Para obter mais informações, consulte:

-   [Administrar clusters HDInsight usando o Portal de Gerenciamento][Administrar clusters HDInsight usando o Portal de Gerenciamento]
-   [Administrar clusters HDInsight usando o PowerShell do Azure][Administrar clusters HDInsight usando o PowerShell do Azure]
-   [Administrar clusters HDInsight usando a interface de linha de comando][Administrar clusters HDInsight usando a interface de linha de comando]
-   [Documentação do HDInsight][Documentação do HDInsight]
-   [Introdução ao HDInsight][Introdução ao HDInsight]

  [O que é Ambari?]: #whatisambari
  [Pré-requisitos]: #prerequisites
  [Iniciar rapidamente]: #jumpstart
  [APIs de monitoramento da Ambari]: #monitor
  [Próximas etapas]: #nextsteps
  [Apache Ambari]: http://ambari.apache.org/
  [referência de API do Ambari]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md
  [Instalar e configurar o PowerShell do Azure]: ../install-configure-powershell/
  [Executar scripts do Windows PowerShell]: http://technet.microsoft.com/pt-br/library/ee176949.aspx
  [Curl]: http://curl.haxx.se
  [aqui]: http://curl.haxx.se/download.html
  [Introdução ao HDInsight]: ../hdinsight-get-started/
  [Provisionar clusters HDInsight]: ../hdinsight-provision-clusters/
  [Saída de jobtracker]: ./media/hdinsight-monitor-use-ambari-api/hdi.ambari.monitor.jobtracker.output.png
  [Administrar clusters HDInsight usando o Portal de Gerenciamento]: ../hdinsight-administer-use-management-portal/
  [Administrar clusters HDInsight usando o PowerShell do Azure]: ../hdinsight-administer-use-powershell/
  [Administrar clusters HDInsight usando a interface de linha de comando]: ../hdinsight-administer-use-command-line/
  [Documentação do HDInsight]: /pt-br/documentation/services/hdinsight/
