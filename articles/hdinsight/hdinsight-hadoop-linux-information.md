<properties
   pageTitle="Dicas para usar Hadoop no HDInsight baseado em Linux | Microsoft Azure"
   description="Obtenha dicas de implementação para usar clusters do HDInsight baseados em Linux (Hadoop) em um ambiente Linux familiar, em execução na nuvem do Azure."
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
   ms.date="08/12/2015"
   ms.author="larryfr"/>

# Informações sobre o uso do HDInsight no Linux

Os clusters Azure HDInsight baseados em Linux fornecem Hadoop em um ambiente Linux conhecido, executados na nuvem do Azure. Para a maioria da coisas, ele deve funcionar exatamente como qualquer outra instalação do Hadoop no Linux. Este documento indica diferenças específicas que você deve estar atento.

## Nomes de domínio

O FQDN (nome de domínio totalmente qualificado) a usar ao se conectar ao cluster é **&lt;clustername>.azurehdinsight.net** ou (somente para SSH) **&lt;clustername-ssh>.azurehdinsight.net**.

## Acesso remoto aos serviços

* **Ambari (web)** - https://&lt;clustername>.azurehdinsight.net

	Autentique usando o usuário e a senha de administrador do cluster e faça logon no Ambari. Aqui também o usuário e a senha de administrador de cluster são usados.

	A autenticação é texto sem formatação - sempre usar HTTPS para ajudar a garantir que a conexão seja segura.

	> [AZURE.IMPORTANT]Enquanto o Ambari para seu cluster possa ser acessado diretamente pela Internet, algumas funcionalidades se baseiam no acesso de nós pelo nome de domínio interno usado pelo cluster. Como é um nome de domínio interno, e não público, você receberá erros de "servidor não encontrado" ao tentar acessar alguns recursos pela Internet.
	>
	> Para usar a funcionalidade completa da interface do usuário do Ambari Web, use um túnel SSH para tráfego Web de proxy para nó de cabeçalho do cluster. Consulte [Usar o Túnel SSH para acessar a interface do usuário da Web do Ambari, ResourceManager, JobHistory, NameNode, Oozie, entre outras](hdinsight-linux-ambari-ssh-tunnel.md)

* **Ambari (REST)** - https://&lt;clustername>.azurehdinsight.net/ambari

	> [AZURE.NOTE]Autentique usando o usuário e a senha de administrador do cluster.
	>
	> A autenticação é texto sem formatação - sempre usar HTTPS para ajudar a garantir que a conexão seja segura.

* **WebHCat (Templeton)** - https://&lt;clustername>.azurehdinsight.net/templeton

	> [AZURE.NOTE]Autentique usando o usuário e a senha de administrador do cluster.
	>
	> A autenticação é texto sem formatação - sempre usar HTTPS para ajudar a garantir que a conexão seja segura.

* **SSH** - &lt;clustername>-ssh.azurehdinsight.net na porta 22 ou 23. A porta 22 é usada para a conexão ao headnode0, enquanto a 23 é usada para a conexão ao headnode1. Para obter mais informações sobre nós de cabeçalho, confira [Disponibilidade e confiabilidade de clusters Hadoop no HDInsight](hdinsight-high-availability-linux.md).

	> [AZURE.NOTE]Você só pode acessar os nós de cabeçalho do cluster por meio de SSH de uma máquina cliente. Uma vez conectado, você pode acessar os nós de trabalho usando SSH no nó de cabeçalho.

## Locais de arquivos

Arquivos relacionados ao Hadoop encontram-se nos nós de cluster em `/usr/hdp`. O diretório raiz contém os seguintes subdiretórios:

* __2.2.4.9-1__: este diretório é nomeado de acordo com a versão do Hortonworks Data Platform usada pelo HDInsight e, portanto, o número em seu cluster pode ser diferente daquele listado aqui.
* __atual__: este diretório contém links para os diretórios sob o diretório __2.2.4.9-1__ e existe para que você não precise digitar um número de versão (que pode ser alterado) sempre que quiser acessar um arquivo.

Dados de exemplo e arquivos JAR encontram-se no HDFS (Sistema de Arquivos Distribuído do Hadoop) ou no armazenamento de Blob do Azure em '/example' ou 'wasb:///example'.

