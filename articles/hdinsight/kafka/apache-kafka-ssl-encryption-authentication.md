---
title: Configurar a criptografia SSL e autenticação para o Apache Kafka no Azure HDInsight
description: Configure a criptografia SSL para comunicação entre clientes do Kafka e agentes do Kafka, bem como entre agentes do Kafka. Configure a autenticação SSL de clientes.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: hrasheed
ms.openlocfilehash: 665b439fb1ca0b907ea7385369f64d255e8e42e6
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2019
ms.locfileid: "54354947"
---
# <a name="setup-secure-sockets-layer-ssl-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>Configurar a criptografia SSL e autenticação para o Apache Kafka no Azure HDInsight

Este artigo descreve como configurar a criptografia SSL de clientes do Apache Kafka para agentes do Apache Kafka e também entre agentes do Apache Kafka. Ele também fornece as etapas necessárias para configurar a autenticação de clientes (também conhecido como SSL bidirecional).

## <a name="server-setup"></a>Configuração do servidor

A primeira etapa é configurar o repositório de chaves e truststore em agentes do Kafka e importar o certificado da CA (autoridade de certificação) e os certificados de agente para esses repositórios.

> [!Note] 
> Este guia usará certificados autoassinados, mas a solução mais segura é usar certificados emitidos por autoridades de certificação confiáveis.

1. Crie uma pasta chamada ssl e exporte a senha do servidor como uma variável de ambiente. No restante deste guia, substitua `<server_password>` pela senha de administrador real para o servidor.

    ```bash
    $export SRVPASS=<server_password>
    $mkdir ssl
    $cd ssl
    ```

2. Em seguida, crie um repositório de chaves java (kafka.server.keystore.jks) e um Certificado de Autoridade de Certificação.

    ```bash
    $keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass $SRVPASS -keypass $SRVPASS -dname "CN=wn0-umakaf.xvbseke35rbuddm4fyvhm2vz2h.cx.internal.cloudapp.net" -storetype pkcs12
    ```

3. Em seguida, crie uma solicitação de assinatura para obter o certificado criado na etapa anterior, assinado pela autoridade de certificação.

    ```bash
    $keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass $SRVPASS -keypass $SRVPASS
    ```

4. Agora, envie a solicitação de assinatura para a autoridade de certificação e obtenha esse certificado assinado. Já que estamos usando um certificado autoassinado, podemos assinar o certificado com a nossa autoridade de certificação usando o comando `openssl`.

    ```bash
    $openssl x509 -req -CA ca-cert -CAkey ca-key -in cert-file -out cert-signed -days 365 -CAcreateserial -passin pass:$SRVPASS
    ```

5. Crie um repositório de confiança e importe o certificado da autoridade de certificação.

    ```bash
    $keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass $SRVPASS -keypass $SRVPASS -noprompt
    ```

6. Importe o Certificado de Autoridade de Certificação pública para repositório de chaves.

    ```bash
    $keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass $SRVPASS -keypass $SRVPASS -noprompt
    ```

7. Importe o certificado assinado no repositório de chaves.

    ```bash
    $keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass $SRVPASS -keypass $SRVPASS -noprompt//output is "Certificate reply was added to keystore"
    ```

Importar o certificado assinado no repositório de chaves é a etapa final necessária para configurar o truststore e o repositório de chaves para um agente do Kafka.

## <a name="update-configuration-to-use-ssl-and-restart-brokers"></a>Atualizar a configuração para usar SSL e reiniciar os agentes

Configuramos cada agente do Kafka com um repositório de chaves e truststore e importamos os certificados corretos.  Em seguida, modifique as propriedades de configuração do Kafka relacionadas usando o Ambari e reinicie os agentes do Kafka.

1. Entre no portal do Azure e selecione seu cluster do Apache Kafka do Azure HDInsight.
1. Vá para a interface do usuário do Ambari, clicando em **Página Inicial do Ambari** nos **Painéis do cluster**.
1. Em **Agente do Kafka**, defina a propriedade **ouvintes** para `PLAINTEXT://localhost:9092,SSL://localhost:9093`
1. Sob **Agente avançado do Kafka**, defina a propriedade **security.inter.broker.protocol** para `SSL`

    ![Editar as propriedades de configuração de SSL do Kafka no Ambari](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png)

