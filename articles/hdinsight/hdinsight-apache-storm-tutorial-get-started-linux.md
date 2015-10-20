<properties
	pageTitle="Tutorial do Apache Storm: Introdução ao Storm baseado em Linux no HDInsight | Microsoft Azure"
	description="Introdução à análise de big data usando o Apache Storm e os exemplos do Storm Starter no HDInsight baseado em Linux. Saiba como usar o Storm para processar dados em tempo real."
	keywords="apache storm,apache storm tutorial,big data analytics,storm starter"
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/09/2015"
   ms.author="larryfr"/>


# Tutorial do Apache Storm: Introdução a exemplos do Storm Starter para análise de big data no HDInsight

O Apache Storm é um sistema de computação escalável, tolerante a falhas, distribuído e em tempo real para o processamento de fluxos de dados. Com o Storm no Azure HDInsight, você pode criar um cluster Storm baseado em nuvem que execute análise de big data em tempo real.

> [AZURE.NOTE]As etapas deste artigo criam um cluster HDInsight baseado em Linux. Para as etapas criarem um Storm baseado em Windows no cluster HDInsight, consulte [tutorial do Apache Storm tutorial: Introdução ao exemplo do Storm Starter usando a análise de dados no HDInsight](hdinsight-apache-storm-tutorial-get-started.md)

## Antes de começar

Você deve ter o seguinte para concluir com êxito este tutorial do Apache Storm:

