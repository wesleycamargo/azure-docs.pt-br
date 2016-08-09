<properties
	pageTitle="Use a ação de Script para instalar o Solr no HDInsight baseado em Linux | Microsoft Azure"
	description="Saiba como instalar o Solr em clusters baseados Hadoop HDInsight baseados em Linux usando as ações de script."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/27/2016"
	ms.author="larryfr"/>

# Instalar e usar o Solr em clusters HDInsight do Hadoop

Neste tópico, você aprenderá como instalar o Solr no Azure HDInsight usando a Ação de Script. O Solr é uma plataforma de pesquisa poderosa e oferece recursos de pesquisa em nível corporativo para os dados gerenciados pelo Hadoop. Depois de instalar o Solr no cluster HDInsight, você também aprenderá como pesquisar dados usando o Solr.

> [AZURE.NOTE] As etapas deste documento exigem um cluster HDInsight baseado em Linux. Para obter informações sobre como usar o Solr com um cluster baseado no Windows, veja [Instalar e usar o Solr em clusters do Hadoop no HDinsight (Windows)](hdinsight-hadoop-solr-install.md)

O script de exemplo usado neste tópico cria um cluster Solr com uma configuração específica. Se você quiser configurar o cluster Solr com diferentes coleções, fragmentos, esquemas, réplicas, etc., você deve modificar o script e os binários do Solr adequadamente.

## <a name="whatis"></a>O que é Solr?

