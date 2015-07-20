<properties
   pageTitle="Dicas para usar Hadoop no HDInsight baseado em Linux | Microsoft Azure"
   description="Obtenha dicas de implementação para usar clusters do HDInsight baseados em Linux (Hadoop) em um ambiente Linux familiar, em execução na nuvem do Azure."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="05/27/2015"
   ms.author="larryfr"/>

# Informações sobre o uso do HDInsight no Linux (visualização)

Os clusters Azure HDInsight baseados em Linux fornecem Hadoop em um ambiente Linux conhecido, executados na nuvem do Azure. Para a maioria da coisas, ele deve funcionar exatamente como qualquer outra instalação do Hadoop no Linux. Este documento indica diferenças específicas que você deve estar atento.

## Nomes de domínio

O FQDN (nome de domínio totalmente qualificado) a usar ao se conectar ao cluster é **&lt;clustername>.azurehdinsight.net** ou (somente para SSH) **&lt;clustername-ssh>.azurehdinsight.net**.

## Acesso remoto aos serviços

* **Ambari (web)** - https://&lt;clustername>.azurehdinsight.net

	> [AZURE.NOTE]Autentique usando o usuário e a senha de administrador do cluster e faça logon no Ambari. Aqui também o usuário e a senha de administrador de cluster são usados.
	>
	> A autenticação é texto sem formatação - sempre usar HTTPS para ajudar a garantir que a conexão seja segura.

	Enquanto o Ambari para seu cluster possa ser acessado diretamente pela Internet, algumas funcionalidades se baseiam no acesso de nós pelo nome de domínio interno usado pelo cluster. Como é um nome de domínio interno, e não público, você receberá erros de "servidor não encontrado" ao tentar acessar alguns recursos pela Internet.

	Para resolver esse problema, use um túnel SSH para tráfego do proxy da Web para o nó principal do cluster. Use a seção **Túnel SSH** dos artigos a seguir para criar um túnel SSH de uma porta no computador local até o cluster:

	* [Usar SSH com Hadoop baseado em Linux no HDInsight por meio de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md): etapas sobre como criar um túnel SSH usando o comando `ssh`.

	* [Usar SSH com Hadoop baseado em Linux no HDInsight por meio do Windows](hdinsight-hadoop-linux-use-ssh-windows): etapas sobre como usar PuTTY para criar um túnel SSH.

* **Ambari (REST)** - https://&lt;clustername>.azurehdinsight.net/ambari

	> [AZURE.NOTE]Autentique usando o usuário e a senha de administrador do cluster.
	>
	> A autenticação é texto sem formatação - sempre usar HTTPS para ajudar a garantir que a conexão seja segura.

* **WebHCat (Templeton)** - https://&lt;clustername>.azurehdinsight.net/templeton

	> [AZURE.NOTE]Autentique usando o usuário e a senha de administrador do cluster.
	>
	> A autenticação é texto sem formatação - sempre usar HTTPS para ajudar a garantir que a conexão seja segura.

* **SSH** - &lt;clustername>-ssh.azurehdinsight.net na porta 22

	> [AZURE.NOTE]Você só pode acessar o nó de cabeçalho do cluster via SSH por um computador cliente. Uma vez conectado, você pode acessar os nós de trabalho usando SSH no nó de cabeçalho.

## Locais de arquivos

Arquivos relacionados ao Hadoop encontram-se nos nós de cluster em `/usr/hdp/current`.

Dados de exemplo e arquivos JAR encontram-se no HDFS (Sistema de Arquivos Distribuído do Hadoop) ou no armazenamento de Blob do Azure em '/example' ou 'wasb:///example'.

## HDFS, armazenamento de Blob do Azure e práticas recomendadas de armazenamento

Na maioria das distribuições do Hadoop, o HDFS é conta com o suporte do armazenamento local nos computadores do cluster. Embora isso seja eficiente, pode ser dispendioso para uma solução baseada em nuvem onde você é cobrado por hora por recursos de computação.

O HDInsight usa o armazenamento de Blob do Azure como armazenamento padrão, o que proporciona os seguintes benefícios:

* Armazenamento de longo prazo econômico

* Acessibilidade por meio de serviços externos, como sites, utilitários de upload/download de arquivos, SDKs para vários idiomas e navegadores da Web

Como é o armazenamento padrão para o HDInsight, você normalmente não precisa fazer nada para usá-lo. Por exemplo, o comando a seguir listará arquivos na pasta **/example/data**, armazenada no armazenamento de Blob do Azure:

	hadoop fs -ls /example/data

Alguns comandos podem exigir que você especifique o uso do armazenamento de Blob. Nesses casos, você poderá prefixar o comando com **WASB://**.

O HDInsight também permite que você associe várias contas de armazenamento de Blob a um cluster. Para acessar dados em uma conta de armazenamento de Blob não padrão, você pode usar o formato **WASB://&lt;container-name>@&lt;account-name>.blob.core.windows.net/**. Por exemplo, o comando a seguir listará o conteúdo do diretório **/example/data** para o contêiner e a conta de armazenamento de Blob especificados:

	hadoop fs -ls wasb://mycontainer@mystorage.blob.core.windows.net/example/data

### Que armazenamento de Blob o cluster está usando?

Durante a criação do cluster, você optou por usar uma conta e um contêiner existentes do Armazenamento do Azure ou por criar uma nova conta. Em seguida, você provavelmente esquece dela. Você pode usar os métodos a seguir para localizar a conta e o contêiner do Armazenamento.

**API do Ambari**

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


**Portal do Azure**

1. No [portal do Azure](https://manage.windowsazure.com/), selecione o cluster HDInsight.

2. Selecione **Painel** na parte superior da página.

3. As contas do Armazenamento e os contêineres são listados na seção **recursos vinculados** da página.

	![recursos vinculados](./media/hdinsight-hadoop-linux-information/storageportal.png)

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

## Próximas etapas

* [Usar o Hive com o HDInsight](hdinsight-use-hive.md)
* [Usar o Pig com o HDInsight](hdinsight-use-pig.md)
* [Usar trabalhos do MapReduce com o HDInsight](hdinsight-use-mapreduce.md)
 

<!---HONumber=July15_HO2-->