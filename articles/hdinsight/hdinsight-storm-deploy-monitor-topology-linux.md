<properties
   pageTitle="Implantar e gerenciar topologias do Apache Storm no HDInsight| Microsoft Azure"
   description="Aprenda a implantar, monitorar e gerenciar topologias do Apache Storm usando o Painel do Storm no HDInsight. Use as ferramentas do Hadoop para Visual Studio."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/14/2015"
   ms.author="larryfr"/>

# Implantar e gerenciar topologias Apache Storm no HDInsight baseado nem Linux

Neste documento, conheça as noções básicas de gerenciamento e topologias Storm de monitoramento funcionando no Storm baseado em Linux e clusters HDInsight.

> [AZURE.IMPORTANT]As etapas deste artigo exigem um Storm baseado em Linux no cluster HDInsight. Para obter informações sobre as topologias de implantação e monitoramento em HDInsight baseado em Windows, consulte [Implantar e gerenciar topologias do Apache Storm no HDInsight baseado em Windows](hdinsight-storm-deploy-monitor-topology.md)

## Pré-requisitos

- **Storm baseado em Linux no cluster HDInsight**: consulte [Introdução com o Apache Storm no HDInsight](hdinsight-storm-get-started-linux.md) para etapas para criar um cluster

- **Familiaridade com SSH e SCP**: para obter mais informações sobre como usar SSH e SCP com o HDInsight, consulte o seguinte:
    - **Clientes Linux, Unix ou OS X**: consultem [Usar SSH com Hadoop baseado em Linux no HDInsight no Linux, OS X ou Unix](hdinsight-hadoop-linux-use-ssh-unix.md)
    - **Clientes Windows**: consulte [Usar SSH com Hadoop baseado em Linux no HDInsight do Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

- **Um cliente SCP**: é fornecido com todos os sistemas Linux, Unix e OS X. Para clientes Windows é recomendável o PSCP, que está disponível na [página de download do PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

## Iniciar uma topologia do Storm

1. Use o SSH para conectar ao cluster HDInsight. Substitua o **USERNAME** pelo nome do seu logon SSH. Substitua o **CLUSTERNAME** pelo nome do seu cluster HDInsight:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    Para obter mais informações sobre como usar o SSH para se conectar ao cluster HDInsight, consulte os seguintes documentos:
    
        - **Linux, Unix or OS X clients**: See [Use SSH with Linux-based Hadoop on HDInsight from Linux, OS X or Unix](hdinsight-hadoop-linux-use-ssh-unix.md)
        
        - **Windows clients**: See [Use SSH with Linux-based Hadoop on HDInsight from Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. Use o comando a seguir para iniciar uma topologia de exemplo:

        storm jar storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-0.9.3.2.2.4.9-1.jar storm.starter.WordCountTopology WordCount

    Isso iniciará a topologia de WordCount de exemplo no cluster. Ele gerar sentenças e conta a ocorrência de cada palavra nas sentenças aleatoriamente.

    > [AZURE.NOTE]Ao enviar a topologia para o cluster, é necessário primeiro copiar o arquivo jar que contém o cluster antes de usar o `storm` comando. Isso pode ser feito usando o `scp` comando do cliente em que o arquivo existe. Por exemplo, `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
    >
    > O exemplo de WordCount e outros exemplos de storm starter, já estão incluídos no cluster em `/usr/hdp/current/storm-client/contrib/storm-starter/`.

##Monitorar e gerenciar usando o comando storm

O `storm` utilitário permite que você trabalhe com as topologias de execução na linha de comando. A lista a seguir é de comandos usados normalmente. Use `storm -h` para uma lista completa de comandos.

###Topologias de lista

Use o comando a seguir para listar todas as topologias em execução:

    storm list
    
Isso retornará informações similares às seguintes:

    Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
    -------------------------------------------------------------------
    WordCount            ACTIVE     29         2            263

###Desativar e reativar

A desativação de uma topologia pausa até que seja interrompido ou reativado. Use o seguinte para desativar e reativar:

    storm Deactivate TOPOLOGYNAME
    
    storm Activate TOPOLOGYNAME

###Eliminar uma topologia em execução

Topologias Storm, uma vez iniciadas, continuará em execução até serem interrompidas. Para interrompê-la, use o comando a seguir:

    storm stop TOPOLOGYNAME

###Rebalanceamento

Rebalancear uma topologia permite que o sistema revise o paralelismo da topologia. Por exemplo, se você redimensionou o cluster para adicionar mais anotações, o rebalanceamento permitirá que uma topologia em execução faça uso de novos nós.

> [AZURE.WARNING]Rebalancear uma topologia primeiro desativa a topologia, em seguida, redistribui os trabalhos uniformemente no cluster e finalmente retorna a topologia para o estado que estava antes do rebalanceamento. Portanto, se a topologia estava ativa, ela se tornará ativa novamente. Se estiver desativada, permanecerá desativada.

    storm rebalance TOPOLOGYNAME

##Monitorar e gerenciar usando a IU do Storm

A IU do Storm fornece uma interface Web para trabalhar com as topologias em funcionamento, e é incluída no seu cluster HDInsight.

> [AZURE.IMPORTANT]A IU do Storm não está disponível publicamente pela Internet e devem ser acessada através de um túnel SSH para o nó principal do cluster HDInsight. Para obter informações sobre como criar e usar um túnel SSH, consulte [Usar um túnel SSH para acessar a interface do usuário da Web Ambari, ResourceManager, JobHistory, NameNode, Oozie e outras interfaces do usuário da Web](hdinsight-linux-ambari-ssh-tunnel.md).

Execute as etapas a seguir para exibir a IU do Storm:

1. Abra um navegador da Web para a Ambari Web para seu cluster HDInsight. A URL da Ambari Web é https://CLUSTERNAME.azurehdinsight.net, em que __CLUSTERNAME__ é o nome do cluster.

2. Na lista de serviços à esquerda da página, selecione __Storm__. Então, selecione __Interface do Usuário do Storm__ em __Links Rápidos__.

    ![Entrada de interface do usuário do Storm em links rápidos](./media/hdinsight-storm-deploy-monitor-topology-linux/ambari-storm.png)

    Isso exibirá a interface do usuário do Storm:

    ![a interface do usuário do storm](./media/hdinsight-storm-deploy-monitor-topology-linux/storm-ui.png)

> [AZURE.NOTE]Ao trabalhar com a IU Storme, você pode observar que algumas versões do Internet Explorer não atualizam corretamente a interface do usuário depois que você o visitou pela primeira vez. Por exemplo, ela pode não mostrar as novas topologias enviadas ou pode mostrar uma topologia como ativa quando na verdade ela foi desativada anteriormente. A Microsoft está ciente desse problema e está trabalhando em uma solução.

### Página principal

A página principal da IU Storm fornece as seguintes informações:- **Resumo de Cluster**: informações básicas sobre o cluster do Storm - **Resumo da topologia**: uma lista das topologias em execução. Use os links desta seção para exibir mais informações sobre topologias específicas. - **Resumo do supervisor**: informações sobre supervisor to Storm. - **Configuração Nimbus**: configuração Nimbus para o cluster.

### Resumo da topologia

Selecionar um link da seção **Resumo da topologia** exibe as seguintes informações sobre a topologia:- **Resumo da topologia**: informações básicas sobre a topologia. - **Ações de topologia**: ações de gerenciamento que podem ser executadas para a topologia. - **Ativar**: processamento de currículos de uma topologia desativada. - **desativar**: Pausa uma execução de topologia. - **Reequilibrar**: ajusta o paralelismo da topologia. Você deve reequilibrar topologias em execução depois de alterar o número de nós no cluster. Isso permite que a topologia ajuste o paralelismo para compensar o aumento/diminuição do número de nós no cluster.

      For more information, see <a href="http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">Understanding the parallelism of a Storm topology</a>.

  - **Eliminar**: encerra uma topologia Storm após o tempo limite especificado.

- **Estatísticas da topologia**: estatísticas sobre a topologia. Use os links na coluna **Janela** para definir o período de tempo para as entradas restantes na página.
- **Spouts**: os spouts usados pela topologia. Use os links desta seção para exibir mais informações sobre spouts específicos.
- **Bolts**: os bolts usados pela topologia. Use os links desta seção para exibir mais informações sobre bolts específicos.
- **Configuração da topologia**: a configuração da topologia selecionada.

### Resumo de Spout e Bolt

Selecionar um spout dos **Spouts** ou **Bolts** exibe as seguintes informações sobre o item selecionado:- **Resumo dos componentes**: Informações básicas sobre o spout ou bolt. - **Estatísticas de relâmpago/Spout**: Estatísticas sobre o spout ou o raio. Use os links na coluna **Janela** para definir o período de tempo para as entradas restantes na página. - **Estatísticas de entrada** (apenas bolt): Informações sobre os fluxos de entrada consumida pelo bolt. - **Estatísticas de saída**: obter informações sobre os fluxos de emissores spout ou bolt. - **Executores**: informações sobre as instâncias do spout ou bolt. Selecione a entrada da **Porta** para um executor específico para exibir um log de informações de diagnóstico produzido para esta instância.- **Erros**: Qualquer informação de erro para esse spout ou bolt.

## API REST

A interface do usuário do Storm é criada sobre a API REST e, portanto, você pode realizar gerenciamento semelhante e monitorar a funcionalidade usando a API REST. Você pode usar a API REST para criar ferramentas personalizadas para o gerenciamento e o monitoramento de topologias Storm.

Para obter mais informações, consulte <a href="https://github.com/apache/storm/blob/master/STORM-UI-REST-API.md" target="_base">API REST da interface do usuário do Storm</a>. As informações a seguir são específicas para o uso da API REST com Apache Storm no HDInsight.

> [AZURE.IMPORTANT]A API do REST Storm não está disponível publicamente pela Internet, e deve ser acessada usando um túnel SSH para o nó principal do cluster HDInsight. Para obter informações sobre como criar e usar um túnel SSH, consulte [Usar um túnel SSH para acessar a interface do usuário da Web Ambari, ResourceManager, JobHistory, NameNode, Oozie e outras interfaces do usuário da Web](hdinsight-linux-ambari-ssh-tunnel.md).

### URI de base

A URI de base para a API REST em clusters baseados em Linux HDInsight é **https://headnode0:8744/api/v1/</a>**.

### Autenticação

As solicitações para a API REST devem usar a **autenticação básica** e, portanto, você usará o nome do administrador e a senha do cluster HDInsight.

> [AZURE.NOTE]Como a autenticação básica é enviada usando texto não criptografado, você **sempre** deverá usar HTTPS para proteger as comunicações com o cluster.

### Valores de retorno

As informações retornadas da API REST só poderão ser usadas dentro do cluster ou de máquinas virtuais na mesma Rede Virtual do Azure que o cluster. Por exemplo, o nome de domínio totalmente qualificado (FQDN) retornado para servidores Zookeeper não será acessível pela Internet.

## Próximas etapas

Agora que você aprendeu a implantar e monitorar topologias usando o Painel do Storm, saiba como [desenvolver topologias baseadas em Java usando Maven](hdinsight-storm-develop-java-topology.md).

Para obter mais topologias de exemplo, consulte [Topologias de exemplo para Storm no HDInsight](hdinsight-storm-example-topology.md).

<!---HONumber=Oct15_HO1-->