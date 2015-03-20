<properties
   pageTitle="O que você precisa saber sobre o Hadoop no HDInsight baseado em Linux | Azure"
   description="Clusters do HDInsight baseados em Linux fornecem Hadoop em um ambiente Linux familiar, em execução na nuvem do Azure."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang=""
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/18/2015"
   ms.author="larryfr"/>

#Trabalhando com o HDInsight no Linux (Preview)

Clusters do HDInsight baseados em Linux fornecem Hadoop em um ambiente Linux familiar, em execução na nuvem do Azure. Para a maioria da coisas, ele deve funcionar exatamente como qualquer outro Hadoop na instalação do Linux. Este documento indica diferenças específicas que você deve estar atento.

##Nomes de domínio

O nome de domínio totalmente qualificado (FQDN) para usar ao se conectar ao cluster é **&lt;clustername>.azurehdinsight.net** ou (apenas para SSH) **&lt;clustername>.aurehdinsight.net**.

##Serviços acessíveis remotamente

* **Ambari (Web)** - https://&lt;clustername>.azurehdinsight.net

	> [AZURE.NOTE] Autenticar usando o usuário do administrador de cluster e a senha e fazer o logon para Ambari. Isso também utiliza o usuário do administrador de cluster e a senha
	> 
	> A autenticação é texto sem formatação - sempre usar HTTPS para garantir que a conexão seja segura.

	Enquanto o Ambari para seu cluster possa ser acessado diretamente pela Internet, algumas funcionalidades se baseiam no acesso de nós pelo nome de domínio interno usado pelo cluster. Como esse é um nome de domínio interno e não público, você receberá erros de servidor não encontrado ao tentar acessar alguns recursos pela Internet.

	Para resolver esse problema, use um túnel SSH para tráfego do proxy da Web para o nó principal do cluster. Use os artigos a seguir para criar um túnel SSH de uma porta no computador local para o cluster.

	* <a href="../hdinsight-hadoop-linux-use-ssh-unix/#tunnel" target="_blank">Usar SSH com Hadoop baseado em Linux no HDInsight por meio do Linux, Unix ou OS X</a> - etapas para a criação de um túnel SSH usando o comando  `ssh`

	* <a href="../hdinsight-hadoop-linux-use-ssh-windows/#tunnel" target="_blank">Usar SSH com Hadoop baseado em Linux no HDInsight por meio do Windows</a> - etapas do uso do Putty para criar um túnel SSH

* **Ambari (REST)** - https://&lt;clustername>.azurehdinsight.net/ambari

	> [AZURE.NOTE] Autenticar usando o usuário do administrador de cluster e a senha.
	> 
	> A autenticação é texto sem formatação - sempre usar HTTPS para garantir que a conexão seja segura.

* **WebHCat (Templeton)** - https://&lt;clustername>.azurehdinsight.net/templeton

	> [AZURE.NOTE] Autenticar usando o usuário do administrador de cluster e a senha.
	> 
	> A autenticação é texto sem formatação - sempre usar HTTPS para garantir que a conexão seja segura.

* **SSH** - &lt;clustername>-ssh.azurehdinsight.net na porta 22

	> [AZURE.NOTE] Você só pode acessar o nó principal do cluster através do SSH de uma máquina cliente. Uma vez conectado, você pode acessar os nós de trabalho usando o SSH do nó principal.

##Locais de arquivos

Arquivos relacionados ao Hadoop podem ser encontrados em nós de cluster em  `/usr/hdp/current`.

Dados de exemplo e jars podem ser encontradas no HDFS (WASB) em '/example' ou 'wasb:///example'.

##Práticas recomendadas de HDFS, WASB e armazenamento

Na maioria das distribuições de Hadoop, o Hadoop Distributed File System (HDFS) é apoiado pelo armazenamento local nos computadores do cluster. Embora isso seja eficiente, pode ser dispendioso para uma solução baseada em nuvem onde você é cobrado por hora por recursos de computação.

O HDInsight usa o Armazenamento de Blob do Azure como o armazenamento padrão, que fornece os seguintes benefícios:

* Armazenamento de longo prazo barato

* Acessível por serviços externos, como sites, utilitários de upload/download de arquivo, SDKs de várias linguagens e navegadores da Web

Como é o armazenamento padrão para o HDInsight, você normalmente não precisa fazer nada para usá-lo. Por exemplo, o comando a seguir lista arquivos na pasta **/example/data**, que é armazenada no Armazenamento de Blob do Azure.

	hadoop fs -ls /example/data

Alguns comandos podem exigir que você especifique que está usando o Armazenamento de Blob. Nesses casos, você poderá prefixar o comando com **WASB://**.

O HDInsight também permite que você associe várias contas de Armazenamento de Blob com um cluster. Para acessar dados em uma conta de armazenamento de Blob não padrão, você pode usar o formato **WASB://&lt;container-name> @&lt;account-name>.blob.core.windows.net/**. Por exemplo, o seguinte lista o conteúdo do diretório **/example/data** para a o contêiner e a conta de armazenamento especificada.

	hadoop fs -ls wasb://mycontainer@mystorage.blob.core.windows.net/example/data

###Qual armazenamento de blob o cluster está usando?

Durante a criação do cluster, você pode selecionar para usar uma conta de armazenamento e contêiner existentes ou para criar um nova. Em seguida, você provavelmente esquece dela. Você pode encontrar a conta de armazenamento e o contêiner usando os seguintes métodos.

**Portal do Azure**

1. No <a href="https://manage.windowsazure.com/" target="_blank">Portal de Gerenciamento do Azure</a>, selecione seu cluster do HDInsight.

2. Selecione **Painel** na parte superior da página.

3. As contas de armazenamento e contêineres são listados a seção **recursos vinculados** da página.

	![linked resources](./media/hdinsight-hadoop-linux-information/storageportal.png)

**Interface de Linha de Comando de Plataforma Cruzada do Azure**

*Em breve!*

###Como acessar o armazenamento de blob?

Diferente do comando Hadoop do cluster, há várias maneiras de acessar blobs:

* <a href="http://azure.microsoft.com/documentation/articles/xplat-cli/" target="_blank">Interface de linha de comando entre plataformas do Azure</a> - após a instalação, consulte `azure storage` para obter ajuda com o uso do armazenamento, ou `azure blob` para ver comandos específicos do blob.

* Uma variedade de SDKs:

	* <a href="https://github.com/Azure/azure-sdk-for-java" target="_blank">Java</a>

	* <a href="https://github.com/Azure/azure-sdk-for-node" target="_blank">Node.js</a>

	* <a href="https://github.com/Azure/azure-sdk-for-php" target="_blank">PHP</a>

	* <a href="https://github.com/Azure/azure-sdk-for-python" target="_blank">Python</a>

	* <a href="https://github.com/Azure/azure-sdk-for-ruby" target="_blank">Ruby</a>

	* <a href="https://github.com/Azure/azure-sdk-for-net" target="_blank">.NET</a>

* <a href="https://msdn.microsoft.com/library/azure/dd135733.aspx" target="_blank">API REST de armazenamento</a>


##Próximas etapas

* [Use o hive com o HDInsight](../hdinsight-use-hive/)
* [Usar o Pig com o HDInsight](../hdinsight-use-pig/)
* [Usar trabalhos MapReduce com o HDInsight](../hdinsight-use-mapreduce)


<!--HONumber=47-->