O [Apache Solr](http://lucene.apache.org/solr/features.html) é uma plataforma de pesquisa empresarial que habilita operações poderosas de pesquisa de texto completo nos dados. Enquanto o Hadoop permite armazenar e gerenciar grandes quantidades de dados, o Apache Solr oferece os recursos de pesquisa para recuperar rapidamente os dados. Este tópico fornece instruções sobre como personalizar um cluster HDInsight para instalar o Solr.

> [AZURE.WARNING] Há suporte total a componentes fornecidos com o cluster HDInsight e o Suporte da Microsoft ajudará a isolar e resolver problemas relacionados a esses componentes.
>
> Componentes personalizados, como o Solr, recebem suporte comercialmente razoável para ajudá-lo a solucionar o problema. Isso pode resultar na resolução do problema ou na solicitação de você buscar nos canais disponíveis as tecnologias de código-fonte aberto, onde é possível encontrar conhecimento aprofundado sobre essa tecnologia. Por exemplo, há muitos sites de comunidades que podem ser usados, como o [Fórum do MSDN para o HDInsight](https://social.msdn.microsoft.com/Forums/azure/pt-BR/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Além disso, os projetos do Apache têm sites do projeto em [http://apache.org](http://apache.org), por exemplo: [Hadoop](http://hadoop.apache.org/).

## O que o script faz

Esse script faz as seguintes alterações ao cluster HDInsight:

* Instala o Solr em `/usr/hdp/current/solr`
* Cria um novo usuário, __solruser__, que é usado para executar o serviço do Solr
* Define __solruser__ como o proprietário de `/usr/hdp/current/solr`
* Adiciona uma configuração [Upstart](http://upstart.ubuntu.com/) que iniciará o Solr se um nó de cluster for reiniciado. O Solr é iniciado automaticamente nos nós de cluster após a instalação

## <a name="install"></a>Instalar o Solr usando ações de script

Um script de exemplo para instalar o Solr em um cluster HDInsight está disponível no seguinte local.

    https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh

Esta seção fornece instruções sobre como usar o script de exemplo durante a criação de um novo cluster usando o portal do Azure.

> [AZURE.NOTE] Modelos do Azure PowerShell, da CLI do Azure, do SDK do .NET do HDInsight ou do Azure Resource Manager também podem ser usados para aplicar ações de script. Também é possível aplicar ações de script a clusters que já estão em execução. Para saber mais, veja [Personalizar clusters HDInsight com as Ações de Script](hdinsight-hadoop-customize-cluster-linux.md).

1. Inicie o provisionamento de um cluster usando as etapas em [Provisionar clusters HDInsight baseados em Linux](hdinsight-provision-linux-clusters.md#portal), mas não conclua o provisionamento.

2. Na folha **Configuração Opcional**, selecione **Ações de Script** e forneça as informações a seguir:

	* __NOME__: insira um nome amigável para a ação de script.
	* __URI DO SCRIPT__: https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh
	* __CABEÇALHO__: marque esta opção
	* __TRABALHO__: marque esta opção
	* __ZOOKEEPER__: marque esta opção para instalar no nó Zookeeper
	* __PARÂMETROS__: deixe este campo em branco

3. Na parte inferior das **Ações de Script**, use o botão **Selecionar** para salvar a configuração. Por fim, use o botão **Selecionar** na parte inferior da folha **Configuração Opcional** para salvar as informações de configuração opcional.

4. Continue o provisionamento do cluster conforme descrito em [Provisionar clusters HDInsight baseados em Linux](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="usesolr"></a>Como usar o Solr no HDInsight?

### Dados de indexação

Você deve começar com indexação Solr, com alguns arquivos de dados. Em seguida, você pode usar o Solr para executar consultas de pesquisa em dados indexados. Use as etapas a seguir para adicionar alguns dados de exemplo para Solr e, em seguida, consultá-lo:

1. Conecte-se ao cluster HDInsight usando SSH:

		ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

	Para obter mais informações sobre como usar SSH com o HDInsight, consulte o seguinte:

	* [Usar SSH com Hadoop baseado em Linux no HDInsight no Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [Usar SSH com Hadoop baseado em Linux no HDInsight no Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

	> [AZURE.IMPORTANT] Etapas posteriormente em tornar este documento usam um túnel SSL para conectar-se a interface de usuário da Web do Solr. Para usar essas etapas, você deve estabelecer um túnel SSL e, em seguida, configurar seu navegador para usá-lo.
	>
	> Para obter mais informações, consulte [Usar túnel SSH para acessar a IU da Web Ambari, ResourceManager, JobHistory, NameNode, Oozie e outras IUs da Web](hdinsight-linux-ambari-ssh-tunnel.md)

2. Use os comandos a seguir para ter dados de exemplo do índice Solr:

		cd /usr/hdp/current/solr/example/exampledocs
		java -jar post.jar solr.xml monitor.xml

	Você verá a saída a seguir no console:

		POSTing file solr.xml
		POSTing file monitor.xml
		2 files indexed.
		COMMITting Solr index changes to http://localhost:8983/solr/update..
		Time spent: 0:00:01.624

	O utilitário post.jar indexa o Solr com dois documentos de exemplo, **solr.xml** e **monitor.xml**. Eles serão armazenados em __coleção1__ no Solr.

3. Use o seguinte para consultar a API REST exposta pelo Solr:

		curl "http://localhost:8983/solr/collection1/select?q=*%3A*&wt=json&indent=true"

	Isso emitirá uma consulta à __coleção1__ para qualquer documento correspondente a __*:*__ (codificado como *%3A* na cadeia de consulta), especificando também que a resposta deve ser retornada como JSON. A resposta deve se assemelhar ao seguinte:

			"response": {
			    "numFound": 2,
			    "start": 0,
			    "maxScore": 1,
			    "docs": [
			      {
			        "id": "SOLR1000",
			        "name": "Solr, the Enterprise Search Server",
			        "manu": "Apache Software Foundation",
			        "cat": [
			          "software",
			          "search"
			        ],
			        "features": [
			          "Advanced Full-Text Search Capabilities using Lucene",
			          "Optimized for High Volume Web Traffic",
			          "Standards Based Open Interfaces - XML and HTTP",
			          "Comprehensive HTML Administration Interfaces",
			          "Scalability - Efficient Replication to other Solr Search Servers",
			          "Flexible and Adaptable with XML configuration and Schema",
			          "Good unicode support: héllo (hello with an accent over the e)"
			        ],
			        "price": 0,
			        "price_c": "0,USD",
			        "popularity": 10,
			        "inStock": true,
			        "incubationdate_dt": "2006-01-17T00:00:00Z",
			        "_version_": 1486960636996878300
			      },
			      {
			        "id": "3007WFP",
			        "name": "Dell Widescreen UltraSharp 3007WFP",
			        "manu": "Dell, Inc.",
			        "manu_id_s": "dell",
			        "cat": [
			          "electronics and computer1"
			        ],
			        "features": [
			          "30" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
			        ],
			        "includes": "USB cable",
			        "weight": 401.6,
			        "price": 2199,
			        "price_c": "2199,USD",
			        "popularity": 6,
			        "inStock": true,
			        "store": "43.17614,-90.57341",
			        "_version_": 1486960637584081000
			      }
			    ]
			  }

### Usando o painel do Solr

O painel do Solr é uma IU Web que permite que você trabalhe com o Solr através do seu navegador da Web. O painel do Solr não seja exposto diretamente na Internet por meio do seu cluster HDInsight, mas deve ser acessado através de um túnel SSH. Para obter mais informações sobre o uso de um túnel SSH, consulte [Usar túnel SSH para acessar a IU da Web Ambari, ResourceManager, JobHistory, NameNode, Oozie e outras IUs da Web](hdinsight-linux-ambari-ssh-tunnel.md)

Depois de estabelecer um túnel SSH, use as seguintes etapas para usar o painel do Solr:

1. Determine o nome de host para o nó de cabeçalho:

    1. Em um navegador, vá para https://CLUSTERNAME.azurehdinsight.net. Quando solicitado, use o nome de usuário e a senha de administrador para se autenticar no site.
    
    2. No menu na parte superior da página, selecione __Hosts__.
    
    3. Selecione a entrada que começa com __hn0__. Quando a página é aberta, o nome do host é exibido na parte superior. O formato do nome do host é __hn0-PARTOFCLUSTERNAME.randomcharacters.cx.internal.cloudapp.net__. Esse é o nome de host, que você deve usar ao se conectar ao painel do Solr.
    
1. No navegador, conecte-se a __http://HOSTNAME:8983/solr/#/__, em que __HOSTNAME\_\_ é o nome determinado nas etapas anteriores.

    Esse tráfego deve ser roteado por meio do túnel SSH para o nó de cabeçalho do cluster HDInsight. Você verá uma página semelhante à seguinte:

	![Imagem do painel do Solr](./media/hdinsight-hadoop-solr-install-linux/solrdashboard.png)

2. No painel à esquerda, use a lista suspensa **Seletor de Núcleo** para selecionar **collection1**. Várias entradas devem aparecer então abaixo de __collection1__.

3. Nas entradas abaixo de __collection1__, selecione __Consulta__. Use os valores a seguir para preencher a página de pesquisa:

	* Na caixa de texto **q**, digite ***:***. Isso retornará como resultado todos os documentos que são indexados em Solr. Se você quiser procurar uma cadeia de caracteres específica dentro dos documentos, você pode inserir essa cadeia de caracteres aqui.

	* Na caixa de texto **wt**, selecione o formato de saída. O padrão é **json**.

	Por fim, selecione o botão **Executar Consulta** na parte inferior da página de pesquisa.

	![Usar Ação de Script para personalizar um cluster](./media/hdinsight-hadoop-solr-install-linux/hdi-solr-dashboard-query.png)

	A saída retorna os dois documentos que foram usados para indexação do Solr. A saída é semelhante ao seguinte:

			"response": {
			    "numFound": 2,
			    "start": 0,
			    "maxScore": 1,
			    "docs": [
			      {
			        "id": "SOLR1000",
			        "name": "Solr, the Enterprise Search Server",
			        "manu": "Apache Software Foundation",
			        "cat": [
			          "software",
			          "search"
			        ],
			        "features": [
			          "Advanced Full-Text Search Capabilities using Lucene",
			          "Optimized for High Volume Web Traffic",
			          "Standards Based Open Interfaces - XML and HTTP",
			          "Comprehensive HTML Administration Interfaces",
			          "Scalability - Efficient Replication to other Solr Search Servers",
			          "Flexible and Adaptable with XML configuration and Schema",
			          "Good unicode support: héllo (hello with an accent over the e)"
			        ],
			        "price": 0,
			        "price_c": "0,USD",
			        "popularity": 10,
			        "inStock": true,
			        "incubationdate_dt": "2006-01-17T00:00:00Z",
			        "_version_": 1486960636996878300
			      },
			      {
			        "id": "3007WFP",
			        "name": "Dell Widescreen UltraSharp 3007WFP",
			        "manu": "Dell, Inc.",
			        "manu_id_s": "dell",
			        "cat": [
			          "electronics and computer1"
			        ],
			        "features": [
			          "30" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
			        ],
			        "includes": "USB cable",
			        "weight": 401.6,
			        "price": 2199,
			        "price_c": "2199,USD",
			        "popularity": 6,
			        "inStock": true,
			        "store": "43.17614,-90.57341",
			        "_version_": 1486960637584081000
			      }
			    ]
			  }

### Iniciando e parando o Solr

Se você precisar interromper ou iniciar o Solr manualmente, use os seguintes comandos:

	sudo stop solr

	sudo start solr

## Backup de dados indexados

Como uma prática recomendada, você deve fazer backup dos dados indexados de nós do cluster Solr no armazenamento de Blob do Azure. Execute as seguintes etapas para fazê-lo:

1. Conecte-se ao cluster usando o SSH, depois use o comando a seguir para obter o nome de host do nó de cabeçalho:

        hostname -f
        
2. Use o seguinte para criar um instantâneo dos dados indexados. Substitua __HOSTNAME__ pelo nome retornado do comando anterior:

		curl http://HOSTNAME:8983/solr/replication?command=backup

	Você verá uma resposta semelhante a essa:

		<?xml version="1.0" encoding="UTF-8"?>
		<response>
		  <lst name="responseHeader">
		    <int name="status">0</int>
		    <int name="QTime">9</int>
		  </lst>
		  <str name="status">OK</str>
		</response>

2. Em seguida, altere os diretórios para __/usr/hdp/current/solr/example/solr__. Haverá um subdiretório aqui para cada coleção. Cada diretório da coleção contém um diretório __dados__, que é onde o instantâneo para essa coleção está localizado.

	Por exemplo, se você usou as etapas anteriores para indexar os documentos de exemplo, o diretório __/usr/hdp/current/solr/example/solr/collection1/data__ agora deve conter um diretório chamado __snapshot.###########__, em que os símbolos “#” são a data e hora do instantâneo.

3. Crie um arquivo compactado da pasta de instantâneos usando um comando semelhante ao seguinte:

		tar -zcf snapshot.20150806185338855.tgz snapshot.20150806185338855

	Isso criará um novo arquivo chamado __snapshot.20150806185338855.tgz__, que contém o conteúdo do diretório __snapshot.20150806185338855__.

3. Em seguida, você pode armazenar o arquivo para armazenamento primário do cluster usando o seguinte comando:

	hadoop fs -copyFromLocal snapshot.20150806185338855.tgz /example/data

	> [AZURE.NOTE] Talvez você queira criar um diretório dedicado para armazenar os instantâneos do Solr. Por exemplo: `hadoop fs -mkdir /solrbackup`.

Para obter mais informações sobre como trabalhar com backups e restaurações do Solr, consulte [Fazendo backups e restaurações de SolrCores](https://cwiki.apache.org/confluence/display/solr/Making+and+Restoring+Backups+of+SolrCores).


## Consulte também

- [Instalar e usar matiz em clusters HDInsight](hdinsight-hadoop-hue-linux.md). A Matiz é uma interface da Web que torna mais fácil criar, executar e salvar trabalhos Pig e Hive, bem como procurar o armazenamento padrão do cluster do HDInsight.

- [Instalar o R em clusters HDInsight][hdinsight-install-r]. Use a personalização do cluster para instalar o R em clusters de Hadoop do HDInsight. R é uma linguagem e ambiente de software livre para computação estatística. Ele fornece centenas de funções estatísticas internas e sua própria linguagem de programação, que combina aspectos de programação funcional e de programação orientada a objetos. Ele também fornece recursos abrangentes de gráficos.

- [Instalar o Giraph em clusters HDInsight](hdinsight-hadoop-giraph-install-linux.md). Use a personalização do cluster para instalar o Giraph em clusters de Hadoop do HDInsight. O Giraph permite que você realize processamento de tabelas usando o Hadoop, além de poder ser usado com o HDInsight do Azure.

- [Instalar matiz em clusters HDInsight](hdinsight-hadoop-hue-linux.md). Use a personalização do cluster para instalar o Hue em clusters de Hadoop do HDInsight. A Matiz é um conjunto de aplicativos da Web usado para interagir com um cluster Hadoop.



[hdinsight-install-r]: hdinsight-hadoop-r-scripts-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md

<!---HONumber=AcomDC_0727_2016-->