## HDFS, armazenamento de Blob do Azure e práticas recomendadas de armazenamento

Na maioria das distribuições do Hadoop, o HDFS é conta com o suporte do armazenamento local nos computadores do cluster. Embora isso seja eficiente, pode ser dispendioso para uma solução baseada em nuvem onde você é cobrado por hora por recursos de computação.

O HDInsight usa o armazenamento de Blob do Azure como armazenamento padrão, o que proporciona os seguintes benefícios:

* Armazenamento de longo prazo econômico

* Acessibilidade por meio de serviços externos, como sites, utilitários de upload/download de arquivos, SDKs para vários idiomas e navegadores da Web

Como é o armazenamento padrão para o HDInsight, você normalmente não precisa fazer nada para usá-lo. Por exemplo, o comando a seguir listará arquivos na pasta **/example/data**, armazenada no armazenamento de Blob do Azure:

	hadoop fs -ls /example/data

Alguns comandos podem exigir que você especifique o uso do armazenamento de Blob. Nesses casos, você poderá prefixar o comando com ****WASB://**.

O HDInsight também permite que você associe várias contas de armazenamento de Blob a um cluster. Para acessar dados em uma conta de armazenamento de Blob não padrão, você pode usar o formato **WASB://&lt;container-name>@&lt;account-name>.blob.core.windows.net/**. Por exemplo, o comando a seguir listará o conteúdo do diretório **/example/data** para o contêiner e a conta de armazenamento de Blob especificados:

	hadoop fs -ls wasb://mycontainer@mystorage.blob.core.windows.net/example/data

### Que armazenamento de Blob o cluster está usando?

Durante a criação do cluster, você optou por usar uma conta e um contêiner existentes do Armazenamento do Azure ou por criar uma nova conta. Em seguida, você provavelmente esquece dela. Você pode a API REST do Ambari para localizar a conta e o contêiner de armazenamento padrão.

1. Use o comando a seguir para recuperar informações de configuração do HDFS:

        curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1"

