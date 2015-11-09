<properties
	pageTitle="Usar o Hue com clusters Linux de Hadoop do HDInsight | Microsoft Azure"
	description="Saiba como instalar e usar o Hue com clusters Linux de Hadoop do HDInsight."
	services="hdinsight"
	documentationCenter=""
	authors="nitinme"
	manager="paulettm"
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/26/2015" 
	ms.author="nitinme"/>

# Instalar e usar o Hue em clusters de Hadoop do HDInsight

Saiba como instalar o Hue em clusters Linux do HDInsight e usar o túnel para rotear as solicitações para o Hue.

## O que é o Hue?

O Hue é um conjunto de aplicativos da Web usado para interagir com um cluster de Hadoop. Você pode usar o Hue para procurar o armazenamento associado a um cluster de Hadoop (WASB, no caso de clusters do HDInsight), executar trabalhos de Hive e scripts do Pig, etc. Com a instalação do Hue em um cluster de Hadoop do HDInsight, há suporte para os componentes a seguir.

* Editor de Hive Beeswax
* Pig
* Gerenciador do Metastore
* Oozie
* Navegador de Arquivos (que dialoga com o contêiner padrão WASB)
* Navegador de Trabalhos


## Instalar o Hue usando Ações de Script

O script [https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/install-hue-uber-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/install-hue-uber-v01.sh) é usado para instalar o Hue em um cluster do HDInsight. Esta seção fornece instruções sobre como usar o script ao provisionar o cluster usando o portal do Azure.

> [AZURE.NOTE]Você também pode usar o Azure PowerShell ou o SDK do .NET do HDInsight para criar um cluster usando esse script. Para obter mais informações sobre como usar esses métodos, consulte [Personalizar clusters HDInsight com Ações de Script](hdinsight-hadoop-customize-cluster-linux.md).

