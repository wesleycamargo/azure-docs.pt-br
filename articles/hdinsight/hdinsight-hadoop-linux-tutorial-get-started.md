<properties
   	pageTitle="Tutorial do Linux: Introdução ao Hadoop e ao Hive | Microsoft Azure"
   	description="Siga este tutorial do Linux para começar a usar o Hadoop no HDInsight. Saiba como configurar clusters do Linux e consultar dados com o Hive."
   	services="hdinsight"
   	documentationCenter=""
   	authors="mumian"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="hero-article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="03/01/2016"
   	ms.author="jgao"/>

# Tutorial do Hadoop: introdução ao uso do Hadoop baseado em Linux no HDInsight

> [AZURE.SELECTOR]
- [Windows](hdinsight-hadoop-tutorial-get-started-windows.md)
- [Linux](hdinsight-hadoop-linux-tutorial-get-started.md)

Aprenda a criar clusters Hadoop baseados em Linux no HDInsight e a usar o Modo de Exibição de Hive do Ambari para executar trabalhos do Hive.

Se for iniciante em Hadoop e big data, você poderá ler mais sobre os termos: [Apache Hadoop](http://go.microsoft.com/fwlink/?LinkId=510084), [MapReduce](http://go.microsoft.com/fwlink/?LinkId=510086),[HDFS (Sistema de Arquivos Distribuído do Hadoop)](http://go.microsoft.com/fwlink/?LinkId=510087) e [Hive](http://go.microsoft.com/fwlink/?LinkId=510085). Para entender como o HDInsight habilita o Hadoop no Azure, consulte [Introdução ao Hadoop no HDInsight](hdinsight-hadoop-introduction.md).

### Pré-requisitos

Antes de começar este tutorial, você deverá ter o seguinte:

- **Assinatura do Azure**: confira [Obter avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## Criar cluster

A maioria dos trabalhos de Hadoop consiste em trabalhos em lotes. Você cria um cluster e executa alguns trabalhos. Nesta seção, você criará um cluster Hadoop baseado em Linux no HDInsight usando o [modelo de ARM Azure](../resource-group-template-deploy.md). Não é necessário ter experiência com o modelo de ARM do Azure para seguir este tutorial. Para obter outros métodos de criação de cluster e noções básicas sobre as configurações, confira [Criar clusters do HDInsight](hdinsight-hadoop-provision-linux-clusters.md). Para saber mais sobre como usar o modelo ARM para criar clusters Hadoop no HDInsight, confira [Criar clusters Hadoop no HDInsight usando modelos de ARM](hdinsight-hadoop-create-windows-clusters-arm-templates.md)

1. Clique na imagem a seguir para abrir um modelo ARM no Portal do Azure. O modelo ARM está localizado em um contêiner de blob público com a URL **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-hadoop-cluster-in-hdinsight.json*.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hadoop-cluster-in-hdinsight.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/pt-BR/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Na folha **Parâmetros**, insira o seguinte:

    - **ClusterName**: insira um nome para o cluster Hadoop que você criará.
    - **ClusterStorageAccountName**: cada cluster tem uma dependência de conta de armazenamento de Blobs do Azure. Depois que você excluir um cluster, os dados serão mantidos na conta de armazenamento.
    - **Nome e senha de logon do cluster**: o nome de logon padrão é **admin**.
    - **Nome de usuário e senha SSH**: o nome de usuário padrão é **sshuser**. Você pode renomeá-lo. 
    
    Outros parâmetros são opcionais. Você pode deixá-los como estão. O nome de conta de armazenamento padrão do cluster é o nome do cluster com "store" acrescentado. Ele é codificado na seção de variáveis no modelo.
3. Clique em **OK** para salvar os parâmetros.
4. Na folha **Implantação personalizada**, clique na caixa suspensa **Grupo de recursos** e clique em **Novo** para criar um novo grupo de recursos. O grupo de recursos é um contêiner que agrupa o cluster, a conta de armazenamento dependente e outros recursos vinculados. O local do grupo de recursos pode ser diferente do local do cluster.
5. Clique em **Termos legais** e em **Criar**.
6. Clique em **Criar**. Você verá um novo bloco intitulado **Como enviar a implantação para a implantação do modelo**. A criação de um cluster demora cerca de 20 minutos. Após a criação do cluster, você pode clicar na folha do cluster no portal para abri-la.

Depois de concluir o tutorial, talvez você queira excluir o cluster. Com o HDInsight, seus dados são armazenados no Armazenamento do Azure, assim você poderá excluir, com segurança, um cluster quando ele não estiver em uso. Você também é cobrado por um cluster HDInsight, mesmo quando ele não está em uso. Como os encargos para o cluster são muitas vezes maiores do que os encargos para armazenamento, faz sentido, do ponto de vista econômico, excluir os clusters quando não estiverem em uso. Para obter instruções sobre como excluir um cluster, confira [Gerenciar clusters Hadoop no HDInsight usando o Portal do Azure](hdinsight-administer-use-management-portal.md#delete-clusters).


##Execute consultas Hive

Os Modos de Exibição do [Ambari](hdinsight-hadoop-manage-ambari.md) fornecem vários utilitários por meio de uma página da Web. Um dos utilitários é a exibição de Hive. Nesta seção, você usará o modo de exibição do Hive para executar consultas do Hive no cluster HDInsight. Para obter outros métodos para executar consultas do Hive, confira [Usar o Hive no HDInsight](hdinsight-use-hive.md).

1. Navegue até **https://&lt;ClusterName>.azurehdinsight**, em que &lt;ClusterName> é o cluster criado na seção anterior para abrir o Ambari.
2. Insira o nome de usuário e a senha do Hadoop que você especificou na seção anterior. O nome de usuário padrão é **admin**.
3. Abra a **Exibição do Hive**, conforme mostrado na seguinte captura de tela:

    ![Escolhendo modos de exibição do Ambari](./media/hdinsight-hadoop-linux-tutorial-get-started/selecthiveview.png).
4. Na seção __Editor de Consultas__ da página, cole as seguintes instruções HiveQL na planilha:

		SHOW tables;
5. Clique em __Executar__. Uma seção __Resultados do Processo de Consulta__ deve aparecer abaixo do Editor de Consultas e exibir informações sobre o trabalho. 

    Depois que a consulta for concluída, a seção __Resultados do Processo de Consulta__ exibirá os resultados da operação. Você deverá ver uma tabela chamada **hivesampletable**. Essa tabela do Hive de exemplo é fornecida com todos os clusters HDInsight.
    
    > [AZURE.TIP] Observe a lista suspensa __Salvar resultados__ no canto superior esquerdo da seção __Resultados do Processo de Consulta__. Você pode usá-la para baixar os resultados ou salvá-los no armazenamento do HDInsight como um arquivo CSV.
6. Repita as etapas 4 e 5 para executar a seguinte consulta:

        SELECT * FROM hivesampletable;

Depois de concluir um trabalho do Hive, você pode [exportar os resultados para o banco de dados do SQL Azure ou do SQL Server](hdinsight-use-sqoop-mac-linux.md). Também pode [visualizar os resultados usando o Excel](hdinsight-connect-excel-power-query.md). Para obter mais informações sobre como usar o Hive no HDInsight, confira [Usar o Hive e o HiveQL com o Hadoop no HDInsight para analisar um arquivo log4j do Apache de exemplo](hdinsight-use-hive.md).

## Próximas etapas

Neste tutorial, você aprendeu como criar um cluster HDInsight baseado em Linux usando um modelo ARM e como executar consultas básicas do Hive.

Para saber mais sobre como trabalhar com o HDInsight, confira:

- Para saber mais sobre como usar o Hive com HDInsight, inclusive como executar consultas de Hive do Visual Studio, consulte [usar o Hive com HDInsight][hdinsight-use-hive].

- Para saber mais sobre o Pig, uma linguagem usada para transformar dados, consulte [Usar o Pig com o HDInsight][hdinsight-use-pig].

- Para saber mais sobre o MapReduce, uma maneira de gravar programas que processam dados no Hadoop, consulte [Usar o MapReduce com HDInsight][hdinsight-use-mapreduce].

- Para saber mais sobre como usar as Ferramentas do HDInsight para o Visual Studio para analisar dados no HDInsight, consulte [Introdução às ferramentas do Hadoop do Visual Studio para o HDInsight](hdinsight-hadoop-visual-studio-tools-get-started.md).

Se você estiver pronto para começar a trabalhar com seus próprios dados e precisa saber mais sobre como o HDInsight armazena dados ou obtém dados no HDInsight, consulte:

- Para obter informações sobre como o HDInsight usa o armazenamento de blobs do Azure, consulte [Usar armazenamento de Blobs do Azure com o HDInsight](hdinsight-hadoop-use-blob-storage.md).

- Para obter informações sobre como carregar arquivos no HDInsight, consulte [Carregar dados no HDInsight][hdinsight-upload-data].

Se você quiser saber mais sobre como criar ou gerenciar um cluster HDInsight, consulte:

- Para saber mais sobre como gerenciar o cluster HDInsight baseado em Linux, consulte [Gerenciar clusters HDInsight usando o Ambari](hdinsight-hadoop-manage-ambari.md).

- Para saber mais sobre as opções que você poderá selecionar ao criar um cluster HDInsight, confira [Como criar o HDInsight no Linux usando opções personalizadas](hdinsight-hadoop-provision-linux-clusters.md).

- Se você estiver familiarizado com o Linux e o Hadoop, mas quiser obter informações específicas sobre o Hadoop no HDInsight, consulte [Trabalhando com o HDInsight no Linux](hdinsight-hadoop-linux-information.md). Haverá informações como:

	* URLs para serviços hospedados no cluster, como Ambari e WebHCat
	* O local dos arquivos do Hadoop e exemplos no sistema de arquivos local
	* O uso do Azure Storage (WASB) em vez de HDFS como armazenamento de dados padrão


[1]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: powershell-install-configure.md
[powershell-open]: powershell-install-configure.md#Install

[img-hdi-dashboard]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.png
[img-hdi-dashboard-query-select]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.png
[img-hdi-dashboard-query-select-result]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.result.png
[img-hdi-dashboard-query-select-result-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.result.output.png
[img-hdi-dashboard-query-browse-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.browse.output.png
[image-hdi-clusterstatus]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.ClusterStatus.png
[image-hdi-gettingstarted-powerquery-importdata]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GettingStarted.PowerQuery.ImportData.png
[image-hdi-gettingstarted-powerquery-importdata2]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GettingStarted.PowerQuery.ImportData2.png

<!---HONumber=AcomDC_0302_2016-->