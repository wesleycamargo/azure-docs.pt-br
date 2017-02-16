---
title: Usar o Apache Kafka com o Storm no HDInsight | Microsoft Docs
description: "O Apache Kafka é instalado com o Apache Storm no HDInsight. Saiba como gravar no Kafka e depois ler dele, usando os componentes KafkaBolt e KafkaSpout fornecidos com o Storm. Saiba também como usar a estrutura Flux para definir e enviar topologias Storm."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: paulettm
editor: cgronlun
ms.assetid: e4941329-1580-4cd8-b82e-a2258802c1a7
ms.service: hdinsight
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/09/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 8c07f0da21eab0c90ad9608dfaeb29dd4a01a6b7
ms.openlocfilehash: c6171ad4077e833162bfad31b01abbc9947f8e45

---
# <a name="use-apache-kafka-preview-with-storm-on-hdinsight"></a>Usar o Apache Kafka (visualização) com o Storm no HDInsight

O Apache Kafka é uma solução de publicação e assinatura de mensagens que está disponível com o HDInsight. O Apache Storm é um sistema distribuído que pode ser usado para analisar dados em tempo real. Este documento demonstra como você pode usar o Storm no HDInsight para ler e processar dados do Kafka no HDInsight. O exemplo neste documento usa uma topologia do Storm do baseada em Java que usa os componentes spout e bolt do Kafka disponíveis com o Apache Storm.

> [!NOTE]
> As etapas neste documento criam um grupo de recursos do Azure que contém um Storm no HDInsight e um Kafka no cluster de HDInsight. Esses clusters são ambos localizados em uma Rede Virtual do Azure, que permite que o cluster Storm se comunique diretamente com o cluster Kafka.
> 
> Quando terminar as etapas deste documento, lembre-se de excluir os clusters para evitar cobranças em excesso.

## <a name="prerequisites"></a>Pré-requisitos

