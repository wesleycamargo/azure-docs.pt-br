---
title: 'Tutorial do Apache Storm: Introdução ao Storm baseado em Linux no HDInsight | Microsoft Docs'
description: Introdução à análise de big data usando o Apache Storm e os exemplos do Storm Starter no HDInsight baseado em Linux. Saiba como usar o Storm para processar dados em tempo real.
keywords: apache storm, tutorial do apache storm, análise de big data, storm starter
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun

ms.service: hdinsight
ms.devlang: java
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/16/2016
ms.author: larryfr

---
# Tutorial do Apache Storm: Introdução a exemplos do Storm Starter para análise de big data no HDInsight
O Apache Storm é um sistema de computação escalável, tolerante a falhas, distribuído e em tempo real para o processamento de fluxos de dados. Com o Storm no Azure HDInsight, você pode criar um cluster Storm baseado em nuvem que execute análise de big data em tempo real.

> [!NOTE]
> As etapas deste artigo criam um cluster HDInsight baseado em Linux. Para obter as etapas de criação de um Storm baseado no Windows no cluster HDInsight, consulte o [tutorial do Apache Storm: Introdução ao exemplo do Storm Starter usando a análise de dados no HDInsight](hdinsight-apache-storm-tutorial-get-started.md)
> 
> 

## Pré-requisitos
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Você deve ter o seguinte para concluir com êxito este tutorial do Apache Storm:

