---
title: Configurar políticas do Kafka no HDInsight com o Enterprise Security Package - Azure
description: Aprenda a configurar as políticas do Apache Ranger para o Kafka no Azure HDInsight com o Enterprise Security Package.
services: hdinsight
ms.service: hdinsight
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.topic: tutorial
ms.date: 09/24/2018
ms.openlocfilehash: 1a8f04f39568816252175fc9e0893f1ab3e2cdc6
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47224810"
---
# <a name="tutorial-configure-kafka-policies-in-hdinsight-with-enterprise-security-package-preview"></a>Tutorial: Configurar políticas do Kafka no HDInsight com o Enterprise Security Package (Versão Prévia)

Saiba como configurar políticas do Apache Ranger para clusters Kafka do Enterprise Security Package (ESP). Os clusters ESP estão conectados a um domínio, permitindo que os usuários se autentiquem com credenciais de domínio. Neste tutorial, você cria duas políticas do Ranger para restringir o acesso aos tópicos `sales*` e `marketingspend`.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Crie usuários de domínio
> * Criar políticas do Ranger
> * Criar tópicos em um cluster do Kafka
> * Testar as políticas do Ranger

## <a name="before-you-begin"></a>Antes de começar

* Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/).

* Entre no [portal do Azure](https://portal.azure.com/).

* Criar uma [HDInsight Kafka cluster com o pacote de segurança empresarial](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-run-hive).

## <a name="connect-to-apache-ranger-admin-ui"></a>Conectar-se à interface do usuário de Administração do Apache Ranger

1. Em um navegador, conecte-se à interface de usuário do Ranger Admin usando o URL `https://<ClusterName>.azurehdinsight.net/Ranger/`. Lembre-se de alterar `<ClusterName>` para o nome do seu cluster Kafka.

    > [!NOTE] 
    > Credenciais do Ranger não são as mesmas credenciais do cluster do Hadoop. Para impedir que os navegadores usem credenciais do Hadoop em cache, use uma nova janela do navegador InPrivate para se conectar à interface de administração do Ranger Admin.

2. Entre usando suas credenciais de administrador do Azure Active Directory (AD). As credenciais de administrador do Azure AD não são iguais às credenciais de cluster do HDInsight nem às credenciais SSH do nó do Linux HDInsight.

   ![Interface de usuário de administração do Apache Ranger](./media/apache-domain-joined-run-kafka/apache-ranger-admin-login.png)

## <a name="create-domain-users"></a>Crie usuários de domínio

Visite [Crie um cluster do HDInsight com o Enterprise Security Package](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds#create-a-domain-joined-hdinsight-cluster), para aprender como criar os usuários de domínio **sales_user** e **marketing_user**. Em um cenário de produção, os usuários do domínio vêm do seu locatário do Active Directory.

## <a name="create-ranger-policy"></a>Criar política do Ranger 

Crie uma política de Ranger para **sales_user** e **marketing_user**.

1. Abra o **interface do usuário administrador do Ranger**.

2. Clique em  **\<ClusterName > _kafka** sob **Kafka**. Uma política previamente configurada pode estar listada.

3. Clique em **adicionar nova política** e insira os seguintes valores:

   |**Configuração**  |**Valor sugerido**  |
   |---------|---------|
   |Nome da política  |  política de vendas * hdi   |
   |Tópico   |  vendas * |
   |Selecionar usuário  |  sales_user1 |
   |Permissões  | publicar, consumir, criar |

   Os curingas a seguir podem ser incluídos no nome do tópico:

   * '*' Indica zero ou mais ocorrências de caracteres.
   * '?' Indica um caractere único.

   ![Política de criação de UI do administrador do Apache Ranger](./media/apache-domain-joined-run-kafka/apache-ranger-admin-create-policy.png)   

   >[!NOTE] 
   >Aguarde alguns instantes para que o Ranger sincronize com o Azure AD se um usuário do domínio não for preenchido automaticamente para **Selecionar usuário**.

4. Clique em **Adicionar** para salvar a política.

5. Clique em **adicionar nova política** e, em seguida, insira os seguintes valores:

   |**Configuração**  |**Valor sugerido**  |
   |---------|---------|
   |Nome da política  |  política de marketing do hdi   |
   |Tópico   |  marketingspend |
   |Selecionar usuário  |  marketing_user1 |
   |Permissões  | publicar, consumir, criar |

   ![Política de criação de UI do administrador do Apache Ranger](./media/apache-domain-joined-run-kafka/apache-ranger-admin-create-policy-2.png)  

6. Clique em **Adicionar** para salvar a política.

## <a name="create-topics-in-a-kafka-cluster-with-esp"></a>Criar tópicos em um cluster do Kafka com ESP

Para criar dois tópicos, **salesevents** e **marketingspend**:

1. Use o seguinte comando para abrir uma conexão SSH para o cluster:

   ```bash
   ssh SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net
   ```

   Substitua `SSHUSER` pelo usuário do SSH do cluster e substitua `CLUSTERNAME` pelo nome do cluster. Se solicitado, insira a senha para a conta de usuário SSH. Para saber mais sobre como usar o `scp` com HDInsight, confira [Usar SSH com HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix).

   Em um cenário de produção, os usuários do domínio configurados durante a criação do cluster podem executar SSH no cluster.

2. Use os seguintes comandos para salvar o nome do cluster em uma variável e instalar um utilitário de análise JSON `jq`. Quando solicitado, insira o nome do cluster Kafka.

   ```bash
   sudo apt -y install jq
   read -p 'Enter your Kafka cluster name:' CLUSTERNAME
   ```

3. Use os comandos a seguir para obter os hosts do broker Kafka e os hosts do Zookeeper. Quando solicitado, digite a senha da conta de administrador do cluster.

   ```bash
   export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`; \
   
   export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`; \
   ```

4. Execute os seguintes comandos: 

   ```bash
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf kafka-producer-consumer.jar create salesevents $KAFKABROKERS
   
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf kafka-producer-consumer.jar create marketingspend $KAFKABROKERS
   ```

   >[!NOTE] 
   >Apenas o proprietário do processo do serviço Kafka, como root, pode gravar nos znodes do Zookeeper `/config/topics`. As políticas de Ranger não são aplicadas quando um usuário não privilegiado cria um tópico. Isso ocorre porque o script `kafka-topics.sh` se comunica diretamente com o Zookeeper para criar o tópico. As entradas são incluídas nos nós do Zookeeper, enquanto os observadores no lado do broker monitoram e criam os tópicos de acordo. A autorização não pode ser feita através do plugin ranger, e o comando acima é executado usando `sudo` através do corretor Kafka.


## <a name="test-the-ranger-policies"></a>Testar as políticas do Ranger

Com base nas políticas Ranger configuradas, **sales_user** pode produzir / consumir **salesevents** mas não **marketingspend**. Por outro lado, **marketing_user** pode produzir / consumir **marketingspend de tópico** , mas não tópico **salesevents**.

1. Abra uma nova conexão de SSH ao cluster. Use o seguinte comando para entrar como **sales_user1**:

   ```bash
   ssh sales_user1@CLUSTERNAME-ssh.azurehdinsight.net
   ```

2. Execute o seguinte comando:

   ```bash
   export KAFKA_OPTS="-Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf"
   ```

3. Use os nomes de Zookeeper e broker da seção anterior para definir as seguintes variáveis de ambiente:

   ```bash
   export KAFKABROKERS=<brokerlist>:9092 
   ```

   Exemplo: `export KAFKABROKERS=wn0-khdicl.contoso.com:9092,wn1-khdicl.contoso.com:9092`

   ```bash
   export KAFKAZKHOSTS=<zklist>:2181
   ```

   Exemplo: `export KAFKAZKHOSTS=zk1-khdicl.contoso.com:2181,zk2-khdicl.contoso.com:2181`

4. Verifique **sales_user1** pode produzir para tópico **salesevents**.
   
   Execute o seguinte comando para iniciar o console-produtor para o evento **salesevents**:

   ```bash
   /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic salesevents --security-protocol SASL_PLAINTEXT
   ```

   Em seguida, insira algumas mensagens no console. Pressione **Ctrl + C** para sair do console-produtor.

5. Execute o seguinte comando para consumir a partir do tópico **salesevents**:

   ```bash
   /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $KAFKAZKHOSTS --topic salesevents --security-protocol PLAINTEXTSASL --from-beginning
   ```
 
6. Verifique se que as mensagens que você inseriu na etapa anterior serão exibidas, e **sales_user1** não é possível produzir ao tópico **marketingspend**.

   Na mesma janela ssh acima, execute o seguinte comando para produzir para o tópico **marketingspend**:

   ```bash
   /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic marketingspend --security-protocol SASL_PLAINTEXT
   ```

   Ocorre um erro de autorização e ele pode ser ignorado. 

7. Observe que **marketing_user1** não pode consumir de tópico **salesevents**.

   Repita as etapas de 1 a 3 acima, mas desta vez como **marketing_user1**.

   Execute o seguinte comando para consumir a partir do tópico **salesevents**:

   ```bash
   /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $KAFKAZKHOSTS --topic marketingspend --security-protocol PLAINTEXTSASL --from-beginning
   ```

   As mensagens anteriores não podem ser vistas.

8. Exiba os eventos de auditoria de acesso da interface do usuário do Ranger.

   ![Auditoria de políticas do Ranger da interface do usuário](./media/apache-domain-joined-run-kafka/apache-ranger-admin-audit.png)

## <a name="next-steps"></a>Próximas etapas

* [Traga sua própria chave no Kafka](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-byok)
* [Uma introdução à segurança do Hadoop com o pacote de segurança empresarial](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-introduction)
