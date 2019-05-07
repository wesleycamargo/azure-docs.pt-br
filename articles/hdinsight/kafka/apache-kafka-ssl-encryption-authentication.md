---
title: Configurar a criptografia de SSL e autenticação para o Apache Kafka no HDInsight do Azure
description: Configure a criptografia SSL para comunicação entre os clientes do Kafka e agentes do Kafka, bem como entre os agentes do Kafka. Configure a autenticação SSL de clientes.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: hrasheed
ms.openlocfilehash: e526908f5ba9feea53b1c1abebbbfc1bd9a51c54
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65147959"
---
# <a name="set-up-secure-sockets-layer-ssl-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>Configurar a criptografia Secure Sockets Layer (SSL) e autenticação para o Apache Kafka no HDInsight do Azure

Este artigo mostra como configurar a criptografia SSL entre os clientes do Apache Kafka e agentes do Apache Kafka. Ele também mostra como configurar a autenticação de clientes (também conhecido como SSL bidirecional).

> [!Important]
> Há dois clientes que você pode usar para aplicativos de Kafka: um cliente de Java e um cliente de console. Somente o cliente de Java `ProducerConsumer.java` pode usar o SSL para produzir e consumir. O cliente de produtor do console `console-producer.sh` não funciona com SSL.

## <a name="apache-kafka-broker-setup"></a>Instalação do agente do Apache Kafka

A configuração do SSL de Kafka broker usará quatro VMs do cluster do HDInsight da seguinte maneira:

* nó principal 0 - autoridade de certificação (CA)
* os agentes de nó de trabalho, 0, 1 e 2-

> [!Note] 
> Este guia usará certificados autoassinados, mas a solução mais segura é usar certificados emitidos por autoridades de certificação confiáveis.

O resumo do processo de instalação do agente é da seguinte maneira:

1. As etapas a seguir são repetidas em cada um de nós de trabalho de três:

    1. Gere um certificado.
    1. Crie um solicitação de assinatura de certificado.
    1. Envie o certificado de assinatura de solicitação para a autoridade de certificação (CA).
    1. Entrar para a autoridade de certificação e assinar a solicitação.
    1. SCP certificado assinado de volta para o nó de trabalho.
    1. O certificado público da autoridade de certificação para o nó de trabalho do SCP.

1. Depois de todos os certificados, colocados os certificados no repositório de certificados.
1. Vá para Ambari e alterar as configurações.

Use as seguintes instruções detalhadas para concluir a instalação do agente:

> [!Important]
> Os trechos de código a seguir wnX é uma abreviação para um de nós de trabalho de três e deve ser substituído por `wn0`, `wn1` ou `wn2` conforme apropriado. `WorkerNode0_Name` e `HeadNode0_Name` devem ser substituídos com os nomes dos respectivos computadores, como `wn0-abcxyz` ou `hn0-abcxyz`.

1. Execute a configuração inicial no nó principal 0, que preencherá a função de autoridade de certificação (CA) para o HDInsight.

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl

    # Export
    export SRVPASS=MyServerPassword123
    ```

1. Execute a mesma configuração inicial em cada um dos agentes do (nós de trabalho 0, 1 e 2).

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl

    # Export
    export MyServerPassword123=MyServerPassword123
    ```

1. Em cada um de nós de trabalho, execute as seguintes etapas usando o trecho de código a seguir.
    1. Criar um repositório de chaves e preenchê-lo com um novo certificado privado.
    1. Crie um solicitação de assinatura de certificado.
    1. A solicitação de assinatura de certificado à autoridade de certificação (headnode0) do SCP

    ```bash
    keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass "MyServerPassword123" -keypass "MyServerPassword123" -dname "CN=FQDN_WORKER_NODE" -storetype pkcs12
    keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass "MyServerPassword123" -keypass "MyServerPassword123"
    scp cert-file sshuser@HeadNode0_Name:~/ssl/wnX-cert-sign-request
    ```

1. Mude para a máquina de autoridade de certificação e assinar todos o certificado recebido solicitações de assinatura:

    ```bash
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn0-cert-sign-request -out wn0-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn1-cert-sign-request -out wn1-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn2-cert-sign-request -out wn2-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    ```

1. Envie os certificados autoassinados de volta para os nós de trabalho da autoridade de certificação (headnode0).

    ```bash
    scp wn0-cert-signed sshuser@WorkerNode0_Name:~/ssl/cert-signed
    scp wn1-cert-signed sshuser@WorkerNode1_Name:~/ssl/cert-signed
    scp wn2-cert-signed sshuser@WorkerNode2_Name:~/ssl/cert-signed
    ```

1. Envie o certificado público da autoridade de certificação para cada nó de trabalho.

    ```bash
    scp ca-cert sshuser@WorkerNode0_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode1_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode2_Name:~/ssl/ca-cert
    ```

1. Em cada nó de trabalho, adicione o certificado de autoridades de certificação pública para o repositório de chaves e truststore. Em seguida, adicione o certificado assinado do nó de trabalho para o repositório de chaves

    ```bash
    keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -import -file cert-signed -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt

    ```

## <a name="update-kafka-configuration-to-use-ssl-and-restart-brokers"></a>Atualizar a configuração do Kafka para usar SSL e reiniciar os agentes

Você agora tem configurar cada agente Kafka com um repositório de chaves e truststore e importar os certificados corretos. Em seguida, modifique as propriedades de configuração do Kafka relacionadas usando o Ambari e reinicie os agentes do Kafka.

Execute as seguintes etapas para concluir a modificação à configuração:

1. Entre no portal do Azure e selecione seu cluster do Apache Kafka do Azure HDInsight.
1. Vá para a interface do usuário do Ambari, clicando em **Página Inicial do Ambari** nos **Painéis do cluster**.
1. Em **Agente do Kafka**, defina a propriedade **ouvintes** para `PLAINTEXT://localhost:9092,SSL://localhost:9093`
1. Sob **Agente avançado do Kafka**, defina a propriedade **security.inter.broker.protocol** para `SSL`

    ![Editar as propriedades de configuração de SSL do Kafka no Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png)

1. Em **Agente do Kafka personalizado**, defina a propriedade **ssl.client.auth** para `required`. Essa etapa só é necessária se você estiver configurando a autenticação e criptografia.

    ![Editar as propriedades de configuração de SSL do Kafka no Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari2.png)

1. Adicione propriedades de configuração ao arquivo `server.properties` do Kafka para anunciar endereços IP em vez do FQDN (nome de domínio totalmente qualificado).

    ```bash
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092,SSL://$IP_ADDRESS:9093" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.password=<server_password>" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.key.password=<server_password>" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.password=<server_password>" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

1. Para verificar se as alterações anteriores foram feitas corretamente, você poderá marcar opcionalmente que as linhas a seguir estão presentes no arquivo `server.properties` do Kafka.

    ```bash
    advertised.listeners=PLAINTEXT://10.0.0.11:9092,SSL://10.0.0.11:9093
    ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks
    ssl.keystore.password=<server_password>
    ssl.key.password=<server_password>
    ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks
    ssl.truststore.password=<server_password>
    ```

1. Reinicie todos os agentes do Kafka.

## <a name="client-setup-with-authentication"></a>Configuração do cliente (com autenticação)

> [!Note]
> As etapas a seguir são necessárias apenas se você está configurando a criptografia SSL **e** também a autenticação. Se você estiver configurando somente a criptografia, vá para [Configuração do cliente sem autenticação](apache-kafka-ssl-encryption-authentication.md#client-setup-without-authentication)

Conclua as seguintes etapas para concluir a instalação do cliente:

1. Entrar no computador cliente (hn1).
1. Exporte a senha do cliente. Substitua `<client_password>` com a senha de administrador real no computador cliente Kafka.
1. Crie um repositório de chaves Java e obtenha um certificado assinado para o agente. Em seguida, copie o certificado para a VM em que a autoridade de certificação está sendo executada.
1. Alterne para a máquina de autoridade de certificação (hn0) para assinar o certificado de cliente.
1. Vá para o computador cliente (hn1) e navegue até a pasta `~/ssl`. Copie o certificado autoassinado para o computador cliente.

```bash
export CLIPASS=<client_password>
cd ssl

# Create a java keystore and get a signed certificate for the broker. Then copy the certificate to the VM where the CA is running.

keytool -genkey -keystore kafka.client.keystore.jks -validity 365 -storepass $CLIPASS -keypass $CLIPASS -dname "CN=mylaptop1" -alias my-local-pc1 -storetype pkcs12

keytool -keystore kafka.client.keystore.jks -certreq -file client-cert-sign-request -alias my-local-pc1 -storepass $CLIPASS -keypass $CLIPASS

# Copy the cert to the CA
scp client-cert-sign-request3 sshuser@HeadNode0_Name:~/tmp1/client-cert-sign-request

# Switch to the CA machine (hn0) to sign the client certificate.
cd ssl
openssl x509 -req -CA ca-cert -CAkey ca-key -in /tmp1/client-cert-sign-request -out /tmp1/client-cert-signed -days 365 -CAcreateserial -passin pass:<server_password>

# Return to the client machine (hn1), navigate to ~/ssl folder and copy signed cert from the CA (hn0) to client machine
scp -i ~/kafka-security.pem sshuser@HeadNode0_Name:/tmp1/client-cert-signed

# Import CA cert to trust store
keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt

# Import CA cert to key store
keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt

# Import signed client (cert client-cert-signed1) to keystore
keytool -keystore kafka.client.keystore.jks -import -file client-cert-signed -alias my-local-pc1 -storepass $CLIPASS -keypass $CLIPASS -noprompt
```

Por fim, exiba o arquivo `client-ssl-auth.properties` com o comando `cat client-ssl-auth.properties`. Ele deve conter as seguintes linhas:

```bash
security.protocol=SSL
ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
ssl.truststore.password=<client_password>
ssl.keystore.location=/home/sshuser/ssl/kafka.client.keystore.jks
ssl.keystore.password=<client_password>
ssl.key.password=<client_password>
```

## <a name="client-setup-without-authentication"></a>Configuração do cliente (sem autenticação)

Se você não precisar da autenticação, as etapas para configurar a criptografia de SSL apenas são:

1. Entrar no computador cliente (hn1) e navegar até a pasta `~/ssl`
1. Exporte a senha do cliente. Substitua `<client_password>` com a senha de administrador real no computador cliente Kafka.
1. Do computador da AC (wn0), copie o certificado autoassinado para o computador cliente.
1. Importar o certificado da AC para o truststore
1. Importar o certificado da AC para o repositório de chaves

Essas etapas são mostradas no snippet de código a seguir.

```bash
export CLIPASS=<client_password>
cd ssl

# Copy signed cert to client machine
scp -i ~/kafka-security.pem sshuser@wn0-umakaf:/home/sshuser/ssl/ca-cert .

# Import CA cert to truststore
keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt

# Import CA cert to keystore
keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file cert-signed -storepass $CLIPASS -keypass $CLIPASS -noprompt
```

Por fim, exiba o arquivo `client-ssl-auth.properties` com o comando `cat client-ssl-auth.properties`. Ele deve conter as seguintes linhas:

```bash
security.protocol=SSL
ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
ssl.truststore.password=<client_password>
```

## <a name="next-steps"></a>Próximas etapas

* [O que é Apache Kafka no HDInsight?](apache-kafka-introduction.md)
