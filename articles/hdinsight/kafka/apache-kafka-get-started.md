---
title: Introdução ao Apache Kafka - Início Rápido do Azure HDInsight | Microsoft Docs
description: Neste início rápido, você aprenderá a criar um cluster Apache Kafka no Azure HDInsight usando o Portal do Azure. Você também aprenderá sobre tópicos, assinantes e consumidores de Kafka.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 43585abf-bec1-4322-adde-6db21de98d7f
ms.service: hdinsight
ms.custom: mvc,hdinsightactive
ms.devlang: ''
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/23/2018
ms.author: larryfr
ms.openlocfilehash: d0917894250c8cf907d721749be506d2c247111a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34626310"
---
# <a name="quickstart-create-a-kafka-on-hdinsight-cluster"></a>Início Rápido: Criar um Kafka no cluster HDInsight

O Kafka é uma plataforma de streaming distribuída de software livre. Ela é geralmente usada como um agente de mensagens, pois fornece funcionalidade semelhante a uma fila de mensagens para publicação e assinatura. 

Neste início rápido, você aprenderá a criar um cluster [Apache Kafka](https://kafka.apache.org) usando o Portal do Azure. Você também aprenderá a usar os utilitários incluídos para enviar e receber mensagens usando o Kafka.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

> [!IMPORTANT]
> A API do Kafka só pode ser acessada por recursos dentro da mesma rede virtual. Neste início rápido, você acessará o cluster diretamente no SSH. Para conectar a outros serviços, redes ou máquinas virtuais ao Kafka, primeiro crie uma rede virtual e, depois, crie os recursos na rede.
>
> Para saber mais, confira o documento [Conectar ao Kafka usando uma rede virtual](apache-kafka-connect-vpn-gateway.md).

## <a name="prerequisites"></a>pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

* Um cliente SSH. As etapas neste documento usam SSH para se conectar ao cluster.

    O comando `ssh` é fornecido por padrão nos sistemas Linux, Unix e macOS. No Windows 10, use um dos métodos a seguir para instalar o comando `ssh`:

    * Use o [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart). O cloud shell fornece o comando `ssh` e pode ser configurado para usar o Bash ou o PowerShell como o ambiente de shell.

    * [Instale o Subsistema do Windows para Linux](https://docs.microsoft.com/windows/wsl/install-win10). As distribuições do Linux disponíveis por meio da Microsoft Store fornecem o comando `ssh`.

    > [!IMPORTANT]
    > As etapas neste documento pressupõem que você esteja usando um dos clientes SSH mencionados acima. Se você estiver usando um cliente SSH diferente e encontrar problemas, consulte a documentação de seu cliente SSH.
    >
    > Para saber mais, consulte o documento [Usar SSH com HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-a-kafka-cluster"></a>Criar um cluster Kafka

Para criar um Kafka no cluster do HDInsight, use as seguintes etapas:

1. No [Portal do Azure](https://portal.azure.com), selecione **+ Criar um recurso**, **Dados + Análise** e selecione **HDInsight**.
   
    ![Criar um cluster HDInsight](./media/apache-kafka-get-started/create-hdinsight.png)

2. Em **Básico**, insira ou selecione as seguintes informações:

    | Configuração | Valor |
    | --- | --- |
    | Nome do cluster | Um nome exclusivo para o cluster HDInsight. |
    | Assinatura | Selecione sua assinatura. |
    
    Selecione __Tipo de Cluster__ para exibir a **Configuração de Cluster**.

    ![Escolha a assinatura](./media/apache-kafka-get-started/hdinsight-basic-configuration-1.png)

3. Em __Configuração de cluster__, selecione os seguintes valores:

    | Configuração | Valor |
    | --- | --- |
    | Tipo de cluster | Kafka |
    | Versão | Kafka 0.10.0 (HDI 3.6) |

    Use o botão **Selecionar** para salvar as configurações do tipo de cluster e retorne a __Básico__.

    ![Selecione o tipo de cluster](./media/apache-kafka-get-started/kafka-cluster-type.png)

4. Em __Básico__, insira ou selecione as seguintes informações:

    | Configuração | Valor |
    | --- | --- |
    | Nome de usuário de logon do cluster | O nome de logon ao acessar serviços Web ou APIs REST hospedadas no cluster. Mantenha o valor padrão (admin). |
    | Senha de logon do cluster | A senha de logon ao acessar serviços Web ou APIs REST hospedadas no cluster. |
    | Nome de usuário do Secure Shell (SSH) | O logon usado ao acessar o cluster via SSH. Por padrão, a senha é a mesma do logon do cluster. |
    | Grupo de recursos | O grupo de recursos no qual criar o cluster. |
    | Local padrão | A região do Azure na qual criar o cluster. |

    > [!TIP]
    > Cada região do Azure (local) fornece _domínios de falha_. Um domínio de falha é um agrupamento lógico de hardware subjacente em um data center do Azure. Cada domínio de falha tem um comutador de rede e uma fonte de alimentação em comum. As máquinas virtuais e os discos gerenciados que implementam os nós em um cluster HDInsight são distribuídos entre esses domínios de falha. Essa arquitetura limita o possível impacto de falhas físicas de hardware.
    >
    > Para alta disponibilidade dos dados, selecione uma região (local) que contenha __três domínios de falha__. Para obter informações sobre o número de domínios de falha em uma região, consulte o documento [Disponibilidade de máquinas virtuais do Linux](../../virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set).

    ![Escolha a assinatura](./media/apache-kafka-get-started/hdinsight-basic-configuration-2.png)

    Use o botão __Avançar__ para concluir a configuração básica.

5. Em **Armazenamento**, selecione ou crie uma Conta de armazenamento. Para as etapas neste documento, deixe os outros campos com os valores padrão. Use o botão __Avançar__ para salvar a configuração de armazenamento.

    ![Definir as configurações de conta de armazenamento do HDInsight](./media/apache-kafka-get-started/storage-configuration.png)

6. Em __Aplicativos (opcionais)__, selecione __Avançar__ para continuar com as configurações padrão.

7. Em __Tamanho do cluster__, selecione __Avançar__ para continuar com as configurações padrão.

    > [!IMPORTANT]
    > Para garantir a disponibilidade de Kafka no HDInsight, a entrada __número de nós de trabalho__ deve ser definida como 3 ou superior. O valor padrão é 4.
    
    > [!TIP]
    > A entrada dos **discos por nó de trabalho** configura a escalabilidade do Kafka no HDInsight. O Kafka no HDInsight usa o disco local das máquinas virtuais no cluster para armazenar dados. Como o Kafka tem E/S bastante pesadas, os [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md) são usados para fornecer alta taxa de transferência e mais armazenamento por nó. O tipo de disco gerenciado pode ser __Standard__ (HDD) ou __Premium__ (SSD). O tipo de disco depende do tamanho da VM usado pelos nós de trabalho (agentes Kafka). Os discos Premium são usados automaticamente com VMs das séries DS e GS. Todos os outros tipos VM usam o padrão.

    ![Definir o tamanho do cluster Kafka](./media/apache-kafka-get-started/kafka-cluster-size.png)

8. Em __Configurações avançadas__, selecione __Avançar__ para continuar com as configurações padrão.

9. Em **Resumo**, examine a configuração do cluster. Use os links __Editar__ para alterar as configurações que estão incorretas. Por fim, use o botão __Criar__ para criar o cluster.
   
    ![Resumo da configuração do cluster](./media/apache-kafka-get-started/kafka-configuration-summary.png)
   
    > [!NOTE]
    > Pode levar até 20 minutos para criar o cluster.

## <a name="connect-to-the-cluster"></a>Conectar-se ao cluster

1. Para conectar-se ao nó principal do cluster Kafka, use o comando a seguir. Substitua `sshuser` pelo nome de usuário SSH. Substitua `mykafka` pelo nome do cluster Kafka

    ```bash
    ssh sshuser@mykafka-ssh.azurehdinsight.net
    ```

2. Quando você se conectar pela primeira vez ao cluster, seu cliente SSH poderá exibir um aviso de que a autenticidade do host não pode ser estabelecida. Quando for solicitado, digite __sim__ e pressione __Enter__ para adicionar o host à lista de servidores confiáveis do cliente SSH.

3. Quando solicitado, insira a senha do usuário SSH.

Após a conexão, você verá informações semelhantes ao seguinte texto:

```text
Authorized uses only. All activity may be monitored and reported.
Welcome to Ubuntu 16.04.4 LTS (GNU/Linux 4.13.0-1011-azure x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

83 packages can be updated.
37 updates are security updates.



Welcome to Kafka on HDInsight.

Last login: Thu Mar 29 13:25:27 2018 from 108.252.109.241
ssuhuser@hn0-mykafk:~$
```

## <a id="getkafkainfo"></a>Obter as informações de host do Zookeeper e Broker

Ao trabalhar com Kafka, você deve conhecer os hosts *Zookeeper* e *Broker*. Esses hosts são usados com a API Kafka e muitos dos utilitários fornecidos com o Kafka.

Nesta seção, você obtém as informações do host da API REST Ambari no cluster.

1. Na conexão SSH com cluster, use o comando a seguir para instalar o utilitário `jq`. Esse utilitário é usado para analisar documentos JSON e é útil para recuperar as informações do host:
   
    ```bash
    sudo apt -y install jq
    ```

2. Para definir uma variável de ambiente para o nome do cluster, use o seguinte comando:

    ```bash
    read -p "Enter the Kafka on HDInsight cluster name: " CLUSTERNAME
    ```

    Ao receber a solicitação, insira o nome do cluster Kafka.

3. Para definir uma variável de ambiente com informações de host Zookeeper, use o seguinte comando:
    
    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin -G http://headnodehost:8080/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

    > [!TIP]
    > Este comando consulta diretamente o serviço do Ambari no nó de cabeçalho do cluster. Você também pode acessar o ambari usando o endereço público de `https://$CLUSTERNAME.azurehdinsight.net:80/`. Algumas configurações de rede podem impedir o acesso ao endereço público. Por exemplo, usar NSG (Grupos de Segurança de Rede) para restringir o acesso ao HDInsight em uma rede virtual.

    Ao receber a solicitação, insira a senha para a conta de logon do cluster (não da conta de SSH).

    > [!NOTE]
    > Esse comando recupera todos os hosts de Zookeeper, depois retorna apenas as duas primeiras entradas. Isso ocorre porque você deseja certa redundância no caso de um host ficar inacessível.

4. Para verificar se a variável de ambiente é definida corretamente, use o seguinte comando:

    ```bash
     echo '$KAFKAZKHOSTS='$KAFKAZKHOSTS
    ```

    Esse comando retorna informações semelhantes ao seguinte texto:

    `zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181`

5. Para definir uma variável de ambiente com informações de host Broker do Kafka, use o seguinte comando:

    ```bash
    export KAFKABROKERS=`curl -sS -u admin -G http://headnodehost:8080/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    ```

    Ao receber a solicitação, insira a senha para a conta de logon do cluster (não da conta de SSH).

6. Para verificar se a variável de ambiente é definida corretamente, use o seguinte comando:

    ```bash   
    echo '$KAFKABROKERS='$KAFKABROKERS
    ```

    Esse comando retorna informações semelhantes ao seguinte texto:
   
    `wn1-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092,wn0-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092`

## <a name="manage-kafka-topics"></a>Gerenciar tópicos Kafka

O Kafka armazena fluxos de dados em *tópicos*. Você pode usar o utilitário `kafka-topics.sh` para gerenciar tópicos.

* **Para criar um tópico**, use o seguinte comando para criar uma conexão SSH:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
    ```

    Esse comando se conecta ao Zookeeper usando as informações de host armazenadas em `$KAFKAZKHOSTS`. Depois, ele cria um tópico Kafka chamado **teste**. 

    * Os dados armazenados neste tópico são particionados em oito partições.

    * Cada partição é replicada em três nós de trabalho no cluster.

        > [!IMPORTANT]
        > Se você tiver criado o cluster em uma região do Azure que fornece três domínios de falha, use um fator de replicação de 3. Caso contrário, use um fator de replicação de 4.
        
        Em regiões com três domínios de falha, um fator de replicação de 3 permite a distribuição das réplicas entre os domínios de falha. Em regiões com dois domínios de falha, um fator de replicação de quatro permite a distribuição uniforme das réplicas entre os domínios.
        
        Para obter informações sobre o número de domínios de falha em uma região, consulte o documento [Disponibilidade de máquinas virtuais do Linux](../../virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set).

        > [!IMPORTANT] 
        > O Kafka não está ciente dos domínios de falha do Azure. Durante a criação de réplicas da partição para tópicos, ele não poderá distribuir réplicas corretamente para alta disponibilidade.

        Para garantir a alta disponibilidade, use a [ferramenta de rebalanceio de partição Kafka](https://github.com/hdinsight/hdinsight-kafka-tools). Essa ferramenta deve ser executada em uma sessão SSH para o nó principal do seu cluster Kafka.

        Para ter a mais alta disponibilidade de seus dados do Kafka, você deve balancear novamente as réplicas de partição do tópico quando:

        * Você criar um novo tópico ou partição

        * Você escalar um cluster verticalmente

* **Para listar os tópicos**, use o seguinte comando:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
    ```

    Esse comando lista os tópicos disponíveis no cluster Kafka.

* **Para excluir um tópico**, use o seguinte comando:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic topicname --zookeeper $KAFKAZKHOSTS
    ```

    Esse comando exclui o tópico chamado `topicname`.

    > [!WARNING]
    > Se você excluir o tópico `test` criado anteriormente, deverá recriá-lo. Isso é usado em etapas posteriores neste documento.

Para saber mais sobre os comandos disponíveis com o utilitário `kafka-topics.sh`, use o seguinte comando:

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh
```

## <a name="produce-and-consume-records"></a>Produzir e consumir registros

O Kafka armazena *registros* nos tópicos. Os registros são produzidos por *produtores* e consumidos por *consumidores*. Os produtores e consumidores se comunicam com o serviço *Agente de Kafka*. Cada nó de trabalho no cluster HDInsight é um host de agente do Kafka.

Para armazenar registros no tópico teste criado anteriormente e lê-los usando um consumidor, use as seguintes etapas:

1. Para gravar registros no tópico, use o utilitário `kafka-console-producer.sh` da conexão SSH:
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test
    ```
   
    Após esse comando, você chega em uma linha em branco.

2. Digite uma mensagem de texto na linha em branco e pressione enter. Insira algumas mensagens dessa forma e depois use **Ctrl + C** para retornar ao prompt normal. Cada linha é enviada como um registro separado para o tópico Kafka.

3. Para ler registros no tópico, use o utilitário `kafka-console-consumer.sh` da conexão SSH:
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic test --from-beginning
    ```
   
    Esse comando recupera os registros do tópico e os exibe. O uso de `--from-beginning` instrui o consumidor a começar do início do fluxo, para que todos os registros sejam recuperados.

    > [!NOTE]
    > Se estiver usando uma versão mais antiga do Kafka, substitua `--bootstrap-server $KAFKABROKERS` por `--zookeeper $KAFKAZKHOSTS`.

4. Use __Ctrl + C__ para interromper o consumidor.

Você também pode criar programaticamente produtores e consumidores. Para obter um exemplo de como usar essa API, consulte o documento [API do Produtor e Consumidor Kafka com HDInsight](apache-kafka-producer-consumer-api.md).

## <a name="clean-up-resources"></a>Limpar recursos

Para limpar os recursos criados por este início rápido, você pode excluir o grupo de recursos. A exclusão do grupo de recursos também exclui o cluster HDInsight associado e todos os outros recursos associados ao grupo de recursos.

Para remover o grupo de recursos usando o portal do Azure:

1. No portal do Azure, expanda o menu à esquerda para abrir o menu de serviços e escolha __Grupo de Recursos__ para exibir a lista dos seus grupos de recursos.
2. Localize o grupo de recursos a ser excluído e clique com o botão direito do mouse no botão __Mais__ (...) do lado direito da lista.
3. Selecione __Excluir grupo de recursos__ e confirme.

> [!WARNING]
> A cobrança do cluster HDInsight começa quando um cluster é criado e para quando o cluster é excluído. A cobrança ocorre por minuto, portanto, sempre exclua o cluster quando ele não estiver mais sendo usado.
> 
> Excluir um Kafka no cluster HDInsight exclui todos os dados armazenados no Kafka.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Usar o Apache Spark com o Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)