- **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Familiaridade com o SSH e SCP**. Para obter mais informações sobre como usar SSH e SCP com o HDInsight, consulte o seguinte:

    - **Clientes Linux, Unix ou OS X**: consultem [Usar SSH com Hadoop baseado em Linux no HDInsight no Linux, OS X ou Unix](hdinsight-hadoop-linux-use-ssh-unix.md)

	- **Clientes Windows**: consulte [Usar SSH com Hadoop baseado em Linux no HDInsight do Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

## Criar um cluster Storm

O Storm no HDInsight usa o armazenamento de Blobs do Azure para armazenar arquivos de log e topologias enviadas para o cluster. Use as seguintes etapas para criar uma conta de armazenamento do Azure a ser usada com o cluster:

1. Entre no [Portal de Visualização do Azure][preview-portal].

2. Selecione **NOVO**, selecione __Análises de Dados__ e, em seguida, selecione __HDInsight__

	![Criar um novo cluster no Portal de Visualização do Azure](./media/hdinsight-apache-storm-tutorial-get-started-linux/new-cluster.png)

3. Insira um __Nome de Cluster__, em seguida, selecione __Storm__ para o __Tipo de cluster__. Uma marca de seleção verde será exibida ao lado do __Nome de Cluster__, se ele estiver disponível.

	![Nome do cluster, tipo de cluster e tipo de sistema operacional](./media/hdinsight-apache-storm-tutorial-get-started-linux/clustername.png)

	Selecione o __Ubuntu__ criar um cluster HDInsight baseado em Linux.
	
4. Se você tiver mais de uma assinatura, selecione a entrada __Assinatura__ para selecionar a assinatura do Azure que será usada para o cluster.

5. Para o __Grupo de Recursos__, você pode selecionar a entrada para ver uma lista de grupos de recursos existentes e, em seguida, selecionar um para criação do cluster. Ou então, você pode selecionar __Criar Novo__ e digitar o nome do novo grupo de recursos. Uma marca de seleção verde será exibida para indicar se o novo nome de grupo está disponível.

	> [AZURE.NOTE]Por padrão, essa entrada será um de seus grupos de recursos existentes, se houver algum disponível.

6. Selecione __Credenciais__ e digite uma __Senha de Logon do Cluster__ para o __Nome de Usuário de Logon do Cluster__. Você também deve inserir um __Nome de Usuário de SSH__ e uma __SENHA__ ou uma __CHAVE PÚBLICA__, que será usada para autenticar o usuário SSH. Por fim, use o botão __Selecionar__ para definir as credenciais.

	![Folha de credenciais de cluster](./media/hdinsight-administer-use-portal-linux/clustercredentials.png)

	Para obter mais informações sobre como usar SSH com o HDInsight, consulte um dos seguintes artigos:

	* [Usar SSH com Hadoop baseado em Linux no HDInsight no Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [Usar SSH com Hadoop baseado em Linux no HDInsight no Windows](hdinsight-hadoop-linux-use-ssh-windows)

6. Para a __Fonte de Dados__, você pode selecionar a entrada para escolher uma fonte de dados existente ou criar uma nova.

	![Folha de fonte de dados](./media/hdinsight-apache-storm-tutorial-get-started-linux/datasource.png)
	
	No momento, você pode selecionar uma Conta de Armazenamento do Azure como fonte de dados para um cluster HDInsight. Use o item a seguir para entender as entradas na folha __Fonte de Dados__.
	
	- __Método de Seleção__: defina-o como __De todas as assinaturas__ para habilitar a procura de contas de armazenamento em suas assinaturas. Defina-o como __Chave de Acesso__ se você desejar inserir o __Nome de Armazenamento__ e a __Chave de Acesso__ de uma conta de armazenamento existente.
	
	- __Criar Novo__: use essa opção para criar uma nova conta de armazenamento. Use o campo exibido para inserir o nome da conta de armazenamento. Uma marca de seleção verde será exibida se o nome estiver disponível.
	
	- __Escolher Contêiner Padrão__: use essa opção para inserir o nome do contêiner padrão a ser usado para o cluster. Embora você possa inserir qualquer nome aqui, é recomendável usar o mesmo nome utilizado para o cluster, para que você possa reconhecer facilmente que o contêiner é usado para esse cluster específico.
	
	- __Local__: a região geográfica em que a conta de armazenamento estará ou na qual será criada.
	
		> [AZURE.IMPORTANT]Se for selecionando o local para a fonte de dados padrão, também será definido o local do cluster HDInsight. O cluster e a fonte de dados padrão devem estar localizados na mesma região.
		
	- __Selecionar__: use essa opção para salvar a configuração da fonte de dados.
	
7. Selecione __Camadas de Preços de Nó__ para exibir informações sobre os nós que serão criados para esse cluster. Por padrão, o número de nós de trabalho será definido como __4__. O custo estimado do cluster será mostrado na parte inferior da folha.

	![Folha de camadas de preços de nó](./media/hdinsight-apache-storm-tutorial-get-started-linux/nodepricingtiers.png)
	
	Use o botão __Selecionar__ para salvar as informações de __Camadas de Preços de Nó__.

8. Selecione __Configuração Opcional__. Esta folha permite que você selecione a versão do cluster, bem como defina outras configurações opcionais, como adicionar um __Rede Virtual__ ou configurar um __Metastore personalizado__ para manter dados de Hive e Oozie.

	![Folha de configuração opcional](./media/hdinsight-apache-storm-tutorial-get-started-linux/optionalconfiguration.png)

9. Verifique se a opção __Fixar no quadro inicial__ está selecionada e selecione __Criar__. Isso criará o cluster e adicionará um bloco para o mesmo para o Quadro inicial do seu Portal do Azure. O ícone indica que o cluster está provisionando e será alterado para exibir o ícone de HDInsight após a conclusão da configuração.

	| Durante o provisionamento | Provisionamento concluído |
	| ------------------ | --------------------- |
	| ![Indicador de provisionamento no quadro inicial](./media/hdinsight-apache-storm-tutorial-get-started-linux/provisioning.png) | ![Bloco de cluster provisionado](./media/hdinsight-apache-storm-tutorial-get-started-linux/provisioned.png) |

	> [AZURE.NOTE]Levará algum tempo para que o cluster seja criado, geralmente, cerca de 15 minutos. Use o bloco no Quadro Inicial ou a entrada __Notificações__ à esquerda da página para verificar o processo de provisionamento.

##Executar uma amostra do Starter Storm no HDInsight

Os exemplos de [storm-starter](https://github.com/apache/storm/tree/master/examples/storm-starter) estão incluídos no cluster HDInsight. Nas etapas a seguir, você vai executar o exemplo WordCount.

1. Conecte-se ao cluster HDInsight usando SSH:

		ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
		
	Caso você tenha usado uma senha para proteger sua conta de usuário SSH, ela será solicitada. Se você tiver usado uma chave pública, talvez precise usar o parâmetro `-i` para especificar a chave privada correspondente. Por exemplo: `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`.
		
	Para obter mais informações sobre como usar SSH com o HDInsight baseado em Linux, confira os seguintes artigos:
	
	* [Usar SSH com Hadoop baseado em Linux no HDInsight no Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [Usar SSH com Hadoop baseado em Linux no HDInsight no Windows](hdinsight-hadoop-linux-use-ssh-windows)

2. Use o comando a seguir para iniciar uma topologia de exemplo:

        storm jar storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-0.9.3.2.2.4.9-1.jar storm.starter.WordCountTopology wordcount
		
	> [AZURE.NOTE]A `0.9.3.2.2.4.9-1` parte do nome do arquivo pode ser alterados conforme HDinsight é atualizado com as versões mais recentes do Storm.

    Isso iniciará a topologia de WordCount de exemplo no cluster, com um nome amigável de 'wordcount'. Ele gerar sentenças e conta a ocorrência de cada palavra nas sentenças aleatoriamente.

    > [AZURE.NOTE]Ao enviar a topologia para o cluster, é necessário primeiro copiar o arquivo jar que contém o cluster antes de usar o `storm` comando. Isso pode ser feito usando o `scp` comando do cliente em que o arquivo existe. Por exemplo, `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
    >
    > O exemplo de WordCount e outros exemplos de storm starter, já estão incluídos no cluster em `/usr/hdp/current/storm-client/contrib/storm-starter/`.

##Monitorar a topologia

A IU do Storm fornece uma interface Web para trabalhar com as topologias em funcionamento, e é incluída no seu cluster HDInsight.

> [AZURE.IMPORTANT]A IU do Storm não está disponível publicamente pela Internet e devem ser acessada através de um túnel SSH para o nó principal do cluster HDInsight. Para obter mais informações, consulte [Usar túnel SSH para acessar o ResourceManager, JobHistory, NameNode, Oozie e outras IUs da Web](hdinsight-linux-ambari-ssh-tunnel.md).

Execute as etapas a seguir para exibir a IU do Storm:

1. Depois de criar um túnel SSH para o cluster, abra um navegador da Web para https://CLUSTERNAME.azurehdinsight.net, em que __CLUSTERNAME__ é o nome do cluster. Isso abrirá a interface da Web Ambari.

	> [AZURE.NOTE]Se solicitado a forneça um nome de usuário e senha, insira o administrador de cluster (admin) e a senha que você usou ao criar o cluster. Você pode ser solicitado a autenticar duas vezes, uma vez pelo navegador e uma segunda vez pela IU Web do Ambari; Use as mesmas credenciais para ambos.

2. Na lista de serviços à esquerda da página, selecione __Storm__. Então, selecione __Interface do Usuário do Storm__ em __Links Rápidos__.

    ![Entrada de interface do usuário do Storm em links rápidos](./media/hdinsight-apache-storm-tutorial-get-started-linux/ambari-storm.png)

    Isso exibirá a interface do usuário do Storm:

    ![a interface do usuário do storm](./media/hdinsight-apache-storm-tutorial-get-started-linux/stormui.png)
	
	> [AZURE.NOTE]Se você receber um erro que o servidor não pode ser localizado, você pode não ter estabelecido um túnel SSH para o cluster. Consulte [Usar túnel SSH para acessar o ResourceManager, JobHistory, NameNode, Oozie e outras IUs da Web para obter mais informações](hdinsight-linux-ambari-ssh-tunnel.md).

4. Em **Resumo da topologia**, selecione a entrada **wordcount** na coluna **Nome**. Isso exibirá mais informações sobre a topologia.

	![Painel do Storm com informações da topologia do Storm Starter WordCount.](./media/hdinsight-apache-storm-tutorial-get-started-linux/topology-summary.png)

	Esta página fornece as seguintes informações:

	* **Estatísticas de topologia** -informações básicas sobre o desempenho de topologia, organizadas em janelas de tempo.

		> [AZURE.NOTE]A seleção de uma janela de tempo específica altera a janela de tempo das informações exibidas em outras seções da página.

	* **Spouts** -informações básicas sobre spouts, incluindo o último erro retornado por cada spout.

	* **Bolts** -informações básicas sobre bolts.

	* **Configuração de topologia** -informações detalhadas sobre a configuração de topologia.

	Esta página também fornece ações que podem ser executadas na topologia:

	* **Ativar** - retoma o processamento de uma topologia desativada.

	* **Desativar** - pausa uma topologia em execução.

	* **Reequilibrar** - ajusta o paralelismo da topologia. Você deve reequilibrar topologias em execução depois de alterar o número de nós no cluster. Isso permite que a topologia ajuste o paralelismo para compensar o aumento/diminuição do número de nós no cluster. Para obter mais informações, consulte [Noções básicas sobre o paralelismo de uma topologia Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

	* **Eliminar** - encerra uma topologia do Storm após o tempo limite especificado.

5. Nessa página, selecione uma entrada da seção **Spouts** ou **Bolts**. Isso exibirá informações sobre o componente selecionado.

	![Painel do Storm com informações sobre os componentes selecionados.](./media/hdinsight-apache-storm-tutorial-get-started-linux/component-summary.png)

	Esta página exibe as seguintes informações:

	* **Estatísticas de spout/bolt** -informações básicas sobre o desempenho de componente, organizadas em janelas de tempo.

		> [AZURE.NOTE]A seleção de uma janela de tempo específica altera a janela de tempo das informações exibidas em outras seções da página.

	* **Estatísticas de entrada** (somente bolt) - informações sobre componentes que geram dados consumidos pelo bolt.

	* **Estatísticas de saída** -informações sobre dados emitidos por este bolt.

	* **Executores** -informações sobre instâncias deste componente.

	* **Erros** -erros produzidos por este componente.

5. Ao exibir os detalhes de um spout ou bolt, selecione uma entrada da coluna **Porta** na seção **Executores** para exibir detalhes de uma instância específica do componente.

		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["with"]
		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["nature"]
		2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [snow]
		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [snow, 747293]
		2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [white]
		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [white, 747293]
		2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [seven]
		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [seven, 1493957]

	A partir desses dados, você pode ver que a palavra **seven** ocorreu 1493957 vezes. Essa é a quantidade de vezes em que ela foi encontrada desde que a topologia foi iniciada.

##Parar a topologia

Volte para a página **Resumo da topologia** para a topologia de contagem de palavras e, em seguida, selecione o botão **Eliminar** da seção **Ações de topologia**. Quando solicitado, insira 10 para os segundos a aguardar antes da interrupção da topologia. Após o período de tempo limite, a topologia não será mais exibida quando você visitar a seção **Interface do usuário do Storm** do painel.

##Resumo

Neste tutorial sobre o Storm Apache, você usou o Storm Starter para aprender a criar um Storm no cluster HDInsight e a usar o Painel Storm para implantar, monitorar e gerenciar topologias Storm.

##<a id="next"></a>Próximas etapas

* O documento a seguir contém uma lista de outros exemplos que podem ser usados com o Storm no HDInsight:

	* [Topologias de exemplo para Storm no HDInsight](hdinsight-storm-example-topology.md)

[apachestorm]: https://storm.incubator.apache.org
[stormdocs]: http://storm.incubator.apache.org/documentation/Documentation.html
[stormstarter]: https://github.com/apache/storm/tree/master/examples/storm-starter
[stormjavadocs]: https://storm.incubator.apache.org/apidocs/
[azureportal]: https://manage.windowsazure.com/
[hdinsight-provision]: hdinsight-provision-clusters.md
[preview-portal]: https://portal.azure.com/

<!---HONumber=Oct15_HO3-->