1. Em **Agente do Kafka personalizado**, defina a propriedade **ssl.client.auth** para `required`. Essa etapa só é necessária se você está configurando a autenticação e também a criptografia.

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

1. Exporte a senha do cliente. Substitua `<client_password>` com a senha de administrador real no computador cliente Kafka.

    ```bash
    $export CLIPASS=<client_password>
    $cd ssl
    ```

1. Crie um repositório de chaves Java e obtenha um certificado assinado para o agente. Em seguida, copie o certificado para a VM em que a autoridade de certificação está sendo executada.

    ```bash
    $keytool -genkey -keystore kafka.client.keystore.jks -validity 365 -storepass $CLIPASS -keypass $CLIPASS -dname "CN=mylaptop1" -alias my-local-pc1 -storetype pkcs12

    $keytool -keystore kafka.client.keystore.jks -certreq -file client-cert-sign-request -alias my-local-pc1 -storepass $CLIPASS -keypass $CLIPASS

    $scp client-cert-sign-request3 sshuser@wn0-umakaf:~/tmp1/client-cert-sign-request
    ```

1. Alterne para o computador da autoridade de certificação (wn0) para assinar o certificado de cliente.

    ```bash
    $cd ssl
    $openssl x509 -req -CA ca-cert -CAkey ca-key -in /tmp1/client-cert-sign-request -out /tmp1/client-cert-signed -days 365 -CAcreateserial -passin pass:<server_password>
    ```

1. Vá para o computador cliente (hn1) e navegue até a pasta `~/ssl`. Copie o certificado autoassinado para o computador cliente.

    ```bash
    #copy signed cert to client machine
    $scp -i ~/kafka-security.pem sshuser@wn0-umakaf:/tmp1/client-cert-signed

    #copy signed cert to client machine
    $scp -i ~/kafka-security.pem sshuser@wn0-umakaf:/home/sshuser/ssl/ca-cert

    #Import CA cert to trust store 
    $keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt

    #Import CA cert to key store
    $keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt

    #Import signed client (cert client-cert-signed1) to keystore
    $keytool -keystore kafka.client.keystore.jks -import -file client-cert-signed -alias my-local-pc1 -storepass $CLIPASS -keypass $CLIPASS -noprompt
    ```

1. Exiba o arquivo `client-ssl-auth.properties` com o comando `$cat client-ssl-auth.properties`. Ele deve conter as seguintes linhas:

    ```bash
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=<client_password>
    ssl.keystore.location=/home/sshuser/ssl/kafka.client.keystore.jks
    ssl.keystore.password=<client_password>
    ssl.key.password=<client_password>
    ```

## <a name="client-setup-without-authentication"></a>Configuração do cliente (sem autenticação)

1. Exporte a senha do cliente. Substitua `<client_password>` com a senha de administrador real no computador cliente Kafka.

    ```bash
    $export CLIPASS=<client_password>
    $cd ssl
    ```

1. Vá para o computador cliente (hn1) e navegue até a pasta `~/ssl`. Copie o certificado autoassinado para o computador cliente.

    ```bash
    #copy signed cert to client machine
    $scp -i ~/kafka-security.pem sshuser@wn0-umakaf:/home/sshuser/ssl/ca-cert .

    #NOW IMPORT CA cert to trust store
    $keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt

    #Import CA cert to key store
    $keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass $CLIPASS -keypass $CLIPASS -noprompt
    ```

1. Exiba o arquivo `client-ssl-auth.properties` com o comando `$cat client-ssl-auth.properties`. Ele deve conter as seguintes linhas:

    ```bash
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=<client_password>
    ```

## <a name="next-steps"></a>Próximas etapas

* [O que é Apache Kafka no HDInsight?](/../azure/hdinsight/kafka/apache-kafka-introduction)