* **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Familiaridade com o SSH e o SCP**. Para obter mais informações sobre como usar SSH e SCP com o HDInsight, consulte o seguinte:
  
  * **Clientes Linux, Unix ou OS X**: consultem [Usar SSH com Hadoop baseado em Linux no HDInsight no Linux, OS X ou Unix](hdinsight-hadoop-linux-use-ssh-unix.md)
  * **Clientes Windows**: consulte [Usar SSH com Hadoop baseado em Linux no HDInsight do Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

### Requisitos de controle de acesso
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## Criar um cluster Storm
Nesta seção, você cria um cluster do HDInsight versão 3.2 (Storm versão 0.9.3) usando um modelo do Azure Resource Manager. Para obter informações sobre as versões do HDInsight e seus SLAs, consulte [Controle de versão de componentes do HDInsight](hdinsight-component-versioning.md). Para obter outros métodos de criação de cluster, confira [Criar clusters do HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

1. Clique na imagem a seguir para abrir o modelo no portal do Azure.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-storm-cluster-in-hdinsight.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
    O modelo está localizado em um contêiner de blob público, *https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-storm-cluster-in-hdinsight.json*.
2. Na folha Parâmetros, insira o seguinte:
   
   * **ClusterName**: insira um nome para o cluster Hadoop que você criará.
   * **Nome e senha de logon do cluster**: o nome de logon padrão é admin.
   * **Nome de usuário e senha de SSH**.
     
     Anote esses valores. Você precisará deles mais tarde no tutorial.
     
     > [!NOTE]
     > O SSH é usado para acessar remotamente o cluster HDInsight usando uma linha de comando. O nome de usuário e a senha usados aqui serão usados para se conectar ao cluster por meio do SSH. Além disso, o nome de usuário SSH deve ser exclusivo, pois ele cria uma conta de usuário em todos os nós de cluster HDInsight. Veja a seguir alguns dos nomes de conta reservados para uso pelos serviços no cluster e que não podem ser usados como o nome de usuário SSH:
     > 
     > root, hdiuser, storm, hbase, ubuntu, zookeeper, hdfs, yarn, mapred, hbase, hive, oozie, falcon, sqoop, admin, tez, hcat, hdinsight-zookeeper.
     > 
     > Para obter mais informações sobre como usar SSH com o HDInsight, consulte um dos seguintes artigos:
     > 
     > * [Usar SSH com Hadoop baseado em Linux no HDInsight no Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
     > * [Usar SSH com Hadoop baseado em Linux no HDInsight no Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
     > 
     > 

3\. Clique em **OK** para salvar os parâmetros.

4\. Na folha **Implantação personalizada**, clique na caixa suspensa **Grupo de recursos** e em **Novo** para criar um novo grupo de recursos. O grupo de recursos é um contêiner que agrupa o cluster, a conta de armazenamento dependente e outros recursos vinculados.

5\. Clique em **Termos legais** e em **Criar**.

6\. Clique em **Criar**. Você verá um novo bloco intitulado Como enviar a implantação para a implantação do modelo. É preciso sobre cerca de 20 minutos para criar o cluster e o banco de dados SQL.

## Executar uma amostra do Starter Storm no HDInsight
Os exemplos de [storm-starter](https://github.com/apache/storm/tree/master/examples/storm-starter) estão incluídos no cluster HDInsight. Nas etapas a seguir, você vai executar o exemplo WordCount.

1. Conecte-se ao cluster HDInsight usando SSH:
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
    Caso você tenha usado uma senha para proteger sua conta de usuário SSH, ela será solicitada. Se você tiver usado uma chave pública, talvez precise usar o parâmetro `-i` para especificar a chave privada correspondente. Por exemplo: `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`.
   
    Para obter mais informações sobre como usar SSH com o HDInsight baseado em Linux, confira os seguintes artigos:
   
   * [Usar SSH com Hadoop baseado em Linux no HDInsight no Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
   * [Usar SSH com Hadoop baseado em Linux no HDInsight no Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
2. Use o comando a seguir para iniciar uma topologia de exemplo:
   
        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-0.10.0.2.4.2.4-5.jar storm.starter.WordCountTopology wordcount
   
   > [!NOTE]
   > A `0.10.0.2.4.2.4-5` parte do nome do arquivo pode ser alterados conforme HDInsight é atualizado com as versões mais recentes do Storm.
   > 
   > 
   
    Isso iniciará a topologia de WordCount de exemplo no cluster, com um nome amigável de 'wordcount'. Ele gerar sentenças e conta a ocorrência de cada palavra nas sentenças aleatoriamente.
   
   > [!NOTE]
   > Ao enviar a topologia para o cluster, primeiro você deverá copiar o arquivo jar com o cluster antes de usar o comando `storm`. Isso pode ser feito usando o comando `scp` do cliente em que o arquivo existe. Por exemplo, `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
   > 
   > O exemplo de WordCount e outros exemplos de storm starter já estão incluídos no cluster em `/usr/hdp/current/storm-client/contrib/storm-starter/`.
   > 
   > 

## Monitorar a topologia
A IU do Storm fornece uma interface Web para trabalhar com as topologias em funcionamento, e é incluída no seu cluster HDInsight.

Use as etapas a seguir para monitorar a topologia usando a interface do usuário do Storm:

1. Abra um navegador da Web em https://CLUSTERNAME.azurehdinsight.net/stormui, em que **CLUSTERNAME** é o nome do cluster. Isso abrirá a interface do usuário do Storm.
   
   > [!NOTE]
   > Se solicitado a forneça um nome de usuário e senha, insira o administrador de cluster (admin) e a senha que você usou ao criar o cluster.
   > 
   > 
2. Em **Resumo da topologia**, selecione a entrada **wordcount** na coluna **Nome**. Isso exibirá mais informações sobre a topologia.
   
    ![Painel do Storm com informações da topologia do Storm Starter WordCount.](./media/hdinsight-apache-storm-tutorial-get-started-linux/topology-summary.png)
   
    Esta página fornece as seguintes informações:
   
   * **Estatísticas de topologia** -informações básicas sobre o desempenho de topologia, organizadas em janelas de tempo.
     
     > [!NOTE]
     > A seleção de uma janela de tempo específica altera a janela de tempo das informações exibidas em outras seções da página.
     > 
     > 
   * **Spouts** -informações básicas sobre spouts, incluindo o último erro retornado por cada spout.
   * **Bolts** -informações básicas sobre bolts.
   * **Configuração de topologia** -informações detalhadas sobre a configuração de topologia.
     
     Esta página também fornece ações que podem ser executadas na topologia:
   * **Ativar** - retoma o processamento de uma topologia desativada.
   * **Desativar** - pausa uma topologia em execução.
   * **Reequilibrar** - ajusta o paralelismo da topologia. Você deve reequilibrar topologias em execução depois de alterar o número de nós no cluster. Isso permite que a topologia ajuste o paralelismo para compensar o aumento/diminuição do número de nós no cluster. Para saber mais, consulte [Noções básicas sobre o paralelismo de uma topologia do Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).
   * **Eliminar** - encerra uma topologia do Storm após o tempo limite especificado.
3. Nessa página, selecione uma entrada da seção **Spouts** ou **Bolts**. Isso exibirá informações sobre o componente selecionado.
   
    ![Painel do Storm com informações sobre os componentes selecionados.](./media/hdinsight-apache-storm-tutorial-get-started-linux/component-summary.png)
   
    Esta página exibe as seguintes informações:
   
   * **Estatísticas de spout/bolt** -informações básicas sobre o desempenho de componente, organizadas em janelas de tempo.
     
     > [!NOTE]
     > A seleção de uma janela de tempo específica altera a janela de tempo das informações exibidas em outras seções da página.
     > 
     > 
   * **Estatísticas de entrada** (somente bolt) - informações sobre componentes que geram dados consumidos pelo bolt.
   * **Estatísticas de saída** -informações sobre dados emitidos por este bolt.
   * **Executores** -informações sobre instâncias deste componente.
   * **Erros** -erros produzidos por este componente.
4. Ao exibir os detalhes de um spout ou bolt, selecione uma entrada da coluna **Porta** na seção **Executores** para exibir detalhes de uma instância específica do componente.
   
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["with"]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["nature"]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [snow]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [snow, 747293]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [white]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [white, 747293]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [seven]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [seven, 1493957]
   
    A partir desses dados, você pode ver que a palavra **seven** ocorreu 1493957 vezes. Essa é a quantidade de vezes em que ela foi encontrada desde que a topologia foi iniciada.

## Parar a topologia
Volte para a página **Resumo da topologia** para a topologia de contagem de palavras e, em seguida, selecione o botão **Eliminar** da seção **Ações de topologia**. Quando solicitado, insira 10 para os segundos a aguardar antes da interrupção da topologia. Após o período de tempo limite, a topologia não será mais exibida quando você visitar a seção **Interface do usuário do Storm** do painel.

## Excluir o cluster
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a id="next"></a>Próximas etapas
Neste tutorial sobre o Storm Apache, você usou o Storm Starter para aprender a criar um Storm no cluster HDInsight e a usar o Painel Storm para implantar, monitorar e gerenciar topologias Storm. A seguir, aprenda a [Desenvolver topologias baseadas em Java usando o Maven](hdinsight-storm-develop-java-topology.md).

Se você já está familiarizado com o desenvolvimento de topologias baseadas em Java e deseja implantar uma topologia existente no HDInsight, consulte [Implantar e gerenciar topologias Apache Storm no HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md).

[apachestorm]: https://storm.incubator.apache.org
[stormdocs]: http://storm.incubator.apache.org/documentation/Documentation.html
[stormstarter]: https://github.com/apache/storm/tree/master/examples/storm-starter
[stormjavadocs]: https://storm.incubator.apache.org/apidocs/
[azureportal]: https://manage.windowsazure.com/
[hdinsight-provision]: hdinsight-provision-clusters.md
[preview-portal]: https://portal.azure.com/

<!---HONumber=AcomDC_1005_2016-->