2. Nos dados JSON retornados, localize a entrada `fs.defaultFS`. Ela conterá o contêiner e a conta de armazenamento padrão em um formato semelhante ao seguinte:

        wasb://CONTAINTERNAME@STORAGEACCOUNTNAME.blob.core.windows.net

	> [AZURE.TIP]Se você tiver instalado o [jq](http://stedolan.github.io/jq/), poderá usar o seguinte para retornar apenas a entrada `fs.defaultFS`:
	>
	> `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'`

3. Para localizar a chave usada para autenticar a conta de armazenamento ou para localizar quaisquer contas de armazenamento secundárias associadas ao cluster, use o seguinte:

		curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1"

4. Nos dados retornados do JSON, localize as entradas que começam com `fs.azure.account.key`. O restante do nome da entrada é o nome da conta de armazenamento. Por exemplo: `fs.azure.account.key.mystorage.blob.core.windows.net`. O valor armazenado nesta entrada é a chave usada para autenticar na conta de armazenamento.

	> [AZURE.TIP]Se tiver instalado o [jq](http://stedolan.github.io/jq/), você poderá usar o seguinte para retornar uma lista das chaves e dos valores:
	>
	> `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties as $in | $in | keys[] | select(. | contains("fs.azure.account.key.")) as $item | $item | ltrimstr("fs.azure.account.key.") | { storage_account: ., storage_account_key: $in[$item] }'`

Você também pode encontrar as informações de armazenamento usando o portal de visualização do Azure:

1. No [Portal de Visualização do Azure](https://portal.azure.com/), selecione o cluster HDInsight.

2. Na seção __Fundamentos__, selecione __Todas as configurações__.

3. Em __Configurações__, selecione __Chaves de Armazenamento do Azure__.

4. Em __Chaves de Armazenamento do Azure__, selecione uma das contas de armazenamento listadas. Isso exibirá informações sobre a conta de armazenamento.

5. Selecione o ícone de chave. Isso exibirá as chaves para esta conta de armazenamento.

### Como acessar o armazenamento de Blob?

Diferente do comando Hadoop do cluster, há várias maneiras de acessar blobs:

* [CLI do Azure para Mac, Linux e Windows](../xplat-cli.md): comandos de interface de linha de comando para trabalhar com o Azure. Depois de instalar, use o comando `azure storage` para obter ajuda sobre o uso do armazenamento ou `azure blob` para comandos específicos do blob.

* [blobxfer.py](https://github.com/Azure/azure-batch-samples/tree/master/Python/Storage): um script Python para trabalhar com blobs no Armazenamento do Azure.

* Uma variedade de SDKs:

	* [Java](https://github.com/Azure/azure-sdk-for-java)

	* [Node.js](https://github.com/Azure/azure-sdk-for-node)

	* [PHP](https://github.com/Azure/azure-sdk-for-php)

	* [Python](https://github.com/Azure/azure-sdk-for-python)

	* [Ruby](https://github.com/Azure/azure-sdk-for-ruby)

	* [.NET](https://github.com/Azure/azure-sdk-for-net)

* [API REST de armazenamento](https://msdn.microsoft.com/library/azure/dd135733.aspx)

##<a name="scaling"></a>Dimensionar o cluster

O recurso de dimensionamento de clusters permite que você altere o número de nós de dados usado por um cluster em execução no Azure HDInsight sem precisar excluir e recriar o cluster.

Você pode executar operações de dimensionamento enquanto outros trabalhos ou processos estão sendo executados em um cluster.

Os diferentes tipos de cluster são afetados pelo dimensionamento da seguinte maneira:

* __Hadoop__: ao reduzir o número de nós em um cluster, alguns dos serviços no cluster são reiniciados. Isso pode fazer com que todos os trabalhos em execução ou pendentes falhem após a conclusão da operação de dimensionamento. Você pode reenviar os trabalhos quando a operação for concluída.

* __HBase__: servidores regionais são balanceados automaticamente em alguns minutos após o término da operação de dimensionamento. Para balancear manualmente servidores regionais, use as seguintes etapas:

	1. Conecte-se ao cluster HDInsight usando SSH. Para obter mais informações sobre como usar SSH com o HDInsight, consulte um dos seguintes documentos:

		* [Usar SSH com o HDInsight no Linux, Unix e Mac OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

		* [Usar SSH com o HDInsight no Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

	1. Use o seguinte para iniciar o shell do HBase:

			hbase shell

	2. Depois que o shell do HBase tiver sido carregado, use o seguinte para balancear manualmente os servidores regionais:

			balancer

* __Storm__: deve rebalancear quaisquer topologias Storm em execução após uma operação de dimensionamento ter sido realizada. Isso permite que a topologia reajuste as configurações de paralelismo com base no novo número de nós no cluster. Para rebalancear topologias em execução, use uma das seguintes opções:

	* __SSH__: conecte-se ao servidor e use o seguinte comando para rebalancear uma topologia:

			storm rebalance TOPOLOGYNAME

		Você também pode especificar parâmetros para substituir as dicas de paralelismo fornecidas originalmente pela topologia. Por exemplo, `storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10` reconfigurará a topologia para 5 processos de trabalho, 3 executores para o componente do jato azul e 10 executores para o componente do raio amarelo.

	* __Interface do usuário do Storm__: use as etapas a seguir para rebalancear uma topologia que usa a interface do usuário do Storm.

		1. [Crie um túnel SSH para o cluster e abra a interface do usuário da Web do Ambari](hdinsight-linux-ambari-ssh-tunnel.md).

		2. Na lista de serviços à esquerda da página, selecione __Storm__. Então, selecione __Interface do Usuário do Storm__ em __Links Rápidos__.

			![Entrada de interface do usuário do Storm em links rápidos](./media/hdinsight-hadoop-linux-information/ambari-storm.png)

			Isso exibirá a interface do usuário do Storm:

			![a interface do usuário do storm](./media/hdinsight-hadoop-linux-information/storm-ui.png)

		3. Selecione a topologia que você deseja rebalancear e, em seguida, selecione o botão __Rebalancear__. Insira o atraso antes de a operação de rebalanceamento ser executada.

Para obter informações específicas sobre como dimensionar o cluster HDInsight, consulte:

* [Gerenciar clusters Hadoop no HDInsight usando o portal de visualização do Azure](hdinsight-administer-use-portal-linux.md#scaling)

* [Gerenciar clusters Hadoop no HDInsight Usando o Azure PowerShell](hdinsight-administer-use-command-line.md#scaling)

## Como instalo o Hue (ou outro componente do Hadoop)?

HDInsight é um serviço gerenciado, o que significa que nós em um cluster poderão ser destruídos e reprovisionados automaticamente pelo Azure se um problema for detectado. Por isso, não é recomendável instalar manualmente as coisas diretamente em nós de cluster. Em vez disso, use [Ações de Script do HDInsight](hdinsight-hadoop-customize-cluster.md) quando você precisar instalar o seguinte:

* Um serviço ou site da Web como Spark ou Hue.
* Um componente que requer alterações de configuração em vários nós no cluster. Por exemplo, uma variável de ambiente necessária, a criação de um diretório de log ou a criação de um arquivo de configuração.

Ações de Script são scripts Bash executados durante o provisionamento do cluster e que podem ser usados para instalar e configurar componentes adicionais no cluster. São fornecidos scripts de exemplo para instalar os seguintes componentes:

* [Hue](hdinsight-hadoop-hue-linux.md)
* [Giraph](hdinsight-hadoop-giraph-install-linux.md)
* [R](hdinsight-hadoop-r-scripts-linux.md)
* [Solr](hdinsight-hadoop-solr-install-linux.md)
* [Spark](hdinsight-hadoop-spark-install-linux.md)

Para obter informações sobre como desenvolver suas próprias ações de Script, consulte [Desenvolvimento de Ação de Script com o HDInsight](hdinsight-hadoop-script-actions-linux.md).

###Arquivos Jar

Algumas tecnologias Hadoop são fornecidas em arquivos jar independentes que contêm funções usadas como parte de um trabalho MapReduce, ou de dentro de Pig ou Hive. Embora elas possam ser instaladas usando Ações de Script, elas geralmente não exigem nenhuma configuração e podem apenas ser carregadas no cluster após o provisionamento e usadas diretamente. Se você deseja certificar-se que o componente resistirá ao refazer a imagem do cluster, você pode armazenar o arquivo jar em WASB.

Por exemplo, se você quer usar a versão mais recente do [DataFu](http://datafu.incubator.apache.org/), você pode baixar um jar que contém o projeto e carregá-lo ao cluster HDInsight. Siga a documentação do DataFu sobre como usá-lo do Pig ou Hive.

> [AZURE.IMPORTANT]Alguns componentes que são arquivos jar autônomos são fornecidos com o HDInsight, mas não estão no caminho. Se você estiver procurando por um componente específico, você pode usar o acompanhamento para procurá-lo em seu cluster:
>
> ```find / -name *componentname*.jar 2>/dev/null```
>
> Isso retornará o caminho de quaisquer arquivos jar correspondentes.

Se o cluster já fornece uma versão de um componente como um arquivo jar independente mas você deseja usar uma versão diferente, você pode carregar uma nova versão do componente no cluster e tentar usá-la em seus trabalhos.

> [AZURE.WARNING]Há suporte total a componentes fornecidos com o cluster HDInsight e o Suporte da Microsoft ajudará a isolar e resolver problemas relacionados a esses componentes.
>
> Componentes personalizados recebem suporte comercialmente razoável para ajudá-lo a solucionar o problema. Isso pode resultar na resolução do problema ou na solicitação de você buscar nos canais disponíveis as tecnologias de código-fonte aberto, onde é possível encontrar conhecimento aprofundado sobre essa tecnologia. Por exemplo, há muitos sites de comunidades que podem ser usados, como o [Fórum do MSDN para o HDInsight](https://social.msdn.microsoft.com/Forums/azure/pt-BR/home?forum=hdinsight) e [http://stackoverflow.com](http://stackoverflow.com). Além disso, projetos Apache têm sites de projetos em [http://apache.org](http://apache.org), por exemplo: [Hadoop](http://hadoop.apache.org/) e [Spark](http://spark.apache.org/).

## Próximas etapas

* [Usar o Hive com o HDInsight](hdinsight-use-hive.md)
* [Usar o Pig com o HDInsight](hdinsight-use-pig.md)
* [Usar trabalhos do MapReduce com o HDInsight](hdinsight-use-mapreduce.md)

<!---HONumber=Sept15_HO4-->