* [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 1.8 ou superior. Ou um equivalente, como [OpenJDK](http://openjdk.java.net/).
  
    > [!NOTE]
    > As etapas deste documento usam um cluster HDInsight 3.5, que usa o Java 8.

* [Maven 3. x](http://maven.apache.org/) - um pacote de gerenciamento de compilação para aplicativos Java.

* Um editor de texto ou Java IDE

* Um cliente SSH (você precisa dos comandos `ssh` e `scp`) - para obter mais informações sobre como usar SSH com o HDInsight, veja os seguintes documentos:
  
  * [Usar SSH com HDInsight baseado em Linux da Linux, Unix ou Mac OS](hdinsight-hadoop-linux-use-ssh-unix.md)

  * [Usar SSH com HDInsight baseado em Linux no Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

## <a name="create-the-clusters"></a>Criar os clusters

Apache Kafka no HDInsight não fornece acesso para Agentes de Kafka pela internet pública. Qualquer item que se comunique com o Kafka deve estar na mesma rede virtual do Azure que os nós no cluster Kafka. Para este exemplo, clusters Storm e Kafka estão localizados em uma rede virtual do Azure. O seguinte diagrama mostra como a comunicação flui entre os clusters:

![Diagrama de clusters Storm e Kafka em uma rede virtual do Azure](./media/hdinsight-apache-storm-with-kafka/storm-kafka-vnet.png)

> [!NOTE]
> Embora o Kafka em si esteja limitado à comunicação na rede virtual, outros serviços no cluster, como SSH e Ambari, podem ser acessados pela Internet. Para obter mais informações sobre as portas públicas disponíveis com o HDInsight, confira [Portas e URIs usados pelo HDInsight](hdinsight-hadoop-port-settings-for-services.md).


Enquanto você pode criar uma rede virtual do Azure, clusters Kafka e Storm manualmente, é mais fácil usar um modelo do Azure Resource Manager. Use as seguintes etapas para implantar uma rede virtual do Azure, clusters Kafka e Storm para sua assinatura do Azure.

1. Use o botão a seguir para entrar no Azure e abra o modelo do Gerenciador de Recursos no portal do Azure.
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-storm-cluster-in-vnet.json" target="_blank"><img src="./media/hdinsight-apache-storm-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
   
    O modelo do Gerenciador de Recursos está localizado em **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-storm-cluster-in-vnet.json**.

2. Use as seguintes diretrizes para preencher as entradas na folha de **implantação personalizada** :
   
    ![Implantação personalizada do HDInsight](./media/hdinsight-apache-storm-with-kafka/parameters.png)

    * **Grupo de recursos**: Crie um grupo ou selecione um existente. Esse grupo contém o cluster HDInsight.
   
    * **Local**: escolha um local geograficamente perto de você. Esse local deve corresponder ao local na seção __CONFIGURAÇÕES__.

    * **Nome do Cluster de base**: esse valor é usado como o nome de base para os clusters Storm e Kafka. Por exemplo, inserir **hdi** cria um cluster Storm chamado **storm-hdi** e um cluster Kafka chamado **kafka-hdi**.
   
    * **Nome de usuário de logon do cluster**: o nome de usuário do administrador para os clusters Storm e Kafka.
   
    * **Senha de logon do cluster**: a senha do administrador para os clusters Storm e Kafka.
    
    * **Nome de usuário SSH**: o usuário SSH para criar para os clusters Storm e Kafka.
    
    * **Senha SSH**: a senha para o usuário SSH dos clusters Kafka e Storm.
    
    * **Local**: a região na qual os clusters são criados.

3. Leia **Termos e Condições**, e depois selecione **Concordo com os termos e condições declarados acima**.

4. Por fim, marque **Fixar no painel** e selecione **Comprar**. Demora cerca de 20 minutos para criar os clusters.

Quando os recursos tiverem sido criados, você será redirecionado para uma folha do grupo de recursos que contém os clusters e o painel da Web.

![Folha do grupo de recursos para rede virtual e clusters](./media/hdinsight-apache-storm-with-kafka/groupblade.png)

> [!IMPORTANT]
> Observe que os nomes dos clusters HDInsight são **storm-BASENAME** e **kafka-BASENAME**, em que BASENAME é o nome fornecido para o modelo. Você usa esses nomes em etapas posteriores ao se conectar aos clusters.

## <a name="get-the-code"></a>Obter o código

O código de exemplo descrito neste documento está disponível em [https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka).

## <a name="understanding-the-code"></a>Compreender o código

Este projeto contém duas topologias:

* **KafkaWriter**: definida pelo arquivo **writer.yaml**, essa topologia grava sentenças aleatórias no Kafka usando o KafkaBolt fornecido com o Apache Storm.
  
    Essa topologia usa um componente **SentenceSpout** personalizado para gerar sentenças aleatórias.

* **KafkaReader**: definida pelo arquivo **reader.yaml**, essa topologia lê dados do Kafka usando o KafkaSpout fornecido com o Apache Storm e registra os dados em stdout.
  
    Essa topologia usa um componente **PrinterBolt** personalizado para registrar em log dados lidos do Kafka.

### <a name="flux"></a>Flux

As topologias são definidas usando o [Flux](https://storm.apache.org/releases/1.0.1/flux.html). O Flux foi introduzido no Storm 0.10.x e permite que você separe a configuração de topologia do código. Para topologias que usam a estrutura Flux, a topologia é definida em um arquivo YAML. O arquivo YAML pode ser incluído como parte da topologia ou pode ser especificado quando você envia a topologia ao servidor Storm. O Flux também dá suporte à substituição de variáveis em tempo de execução, o que é usado neste exemplo.

Ambas as topologias esperam as seguintes variáveis de ambiente:

* **KAFKATOPIC**: o nome do tópico do Kafka em que as topologias de leem/gravam.

* **KAFKABROKERS**: os hosts que os agentes do Kafka são executados. As informações de agente são usadas pelo KafkaBolt ao gravar no Kafka.

* **KAFKAZKHOSTS**: os hosts em que o Zookeeper é executado.

As etapas deste documento demonstram como definir essas variáveis de ambiente.

## <a name="create-a-kafka-topic"></a>Criar um tópico Kafka

1. Conecte-se ao cluster Kafka usando SSH. Substitua**USERNAME** pelo nome de usuário do SSH usado ao criar o cluster. Substitua **BASENAME** pelo nome base usado ao criar o cluster.
   
        ssh USERNAME@kafka-BASENAME-ssh.azurehdinsight.net
   
    Quando solicitado, digite a senha usada durante a criação dos clusters.
   
    Para saber mais sobre como usar SSH com o HDInsight, veja os seguintes documentos:
   
    * [Usar SSH com HDInsight baseado em Linux do Linux, Unix e Mac OS](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Usar SSH com HDInsight baseado em Linux no Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. Na conexão SSH ao cluster Kafka, use os seguintes comandos para obter os nós zookeeper do Ambari:

        # Install JQ to make working with JSON easier
        sudo apt -y install jq
        # Query Ambari for 
        KAFKAZKHOSTS=`curl -u admin:PASSWORD -G "http://headnodehost:8080/api/v1/clusters/kafka-BASENAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")'`
    
    Substitua __SENHA__ pela senha de admin que você usou ao criar o cluster. Substitua __BASENAME__ pelo nome base que você usou ao criar o cluster.

    Esse comando lê os valores para os hosts Zookeeper do Ambari e os armazena na variável KAFKAZKHOSTS. Use o seguinte para ver esses valores:

        echo $KAFKAZKHOSTS
    
    A saída desse comando é semelhante ao seguinte exemplo:

        zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk3-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181

    Salve os valores retornados desse comando, pois esses valores são usados ao iniciar a topologia no cluster Storm.

    > [!NOTE]
    > Os comandos anteriores usam __http://headnodehost:8080/__, que se conecta diretamente ao Ambari. Se precisar recuperar essas informações de fora do cluster, pela Internet, você deverá usar __https://kafka-BASENAME/__ em vez disso.

3. Use o seguinte comando para criar um tópico no Kafka:
   
        /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic stormtest --zookeeper $KAFKAZKHOSTS
   
    Esse comando se conecta ao Zookeeper usando as informações do host armazenadas em `$KAFKAZKHOSTS` e cria um tópico do Kafka chamado **stormtest**. Você pode verificar se o tópico foi criado usando o seguinte comando para listar tópicos:
   
        /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
   
    A saída desse comando lista os tópicos do Kafka, que devem conter o novo tópico **stormtest**.

Mantenha a conexão SSH ao cluster Kafka ativa, pois você poderá usá-la para verificar se a topologia do Storm está gravando mensagens no tópico.

## <a name="download-and-compile-the-project"></a>Baixar e compilar o projeto

1. No ambiente de desenvolvimento, baixe o projeto de [https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka), abra uma linha de comando e altere os diretórios para o local em que você baixou o projeto.
   
    Reserve alguns minutos para examinar o código e entender como o projeto funciona.

2. No diretório **hdinsight-storm-java-kafka**, use o seguinte comando para compilar o projeto e criar um pacote para implantação:
   
        mvn clean package
   
    O processo de pacote cria um arquivo chamado `KafkaTopology-1.0-SNAPSHOT.jar` no `target` diretório.

3. Use os seguintes comandos para copiar o pacote para o cluster Storm no HDInsight. Substitua **NOMEDOUSUÁRIO** pelo nome de usuário SSH do cluster. Substitua **BASENAME** pelo nome base que você usou ao criar o cluster.
   
        scp ./target/KafkaTopology-1.0-SNAPSHOT.jar USERNAME@storm-BASENAME-ssh.azurehdinsight.net:KafkaTopology-1.0-SNAPSHOT.jar
   
    Quando solicitado, digite a senha usada durante a criação dos clusters.

4. Use o seguinte comando para copiar o arquivo `set-env-variables.sh` do diretório `scripts` do projeto para o cluster Storm:

        scp ./scripts/set-env-variables.sh USERNAME@storm-BASENAME-ssh.azurehdinsight.net:set-env-variables.sh
    
    Esse script é usado para definir as variáveis de ambiente que as topologias Storm usam para se comunicar com o cluster Kafka.

## <a name="start-the-writer"></a>Iniciar o gravador

1. Use o seguinte para se conectar ao cluster Storm usando o SSH. Substitua**USERNAME** pelo nome de usuário do SSH usado ao criar o cluster. Substitua **BASENAME** pelo nome base usado ao criar o cluster.
   
        ssh USERNAME@storm-BASENAME-ssh.azurehdinsight.net
   
    Quando solicitado, digite a senha usada durante a criação dos clusters.
   
    Para saber mais sobre como usar SSH com o HDInsight, veja os seguintes documentos:
   
    * [Usar SSH com HDInsight baseado em Linux do Linux, Unix e Mac OS](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Usar SSH com HDInsight baseado em Linux no Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. Na conexão SSH ao cluster Storm, use os seguintes comandos para executar o script `set-env-variables.sh`:

        chmod +x set-env-variables.sh
        . ./set-env-variables.sh KAFKACLUSTERNAME PASSWORD

    Substitua __KAFKACLUSTERNAME__ pelo nome do cluster Kafka. Substitua __SENHA__ pela senha de logon de administrador do cluster Kafka.

    O script se conecta ao cluster Kafka e recupera uma lista dos hosts do Zookeeper e de agente Kafka. As informações são armazenadas em variáveis de ambiente que são usadas pelas topologias Storm.

    A saída do script é semelhante ao seguinte exemplo:

        Checking for jq: install ok installed
        Exporting variables:
        $KAFKATOPIC=stormtest
        $KAFKABROKERS=wn0-storm.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:9092,wn1-storm.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:9092
        $KAFKAZKHOSTS=zk1-storm.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:2181,zk3-storm.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:2181,zk5-storm.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:2181

3. Na conexão SSH ao cluster Storm, use o seguinte comando para iniciar a topologia de gravador:
   
        storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writer.yaml -e
   
    Os parâmetros usados com esse comando são:
   
    * **org.apache.storm.flux.Flux**: use o Flux para configurar e executar essa topologia.
   
    * **--remote**: envie a topologia ao Nimbus. A topologia é distribuída entre os nós de trabalho no cluster.
   
    * **-R /writer.yaml**: use **writer.yaml** para configurar a topologia. `-R` indica que esse recurso está incluído no arquivo jar. Está na raiz do jar, então `/writer.yaml` é o caminho para ele.
   
    * **-e**: use a substituição de variável de ambiente. O Flux escolhe os valores $KAFKABROKERS e $KAFKATOPIC definidos anteriormente e os utiliza no arquivo reader.yaml, em vez das entradas `${ENV-KAFKABROKER}` e `${ENV-KAFKATOPIC}`.

5. Depois que a topologia for iniciada, alterne para a conexão SSH ao cluster Kafka e use o seguinte comando para exibir mensagens gravadas no tópico **stormtest**:
   
         /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $KAFKAZKHOSTS --from-beginning --topic stormtest
   
    Esse comando usa um script fornecido com o Kafka para monitorar o tópico. Após alguns instantes, ele deve começar a retornar sentenças aleatórias que foram gravadas para o tópico. A saída deverá ser semelhante ao seguinte exemplo:
   
        i am at two with nature             
        an apple a day keeps the doctor away
        snow white and the seven dwarfs     
        the cow jumped over the moon        
        an apple a day keeps the doctor away
        an apple a day keeps the doctor away
        the cow jumped over the moon        
        an apple a day keeps the doctor away
        an apple a day keeps the doctor away
        four score and seven years ago      
        snow white and the seven dwarfs     
        snow white and the seven dwarfs     
        i am at two with nature             
        an apple a day keeps the doctor away
   
    Use Ctrl + c para interromper o script.

## <a name="start-the-reader"></a>Iniciar o leitor

1. Na sessão SSH ao cluster Storm, use o seguinte comando para iniciar a topologia de leitor:
   
        storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /reader.yaml -e

2. Quando a topologia for iniciada, abra a interface do usuário do Storm. Essa interface do usuário da Web está localizada em https://storm-BASENAME.azurehdinsight.net/stormui. Substitua __BASENAME__ pelo nome de base usado quando o cluster foi criado. 

    Quando solicitado, use o nome de logon de administrador (o padrão, `admin`) e a senha usados quando o cluster foi criado. Você verá uma página semelhante à seguinte imagem:

    ![Interface do Usuário do Storm](./media/hdinsight-apache-storm-with-kafka/stormui.png)

3. Na interface do usuário do Storm, selecione o link __kafka-reader__ na seção __Resumo da Topologia__ para exibir informações sobre a topologia __kafka-reader__.

    ![Seção de resumo da topologia da interface do usuário da Web do Storm](./media/hdinsight-apache-storm-with-kafka/topology-summary.png)

4. Selecione o link __logger-bolt__ na seção __Bolts (Sempre)__ para exibir informações sobre as instâncias do componente logger-bolt.

    ![Link logger-bolt na seção bolts](./media/hdinsight-apache-storm-with-kafka/bolts.png)

5. Na seção __Executores__, selecione um link na coluna __Porta__ para exibir informações de log sobre essa instância do componente.

    ![Link de executores](./media/hdinsight-apache-storm-with-kafka/executors.png)

    O log contém um log dos dados lidos do tópico Kafka. As informações no log são semelhantes ao seguinte texto:

        2016-11-04 17:47:14.907 c.m.e.LoggerBolt [INFO] Received data: four score and seven years ago
        2016-11-04 17:47:14.907 STDIO [INFO] the cow jumped over the moon
        2016-11-04 17:47:14.908 c.m.e.LoggerBolt [INFO] Received data: the cow jumped over the moon
        2016-11-04 17:47:14.911 STDIO [INFO] snow white and the seven dwarfs
        2016-11-04 17:47:14.911 c.m.e.LoggerBolt [INFO] Received data: snow white and the seven dwarfs
        2016-11-04 17:47:14.932 STDIO [INFO] snow white and the seven dwarfs
        2016-11-04 17:47:14.932 c.m.e.LoggerBolt [INFO] Received data: snow white and the seven dwarfs
        2016-11-04 17:47:14.969 STDIO [INFO] an apple a day keeps the doctor away
        2016-11-04 17:47:14.970 c.m.e.LoggerBolt [INFO] Received data: an apple a day keeps the doctor away

## <a name="stop-the-topologies"></a>Interromper as topologias

Em uma sessão SSH ao cluster Storm, use os seguintes comandos para interromper as topologias Storm:

    storm kill kafka-writer
    storm kill kafka-reader

## <a name="delete-the-cluster"></a>Excluir o cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Como as etapas neste documento criam ambos os clusters no mesmo grupo de recursos do Azure, você pode excluir o grupo de recursos no portal do Azure. Isso remove todos os recursos criados por este documento, a rede Virtual do Azure e a conta de armazenamento usada pelos clusters.

## <a name="next-steps"></a>Próximas etapas

Para obter mais topologias de exemplo que podem ser usadas com o Storm no HDInsight, confira [Topologias Storm de exemplo e componentes](hdinsight-storm-example-topology.md).

Para obter informações sobre como implantar e monitorar topologias no HDInsight baseado em Linux, confira [Implantar e gerenciar topologias Apache Storm no HDInsight baseado em Linux](hdinsight-storm-deploy-monitor-topology-linux.md)




<!--HONumber=Jan17_HO3-->


