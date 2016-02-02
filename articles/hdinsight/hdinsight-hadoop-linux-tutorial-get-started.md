<properties
   	pageTitle="Tutorial do Linux: Introdução ao Hadoop e ao Hive | Microsoft Azure"
   	description="Siga este tutorial do Linux para começar a usar o Hadoop no HDInsight. Saiba como configurar clusters do Linux e consultar dados com o Hive."
   	services="hdinsight"
   	documentationCenter=""
   	authors="nitinme"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="hero-article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="01/21/2016"
   	ms.author="nitinme"/>

# Tutorial de hadoop: introdução usando o Hadoop com o Hive em HDInsight no Linux

> [AZURE.SELECTOR]
- [Windows](hdinsight-hadoop-tutorial-get-started-windows.md)
- [Linux](hdinsight-hadoop-linux-tutorial-get-started.md)

Este documento fornece uma introdução rápida ao Azure HDInsight no Linux, mostrando como criar um cluster Hadoop baseado em Linux, abrir a interface do usuário da Web do Ambari e, em seguida, executar uma consulta do Hive usando o Modo de Exibição do Hive do Ambari.

> [AZURE.NOTE] Se for iniciante em Hadoop e Big Data, você pode ler mais sobre os termos [Apache Hadoop](http://go.microsoft.com/fwlink/?LinkId=510084), [MapReduce](http://go.microsoft.com/fwlink/?LinkId=510086),[HDFS (Sistema de Arquivos Distribuído do Hadoop)](http://go.microsoft.com/fwlink/?LinkId=510087) e [Hive](http://go.microsoft.com/fwlink/?LinkId=510085). Para entender como o HDInsight habilita o Hadoop no Azure, consulte [Introdução ao Hadoop no HDInsight](hdinsight-hadoop-introduction.md).

## Pré-requisitos

Antes de começar este tutorial do Linux para Hadoop, você deve ter o seguinte:

- **Uma assinatura do Azure**: consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="provision"></a>Provisionar um cluster HDInsight no Linux

Ao provisionar um cluster, você cria recursos de computação do Azure que contêm serviços de Hadoop e recursos. Nesta seção, você provisiona um cluster HDInsight versão 3.2, que se contém o Hadoop versão 2.2. Para obter informações sobre as versões do HDInsight e seus SLAs, consulte [Controle de versão de componentes do HDInsight](hdinsight-component-versioning.md). Para obter informações mais detalhadas sobre como criar um cluster HDInsight, consulte [Provisionar clusters HDInsight usando opções personalizadas][hdinsight-provision].

>[AZURE.NOTE]  Você também pode criar clusters Hadoop que executam o sistema operacional Windows Server. Para obter instruções, consulte [Introdução ao HDInsight no Windows](hdinsight-hadoop-tutorial-get-started-windows.md).

Use as seguintes etapas para criar um novo cluster:

1. Entre no [Portal do Azure](https://ms.portal.azure.com/).
2. Clique em **NOVO**, em **Análises de Dados** e em **HDInsight**.

    ![Criando um novo cluster no Portal do Azure](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.CreateCluster.1.png "Criando um novo cluster no Portal do Azure")

3. Insira um **Nome de Cluster**, selecione **Hadoop** para o **Tipo de Cluster** e, na lista suspensa **Sistema Operacional do Cluster**, selecione **Ubuntu**. Uma marca de seleção verde aparecerá ao lado do nome do cluster, se disponível.

	![Digite o tipo e o nome do cluster](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.CreateCluster.2.png "Digite o tipo e o nome do cluster")

4. Se você tiver mais de uma assinatura, clique na entrada **Assinatura** para selecionar a assinatura do Azure que será usada para o cluster.

5. Clique em **Grupo de recursos** para ver uma lista de grupos de recursos existente e, em seguida, selecione um em que será criado o cluster. Ou, você pode clicar em **Criar novo** e, em seguida, digitar o nome do novo grupo de recursos. Uma marca de seleção verde será exibida para indicar se o novo nome de grupo estiver disponível.

	> [AZURE.NOTE] A entrada padrão será um dos seus grupos de recursos existentes, se houver algum disponível.

6. Clique em **Credenciais** e insira uma senha para o usuário administrador. Você também deve inserir um **Nome de usuário SSH**. Para **Tipo de Autenticação SSH**, clique em **SENHA** e especifique uma senha para o usuário SSH. Clique em **Selecionar** na parte inferior para salvar a configuração de credenciais.

	![Forneça credenciais de cluster](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.CreateCluster.3.png "Forneça credenciais de cluster")

    > [AZURE.NOTE] O SSH é usado para acessar remotamente o cluster HDInsight usando uma linha de comando. O nome de usuário e a senha usados aqui serão usados para se conectar ao cluster por meio do SSH.

	Para obter mais informações sobre como usar SSH com o HDInsight, consulte um dos seguintes documentos:

	* [Usar SSH com Hadoop baseado em Linux no HDInsight no Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
	* [Usar SSH com Hadoop baseado em Linux no HDInsight no Windows](hdinsight-hadoop-linux-use-ssh-windows.md)


7. Clique em **Fonte de Dados** para escolher uma fonte de dados existente para o cluster ou criar uma nova. Ao provisionar um cluster Hadoop no HDInsight, você especifica uma conta de armazenamento do Azure. Um contêiner de armazenamento de blob específico dessa conta é designado como sistema de arquivos padrão, como no sistema de arquivos distribuído Hadoop (HDFS). Por padrão, o cluster HDInsight é provisionado no mesmo datacenter da conta de armazenamento que você especificou. Para obter mais informações, consulte [Usar o Armazenamento de Blob do Azure com o HDInsight](hdinsight-use-blob-storage.md).

	![Folha de fonte de dados](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.CreateCluster.4.png "Forneça a configuração da fonte de dados")

	No momento, você pode selecionar uma Conta de Armazenamento do Azure como fonte de dados para um cluster HDInsight. Use o item a seguir para entender as entradas na folha **Fonte de Dados**.

	- **Método de Seleção**: defina essa opção como **De todas as assinaturas** para habilitar a procura de contas de armazenamento em todas as suas assinaturas. Defina essa opção como **Tecla de Acesso** se você desejar inserir o **Nome de Armazenamento** e a **Tecla de Acesso** de uma conta de armazenamento existente.

	- **Selecionar conta de armazenamento/Criar Nova**: clique em **Selecionar conta de armazenamento** para procurar e escolher uma conta de armazenamento existente que deseja associar ao cluster. Ou, clique em **Criar nova** para criar uma nova conta de armazenamento. Use o campo exibido para inserir o nome da conta de armazenamento. Uma marca de seleção verde será exibida se o nome estiver disponível.

	- **Escolher Contêiner Padrão**: use essa opção para inserir o nome do contêiner padrão a ser usado para o cluster. Embora você possa inserir qualquer nome aqui, é recomendável usar o mesmo nome que o cluster para que você possa reconhecer facilmente que o contêiner é usado para este cluster específico.

	- **Local**: a região geográfica na qual a conta de armazenamento está ou será criada.

		> [AZURE.IMPORTANT] Selecionar o local para a fonte de dados padrão também definirá o local do cluster do HDInsight. A fonte de dados padrão e o cluster devem estar localizados na mesma região.

	Clique em **Selecionar** para salvar a configuração de fonte de dados.

8. Clique em **Camadas de preços do nó** para exibir informações sobre os nós que serão criados para esse cluster. Defina o número de nós de trabalho que você precisa para o cluster. O custo estimado do cluster será mostrado na folha.

	![Folha de camadas de preços de nó](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.CreateCluster.5.png "Especifique o número de nós de cluster")
    
    > [AZURE.IMPORTANT] Se você planeja ter mais de 32 nós de trabalho, seja na criação do cluster ou em seu dimensionamento após a criação, deverá selecionar um tamanho de nó de cabeçalho com pelo menos 8 núcleos e 14 GB de RAM.
    >
    > Para saber mais sobre tamanhos de nós e custos associados, consulte [Preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

	Clique em **Selecionar** para salvar a configuração de preços do nó.

9. Na folha **Novo Cluster HDInsight**, verifique se **Fixar no Quadro Inicial** está selecionado e clique em **Criar**. Isso criará o cluster e adicionará um bloco para o mesmo para o quadro inicial do seu Portal do Azure. O ícone indica que o cluster está provisionando e será alterado para exibir o ícone de HDInsight após a conclusão da configuração.

Durante o provisionamento|Provisionamento concluído
------------------|---------------------
	![Indicador de provisionamento no quadro inicial](./media/hdinsight-hadoop-linux-tutorial-get-started/provisioning.png)|![Bloco de cluster provisionado](./media/hdinsight-hadoop-linux-tutorial-get-started/provisioned.png)

> [AZURE.NOTE] Levará algum tempo para que o cluster seja criado, geralmente, cerca de 15 minutos. Use o bloco no Quadro Inicial ou a entrada **Notificações** à esquerda da página para verificar o processo de provisionamento.

Quando o provisionamento for concluído, clique no bloco para o cluster a partir do quadro inicial para iniciar a folha de cluster.

##Conectar-se ao Modo de Exibição do Hive

Os Modos de Exibição do Ambari fornecem vários utilitários usando uma página da Web. Nas seções a seguir, você usará o modo de exibição do Hive para executar consultas do Hive no cluster HDInsight.

> [AZURE.NOTE] O Ambari é um utilitário de monitoramento e gerenciamento fornecido com clusters HDInsight baseados em Linux. O Ambari tem vários recursos que não serão discutidos neste documento. Para saber mais, confira [Gerenciar clusters HDInsight usando a interface do usuário da Web do Ambari](hdinsight-hadoop-manage-ambari.md).

Para abrir os Modos de Exibição do Ambari do Portal do Azure, selecione seu cluster HDInsight e escolha __Modos de Exibição do Ambari__ na seção __Links Rápidos__

![seção links rápidos](./media/hdinsight-hadoop-linux-tutorial-get-started/quicklinks.png)

Você também pode navegar diretamente para o Ambari indo para https://CLUSTERNAME.azurehdinsight.net em um navegador da Web (em que __NOMEDOCLUSTER__ é o nome do cluster HDInsight) e escolhendo o conjunto de quadrados no menu da página (ao lado do link __Admin__ e do botão no lado esquerdo da página) para listar os modos de exibição disponíveis. Escolha o __modo de exibição do Hive__.

![Escolhendo modos de exibição do Ambari](./media/hdinsight-hadoop-linux-tutorial-get-started/selecthiveview.png).

> [AZURE.NOTE] Ao acessar o Ambari, você receberá uma solicitação para se autenticar no site. Insira o nome da conta e senha de administrador (o padrão é `admin`) que você usou ao criar o cluster.

Você verá uma página semelhante à seguinte:

![Imagem da página de modo de exibição do Hive, que contém uma seção de editor de consultas](./media/hdinsight-hadoop-linux-tutorial-get-started/hiveview.png)

##<a name="hivequery"></a>Executar uma consulta do Hive

Use as etapas a seguir no modo de exibição do Hive para executar uma consulta do Hive em relação aos dados incluídos no cluster.

1. Na seção __Editor de Consultas__ da página, cole as seguintes instruções HiveQL na planilha:

		DROP TABLE log4jLogs;
		CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
		ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
		STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
		SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;

	Essas instruções executam as seguintes ações:

	- **DROP TABLE** - exclui a tabela e o arquivo de dados caso a tabela já exista.
	- **CREATE EXTERNAL TABLE**: cria uma nova tabela "externa" no Hive. As tabelas externas armazenam apenas a definição da tabela no Hive; os dados são deixados no local original.
	- **ROW FORMAT** - informa ao Hive como os dados são formatados. Nesse caso, os campos em cada log são separados por um espaço.
	- **STORED AS TEXTFILE LOCATION** - informa ao Hive onde os dados são armazenados (o diretório de exemplos/dados) e que estão armazenados como texto.
	- **SELECT** - Seleciona uma contagem de todas as linhas em que a coluna t4 contém o valor [ERROR].

	>[AZURE.NOTE] Use tabelas externas se os dados subjacentes tiverem que ser atualizados por uma origem externa, como um processo automatizado de carregamento de dados, ou por outra operação MapReduce, mas você quiser que as consultas Hive sempre usem os dados mais recentes. Remover uma tabela externa *não* exclui os dados, somente a definição de tabela.

2. Use o botão __Executar__ na parte inferior do Editor de Consultas para iniciar a consulta. Ele deverá ficar laranja e o texto será alterado para __Parar a execução__. Uma seção __Resultados do Processo de Consulta__ deve aparecer abaixo do Editor de Consultas e exibir informações sobre o trabalho.

    > [AZURE.IMPORTANT] Alguns navegadores podem não atualizar corretamente o log ou as informações dos resultados. Se você executar um trabalho e parecer que ele não cessa a execução sem atualizar o log ou retornar resultados, em vez disso, experimente usar o Mozilla FireFox ou o Google Chrome.
    
3. Depois que a consulta for concluída, a seção __Resultados do Processo de Consulta__ exibirá os resultados da operação. O botão __Parar a execução__ também se transformará em um botão verde __Executar__. A guia __Resultados__ deve conter as seguintes informações:

        sev       cnt
        [ERROR]   3

    A guia __Logs__ pode ser usada para exibir as informações de log criadas pelo trabalho. Você pode usar isso na solução de problemas, caso haja problemas com uma consulta.
    
    > [AZURE.TIP] Observe a lista suspensa __Salvar resultados__ no canto superior esquerdo da seção __Resultados do Processo de Consulta__. Você pode usá-la para baixar os resultados ou salvá-los no armazenamento do HDInsight como um arquivo CSV.

3. Selecione as quatro primeiras linhas dessa consulta e selecione __Executar__. Observe que não há resultados quando o trabalho é concluído. Isso ocorre porque, com o uso do botão __Executar__ quando parte da consulta é selecionada, apenas as instruções escolhidas são executadas. Nesse caso, a seleção não incluiu a instrução final que recupera linhas da tabela. Se escolher apenas essa linha e usar __Executar__, você verá os resultados esperados.

3. Use o botão __Nova Planilha__ na parte inferior do __Editor de Consultas__ para criar uma nova planilha. Na nova planilha, digite as seguintes instruções HiveQL:

		CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
		INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';

	Essas instruções executam as seguintes ações:

	- **CREATE TABLE IF NOT EXISTS** - cria uma tabela, se ela ainda não existir. Como a palavra-chave **EXTERNAL** não é usada, esta é uma tabela interna, que é armazenada no data warehouse do Hive e totalmente gerenciada pelo Hive. Ao contrário das tabelas externas, remover uma tabela interna excluirá também os dados subjacentes.
	- **STORES AS ORC**: armazena os dados no formato ORC (Optimized Row Columnar). Esse é um formato altamente otimizado e eficiente para o armazenamento de dados do Hive.
	- **INSERT OVERWRITE ... SELECT** - Seleciona linhas da tabela **log4jLogs** que contêm [ERROR] e insere os dados na tabela **errorLogs**.
    
    Use o botão __Executar__ para executar essa consulta. A guia __Resultados__ não conterá informações, pois nenhuma linha é retornada por essa consulta, mas o status deverá ser mostrado como __ÊXITO__.
    
4. À direita do Editor de Consultas, há uma linha de ícones. Selecione o que se parece com uma cadeia.

    ![ícones](./media/hdinsight-hadoop-linux-tutorial-get-started/icons.png)
    
    Esse é o modo de exibição __Explicação Visual__ da consulta, que pode ser útil na compreensão do fluxo de consultas complexas. Você pode exibir um equivalente textual desse modo de exibição usando o botão __Explicar__ no Editor de Consultas.
    
    ![imagem de explicação visual](./media/hdinsight-hadoop-linux-tutorial-get-started/visualexplain.png)
    
    Os outros ícones são os seguintes:
    
    * **Configurações**: o ícone de engrenagem permite que você altere as configurações do Hive, como a configuração `hive.execution.engine` ou os parâmetros do Tez.
    * **Tez**: exibe o Gráfico Acíclico Dirigido (DAG) que o Tez utilizou para fazer a consulta. Se você quiser exibir o DAG de consultas executadas anteriormente, use __Modo de Exibição do Tez exibição__.
    * **Notificações**: exibe notificações, como “A consulta foi enviada” ou se algum erro ocorrer ao executar uma consulta.

5. Escolha o ícone __SQL__ para retornar ao Editor de Consultas. Em seguida, crie uma nova planilha e insira a seguinte consulta:

        SELECT * from errorLogs;
    
    Clique no botão __Salvar como__ na parte inferior do editor. Nomeie essa consulta como __Errorlogs__ e marque __OK__. Observe que o nome da planilha é alterado para __Errorlogs__.
    
    As consultas salvas também aparecem na guia __Consultas Salvas__ na parte superior da página. Selecione essa opção e você verá __Errorlogs__ listado. A seleção do nome abrirá a consulta no Editor de Consultas.

4. Execute a consulta __Errorlogs__. Os resultados serão os seguintes:

        errorlogs.t1 	errorlogs.t2 	errorlogs.t3 	errorlogs.t4 	errorlogs.t5 	errorlogs.t6 	errorlogs.t7
        2012-02-03 	18:35:34 	SampleClass0 	[ERROR] 	incorrect 	id 	
        2012-02-03 	18:55:54 	SampleClass1 	[ERROR] 	incorrect 	id 	
        2012-02-03 	19:25:27 	SampleClass4 	[ERROR] 	incorrect 	id

## <a name="nextsteps"></a>Próximas etapas

Neste documento, você aprendeu como criar um cluster HDInsight baseado em Linux usando o Portal do Azure, conectar-se ao cluster usando o SSH e como executar consultas básicas do Hive.

Para saber mais sobre como trabalhar com o HDInsight, consulte:

- Para saber mais sobre como usar o Hive com HDInsight, inclusive como executar consultas de Hive do Visual Studio, consulte [usar o Hive com HDInsight][hdinsight-use-hive].

- Para saber mais sobre o Pig, uma linguagem usada para transformar dados, consulte [Usar o Pig com o HDInsight][hdinsight-use-pig].

- Para saber mais sobre o MapReduce, uma maneira de gravar programas que processam dados no Hadoop, consulte [Usar o MapReduce com HDInsight][hdinsight-use-mapreduce].

- Para saber mais sobre como usar as Ferramentas do HDInsight para o Visual Studio para analisar dados no HDInsight, consulte [Introdução às ferramentas do Hadoop do Visual Studio para o HDInsight](hdinsight-hadoop-visual-studio-tools-get-started.md).

Se você estiver pronto para começar a trabalhar com seus próprios dados e precisa saber mais sobre como o HDInsight armazena dados ou obtém dados no HDInsight, consulte:

- Para obter informações sobre como o HDInsight usa o armazenamento de blobs do Azure, consulte [Usar armazenamento de Blobs do Azure com o HDInsight](hdinsight-use-blob-storage.md).

- Para obter informações sobre como carregar arquivos no HDInsight, consulte [Carregar dados no HDInsight][hdinsight-upload-data].

Se você quiser saber mais sobre como criar ou gerenciar um cluster HDInsight, consulte:

- Para saber mais sobre como gerenciar o cluster HDInsight baseado em Linux, consulte [Gerenciar clusters HDInsight usando o Ambari](hdinsight-hadoop-manage-ambari.md).

- Para saber mais sobre as opções que você poderá selecionar ao criar um cluster HDInsight, consulte [Provisionar o HDInsight no Linux usando opções personalizadas](hdinsight-hadoop-provision-linux-clusters.md).

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
[powershell-install-configure]: ../install-configure-powershell.md
[powershell-open]: ../install-configure-powershell.md#Install

[img-hdi-dashboard]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.png
[img-hdi-dashboard-query-select]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.png
[img-hdi-dashboard-query-select-result]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.result.png
[img-hdi-dashboard-query-select-result-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.result.output.png
[img-hdi-dashboard-query-browse-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.browse.output.png
[image-hdi-clusterstatus]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.ClusterStatus.png
[image-hdi-gettingstarted-powerquery-importdata]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GettingStarted.PowerQuery.ImportData.png
[image-hdi-gettingstarted-powerquery-importdata2]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GettingStarted.PowerQuery.ImportData2.png

<!---HONumber=AcomDC_0128_2016-->