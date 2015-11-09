<properties
   pageTitle="Monitorar e gerenciar clusters HDInsight usando a API REST do Apache Ambari | Microsoft Azure"
   description="Aprenda a usar o Ambari para monitorar e gerenciar clusters HDInsight baseados em Linux. Neste documento, você aprenderá a usar a API REST do Ambari incluída com clusters HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/27/2015"
   ms.author="larryfr"/>

#Gerenciar clusters HDInsight usando a API REST do Ambari

[AZURE.INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

O Apache Ambari simplifica o gerenciamento e monitoramento de um cluster Hadoop, fornecendo uma forma fácil de usar a interface do usuário da Web e a API REST. O Ambari está incluído em clusters HDInsight baseados em Linux e é usado para monitorar o cluster e fazer alterações de configuração. Neste documento, você aprenderá os fundamentos de como trabalhar com a API REST do Ambari realizando tarefas comuns, como encontrar o nome de domínio totalmente qualificado dos nós do cluster ou localizar a conta de armazenamento padrão usada pelo cluster.

> [AZURE.NOTE]As informações deste artigo só se aplicam a clusters HDInsight baseados em Linux. Para os clusters HDInsight baseados no Windows, apenas um subconjunto da funcionalidade de monitoramento está disponível pela API REST do Ambari. Consulte [Monitorar Hadoop baseado em Windows no HDInsight usando a API do Ambari](hdinsight-monitor-use-ambari-api.md).

##Pré-requisitos

* [cURL](http://curl.haxx.se/): o cURL é um utilitário de plataforma cruzada que pode ser usado para trabalhar com APIs REST na linha de comando. Neste documento, ele é usado para se comunicar com a API REST do Ambari.
* [jq](https://stedolan.github.io/jq/): o jq é um utilitário de linha de comando de plataforma cruzada para trabalhar com documentos JSON. Neste documento, ele é usado para analisar os documentos JSON retornados da API REST do Ambari.

##<a id="whatis"></a>O que é o Ambari?

O <a href="http://ambari.apache.org" target="_blank">Apache Ambari</a> simplifica o gerenciamento do Hadoop, fornecendo uma IU da Web fácil de usar que pode ser utilizada para provisionar, gerenciar e monitorar clusters Hadoop. Os desenvolvedores podem integrar esses recursos em seus aplicativos usando as <a href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md" target="_blank">APIs REST do Ambari</a>.

Ambari é fornecido por padrão com os clusters HDInsight baseados em Linux.

##API REST

O URI de base para a API REST do Ambari no HDInsight é https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME, no qual __NOMEDOCLUSTER__ é o nome do cluster.

> [AZURE.IMPORTANT]A conexão com o Ambari no HDInsight exige HTTPS. Você também deve autenticar no Ambari usando o nome da conta de administrador (o padrão é __admin__) e a senha fornecidos durante a criação do cluster.

Veja a seguir um exemplo do uso de cURL para executar uma solicitação GET na API REST:

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME"
    
Se você executar esse exemplo substituindo __SENHA__ pela senha de administrador do cluster e __NOMEDOCLUSTER__ pelo nome do cluster, você receberá um documento JSON que começa com informações semelhantes às seguintes:

    {
    "href" : "http://10.0.0.10:8080/api/v1/clusters/CLUSTERNAME",
    "Clusters" : {
        "cluster_id" : 2,
        "cluster_name" : "CLUSTERNAME",
        "health_report" : {
        "Host/stale_config" : 0,
        "Host/maintenance_state" : 0,
        "Host/host_state/HEALTHY" : 7,
        "Host/host_state/UNHEALTHY" : 0,
        "Host/host_state/HEARTBEAT_LOST" : 0,
        "Host/host_state/INIT" : 0,
        "Host/host_status/HEALTHY" : 7,
        "Host/host_status/UNHEALTHY" : 0,
        "Host/host_status/UNKNOWN" : 0,
        "Host/host_status/ALERT" : 0

Como se trata de JSON, normalmente é mais fácil usar um analisador JSON para recuperar dados. Por exemplo, se você quiser recuperar uma contagem de alertas (contidas no elemento __"Host/host\_status/ALERT"__) você poderá usar o seguinte para acessar diretamente o valor:

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME" | jq '.Clusters.health_report."Host/host_status/ALERT"'
    
Isso recupera o documento JSON e redireciona a saída para jq. `'.Clusters.health_report."Host/host_status/ALERT"'` indica o elemento dentro do documento JSON que você deseja recuperar.

> [AZURE.NOTE]O elemento __Host/host\_status/ALERT__ está entre aspas para indicar que '/' faz parte do nome do elemento. Para saber mais sobre como usar jq, consulte o [site do jq](https://stedolan.github.io/jq/).

##Exemplo: obter o FQDN de nós do cluster

Ao trabalhar com o HDInsight, você precisará saber o nome de domínio totalmente qualificado (FQDN) de um nó do cluster. Você pode recuperar facilmente o FQDN para vários nós no cluster usando o seguinte:

* __Nós de cabeçalho__:`curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'`
* __Nós de trabalho__: `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/DATANODE" | jq '.host_components[].HostRoles.host_name'`
* __Nós do Zookeeper__: `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq '.host_components[].HostRoles.host_name'`

Observe que cada um deles segue o mesmo padrão de consulta de um componente o qual sabemos que é executado nesses nós, recuperando assim os elementos `host_name` que contêm o FQDN desses nós.

O elemento `host_components` do documento de retorno contém vários itens. O uso do `.host_components[]` e, em seguida, a especificação de um caminho dentro do elemento causará um loop por cada item e extrairá o valor do caminho específico. Caso você queira apenas um valor, como a primeira entrada de FQDN, você poderá retornar os itens como uma coleção e selecionar uma entrada específica:

    jq '[.host_components[].HostRoles.host_name][0]'

Isso retornará o primeiro FQDN da coleção.

##Exemplo: obter a conta de armazenamento padrão e um contêiner

Quando você criar um cluster HDInsight, será necessário usar uma conta do Armazenamento do Azure e um contêiner de blob como o armazenamento padrão para o cluster. Você pode usar o Ambari para recuperar essas informações após a criação do cluster. Por exemplo, se você quiser gravar programaticamente os dados direto no contêiner.

O exemplo a seguir recuperará o URI WASB do armazenamento padrão de clusters:

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
    
> [AZURE.NOTE]Isso retornará a primeira configuração aplicada ao servidor (`service_config_version=1`) que conterá essas informações. Se você estiver recuperando um valor que foi modificado após a criação do cluster, talvez seja necessário listar as versões de configuração e recuperar a mais recente.

Isso retornará um valor semelhante ao seguinte, no qual __CONTÊINER__ é o contêiner padrão e __NOMEDACONTA__ é o nome da conta de Armazenamento do Azure:

    wasb://CONTAINER@ACCOUNTNAME.blob.core.windows.net

Você pode usar essas informações com a [CLI do Azure](../xplat-cli-install.md) para carregar ou baixar dados do contêiner. Por exemplo:

1. Obter o grupo de recursos para a conta de armazenamento. Substitua __NOMEDACONTA__ pelo nome da conta de armazenamento recuperada do Ambari:

        azure storage account list --json | jq '.[] | select(.name=="ACCOUNTNAME").resourceGroup'
    
    Isso retornará o nome do grupo de recursos para a conta.
    
2. Obtenha a chave da conta de armazenamento. Substitua __NOMEDOGRUPO__ pelo Grupo de recursos criado na seção anterior: Substitua __NOMEDACONTA__ pelo nome da conta de armazenamento:

        azure storage account keys list -g GROUPNAME ACCOUNTNAME --json | jq '.storageAccountKeys.key1'

    Isso retornará a chave primária da conta.
    
3. Use o comando Carregar para armazenar um arquivo no contêiner:

        azure storage blob upload -a ACCOUNTNAME -k ACCOUNTKEY -f FILEPATH --container __CONTAINER__ -b BLOBPATH
        
    Substitua __NOMEDACONTA__ pelo nome da conta de armazenamento. Substitua __CHAVEDACONTA__ pela chave recuperada anteriormente. __CAMINHODOARQUIVO__ é o caminho para o arquivo que você deseja carregar, enquanto __CAMINHODOBLOB__ é o caminho no contêiner.

    Por exemplo, se você quiser exibir o arquivo no HDInsight em wasb://example/data/filename.txt, __CAMINHODOBLOB__ deve ser `example/data/filename.txt`.

##Próximas etapas

Para obter uma referência completa da API REST, consulte [Referência de API do Ambari, V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

> [AZURE.NOTE]Algumas funcionalidades do Ambari estão desabilitadas, já que ele é gerenciado pelo serviço de nuvem HDInsight; por exemplo, adicionar ou remover hosts do cluster ou adicionar novos serviços.

<!---HONumber=Nov15_HO1-->