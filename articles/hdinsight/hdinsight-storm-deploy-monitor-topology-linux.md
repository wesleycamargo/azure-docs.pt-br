<properties
   pageTitle="Implantar e gerenciar topologias Apache Storm no HDInsight baseado em Linux | Microsoft Azure"
   description="Aprenda a implantar, monitorar e gerenciar topologias do Apache Storm usando o Painel do Storm no HDInsight baseado em Linux. Use as ferramentas do Hadoop para Visual Studio."
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
   ms.date="05/20/2016"
   ms.author="larryfr"/>

# Implantar e gerenciar topologias Apache Storm no HDInsight baseado nem Linux

Neste documento, conheça as noções básicas de gerenciamento e topologias Storm de monitoramento funcionando no Storm baseado em Linux e clusters HDInsight.

> [AZURE.IMPORTANT] As etapas deste artigo exigem um Storm baseado em Linux no cluster HDInsight. Para obter informações sobre as topologias de implantação e monitoramento em HDInsight baseado em Windows, consulte [Implantar e gerenciar topologias do Apache Storm no HDInsight baseado em Windows](hdinsight-storm-deploy-monitor-topology.md)

## Pré-requisitos

- **Storm baseado em Linux no cluster HDInsight**: consulte [Introdução com o Apache Storm no HDInsight](hdinsight-apache-storm-tutorial-get-started-linux.md) para etapas para criar um cluster

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

        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-0.9.3.2.2.4.9-1.jar storm.starter.WordCountTopology WordCount

    Isso iniciará a topologia de WordCount de exemplo no cluster. Ele gerar sentenças e conta a ocorrência de cada palavra nas sentenças aleatoriamente.

    > [AZURE.NOTE] Ao enviar a topologia para o cluster, primeiro você deverá copiar o arquivo jar com o cluster antes de usar o comando `storm`. Isso pode ser feito usando o comando `scp` do cliente em que o arquivo existe. Por exemplo, `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
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

> [AZURE.WARNING] Rebalancear uma topologia primeiro desativa a topologia, em seguida, redistribui os trabalhos uniformemente no cluster e finalmente retorna a topologia para o estado que estava antes do rebalanceamento. Portanto, se a topologia estava ativa, ela se tornará ativa novamente. Se estiver desativada, permanecerá desativada.

    storm rebalance TOPOLOGYNAME

##Monitorar e gerenciar usando a IU do Storm

A IU do Storm fornece uma interface Web para trabalhar com as topologias em funcionamento, e é incluída no seu cluster HDInsight. Para exibir a interface do usuário do Storm, use um navegador da Web para abrir \_\___https://CLUSTERNAME.azurehdinsight.net/stormui__, em que __CLUSTERNAME__ é o nome do seu cluster.

> [AZURE.NOTE] Se solicitado a forneça um nome de usuário e senha, insira o administrador de cluster (admin) e a senha que você usou ao criar o cluster.


### Página principal

A página principal da interface do usuário do Storm fornece as seguintes informações:
- **Resumo do cluster **: informações básicas sobre o cluster do Storm
- **Resumo da topologia**: uma lista das topologias em execução. Use os links desta seção para exibir mais informações sobre topologias específicas.
- **Resumo do Supervisor**: informações sobre o supervisor do Storm.
- **Configuração do Nimbus**: configuração do Nimbus para o cluster.

### Resumo da topologia

Selecionar um link na seção **Resumo da topologia** exibirá as seguintes informações sobre a topologia:
- **Resumo da topologia**: informações básicas sobre a topologia.
- **Ações da topologia**: ações de gerenciamento que podem ser executadas para a topologia.
  - **Ativar**: retoma o processamento de uma topologia de desativada.
  - **Desativar**: pausa uma topologia em execução.
  - **Reequilibrar**: ajusta o paralelismo da topologia. Você deve reequilibrar topologias em execução depois de alterar o número de nós no cluster. Isso permite que a topologia ajuste o paralelismo para compensar o aumento/diminuição do número de nós no cluster.

      Para obter mais informações, consulte <a href="http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">Noções básicas sobre o paralelismo de uma topologia Storm</a>

  - **Eliminar**: encerra uma topologia Storm após o tempo limite especificado.

- **Estatísticas da topologia**: estatísticas sobre a topologia. Use os links na coluna **Janela** para definir o período de tempo para as entradas restantes na página.
- **Spouts**: os spouts usados pela topologia. Use os links desta seção para exibir mais informações sobre spouts específicos.
- **Bolts**: os bolts usados pela topologia. Use os links desta seção para exibir mais informações sobre bolts específicos.
- **Configuração da topologia**: a configuração da topologia selecionada.

### Resumo de Spout e Bolt

Selecionar um spout nas seções **Spouts** ou **Bolts** exibirá as seguintes informações sobre o item selecionado:
- **Resumo do componente**: informações básicas sobre o spout ou o bolt.
- **Estatísticas de Spout/Bolt**: estatísticas sobre o spout ou o bolt. Use os links na coluna **Janela** para definir o período de tempo para as entradas restantes na página.
- **Estatísticas de entrada** (somente bolt): informações sobre os streams de entrada consumidos pelo bolt.
- **Estatísticas de saída**: informações sobre os streams emitidos por esse spout ou bolt.
- **Executores**: informações sobre as instâncias do spout ou bolt. Selecione a entrada **Porta** gerada por um executor específico para exibir um log de informações de diagnóstico produzido para esta instância.
- **Erros**: qualquer informação de erro para este spout ou bolt.

## API REST

A interface do usuário do Storm é criada sobre a API REST e, portanto, você pode realizar gerenciamento semelhante e monitorar a funcionalidade usando a API REST. Você pode usar a API REST para criar ferramentas personalizadas para o gerenciamento e o monitoramento de topologias Storm.

Para obter mais informações, veja [API REST da interface do usuário do Storm](http://storm.apache.org/releases/0.9.6/STORM-UI-REST-API.html). As informações a seguir são específicas para o uso da API REST com Apache Storm no HDInsight.

> [AZURE.IMPORTANT] A API do REST Storm não está disponível publicamente pela Internet, e deve ser acessada usando um túnel SSH para o nó principal do cluster HDInsight. Para obter informações sobre como criar e usar um túnel SSH, consulte [Usar um túnel SSH para acessar a interface do usuário da Web Ambari, ResourceManager, JobHistory, NameNode, Oozie e outras interfaces do usuário da Web](hdinsight-linux-ambari-ssh-tunnel.md).

### URI de base

O URI de base para a API REST em clusters baseados no Linux HDInsight está disponível no nó do cabeçalho em **https://HEADNODEFQDN:8744/api/v1/**; no entanto, o nome de domínio do nó de cabeçalho é gerado durante a criação do cluster e não é estático.

Você pode encontrar o FQDN (Nome de Domínio Totalmente Qualificado) para o nó de cabeçalho do cluster de várias maneiras diferentes:

* __Sessão de um SSH__: use o comando `headnode -f` de uma sessão SSH para o cluster.
* __Do Ambari Web__: selecione __Serviços__ na parte superior da página, em seguida, selecione __Storm__. Na guia __Resumo__ selecione __Servidor de IU do Storm__. O FQDN do nó que a interface do usuário do Storm e a API REST estão executando estarão na parte superior da página.
* __Da API REST do Ambari__: use o comando `curl -u admin:PASSWORD -G "https://CLUSTERNAME
.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"` para recuperar informações sobre o nó em que a interface do usuário do Storm e a API REST estão sendo executados. Substitua __PASSWORD__ pela senha do administrador do cluster. Substitua __CLUSTERNAME__ pelo nome do cluster. Na resposta, a entrada "host\_name" contém o FQDN do nó.

### Autenticação

As solicitações para a API REST devem usar a **autenticação básica** e, portanto, você usará o nome do administrador e a senha do cluster HDInsight.

> [AZURE.NOTE] Como a autenticação básica é enviada usando texto não criptografado, você **sempre** deverá usar HTTPS para proteger as comunicações com o cluster.

### Valores de retorno

As informações retornadas da API REST só poderão ser usadas dentro do cluster ou de máquinas virtuais na mesma Rede Virtual do Azure que o cluster. Por exemplo, o nome de domínio totalmente qualificado (FQDN) retornado para servidores Zookeeper não será acessível pela Internet.

## Próximas etapas

Agora que você aprendeu a implantar e monitorar topologias usando o Painel do Storm, saiba como [Desenvolver topologias baseadas em Java usando Maven](hdinsight-storm-develop-java-topology.md).

Para obter mais topologias de exemplo, consulte [Topologias de exemplo para Storm no HDInsight](hdinsight-storm-example-topology.md).

<!---HONumber=AcomDC_0525_2016-->