---
title: Configurar a criptografia SSL e autenticação para o Apache Kafka no Azure HDInsight
description: Configure a criptografia SSL para comunicação entre clientes do Kafka e agentes do Kafka, bem como entre agentes do Kafka. Configure a autenticação SSL de clientes.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: hrasheed
ms.openlocfilehash: 9d8d5e57d0dd7d7022e65a061360c8450848fb4b
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64682910"
---
# <a name="setup-secure-sockets-layer-ssl-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>Configurar a criptografia SSL e autenticação para o Apache Kafka no Azure HDInsight

Este artigo descreve como configurar a criptografia SSL entre clientes e agentes do Apache Kafka. Ele também fornece as etapas necessárias para configurar a autenticação de clientes (também conhecido como SSL bidirecional).

## <a name="server-setup"></a>Configuração do servidor

A primeira etapa é criar um repositório de chaves e truststore em cada agente Kafka. Depois que eles são criados, importe os certificados de AC (autoridade de certificação) e do agente para esses repositórios.

> [!Note] 
> Este guia usará certificados autoassinados, mas a solução mais segura é usar certificados emitidos por autoridades de certificação confiáveis.

Faça o seguinte para concluir a configuração do servidor:

1. Crie uma pasta chamada ssl e exporte a senha do servidor como uma variável de ambiente. No restante deste guia, substitua `<server_password>` pela senha de administrador real para o servidor.
1. Em seguida, crie um repositório de chaves java (kafka.server.keystore.jks) e um Certificado de Autoridade de Certificação.
1. Em seguida, crie uma solicitação de assinatura para obter o certificado criado na etapa anterior, assinado pela autoridade de certificação.
1. Agora, envie a solicitação de assinatura para a autoridade de certificação e obtenha esse certificado assinado. Já que estamos usando um certificado autoassinado, podemos assinar o certificado com a nossa autoridade de certificação usando o comando `openssl`.
1. Crie um repositório de confiança e importe o certificado da autoridade de certificação.
1. Importe o Certificado de Autoridade de Certificação pública para repositório de chaves.
1. Importe o certificado assinado no repositório de chaves.

Os comandos para concluir essas etapas são mostrados no snippet de código a seguir.

```bash
export SRVPASS=<server_password>
mkdir ssl
cd ssl

# Create a java keystore (kafka.server.keystore.jks) and a CA certificate.

keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass $SRVPASS -keypass $SRVPASS -dname "CN=wn0-umakaf.xvbseke35rbuddm4fyvhm2vz2h.cx.internal.cloudapp.net" -storetype pkcs12

# Create a signing request to get the certificate created in the previous step signed by the CA.

keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass $SRVPASS -keypass $SRVPASS

# Send the signing request to the CA and get this certificate signed.

openssl x509 -req -CA ca-cert -CAkey ca-key -in cert-file -out cert-signed -days 365 -CAcreateserial -passin pass:$SRVPASS

# Create a trust store and import the certificate of the CA.

keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass $SRVPASS -keypass $SRVPASS -noprompt

# Import the public CA certificate into the keystore.

keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass $SRVPASS -keypass $SRVPASS -noprompt

# Import the signed certificate into the keystore.

keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass $SRVPASS -keypass $SRVPASS -noprompt

# The output should say "Certificate reply was added to keystore"
```

Importar o certificado assinado no repositório de chaves é a etapa final necessária para configurar o truststore e o repositório de chaves para um agente do Kafka.

## <a name="update-kafka-configuration-to-use-ssl-and-restart-brokers"></a>Atualizar a configuração do Kafka para usar SSL e reiniciar os agentes

Agora você configurou cada agente do Kafka com um repositório de chaves e truststore e importou os certificados corretos.  Em seguida, modifique as propriedades de configuração do Kafka relacionadas usando o Ambari e reinicie os agentes do Kafka. 

Execute as seguintes etapas para concluir a modificação à configuração:

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

Realize as seguintes etapas para concluir a configuração do cliente:

1. Faça logon no computador cliente (hn1).
1. Exporte a senha do cliente. Substitua `<client_password>` com a senha de administrador real no computador cliente Kafka.
1. Crie um repositório de chaves Java e obtenha um certificado assinado para o agente. Em seguida, copie o certificado para a VM em que a autoridade de certificação está sendo executada.
1. Alterne para o computador da autoridade de certificação (wn0) para assinar o certificado de cliente.
1. Vá para o computador cliente (hn1) e navegue até a pasta `~/ssl`. Copie o certificado autoassinado para o computador cliente.

```bash
export CLIPASS=<client_password>
cd ssl

# Create a java keystore and get a signed certificate for the broker. Then copy the certificate to the VM where the CA is running.

keytool -genkey -keystore kafka.client.keystore.jks -validity 365 -storepass $CLIPASS -keypass $CLIPASS -dname "CN=mylaptop1" -alias my-local-pc1 -storetype pkcs12

keytool -keystore kafka.client.keystore.jks -certreq -file client-cert-sign-request -alias my-local-pc1 -storepass $CLIPASS -keypass $CLIPASS

# Copy the cert to the vm where the CA is
scp client-cert-sign-request3 sshuser@wn0-umakaf:~/tmp1/client-cert-sign-request

# Switch to the CA machine (wn0) to sign the client certificate.
cd ssl
openssl x509 -req -CA ca-cert -CAkey ca-key -in /tmp1/client-cert-sign-request -out /tmp1/client-cert-signed -days 365 -CAcreateserial -passin pass:<server_password>

# Return to the client machine (hn1), navigate to ~/ssl folder and copy signed cert to client machine
scp -i ~/kafka-security.pem sshuser@wn0-umakaf:/tmp1/client-cert-signed

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

Se você não precisar de autenticação, as etapas para configurar apenas a criptografia de SSL serão:

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
