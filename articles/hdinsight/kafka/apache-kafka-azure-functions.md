---
title: Usar o Azure Functions para enviar dados para o Kafka no HDInsight | Microsoft Docs
description: "Saiba como usar uma Função do Azure para gravação de dados no Kafka no HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/09/2018
ms.author: larryfr
ms.openlocfilehash: c1c03cfcbcb7e0bfdb4a631b9e2ae568f0684069
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2018
---
# <a name="use-kafka-on-hdinsight-from-an-azure-function-app"></a>Usar o Kafka no HDInsight a partir de um aplicativo de Funções do Azure

Saiba como criar um aplicativo de Funções do Azure que envia dados para o Kafka no HDInsight.

O [Azure Functions](https://docs.microsoft.com/azure/azure-functions/) é um serviço de computação sem servidor. Ele permite que você execute o código sob demanda sem precisar provisionar explicitamente ou gerenciar a infraestrutura.

O [Apache Kafka](https://kafka.apache.org) é uma plataforma de streaming distribuída de software livre que pode ser usada para compilar pipelines e aplicativos de dados de streaming em tempo real. O Kafka também fornece funcionalidade de agente de mensagem semelhante a uma fila de mensagens, onde você pode publicar e assinar os fluxos de dados nomeados. O Kafka no HDInsight oferece um serviço gerenciado, altamente escalonável e altamente disponível na nuvem do Microsoft Azure.

O Kafka no HDInsight não fornece uma API na internet pública. Para publicar ou consumir dados do Kafka, é necessário conectar-se ao cluster do Kafka usando uma Rede Virtual do Azure. O Azure Functions fornece uma maneira conveniente de criar um ponto de extremidade público que efetua push de dados para o Kafka no HDInsight através de um gateway de Rede Virtual.

> [!NOTE]
> O foco deste documento está nas etapas necessárias para permitir que o Azure Functions comunique-se com o Kafka no HDInsight. O próprio exemplo é apenas um produtor Kafka básico para demonstrar que a configuração funciona.

## <a name="prerequisites"></a>pré-requisitos

* Um aplicativo de Funções do Azure. Para obter mais informações, consulte a documentação [Criar sua primeira função](../../azure-functions/functions-create-first-azure-function.md).

* Uma Rede Virtual do Azure. Para trabalhar com o Azure Functions, a rede virtual deve usar um dos intervalos de IP a seguir:

    * 10.0.0.0-10.255.255.255
    * 172.16.0.0-172.31.255.255
    * 192.168.0.0-192.168.255.255

    Para obter mais informações, consulte o documento [Integrar seu aplicativo a uma Rede Virtual do Azure](../../app-service/web-sites-integrate-with-vnet.md).

* Um Kafka no Cluster HDInsight. Para obter informações sobre como criar um Kafka no Cluster HDInsight, consulte o documento [Criar um cluster do Kafka](apache-kafka-get-started.md).

    > [!IMPORTANT]
    > Durante a configuração do cluster, será necessário usar as __Configurações avançadas__ para selecionar a Rede Virtual do Azure e a sub-rede que o HDInsight usa. Selecione a rede virtual e a sub-rede que você criou na etapa anterior.

    Para obter mais informações sobre o Kafka e as redes virtuais, consulte o documento [Conectar o Kafka através de uma rede virtual](apache-kafka-connect-vpn-gateway.md).

## <a name="architecture"></a>Arquitetura

O Kafka no HDInsight está contido em uma Rede Virtual do Azure. O Azure Functions pode comunicar-se com a rede virtual usando um gateway de ponto a site. A imagem a seguir é um diagrama dessa topologia de rede:

![Arquitetura do Azure Functions conectando-se ao HDInsight](./media/apache-kafka-azure-functions/kafka-azure-functions.png)

## <a name="configure-kafka"></a>Configurar Kafka

As informações nesta seção preparam o cluster do Kafka para aceitar dados de Funções do Azure. Cobre as ações de configuração a seguir:

* Anúncio IP
* Coletar endereços IP do agente do Kafka
* Criar um tópico do Kafka

### <a name="configure-kafka-for-ip-advertising"></a>Configurar Kafka para anúncio de IP

Por padrão, o Zookeeper retorna o nome de domínio dos agentes do Kafka aos clientes. Essa configuração não funciona sem um servidor DNS, pois o cliente (Azure Functions) não pode resolver nomes para a rede virtual. Para essa configuração, use as seguintes etapas para configurar o Kafka a fim de anunciar endereços IP no lugar de nomes de domínio:

1. Usando um navegador da Web, acesse https://NOMEDOCLUSTER.azurehdinsight.net. Substitua __NOMEDOCLUSTER__ pelo nome do Kafka no cluster HDInsight.

    Quando solicitado, use o nome de usuário e a senha HTTPS para o cluster. A Interface de Usuário Ambari Web para o cluster é exibida.

2. Para exibir informações sobre o Kafka, selecione __Kafka__ na lista à esquerda.

    ![Lista de serviços com Kafka realçado](./media/apache-kafka-azure-functions/select-kafka-service.png)

3. Para exibir a configuração do Kafka, selecione __Configurações__ na parte central superior.

    ![Links de Configurações para Kafka](./media/apache-kafka-azure-functions/select-kafka-config.png)

4. Para localizar a configuração __kafka-env__, digite `kafka-env` no campo __Filtro__ na parte superior direita.

    ![Configuração do Kafka, para kafka-env](./media/apache-kafka-azure-functions/search-for-kafka-env.png)

5. Para configurar o Kafka para anunciar endereços IP, adicione o seguinte texto à parte inferior do campo __kafka-env-template__:

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. Para configurar a interface na qual o Kafka escuta, digite `listeners` no campo __Filtro__ na parte superior direita.

7. Para configurar o Kafka para escutar em todas as interfaces de rede, altere o valor no campo __ouvintes__ para `PLAINTEXT://0.0.0.0:9092`.

8. Para salvar as alterações de configuração, use o botão __Salvar__. Digite uma mensagem de texto que descreva as alterações. Selecione __OK__ assim que as alterações tiverem sido salvas.

    ![Botão Salvar configuração](./media/apache-kafka-azure-functions/save-button.png)

9. Para evitar erros ao reiniciar o Kafka, use o botão __Ações de Serviço__ e selecione __Ativar o Modo de Manutenção__. Selecione OK para concluir essa operação.

    ![Ações de serviço, com ativar manutenção realçada](./media/apache-kafka-azure-functions/turn-on-maintenance-mode.png)

10. Para reiniciar o Kafka, use o botão __Reiniciar__ e selecione __Reiniciar Todos os Afetados__. Confirme a reinicialização e, em seguida, use o botão __OK__ depois que a operação for concluída.

    ![Botão Reiniciar com reiniciar todos os afetados realçada](./media/apache-kafka-azure-functions/restart-button.png)

11. Para desabilitar o modo de manutenção, use o botão __Ações de Serviço__ e selecione __Ativar o Modo de Manutenção__. Selecione **OK** para concluir essa operação.

### <a name="get-the-kafka-broker-ip-address"></a>Coletar endereço IP do agente do Kafka

Use um dos seguintes métodos para recuperar o FQDN (nome de domínio totalmente qualificado) e os endereços IP dos nós no cluster Kafka:

```powershell
$resourceGroupName = "The resource group that contains the virtual network used with HDInsight"

$clusterNICs = Get-AzureRmNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

$nodes = @()
foreach($nic in $clusterNICs) {
    $node = new-object System.Object
    $node | add-member -MemberType NoteProperty -name "Type" -value $nic.Name.Split('-')[1]
    $node | add-member -MemberType NoteProperty -name "InternalIP" -value $nic.IpConfigurations.PrivateIpAddress
    $node | add-member -MemberType NoteProperty -name "InternalFQDN" -value $nic.DnsSettings.InternalFqdn
    $nodes += $node
}
$nodes | sort-object Type
```

```azurecli
az network nic list --resource-group <resourcegroupname> --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
```

Esse comando assume que `<resourcegroupname>` é o nome do grupo de recursos do Azure que contém a rede virtual.

A partir da lista de nomes retornados, selecione o endereço IP de um workernode. O nome de domínio interno totalmente qualificado do nó inicia com as letras `wn`. Esse endereço IP é usado pela função para comunicar-se com o Kafka.

### <a name="create-a-kafka-topic"></a>Criar um tópico Kafka

O Kafka armazena dados em __tópicos__. Antes de enviar dados para o Kafka a partir de uma Função do Azure, será necessário criar a função.

Para criar um tópico, use as etapas no documento [Criar um cluster do Kafka](apache-kafka-get-started.md).

## <a name="create-a-function-that-sends-to-kafka"></a>Crie uma função que envie para o Kafka

> [!NOTE]
> As etapas nesta seção criam uma função JavaScript que usa o pacote [kafka-node](https://www.npmjs.com/package/kafka-node) para publicar dados no Kafka:

1. Crie uma nova função __WebHook + API__ e selecione __JavaScript__ como a linguagem. Para obter mais informações sobre a criação de novas funções, consulte o documentos [Criar sua primeira função](../../azure-functions/functions-create-first-azure-function.md#create-function).

2. Use o código a seguir como o corpo da função:

    ```javascript
    var kafka = require('kafka-node');

    // The topic and a Kafka broker host for your HDInsight cluster
    var topic = 'test';
    var brokerHost = '10.1.0.7:9092';
    // Create the client
    var client= new kafka.KafkaClient({kafkaHost: brokerHost});

    module.exports = function (context, req) {
        // Create the producer
        var producer = new kafka.Producer(client, {requireAcks: 1});

        context.log('JavaScript HTTP trigger function processed a request.');

        if (req.query.name || (req.body && req.body.name)) {
            var name = req.query.name || req.body.name
            context.res = {
                // status: 200, /* Defaults to 200 */
                body: "Hello " + name
            };
            // Create the payload, using the name as the body
            var payloads = [
                    { topic: topic, messages: [name]}
            ];
            context.log("calling producer.send");
            // Send the data to Kafka
            producer.send(payloads, function(err, data) {
                if(err) {
                    context.log(err);
                } else {
                    context.log(data);
                }
            });
        }
        else {
            context.res = {
                status: 400,
                body: "Please pass a name on the query string or in the request body"
            };
        }
        context.done();
    };
    ```

    Substitua `'test'` pelo o nome do tópico do Kafka que você criou no Cluster HDInsight.

    Substitua `10.1.0.7` pelos endereço IP que você recuperou anteriormente. Mantenha o valor `:9092`. 9092 é a porta que Kafka escuta.

    Use o botão __Salvar__ para salvar as alterações.

    ![Editor com o botão Salvar](./media/apache-kafka-azure-functions/function-editor.png)

3. Do lado direito do editor de funções, selecione __Exibir Arquivos__. Selecione __+ Adicionar__ e adicione um novo arquivo nomeado `package.json`. Esse arquivo é usado para especificar a dependência no pacote `kafka-node`.

    ![Adicionar um arquivo](./media/apache-kafka-azure-functions/add-files.png)

4. Para editar o novo arquivo, selecione `package.json` da lista __Exibir Arquivos__. Use o texto a seguir como o conteúdo do arquivo:

    ```json
    {
    "name": "kafkatest",
    "version": "1.0.0",
    "description": "",
    "main": "index.js",
    "author": "",
    "license": "ISC",
    "dependencies": {
            "kafka-node": "^2.4.1"
        }
    }
    ```

    Use o botão __Salvar__ para salvar as alterações.

5. Para instalar o pacote `kafka-node`, use a seção _Versão do nó e gerenciamento de pacotes_ do [Guia do desenvolvedor JavaScript do Azure Functions](../../azure-functions/functions-reference-node.md#node-version-and-package-management).

    > [!NOTE]
    > É possível que você receba vários erros na medida em que o pacote kafka-node é instalado. Você pode ignorar com segurança esses erros.

## <a name="run-the-function"></a>Executar a função

Da direita do editor de funções, selecione __Testar__. Mantenha as configurações padrão para o teste e selecione __Executar__. Na medida em que o teste é executado, ele passa um parâmetro `name` para a função. Esse parâmetro contém um valor de `Azure`, que a função insere no Kafka.

![Captura de tela da caixa de diálogo do teste](./media/apache-kafka-azure-functions/function-test-dialog.png)

Para exibir as informações registradas pela função enquanto o teste é executado, selecione __Logs__ na parte inferior da página. Execute o teste novamente para gerar informações do log.

![Exemplo da saída de log de função](./media/apache-kafka-azure-functions/function-log.png)

## <a name="verify-the-data-is-in-kafka"></a>Verificar se os dados estão no Kafka

Para verificar se os dados chegaram ao tópico do Kafka, use as informações na seção _Produzir e consumir registros_ do documento [Criar um cluster do Kafka ](apache-kafka-get-started.md#produce-and-consume-records). `kafka-console-consumer` lê os dados do tópico e exibe uma lista de mensagens armazenadas no tópico.

## <a name="next-steps"></a>Próximas etapas

Use os links a seguir para aprender a usar o Apache Kafka no HDInsight:

* [Introdução ao Kafka no HDInsight](apache-kafka-get-started.md)

* [Use MirrorMaker para criar uma réplica de Kafka no HDInsight](apache-kafka-mirroring.md)

* [Usar Apache Storm com Kafka no HDInsight](../hdinsight-apache-storm-with-kafka.md)

* [Usar o Apache Spark com o Kafka no HDInsight](../hdinsight-apache-spark-with-kafka.md)

* [Conectar-se ao Kafka por meio de uma Rede Virtual do Azure](apache-kafka-connect-vpn-gateway.md)