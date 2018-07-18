---
title: Usando Kafka MirrorMaker com Hubs de Eventos do Azure para o Ecossistema Kafka | Microsoft Docs
description: Use o Kafka MirrorMaker para espelhar um cluster Kafka em Hubs de Eventos.
services: event-hubs
documentationcenter: .net
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.topic: mirror-maker
ms.custom: mvc
ms.date: 05/07/2018
ms.author: bahariri
ms.openlocfilehash: 0693fc2fff5735fb2b3c0a9b8f1d3d256746f40d
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35298314"
---
# <a name="using-kafka-mirrormaker-with-event-hubs-for-kafka-ecosystems"></a>Usando Kafka MirrorMaker com Hubs de Eventos para ecossistemas Kafka

> [!NOTE]
> Este exemplo está disponível no [GitHub](https://github.com/Azure/azure-event-hubs)

Uma consideração importante para aplicativos de escala de nuvem modernos é a capacidade de atualizar, melhorar e alterar a infraestrutura sem interromper o serviço. Este tutorial mostra como um hub de eventos habilitado para Kafka e como o Kafka MirrorMaker pode integrar um pipeline Kafka existente no Azure "espelhando" o fluxo de entrada do Kafka no serviço Hubs de Eventos. 

Um ponto de extremidade Kafka no Hubs de Eventos do Azure permite que você se conecte a Hubs de Eventos do Azure usando o protocolo Kafka (ou seja, clientes Kafka). Ao fazer alterações mínimas em um aplicativo do Kafka, você pode se conectar aos Hubs de Eventos do Azure e aproveitar os benefícios do ecossistema do Azure. Os Hubs de Eventos habilitados para Kafka atualmente oferecem suporte às versões de Kafka 1.0 e posteriores.

Este exemplo mostra como espelhar um agente Kafka em um hub de eventos habilitado para Kafka usando o Kafka MirrorMaker.

   ![MirrorMaker Kafka com Hubs de Eventos](./media/event-hubs-kafka-mirror-maker-tutorial/evnent-hubs-mirror-maker1.png)

## <a name="prerequisites"></a>pré-requisitos

Para concluir este tutorial, verifique se você tem:

* Uma assinatura do Azure. Se você não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
* [Java Development Kit (JDK) 1.7 +](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
    * No Ubuntu, execute `apt-get install default-jdk` para instalar o JDK.
    * Defina a variável de ambiente JAVA_HOME para apontar para a pasta onde o JDK está instalado.
* [Baixar](http://maven.apache.org/download.cgi) e [instalar](http://maven.apache.org/install.html) um armazenamento binário Maven
    * No Ubuntu, você pode executar `apt-get install maven` para instalar o Maven.
* [Git](https://www.git-scm.com/downloads)
    * No Ubuntu, você pode executar `sudo apt-get install git` para instalar o Git.

## <a name="create-an-event-hubs-namespace"></a>Criar um namespace de Hubs de Eventos

É necessário um namespace do Hubs de Eventos para enviar e receber de qualquer serviço de Hub de Eventos. Consulte [Criando um Hub de Eventos habilitado para Kafka](event-hubs-create.md) para obter instruções sobre como obter um ponto de extremidade Kafka nos Hubs de Eventos. Certifique-se de copiar a cadeia de caracteres de conexão dos Hubs de Eventos para uso posterior.

## <a name="clone-the-example-project"></a>Clonar o projeto de exemplo

Agora que você tem uma cadeia de caracteres de conexão de Hubs de Eventos habilitados para Kafka, clone o repositório de Hubs de Eventos do Azure e navegue até a subpasta `mirror-maker`:

```shell
git clone https://github.com/Azure/azure-event-hubs.git
cd azure-event-hubs/samples/kafka/mirror-maker
```

## <a name="set-up-a-kafka-cluster"></a>Configurar um cluster Kafka

Use o [guia de início rápido do Kafka](https://kafka.apache.org/quickstart) para configurar um cluster com as configurações desejadas (ou use um cluster Kafka existente).

## <a name="kafka-mirrormaker"></a>Kafka MirrorMaker

O Kafka MirrorMaker permite o "espelhamento" de um fluxo. Dada a origem e destino dos clusters Kafka, o MirrorMaker garante que todas as mensagens enviadas para o cluster de origem são recebidas por clusters de origem e de destino. Este exemplo mostra como espelhar um cluster Kafka de origem com um hub de eventos habilitado para Kafka de destino. Esse cenário pode ser usado para enviar dados de um pipeline Kafka existente para Hubs de Eventos sem interromper o fluxo de dados. 

Para obter mais informações sobre o Kafka MirrorMaker, consulte o [guia de Espelhamento do Kafka/MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330).

### <a name="configuration"></a>Configuração

Para configurar o Kafka MirrorMaker, dê a ele um cluster Kafka como consumidor/origem e um hub de eventos habilitado para Kafka como produtor/destino.

#### <a name="consumer-configuration"></a>Configuração do consumidor

Atualize o arquivo de configuração do consumidor `source-kafka.config`, que informa ao MirrorMaker as propriedades do cluster Kafka de origem.

##### <a name="source-kafkaconfig"></a>source-kafka.config

```xml
bootstrap.servers={SOURCE.KAFKA.IP.ADDRESS1}:{SOURCE.KAFKA.PORT1},{SOURCE.KAFKA.IP.ADDRESS2}:{SOURCE.KAFKA.PORT2},etc
group.id=example-mirrormaker-group
exclude.internal.topics=true
client.id=mirror_maker_consumer
```

#### <a name="producer-configuration"></a>Configuração do produtor

Agora, atualize o arquivo de configuração do produtor `mirror-eventhub.config`, que manda o MirrorMaker enviar os dados duplicados (ou "espelhados") para o serviço de Hubs de Eventos. Especificamente, altere `bootstrap.servers` e `sasl.jaas.config` para apontar para o ponto de extremidade Kafka dos Hubs de Eventos. O serviço dos Hubs de Eventos requer uma comunicação segura (SASL), que é obtida com a definição das três últimas propriedades na configuração a seguir: 

##### <a name="mirror-eventhubconfig"></a>mirror-eventhub.config

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=mirror_maker_producer

#Required for Event Hubs
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### <a name="run-mirrormaker"></a>Executar o MirrorMaker

Execute o script do Kafka MirrorMaker a partir do diretório raiz do Kafka usando os arquivos de configuração atualizados recentemente. Certifique-se de copiar os arquivos de configuração para o diretório raiz do Kafka, ou de atualizar seus caminhos no comando a seguir.

```shell
bin/kafka-mirror-maker.sh --consumer.config source-kafka.config --num.streams 1 --producer.config mirror-eventhub.config --whitelist=".*"
```

Para verificar que eventos estão atingindo o hub de eventos habilitado para Kafka, confira as estatísticas de entrada no [portal do Azure](https://azure.microsoft.com/features/azure-portal/), ou execute um consumidor no hub de eventos.

Com o MirrorMaker em execução, todos os eventos enviados para a fonte de cluster Kafka são recebidos pelo cluster Kafka e o serviço do hub de eventos habilitado para Kafka espelhado. Usando o MirrorMaker e um ponto de extremidade Kafka nos Hubs de Eventos, você pode migrar um pipeline Kafka existente para o serviço de Hubs de Eventos do Azure gerenciado sem alterar o cluster existente ou interromper qualquer fluxo de dados em andamento.

## <a name="next-steps"></a>Próximas etapas

* [Saiba sobre os Hubs de Evento](event-hubs-what-is-event-hubs.md)
* [Saiba mais sobre os Hubs de Eventos para o ecossistema Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* Saiba mais sobre o [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) para transmitir eventos do Kafka local para hubs de eventos habilitados para Kafka na nuvem.
* Saiba como transmitir em Hubs de Eventos habilitados para Kafka usando [aplicativos nativos do Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md), [Akka Streams](event-hubs-kafka-flink-tutorial.md) ou [Apache Flink](event-hubs-kafka-akka-streams-tutorial.md).