1. Inicie o provisionamento de um cluster usando as etapas em [Provisionar clusters HDInsight no Linux](hdinsight-hadoop-provision-linux-clusters.md#portal), mas não conclua o provisionamento.

	> [AZURE.NOTE]Para instalar o Hue em clusters HDInsight, o tamanho do nó de cabeçalho recomendado é de, pelo menos, A4 (8 núcleos, memória de 14 GB).

2. Na folha **Configuração Opcional**, selecione **Ações de Script** e forneça as informações a seguir:

	* __NOME__: insira um nome amigável para a ação de script.
	* __URI DO SCRIPT__: https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/install-hue-uber-v01.sh
	* __CABEÇALHO__: marque esta opção
	* __TRABALHO__: deixe essa opção em branco.
	* __ZOOKEEPER__: deixe essa opção em branco.
	* __PARÂMETROS__: o script espera a **senha do administrador de cluster** como um parâmetro. Essa é a senha especificada durante o provisionamento do cluster. Considerações importantes ao fornecer a senha:
		* Se o nome de usuário do cluster for "admin", você precisará apenas especificar a senha entre aspas.
		* Se o nome do cluster for algo diferente de "admin", especifique o parâmetro como `-u [username] [password in single quotes]`

3. Na parte inferior das **Ações de Script**, use o botão **Selecionar** para salvar a configuração. Por fim, use o botão **Selecionar** na parte inferior da folha **Configuração Opcional** para salvar as informações da configuração opcional.

4. Continue o provisionamento do cluster, conforme descrito em [Provisionar clusters HDInsight no Linux](hdinsight-hadoop-provision-linux-clusters.md#portal).

## Usar o Hue com clusters do HDInsight

O túnel SSH é a única maneira de acessar o Hue no cluster a partir do momento em que ele está em execução. O túnel via SSH permite que o tráfego vá diretamente para o nó de cabeçalho do cluster no qual o Hue está sendo executado. Após a conclusão do provisionamento do cluster, use as etapas a seguir para usar o Hue em um cluster Linux do HDInsight.

1. Use as informações em [Usar túnel SSH para acessar a IU Web da Ambari, ResourceManager, JobHistory, NameNode, Oozie e outra IU da Web](hdinsight-linux-ambari-ssh-tunnel.md) para criar um túnel SSH por meio do seu sistema de cliente para o cluster HDInsight, e em seguida, configura o seu navegador da Web para usar o túnel como um proxy.

2. Depois de criar um túnel SSH e configurar seu navegador para tráfego de proxy através dele, encontre o nome de host do nó do cabeçalho. Use as seguintes etapas para obter essas informações do Ambari:

    1. Em um navegador, vá para https://CLUSTERNAME.azurehdinsight.net. Quando solicitado, use o nome de usuário e a senha de administrador para se autenticar no site.
    
    2. No menu na parte superior da página, selecione __Hosts__.
    
    3. Selecione a entrada que começa com __hn0__. Quando a página é aberta, o nome do host é exibido na parte superior. O formato do nome do host é __hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net__. Esse é o nome de host, que você deve usar ao se conectar ao matiz.

2. Depois de criar um túnel SSH e configurar o navegador para tráfego de proxy através dele, use o navegador para abrir o portal do Hue em http://HOSTNAME:8888. Substitua HOSTNAME pelo nome obtido do Ambari na etapa anterior.

    > [AZURE.NOTE]Ao fazer logon pela primeira vez, será solicitado criar uma conta para poder efetuar logon no portal do Hue. As credenciais que você especificar aqui serão limitadas ao portal e não serão relacionadas às credenciais de usuário SSH ou de administrador que você especificou durante o provisionamento do cluster.

	![Fazer logon no portal do Hue](./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.Login.png "Especificar credenciais para o portal do Hue")

### Executar um trabalho do Hive

1. No portal do Hue, clique em **Editores de Consulta** e, em seguida, clique em **Hive** para abrir o editor do Hive.

	![Usar o Hive](./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.Hive.png "Usar o Hive")

2. Na guia **Ajuda**, em **Banco de Dados**, você deverá ver **hivesampletable**. Essa é uma tabela de exemplo que é enviada juntamente com todos os clusters de Hadoop no HDInsight. Insira uma consulta de exemplo no painel direito e veja a saída na guia **Resultados** no painel abaixo, conforme mostrado na captura de tela.

	![Executar consulta Hive](./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.Hive.Query.png "Executar consulta Hive")

	Você também pode usar a guia **Gráfico** para ver uma representação visual do resultado.

### Procurar no armazenamento de cluster

1. No portal do Hue, clique em **Navegador de Arquivos** no canto superior direito da barra de menus.

2. Por padrão, o navegador de arquivos é aberto no diretório **/user/myuser**. Clique na barra invertida imediatamente antes do diretório “user” no caminho até a raiz do contêiner de armazenamento do Azure associado ao cluster.

	![Usar navegador de arquivos](./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.File.Browser.png "Usar navegador de arquivos")

3. Clique com o botão direito do mouse em um arquivo ou pasta para ver as operações disponíveis. Use o botão **Carregar**, no canto superior direito, para carregar arquivos para o diretório atual. Use o botão **Novo** para criar novos arquivos ou diretórios.

> [AZURE.NOTE]O navegador de arquivos do Hue só pode mostrar o conteúdo do contêiner padrão associado ao cluster do HDInsight. Quaisquer contêineres/contas de armazenamento adicionais associados ao cluster não poderão ser acessados usando o navegador de arquivos. No entanto, os contêineres adicionais associados ao cluster sempre estarão acessíveis para os trabalhos do Hive. Por exemplo, se você digitar o comando `dfs -ls wasb://newcontainer@mystore.blob.core.windows.net` no editor do Hive, poderá ver também o conteúdo de contêineres adicionais. Neste comando, **newcontainer** não é o contêiner padrão associado a um cluster.

## Considerações importantes

1. O script usado para instalar o Hue instala-o apenas no nó de cabeçalho 0 do cluster.

2. Durante a instalação, vários serviços do Hadoop (HDFS, YARN, MR2, Oozie) são reiniciados para atualizar a configuração. Depois que o script termina de instalar o Hue, pode levar algum tempo para que outros serviços do Hadoop sejam iniciados. Isso pode, inicialmente, afetar o desempenho do Hue. Depois que todos os serviços tiverem sido iniciados, o Hue estará totalmente funcional.

3.	O Hue não entende trabalhos Tez, que é o padrão atual para o Hive. Se você quiser usar o MapReduce como o mecanismo de execução do Hive, atualize o script para usar o comando a seguir em seu script:

		set hive.execution.engine=mr;

4.	Com clusters do Linux, pode existir um cenário no qual os serviços estão em execução no nó de cabeçalho 0 enquanto o Gerenciador de Recursos pode estar em execução no nó de cabeçalho 1. Um cenário como esse pode resultar em erros (mostrados abaixo) ao usar o Hue para exibir detalhes de trabalhos EM EXECUÇÃO no cluster. No entanto, você pode exibir os detalhes do trabalho após ele ser concluído.

	![Erro no portal do Hue](./media/hdinsight-hadoop-hue-linux/HDI.Hue.Portal.Error.png "Erro no portal do Hue")

	Isso ocorre devido a um problema conhecido. Como solução alternativa, modifique o Ambari para que o Gerenciador de Recursos ativo também seja executado em nó de cabeçalho 0.

5.	O Hue compreende o WebHDFS, enquanto os clusters HDInsight utilizam o armazenamento do Azure usando `wasb://`. Portanto, o script personalizado utilizado com a ação de script instala WebWasb, que é um serviço compatível com WebHDFS para conversar com o WASB. Portanto, embora em alguns lugares o portal do Hue esteja marcado como HDFS (como quando você move o mouse sobre o **Navegador de Arquivos**), ele deve ser interpretado como WASB.


## Próximas etapas

- [Instalar e usar Spark em clusters HDInsight](hdinsight-hadoop-spark-install-linux.md) para obter instruções sobre como usar o cluster de personalização para instalar e usar o Spark em clusters Hadoop do HDInsight. O Spark é uma estrutura de processamento paralelo de código-fonte aberto que oferece suporte ao processamento de memória para melhorar o desempenho dos aplicativos analíticos de big data.

- [Instalar o Giraph em clusters HDInsight](hdinsight-hadoop-giraph-install-linux.md). Use a personalização do cluster para instalar o Giraph em clusters de Hadoop do HDInsight. O Giraph permite que você realize processamento de tabelas usando o Hadoop, além de poder ser utilizado com o HDInsight do Azure.

- [Instalar o Solr em clusters HDInsight](hdinsight-hadoop-solr-install-linux.md). Use a personalização do cluster para instalar o Solr em clusters de Hadoop do HDInsight. O Solr permite que você execute operações de pesquisa poderosas nos dados armazenados.

- [Instalar o R em clusters HDInsight](hdinsight-hadoop-r-scripts-linux.md). Use a personalização do cluster para instalar o R em clusters de Hadoop do HDInsight. R é uma linguagem e ambiente de software livre para computação estatística. Ele fornece centenas de funções estatísticas internas e sua própria linguagem de programação, que combina aspectos de programação funcional e de programação orientada a objetos. Ele também fornece recursos abrangentes de gráficos.

[powershell-install-configure]: install-configure-powershell-linux.md
[hdinsight-provision]: hdinsight-provision-clusters-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install-linux.md

<!---HONumber=Nov15_HO1-->