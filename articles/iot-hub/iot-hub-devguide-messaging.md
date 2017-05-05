---
title: Entender as mensagens do Hub IoT do Azure| Microsoft Docs
description: "Guia do desenvolvedor – Mensagens do dispositivo para a nuvem e da nuvem para o dispositivo com o Hub IoT. Inclui informações sobre formatos de mensagem e protocolos de comunicação com suporte."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 3fc5f1a3-3711-4611-9897-d4db079b4250
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/04/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: ac3f4d2220c1902f00049ce237468ddee992209d
ms.lasthandoff: 04/03/2017


---
# <a name="send-and-receive-messages-with-iot-hub"></a>Enviar e receber mensagens com o Hub IoT
## <a name="overview"></a>Visão geral
O Hub IoT fornece os seguintes primitivos de sistema de mensagens para se comunicar com um dispositivo:

* [Do dispositivo para a nuvem][lnk-d2c] de um dispositivo para um aplicativo de back-end.
* [Da nuvem para o dispositivo][lnk-c2d] de um aplicativo de back-end (*serviço* ou *nuvem*).

As propriedades básicas da funcionalidade de mensagens do Hub IoT são a confiabilidade e a durabilidade das mensagens. Essas propriedades habilitam a adaptação à conectividade intermitente no lado do dispositivo e a picos de carga no processamento de eventos no lado da nuvem. O Hub IoT implementa *pelo menos uma vez* as garantias de entrega de mensagens do dispositivo para a nuvem e da nuvem para o dispositivo.

O Hub IoT dá suporte a vários [protocolos voltados para o dispositivo][lnk-protocols] (como MQTT, AMQP e HTTP). Para dar suporte à interoperabilidade contínua entre protocolos, o Hub IoT define um [formato de mensagem comum][lnk-message-format] com suporte de todos os protocolos voltados para o dispositivo.

O Hub IoT expõe um [ponto de extremidade compatível com o Hub de Eventos][lnk-compatible-endpoint] interno para habilitar os aplicativos de back-end para ler as mensagens de dispositivo para a nuvem recebidas pelo hub. Você também pode criar pontos de extremidade personalizados em seu Hub IoT vinculando outros serviços em sua assinatura ao hub.

### <a name="when-to-use"></a>Quando usar
Use mensagens de dispositivo para nuvem a fim de enviar alertas e telemetria de série temporal de seu aplicativo de dispositivo e de nuvem para dispositivo no caso de notificações unidirecionais para o aplicativo do dispositivo.

Veja as[diretrizes de comunicação do dispositivo para a nuvem][lnk-d2c-guidance] se está em dúvida entre o uso de propriedades reportadas, mensagens do dispositivo para a nuvem ou carregamento do arquivo.
Veja as [diretrizes de comunicação da nuvem para o dispositivo][lnk-c2d-guidance] se está em dúvida entre o uso de propriedades desejadas, métodos diretos ou mensagens da nuvem para o dispositivo.

Para encontrar uma comparação dos serviços Hub IoT e Hubs de Eventos, consulte [Comparação do Hub IoT com os Hubs de Eventos][lnk-compare].

## <a name="device-to-cloud-messages"></a>Mensagens do dispositivo para a nuvem
Você envia mensagens do dispositivo para a nuvem por meio de um ponto de extremidade voltado para o dispositivo (**/devices/{deviceId}/messages/events**). As regras de roteamento roteiam as mensagens para um dos pontos de extremidade voltados para o serviço em seu Hub IoT. As regras de roteamento usam as propriedades das mensagens de dispositivo para a nuvem que fluem através de seu hub para determinar para onde roteá-las. Por padrão, as mensagens são roteadas para o ponto de extremidade voltado para o serviço interno (mensagens/eventos) compatíveis com [Hubs de Eventos][lnk-event-hubs]. Portanto, você pode usar a integração de [Hubs de Eventos Standard e SDKs][lnk-compatible-endpoint] para receber mensagens do dispositivo para a nuvem.

O Hub IoT implementa mensagens de dispositivo para nuvem usando um padrão de sistema de mensagens de streaming. As mensagens do dispositivo para nuvem do Hub IoT são mais semelhantes a *eventos* de [Hubs de Eventos][lnk-event-hubs] do que a *mensagens* do [Barramento de Serviço][lnk-servicebus] na medida em que há um alto volume de eventos passando pelo serviço que pode ser lido por vários leitores.

Esta implementação tem as seguintes implicações:

* Assim como eventos dos Hubs de Eventos, as mensagens do dispositivo para a nuvem são duráveis e mantidas em um ponto de extremidade de **mensagens/eventos** padrão do Hub IoT por até sete dias.
* Assim como os eventos dos Hubs de Eventos, as mensagens do dispositivo para a nuvem podem ter no máximo 256 KB e podem ser agrupadas em lotes para otimizar os envios. Os lotes podem ter no máximo 256 KB.

No entanto, há algumas distinções importantes entre as mensagens do dispositivo para a nuvem do Hub IoT e os Hubs de Eventos:

* Como explicado na seção [Controlar o acesso ao Hub IoT][lnk-devguide-security], o Hub IoT permite a autenticação e o controle de acesso por dispositivo.
* O Hub IoT permite que você crie até 10 pontos de extremidade personalizados. As mensagens são entregues aos pontos de extremidade com base nas rotas configuradas em seu Hub IoT.
* O Hub IoT permite que milhões de dispositivos se conectem ao mesmo tempo (consulte [Cotas e limitação][lnk-quotas]), enquanto os Hubs de Eventos estão limitados a 5 mil conexões AMQP por namespace.
* O Hub IoT não permite o particionamento arbitrário usando uma **PartitionKey**. As mensagens do dispositivo para a nuvem são particionadas com base em sua **deviceId**de origem.
* O dimensionamento do Hub IoT é um pouco diferente do dimensionamento de Hubs de Eventos. Para saber mais, consulte [Dimensionando o Hub IoT][lnk-guidance-scale].

Para obter detalhes sobre como usar as mensagens do dispositivo para a nuvem, consulte [SDKs do IoT do Azure][lnk-sdks].

Para obter detalhes sobre como configurar o roteamento de mensagens, consulte [Regras de roteamento](#routing-rules).

> [!NOTE]
> Ao usar o HTTP para enviar mensagens do dispositivo para a nuvem, os nomes e valores de propriedade poderão conter apenas caracteres alfanuméricos ASCII e ``{'!', '#', '$', '%, '&', "'", '*', '*', '+', '-', '.', '^', '_', '`', '|', '~'}``.
> 
> 

### <a name="non-telemetry-traffic"></a>Tráfego sem telemetria
Muitas vezes, além dos pontos de dados de telemetria, os dispositivos enviam mensagens e solicitações que exigem a execução e a manipulação separadas da camada de lógica de negócios do aplicativo. Por exemplo, alertas críticos que devem disparar uma ação específica no back-end. Você pode facilmente escrever uma regra de roteamento para enviar esses tipos de mensagens para um ponto de extremidade dedicado para seu processamento.

Para obter mais informações sobre a melhor maneira de processar esse tipo de mensagem, consulte o [Tutorial: como processar mensagens do dispositivo para a nuvem do Hub IoT][lnk-d2c-tutorial].

### <a name="routing-rules"></a>Regras de roteamento

Hub IoT permite rotear as mensagens para pontos de extremidade de Hub IoT com base nas propriedades da mensagem. As regras de roteamento oferecem a flexibilidade para enviar mensagens para onde elas precisam ir sem a necessidade de criar serviços adicionais para processar mensagens ou escrever código adicional. Cada regra de roteamento que você configurar tem as seguintes propriedades:

* **Nome**. O nome exclusivo que identifica a regra.
* **Fonte**. A origem do fluxo de dados a ser afetado. Por exemplo, telemetria do dispositivo.
* **Condição**. A expressão de consulta para a regra de roteamento que é executada em relação às propriedades da mensagem e usada para determinar se ela é uma correspondência para o ponto de extremidade. Para obter mais informações sobre como construir uma condição de rota, consulte o [Referência – linguagem de consulta para dispositivos gêmeos e trabalhos][lnk-devguide-query-language].
* **Ponto de extremidade**. O nome do ponto de extremidade em que Hub IoT envia as mensagens que correspondem à condição. Os pontos de extremidade devem estar na mesma região que o Hub IoT, caso contrário, você pode ser cobrado por gravações entre regiões.

Uma única mensagem pode corresponder à condição em várias regras de roteamentos, caso em que o Hub IoT entrega a mensagem para o ponto de extremidade associado a cada regra correspondente. O Hub IoT também elimina a duplicação da entrega de mensagem automaticamente, portanto, se uma mensagem corresponder a várias regras que têm o mesmo destino, ela será gravada apenas uma vez no destino.

Para obter mais informações sobre a criação de pontos de extremidade personalizados no Hub IoT, consulte [Pontos de extremidade do Hub IoT][lnk-devguide-endpoints].

### <a name="built-in-endpoint-messagesevents"></a>Ponto de extremidade interno: mensagens/eventos

Um Hub IoT expõe as propriedades a seguir para permitir que você controle as **mensagens/eventos** do ponto de extremidade do sistema de mensagens interno compatível com o Hub de Eventos.

* **Contagem de partição**. Defina essa propriedade no momento da criação e define o número de [partições][lnk-event-hub-partitions] para inclusão do evento do dispositivo para a nuvem.
* **Período de retenção**. Esta propriedade especifica por quanto tempo, em dias, as mensagens são retidas pelo Hub IoT. O padrão é de um dia, mas pode ser aumentado para sete dias.

O Hub IoT também permite que você gerencie grupos de consumidores no ponto de extremidade de recebimento do dispositivo para a nuvem interno.

Por padrão, todas as mensagens que não correspondem explicitamente a uma regra de roteamento de mensagem são gravadas no ponto de extremidade interno. Se você desabilitar esta rota de fallback, as mensagens que não corresponderão explicitamente a nenhuma regra de roteamento de mensagem são descartadas.

Você pode modificar o tempo de retenção, seja de maneira programática por meio das [APIs REST do provedor de recursos do Hub IoT][lnk-resource-provider-apis] ou usando o [portal do Azure][lnk-management-portal].

### <a name="anti-spoofing-properties"></a>Propriedades antifalsificação
Para evitar a falsificação em mensagens do dispositivo para a nuvem, o Hub IoT carimba todas as mensagens com as seguintes propriedades:

* **ConnectionDeviceId**
* **ConnectionDeviceGenerationId**
* **ConnectionAuthMethod**

As duas primeiras contêm a **deviceId** e a **generationId** do dispositivo de origem, conforme as [Propriedades de identidade do dispositivo][lnk-device-properties].

A propriedade **ConnectionAuthMethod** contém um objeto JSON serializado com as seguintes propriedades:

```
{
  "scope": "{ hub | device}",
  "type": "{ symkey | sas}",
  "issuer": "iothub"
}
```

## <a name="cloud-to-device-messages"></a>Mensagens da nuvem para o dispositivo
Você pode enviar mensagens da nuvem para o dispositivo por meio de um ponto de extremidade voltado para o serviço (**/messages/devicebound**). Um dispositivo as recebe por meio de um ponto de extremidade específico de dispositivo (**/devices/{deviceId}/messages/devicebound**).

Cada mensagem da nuvem para o dispositivo é direcionada a um único dispositivo definindo a propriedade **to** como **/devices/{deviceId}/messages/devicebound**.

> [!IMPORTANT]
> Cada fila de dispositivo contém no máximo 50 mensagens da nuvem para o dispositivo. Tentar enviar mais mensagens para o mesmo dispositivo resultará em um erro.
> 
> [!NOTE]
> Ao enviar mensagens da nuvem para o dispositivo, os nomes e valores de propriedade poderão conter apenas caracteres alfanuméricos ASCII e ``{'!', '#', '$', '%, '&', "'", '*', '*', '+', '-', '.', '^', '_', '`', '|', '~'}``.
> 
> 

### <a name="message-lifecycle"></a>Ciclo de vida da mensagem
Para assegurar pelo menos uma entrega de mensagem, o Hub IoT mantém as mensagens da nuvem para o dispositivo em filas por dispositivo. Os dispositivos devem reconhecer explicitamente a *conclusão* para que o Hub IoT as remova da fila. Isso garante a resiliência contra falhas de conectividade e do dispositivo.

O diagrama a seguir mostra o gráfico de estado do ciclo de vida de uma mensagem da nuvem para o dispositivo.

![Ciclo de vida de mensagens da nuvem para o dispositivo][img-lifecycle]

Quando o serviço envia uma mensagem, ela é considerada *Enfileirada*. Quando um dispositivo deseja *receber* uma mensagem, o Hub IoT *bloqueia* a mensagem (define o estado como **Invisível**), permitindo que outros threads no mesmo dispositivo comecem a receber outras mensagens. Quando um thread de dispositivo conclui o processamento de uma mensagem, ele notifica o Hub IoT *concluindo* a mensagem.

Um dispositivo também pode:

* *Rejeitar* a mensagem, o que faz com que o Hub IoT a defina no estado **Morto** . Observação: dispositivos que se conectam com MQTT não podem rejeitar as mensagens da nuvem para o dispositivo.
* *Abandonar* a mensagem, o que faz com que o Hub IoT coloque a mensagem de volta na fila com o estado definido como **Enfileirada**.

Um thread pode falhar ao processar uma mensagem sem notificar o Hub IoT. Nesse caso, as mensagens passam automaticamente do estado **Invisível** de volta para o estado **Enfileirada** após um *tempo limite de visibilidade (ou de bloqueio)*. O valor padrão desse tempo limite é um minuto.

Uma mensagem pode transitar entre os estados **Enfileirada** e **Invisível**, no máximo, pelo número de vezes especificado na propriedade **contagem máx. de entregas** no Hub IoT. Após esse número de transições, o Hub IoT definirá o estado da mensagem como **Morto**. Da mesma forma, o Hub IoT define o estado de uma mensagem como **Morto** após o tempo de expiração (confira [Vida útil][lnk-ttl]).

Para obter um tutorial sobre mensagens de nuvem para dispositivos, consulte o [Tutorial: Como enviar mensagens de nuvem para dispositivo com o Hub IoT][lnk-c2d-tutorial]. Para obter tópicos de referência sobre como os SDKs do IoT do Azure diferentes expõem a funcionalidade de nuvem para dispositivo, confira [SDKs do IoT do Azure][lnk-sdks].

> [!NOTE]
> Normalmente, as mensagens da nuvem para o dispositivo serão concluídas sempre que a perda da mensagem não afetar a lógica do aplicativo. Por exemplo, o conteúdo da mensagem foi mantido com êxito no armazenamento local ou uma operação foi executada com êxito. A mensagem também pode transportar informações temporárias, cuja perda não afeta a funcionalidade do aplicativo. Às vezes, para tarefas de longa duração, você pode concluir a mensagem do dispositivo para a nuvem depois de manter a descrição da tarefa no armazenamento local. Em seguida, você pode notificar o back-end da solução com uma ou mais mensagens de dispositivo para a nuvem em vários estágios de progresso da tarefa.
> 
> 

### <a name="message-expiration-time-to-live"></a>Expiração da mensagem (vida útil)
Todas as mensagens da nuvem para o dispositivo têm um tempo de expiração. Esse tempo é definido pelo serviço (na propriedade **ExpiryTimeUtc**) ou pelo Hub IoT usando a *vida útil* padrão especificada como uma propriedade do Hub IoT. Consulte [Opções de configuração da nuvem para o dispositivo][lnk-c2d-configuration].

> [!NOTE]
> Uma maneira comum de tirar proveito da expiração da mensagem e evitar enviar mensagens para dispositivos desconectados é definir valores de vida útil baixos. Essa abordagem proporciona o mesmo resultado que a manutenção do estado de conexão do dispositivo, enquanto é mais eficiente. Quando você solicita confirmações de mensagem, o Hub IoT notifica você sobre quais dispositivos podem receber mensagens e quais dispositivos não estão online ou apresentam falha.
> 
> 

### <a name="message-feedback"></a>Comentários da mensagem
Quando você envia uma mensagem da nuvem para o dispositivo, o serviço pode solicitar a entrega de um comentário por mensagem sobre o estado final dessa mensagem.

* Se você definir a propriedade **Ack** como **positivo**, o Hub IoT gerará uma mensagem de comentários se, e apenas se, a mensagem da nuvem para o dispositivo atingir o estado **Concluído**.
* Se você definir a propriedade **Ack** como **negativo**, o Hub IoT gerará uma mensagem de comentários se, e apenas se, a mensagem da nuvem para o dispositivo atingir o estado **Morto**.
* Se você definir a propriedade **Ack** como **total**, o Hub IoT gerará uma mensagem de comentários em ambos os casos.

> [!NOTE]
> Se **Ack** estiver definida como **total** e você não receber uma mensagem de comentários, isso significará que a mensagem de comentários expirou. O serviço não pode saber o que aconteceu com a mensagem original. Na prática, um serviço deve garantir que possa processar os comentários antes que eles expirem. O tempo máximo de expiração é de dois dias, o que permite tempo suficiente para restabelecer a execução do problema caso ocorra uma falha.
> 
> 

Como explicado em [Pontos de extremidade][lnk-endpoints], o Hub IoT fornece comentários por meio de um ponto de extremidade voltado para o serviço (**/messages/servicebound/feedback**) como mensagens. A semântica de recebimento dos comentários é a mesma das mensagens da nuvem para o dispositivo e tem o mesmo [Ciclo de vida da mensagem][lnk-lifecycle]. Sempre que possível, os comentários de mensagem são feitos em lotes em uma única mensagem, com o seguinte formato:

| Propriedade | Descrição |
| --- | --- |
| EnqueuedTime |Carimbo de data/hora que indica quando a mensagem foi criada. |
| UserId |`{iot hub name}` |
| ContentType |`application/vnd.microsoft.iothub.feedback.json` |

O corpo é uma matriz de registros serializada em JSON, cada um com as seguintes propriedades:

| Propriedade | Descrição |
| --- | --- |
| EnqueuedTimeUtc |Carimbo de data e hora que indica quando ocorreu a saída da mensagem. Por exemplo, o dispositivo foi concluído ou a mensagem expirou. |
| OriginalMessageId |**MessageId** da mensagem da nuvem para o dispositivo a qual essas informações de comentários pertencem. |
| StatusCode |Cadeia de caracteres obrigatória. Usado em mensagens de comentários geradas pelo Hub IoT. <br/> 'Êxito' <br/> 'Expirado' <br/> 'DeliveryCountExceeded' <br/> 'Rejeitado' <br/> 'Limpo' |
| Descrição |Valores de cadeia de caracteres para **StatusCode**. |
| deviceId |**DeviceId** do dispositivo de destino da mensagem da nuvem para o dispositivo a qual pertence esses comentários. |
| DeviceGenerationId |**DeviceGenerationId** do dispositivo de destino da mensagem da nuvem para o dispositivo a qual pertence esses comentários. |

> [!IMPORTANT]
> O serviço deve especificar uma **MessageId** para a mensagem da nuvem para o dispositivo, para poder correlacionar seus comentários com a mensagem original.
> 
> 

O exemplo a seguir mostra o corpo de uma mensagem de comentários.

```
[
  {
    "OriginalMessageId": "0987654321",
    "EnqueuedTimeUtc": "2015-07-28T16:24:48.789Z",
    "StatusCode": 0
    "Description": "Success",
    "DeviceId": "123",
    "DeviceGenerationId": "abcdefghijklmnopqrstuvwxyz"
  },
  {
    ...
  },
  ...
]
```

### <a name="cloud-to-device-configuration-options"></a>Opções de configuração da nuvem para o dispositivo
Cada hub IoT expõe as seguintes opções de configuração para mensagens da nuvem para o dispositivo.

| Propriedade | Descrição | Intervalo e padrão |
| --- | --- | --- |
| defaultTtlAsIso8601 |TTL padrão para mensagens da nuvem para o dispositivo. |Intervalo ISO_8601 de até 2D (mínimo de 1 minuto). Padrão: 1 hora. |
| maxDeliveryCount |Contagem máxima de entrega para filas de nuvem para o dispositivo por dispositivo. |1 a 100. Padrão: 10. |
| feedback.ttlAsIso8601 |Retenção de mensagens informativas do serviço associado. |Intervalo ISO_8601 de até 2D (mínimo de 1 minuto). Padrão: 1 hora. |
| feedback.maxDeliveryCount |Contagem máxima de entrega para a fila de comentários. |1 a 100. Padrão: 100. |

Para saber mais, confira [Criar Hubs IoT][lnk-portal].

## <a name="read-device-to-cloud-messages"></a>Ler mensagens do dispositivo para a nuvem
O Hub IoT expõe um ponto de extremidade interno de **mensagens/eventos** para os serviços de back-end lerem as mensagens de dispositivo para nuvem recebidas por seu hub. Este ponto de extremidade é compatível com o Hub de Eventos, o que permite que você use qualquer um dos mecanismos para os quais o serviço Hubs de Eventos dá suporte para ler as mensagens.

Você também pode criar pontos de extremidade personalizados no Hub IoT. O Hub IoT atualmente dá suporte aos Hubs de Eventos, filas do Barramento de Serviço e tópicos do Barramento de Serviço como pontos de extremidade personalizados. Para obter mais informações sobre a leitura desses serviços, consulte: leitura de [Hubs de Eventos][lnk-getstarted-eh], leitura de [filas do Barramento de Serviço][lnk-getstarted-queue], leitura de [tópicos do Barramento de Serviço][lnk-getstarted-topic].

### <a name="reading-from-the-built-in-endpoint"></a>Leitura do ponto de extremidade interno

Ao usar o [SDK do Barramento de Serviço do Azure para .NET][lnk-servicebus-sdk] ou [Hubs de Eventos – Host Processador de Evento][lnk-eventprocessorhost], você pode usar qualquer cadeia de conexão do Hub IoT com as permissões corretas. Em seguida, use **mensagens/eventos** como o nome do Hub de Eventos.

Ao usar os SDKs (ou integrações de produtos) que não reconhecem o Hub IoT, será necessário recuperar um ponto de extremidade compatível com os Hubs de Eventos e o nome do Hub de Eventos das configurações do Hub IoT no [Portal do Azure][lnk-management-portal]:

1. Na folha de Hub IoT, clique em **Pontos de Extremidade**.
2. Na seção **Pontos de extremidade internos**, clique em **Eventos**. A folha contém os seguintes valores: **Ponto de extremidade compatível com o Hub de Eventos**, **Nome compatível com o Hub de Eventos**, **Partições**, **Ponto de retenção** e **Grupos de consumidores**.
   
    ![Configurações de dispositivo para a nuvem][img-eventhubcompatible]

> [!NOTE]
> O SDK do Hub IoT requer o nome de ponto de extremidade do Hub IoT, que é **mensagens/eventos** conforme mostrado na folha **Pontos de extremidade**.
>
>

> [!NOTE]
> Se o SDK sendo usado exigir um valor de **Nome do host** ou **Namespace**, remova o esquema do **Ponto de extremidade compatível com o Hub de Eventos**. Por exemplo, se o ponto de extremidade compatível com o Hub de Eventos fosse **sb://iothub-ns-myiothub-1234.servicebus.windows.net/**, o **Nome do host** seria **iothub-ns-myiothub-1234.servicebus.windows.net** e o **Namespace** seria **iothub-ns-myiothub-1234**.
> 
>

Dessa forma, você poderá usar qualquer política de acesso compartilhado com permissões **ServiceConnect** para se conectar ao Hub de Eventos especificado.

Caso você precise criar uma cadeia de conexão do Hub de Eventos usando as informações anteriores, use o seguinte padrão:

```
Endpoint={Event Hub-compatible endpoint};SharedAccessKeyName={iot hub policy name};SharedAccessKey={iot hub policy key}
```

A seguir, uma lista dos SDKs e das integrações que você pode usar com os pontos de extremidade compatíveis com o Hub de Eventos expostos pelo Hub IoT:

* [Cliente Java dos Hubs de Eventos](https://github.com/hdinsight/eventhubs-client)
* [Spout do Apache Storm](../hdinsight/hdinsight-storm-develop-csharp-event-hub-topology.md). Você pode exibir a [fonte do spout](https://github.com/apache/storm/tree/master/external/storm-eventhubs) no GitHub.
* [Integração do Apache Spark](../hdinsight/hdinsight-apache-spark-eventhub-streaming.md)

## <a name="reference-topics"></a>Tópicos de referência:
Os tópicos de referência a seguir fornecem a você mais informações sobre como trocar mensagens com o Hub IoT.

## <a name="message-format"></a>Formato da mensagem
As mensagens do Hub IoT são formadas por:

* Um conjunto de *propriedades do sistema*. Propriedades que o Hub IoT interpreta ou define. Esse conjunto é predeterminado.
* Um conjunto de *propriedades do aplicativo*. Um dicionário de propriedades de cadeia de caracteres que o aplicativo pode definir e acessar sem precisar desserializar o corpo da mensagem. O Hub IoT nunca modifica essas propriedades.
* Um corpo de binário opaco.

Para saber mais sobre como a mensagem é codificada em protocolos diferentes, veja [SDKs do IoT do Azure][lnk-sdks].

A tabela a seguir lista o conjunto de propriedades do sistema em mensagens do Hub IoT.

| Propriedade | Descrição |
| --- | --- |
| MessageId |Um identificador configurável pelo usuário para a mensagem, usado para padrões de solicitação-resposta. Formato: uma cadeia de caracteres que diferencia maiúsculas de minúsculas (com até 128 caracteres) de caracteres alfanuméricos ASCII de 7 bits + `{'-', ':',’.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`. |
| Número de sequência |Um número (exclusivo por fila de dispositivos) atribuído pelo Hub IoT a cada mensagem da nuvem para o dispositivo. |
| Para |Um destino especificado em mensagens [Da nuvem para o dispositivo][lnk-c2d]. |
| ExpiryTimeUtc |Data e hora de expiração da mensagem. |
| EnqueuedTime |Data e hora do recebimento da mensagem [Nuvem para o Dispositivo][lnk-c2d] pelo Hub IoT. |
| CorrelationId |Uma cadeia de propriedade em uma mensagem de resposta que geralmente contém a MessageId da solicitação em padrões de solicitação-resposta. |
| UserId |Uma ID usada para especificar a origem das mensagens. Quando as mensagens são geradas pelo Hub IoT, são definidas como `{iot hub name}`. |
| Ack |Um gerador de mensagem de comentários. Essa propriedade é usada em mensagens da nuvem para o dispositivo para solicitar ao Hub IoT a geração de mensagens de comentários como resultado do consumo da mensagem pelo dispositivo. Valores possíveis: **nenhum** (padrão): nenhuma mensagem de comentários é gerada, **positivo**: ocorrerá o recebimento de uma mensagem de comentários se a mensagem estiver completa, **negativo**: ocorrerá o recebimento de uma mensagem de comentários se a mensagem expirar (ou se a contagem máxima de entrega tiver sido atingida) sem ser concluída pelo dispositivo ou **total**: positivos e negativos. Para saber mais, consulte [Comentários sobre a mensagem][lnk-feedback]. |
| ConnectionDeviceId |Uma ID definida pelo Hub IoT em mensagens do dispositivo para a nuvem. Contém a **deviceId** do dispositivo que enviou a mensagem. |
| ConnectionDeviceGenerationId |Uma ID definida pelo Hub IoT em mensagens do dispositivo para a nuvem. Contém a **generationId** (de acordo com as [Propriedades de identidade de dispositivo][lnk-device-properties]) do dispositivo que enviou a mensagem. |
| ConnectionAuthMethod |Um método de autenticação definido pelo Hub IoT em mensagens do dispositivo para a nuvem. Essa propriedade contém informações sobre o método de autenticação usado para autenticar o dispositivo que envia a mensagem. Para saber mais, veja [Antifalsificação do dispositivo para a nuvem][lnk-antispoofing]. |

## <a name="message-size"></a>Tamanho da mensagem

O Hub IoT mede o tamanho da mensagem independentemente do protocolo, considerando apenas a carga real. O tamanho em bytes é calculado como a soma do seguinte:

* O tamanho do corpo em bytes mais
* O tamanho em bytes de todos os valores das propriedades do sistema de mensagens mais
* O tamanho em bytes de todos os nomes e valores de propriedade do usuário.

Observe que os nomes e valores de propriedade são limitados a caracteres ASCII, para que o comprimento das cadeias de caracteres seja igual ao tamanho em bytes.

## <a name="communication-protocols"></a>Protocolos de comunicação
O Hub IoT permite que os dispositivos usem protocolos [MQTT][lnk-mqtt], MQTT sobre WebSockets, [AMQP][lnk-amqp], AMQP sobre WebSockets e HTTP para comunicação do lado do dispositivo. A tabela a seguir fornece as recomendações de alto nível para sua escolha de protocolo:

| Protocolo | Quando você deve escolher este protocolo |
| --- | --- |
| MQTT <br> MQTT sobre WebSocket |Use em todos os dispositivos que não exigem conectar vários dispositivos (cada um com suas próprias credenciais por dispositivo) sobre a mesma conexão TLS. |
| AMQP <br> AMQP sobre WebSocket |Use em gateways de campo e de nuvem para tirar proveito da multiplexação de conexão entre dispositivos. |
| HTTP |Use para dispositivos que não dão suporte a outros protocolos. |

Considere os seguintes pontos ao escolher seu protocolo de comunicação do lado do dispositivo:

* **Padrão da nuvem para o dispositivo**. O HTTP não tem uma maneira eficiente de implementar o envio do servidor. Assim, ao usar HTTP, os dispositivos sondam o Hub IoT em busca de mensagens da nuvem para o dispositivo. Essa abordagem é ineficiente para o dispositivo e para o Hub IoT. De acordo com as diretrizes atuais de HTTP, cada dispositivo deve sondar mensagens a cada 25 minutos ou mais. Por outro lado, o MQTT e o AMQP dão suporte ao envio por push do servidor quando recebem mensagens de nuvem para o dispositivo. Eles permitem envios por push imediatos de mensagens do Hub IoT para o dispositivo. Se a latência de entrega for uma preocupação, o MQTT ou AMQP serão as melhores opções de protocolo. Para dispositivos conectados raramente, o HTTP funciona também.
* **Gateways de campo**. Ao usar MQTT e HTTP, você não pode conectar vários dispositivos (cada um com suas próprias credenciais por dispositivo) usando a mesma conexão TLS. Assim, para [Cenários de gateway de campo][lnk-azure-gateway-guidance], esses protocolos ficam abaixo do ideal, pois exigem uma conexão TLS entre o gateway de campo e o Hub IoT para cada dispositivo conectado ao gateway de campo.
* **Dispositivos com poucos recursos**. As bibliotecas de MQTT e HTTP têm uma superfície menor do que as bibliotecas de AMQP. Dessa forma, caso o dispositivo tenha recursos limitados (por exemplo, menos de 1 MB de RAM), esses protocolos poderão ser a única implementação de protocolo disponível.
* **Percurso da rede**. O protocolo AMQP padrão usa a porta 5671, enquanto o MQTT escuta na porta 8883, o que pode causar problemas em redes que estão fechadas para protocolos não HTTP. MQTT sobre WebSockets, AMQP sobre WebSockets e HTTP estão disponíveis para serem usados nesse cenário.
* **Tamanho da carga**. O MQTT e o AMQP são protocolos binários, que resultam em cargas mais compactas que o HTTP.

> [!NOTE]
> Ao usar o HTTP, cada dispositivo deverá sondar se há mensagens da nuvem para o dispositivo a cada 25 minutos ou mais. No entanto, durante o desenvolvimento, é aceitável sondar com mais frequência do que a cada 25 minutos.
> 
> 

## <a name="port-numbers"></a>Números de porta
Os dispositivos podem se comunicar com o Hub IoT no Azure usando uma variedade de protocolos. Normalmente, a opção de protocolo é orientada por requisitos específicos da solução. A tabela a seguir lista as portas de saída que devem ser abertas para um dispositivo para poder usar um protocolo específico:

| Protocolo | Porta |
| --- | --- |
| MQTT |8883 |
| MQTT sobre WebSockets |443 |
| AMQP |5671 |
| AMQP sobre WebSockets |443 |
| HTTP |443 |
| LWM2M (Gerenciamento de dispositivo) |5684 |

Depois de criar um Hub IoT em uma região do Azure, o hub manterá o mesmo endereço IP durante a vida útil desse Hub IoT. No entanto, para manter a qualidade do serviço, se a Microsoft mover o Hub IoT para uma unidade de escala diferente, ele receberá um novo endereço IP.

## <a name="notes-on-mqtt-support"></a>Observações sobre o suporte ao MQTT
O Hub IoT implementa o protocolo MQTT v3.1.1 com as seguintes limitações e comportamento específico:

* **Não há suporte para QoS 2**. Quando um aplicativo de dispositivo publica uma mensagem com o **QoS 2**, o Hub IoT fecha a conexão de rede. Quando um aplicativo do dispositivo assina um tópico com o **QoS 2**, o Hub IoT concede, no máximo, o nível 1 do QoS no pacote **SUBACK**.
* **Mensagens marcadas como Retain não permanecem**. Se um aplicativo do dispositivo publicar uma mensagem com o sinalizador RETAIN definido como 1, o Hub IoT adicionará a propriedade de aplicativo **x-opt-retain** à mensagem. Nesse caso, o Hub IoT não mantém a mensagem de retenção, mas a transmite ao aplicativo de back-end.

Para saber mais, veja [Suporte ao MQTT do Hub IoT][lnk-devguide-mqtt].

Como uma consideração final, você deve examinar o [Gateway de protocolo do IoT do Azure][lnk-azure-protocol-gateway], que o habilita a implantar um gateway de protocolo personalizado de alto desempenho que interage diretamente com o Hub IoT. O gateway do protocolo IoT do Azure permite que você personalize o protocolo de dispositivo para acomodar as implantações de MQTT de nível industrial ou outros protocolos personalizados. Essa abordagem exige, no entanto, que você execute e opere um gateway de protocolo personalizado.

## <a name="additional-reference-material"></a>Material de referência adicional
Outros tópicos de referência no Guia do desenvolvedor do Hub IoT incluem:

* [Pontos de extremidade do Hub IoT][lnk-endpoints] descreve os vários pontos de extremidade que cada Hub IoT expõe para operações de tempo de execução e de gerenciamento.
* [Limitação e cotas][lnk-quotas] descreve as cotas que se aplicam ao serviço Hub IoT e o comportamento de limitação esperado ao usar o serviço.
* [SDKs de dispositivo e serviço IoT do Azure][lnk-sdks] lista os vários SDKs de linguagem que você pode usar no desenvolvimento de aplicativos de dispositivo e de serviço que interagem com o Hub IoT.
* [Linguagem de consulta do Hub IoT para dispositivos gêmeos e trabalhos][lnk-query] descreve a linguagem de consulta do Hub IoT que você pode usar para recuperar informações do Hub IoT sobre dispositivos gêmeos e trabalhos.
* [Suporte ao MQTT do Hub IoT][lnk-devguide-mqtt] fornece mais informações sobre o suporte do Hub IoT para o protocolo MQTT.

## <a name="next-steps"></a>Próximas etapas
Agora que você aprendeu a enviar e receber mensagens com o Hub IoT, pode ser interessante ler os seguintes tópicos do Guia do Desenvolvedor do Hub IoT:

* [Carregar arquivos de um dispositivo][lnk-devguide-upload]
* [Gerenciar identidades do dispositivo no Hub IoT][lnk-devguide-identities]
* [Controlar o acesso ao Hub IoT][lnk-devguide-security]
* [Usar dispositivos gêmeos para sincronizar o estado e as configurações][lnk-devguide-device-twins]
* [Invocar um método direto em um dispositivo][lnk-devguide-directmethods]
* [Agendar trabalhos em vários dispositivos][lnk-devguide-jobs]

Se você quiser experimentar alguns dos conceitos descritos neste artigo, talvez se interesse pelos seguintes tutoriais de Hub IoT:

* [Introdução ao Hub IoT do Azure][lnk-getstarted-tutorial]
* [Como enviar mensagens da nuvem para o dispositivo com o Hub IoT][lnk-c2d-tutorial]
* [Como processar mensagens de dispositivo para nuvem do Hub IoT][lnk-d2c-tutorial]

[img-lifecycle]: ./media/iot-hub-devguide-messaging/lifecycle.png
[img-eventhubcompatible]: ./media/iot-hub-devguide-messaging/eventhubcompatible.png

[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-azure-gateway-guidance]: iot-hub-devguide-endpoints.md#field-gateways
[lnk-guidance-scale]: iot-hub-scaling.md
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md
[lnk-amqp]: http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf
[lnk-mqtt]: http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/mqtt-v3.1.1.pdf
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/
[lnk-event-hubs-consuming-events]: ../event-hubs/event-hubs-programming-guide.md#event-consumers
[lnk-management-portal]: https://portal.azure.com
[lnk-servicebus]: http://azure.microsoft.com/documentation/services/service-bus/
[lnk-eventhub-partitions]: ../event-hubs/event-hubs-overview.md#partitions
[lnk-portal]: iot-hub-create-through-portal.md
[lnk-getstarted-eh]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-getstarted-queue]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md
[lnk-getstarted-topic]: ../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md

[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-c2d]: iot-hub-devguide-messaging.md#cloud-to-device-messages
[lnk-compatible-endpoint]: iot-hub-devguide-messaging.md#read-device-to-cloud-messages
[lnk-protocols]: iot-hub-devguide-messaging.md#communication-protocols
[lnk-message-format]: iot-hub-devguide-messaging.md#message-format
[lnk-device-properties]: iot-hub-devguide-identity-registry.md#device-identity-properties
[lnk-ttl]: iot-hub-devguide-messaging.md#message-expiration-time-to-live
[lnk-c2d-configuration]: iot-hub-devguide-messaging.md#cloud-to-device-configuration-options
[lnk-lifecycle]: iot-hub-devguide-messaging.md#message-lifecycle
[lnk-feedback]: iot-hub-devguide-messaging.md#message-feedback
[lnk-antispoofing]: iot-hub-devguide-messaging.md#anti-spoofing-properties
[lnk-compare]: iot-hub-compare-event-hubs.md

[lnk-devguide-upload]: iot-hub-devguide-file-upload.md
[lnk-devguide-identities]: iot-hub-devguide-identity-registry.md
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-servicebus-sdk]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-eventprocessorhost]: http://blogs.msdn.com/b/servicebus/archive/2015/01/16/event-processor-host-best-practices-part-1.aspx
[lnk-devguide-query-language]: iot-hub-devguide-query-language.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md

[lnk-getstarted-tutorial]: iot-hub-csharp-csharp-getstarted.md
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
[lnk-event-hub-partitions]: ../event-hubs/event-hubs-what-is-event-hubs.md#partitions

