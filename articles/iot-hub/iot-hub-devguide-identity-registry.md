---
title: Entender o Registro da identidade do Hub IoT do Azure | Microsoft Docs
description: "Guia do desenvolvedor ‑ descrição do Registro de identidade do Hub IoT e como usá-lo para gerenciar seus dispositivos. Inclui informações sobre a importação e exportação de identidades de dispositivo em massa."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 0706eccd-e84c-4ae7-bbd4-2b1a22241147
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/04/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 532ff423ff53567b6ce40c0ea7ec09a689cee1e7
ms.openlocfilehash: 6a69dc900eee2f539a2b1740c4e89132e2bd6db7
ms.contentlocale: pt-br
ms.lasthandoff: 06/05/2017


---
# <a name="understand-identity-registry-in-your-iot-hub"></a>Entender o Registro de identidade no hub IoT

## <a name="overview"></a>Visão geral

Cada Hub IoT tem um registro de identidade que armazena informações sobre os dispositivos com permissão para se conectar ao Hub IoT. Antes de um dispositivo poder se conectar a um Hub IoT, deve existir uma entrada para esse dispositivo no registro de identidade do Hub IoT. O dispositivo também deve realizar a autenticação no Hub IoT com base em credenciais armazenadas no registro de identidade.

A ID do dispositivo armazenada no Registro de identidade diferencia maiúsculas de minúsculas.

Em um alto nível, o registro de identidade é uma coleção compatível com REST de recursos de identidade do dispositivo. Quando você adiciona uma entrada ao Registro de identidade, o Hub IoT cria um conjunto de recursos por dispositivo no serviço, tal como a fila que contém mensagens em andamento da nuvem para o dispositivo.

### <a name="when-to-use"></a>Quando usar

Use o registro de identidade quando você precisar provisionar dispositivos que se conectam ao Hub IoT e quando precisar controlar o acesso de cada dispositivo aos pontos de extremidade voltados ao dispositivo em seu hub.

> [!NOTE]
> O registro de identidade não contém os metadados específicos do aplicativo.

## <a name="identity-registry-operations"></a>Operações de registro de identidade

O registro de identidade do Hub IoT expõe as seguintes operações:

* Criar identidade do dispositivo
* Atualizar identidade do dispositivo
* Recuperar a identidade do dispositivo por ID
* Excluir identidade do dispositivo
* Listar até 1000 identidades
* Exportar todas as identidades para o Armazenamento de Blobs do Azure
* Importar identidades do Armazenamento de Blobs do Azure

Todas essas operações podem usar a simultaneidade otimista, conforme especificado na [RFC7232][lnk-rfc7232].

> [!IMPORTANT]
> A única maneira de recuperar todas as identidades em um registro de identidade do Hub IoT é usar a funcionalidade [Exportar][lnk-export].

Um registro de identidade do Hub IoT:

* Não contém metadados de aplicativo.
* Pode ser acessado como um dicionário usando o **deviceId** como a chave.
* Não permite consultas expressivas.

Normalmente, uma solução de IoT tem um armazenamento específico da solução separado que contém metadados específicos do aplicativo. Por exemplo, o armazenamento específico da solução em uma solução de construção inteligente seria registrar a sala na qual um sensor de temperatura está implantado.

> [!IMPORTANT]
> Você deve usar o registro de identidade apenas para operações de provisionamento e gerenciamento de dispositivos. As operações de alta produtividade no tempo de execução não devem depender da execução de operações no registro de identidade. Por exemplo, verificar o estado da conexão de um dispositivo antes de enviar um comando não é um padrão permitido. Lembre-se de verificar as [taxas de limitação][lnk-quotas] do registro de identidade e o padrão de [pulsação do dispositivo][lnk-guidance-heartbeat].

## <a name="disable-devices"></a>Desabilitar dispositivos

Você pode desabilitar dispositivos atualizando a propriedade **status** de uma identidade no Registro. Normalmente, você deve usar essa propriedade em dois cenários:

* Durante um processo de orquestração de provisionamento. Para saber mais, veja [Provisionamento de dispositivo][lnk-guidance-provisioning].
* Se, por algum motivo, você considerar que um dispositivo está comprometido ou que se tornou não autorizado.

## <a name="import-and-export-device-identities"></a>Importar e exportar identidades de dispositivo

É possível exportar identidades de dispositivo em massa do registro de identidade de um Hub IoT usando operações assíncronas no [ponto de extremidade do provedor de recursos do Hub IoT][lnk-endpoints]. As exportações são trabalhos de execução longa que usam um contêiner de blobs fornecido pelo cliente para salvar dados de identidade do dispositivo lidos no registro de identidade.

É possível importar identidades de dispositivo em massa para um registro de identidade de um Hub IoT usando operações assíncronas no [ponto de extremidade do provedor de recursos do Hub IoT][lnk-endpoints]. As exportações são trabalhos de execução longa que usam dados em um contêiner de blobs fornecido pelo cliente para gravar dados de identidade do dispositivo no registro de identidade.

* Para obter informações detalhadas sobre as APIs de importação e exportação, consulte [APIs REST do provedor de recursos do Hub IoT][lnk-resource-provider-apis].
* Para saber mais sobre como executar trabalhos de importação e exportação, veja [Gerenciamento em massa de identidades de dispositivo do Hub IoT][lnk-bulk-identity].

## <a name="device-provisioning"></a>Provisionamento de dispositivos

Os dados de dispositivo que uma determinada solução IoT armazena dependem dos requisitos específicos dessa solução. Porém, no mínimo, uma solução deve armazenar identidades e chaves de autenticação. O Hub IoT do Azure inclui um registro de identidades que pode armazenar valores para cada dispositivo, como IDs, chaves de autenticação e códigos de status. Uma solução pode usar outros serviços do Azure como armazenamento de tabelas do Azure, armazenamento de blobs do Azure ou Azure Cosmos DB para armazenar outros dados de dispositivo.

*Provisionamento de dispositivos* é o processo de adição dos dados iniciais do dispositivo para as lojas em sua solução. Para permitir que um dispositivo se conecte ao hub, você deve adicionar uma ID e chaves de dispositivo ao registro de identidade do Hub IoT. Como parte do processo de provisionamento, talvez seja necessário inicializar dados específicos do dispositivo em outros repositórios da solução.

## <a name="device-heartbeat"></a>Pulsação do dispositivo

O registro de identidade do Hub IoT contém um campo chamado **connectionState**. Use somente o campo **connectionState** durante o desenvolvimento e a depuração. As soluções de IoT não devem consultar o campo no tempo de execução (por exemplo, para verificar se um dispositivo está conectado, a fim de decidir se uma mensagem da nuvem para o dispositivo ou um SMS deve ser enviado).

Se a solução de IoT precisar saber se um dispositivo está conectado (em tempo de execução ou com mais precisão do que a fornecida pela propriedade **connectionState**), a solução deverá implementar o *padrão de pulsação*.

No padrão de pulsação, o dispositivo envia mensagens do dispositivo para a nuvem pelo menos uma vez a cada período de tempo fixo (por exemplo, pelo menos uma vez a cada hora). Portanto, mesmo quando um dispositivo não tiver dados para enviar, ele enviará uma mensagem vazia do dispositivo para a nuvem (geralmente com uma propriedade que a identifique como uma pulsação). No lado do serviço, a solução mantém um mapa com a última pulsação recebida para cada dispositivo. A solução supõe que haja um problema com um dispositivo se não receber uma mensagem de pulsação no tempo esperado.

Uma implementação mais complexa pode incluir as informações do [monitoramento de operações][lnk-devguide-opmon] para identificar dispositivos que estão tentando se conectar ou se comunicar, mas falham. Ao implementar o padrão de pulsação, verifique as [Cotas e limitações do Hub IoT][lnk-quotas].

> [!NOTE]
> Se uma solução IoT precisar do estado de conexão do dispositivo apenas para determinar se deve enviar mensagens da nuvem para o dispositivo, e as mensagens não forem transmitidas para conjuntos grandes de dispositivos, um padrão mais simples a ser considerado é usar um tempo de validade mais curto. Esse padrão é o mesmo que manter um registro do estado da conexão do dispositivo usando o padrão de pulsação, embora seja mais eficiente. Também é possível, por meio da solicitação de confirmações de mensagens, receber uma notificação pelo Hub IoT de quais dispositivos são capazes de receber mensagens e quais não estão online ou apresentam falha.

## <a name="device-lifecycle-notifications"></a>Notificações do ciclo de vida do dispositivo

O Hub IoT pode notificar sua solução de IoT quando uma identidade de dispositivo é criada ou excluída, enviando notificações do ciclo de vida do dispositivo. Para fazer isso, sua solução de IoT precisa para criar uma rota e definir a Fonte de Dados como *DeviceLifecycleEvents*. Por padrão, nenhuma notificação de ciclo de vida é enviada, ou seja, nenhuma dessas rotas existe previamente. A mensagem de notificação inclui propriedades e o corpo.

- Propriedades

As propriedades do sistema de mensagens são fixadas previamente com o símbolo `'$'`.

| Nome | Valor |
| --- | --- |
$content-type | aplicativo/json |
$iothub-enqueuedtime |  Hora em que a notificação foi enviada |
$iothub-message-source | deviceLifecycleEvents |
$content-encoding | utf-8 |
opType | “createDeviceIdentity” ou “deleteDeviceIdentity” |
hubName | Nome do Hub IoT |
deviceId | ID do dispositivo |
operationTimestamp | Carimbo de data/hora ISO8601 da operação |
iothub-message-schema | deviceLifecycleNotification |

- Corpo

Esta seção está no formato JSON e representa o gêmeo da identidade de dispositivo criada. Por exemplo,
``` 
{
    "deviceId":"11576-ailn-test-0-67333793211",
    "etag":"AAAAAAAAAAE=",
    "properties": {
        "desired": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        },
        "reported": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        }
    }
}
```

## <a name="reference-topics"></a>Tópicos de referência:

Os tópicos de referência a seguir fornecem a você mais informações sobre o registro de identidade.

## <a name="device-identity-properties"></a>Propriedades de identidade do dispositivo

As identidades do dispositivo são representadas como documentos JSON com as seguintes propriedades:

| Propriedade | Opções | Descrição |
| --- | --- | --- |
| deviceId |obrigatória, somente leitura em atualizações |Uma cadeia de caracteres que diferencia maiúsculas de minúsculas (com até 128 caracteres) de caracteres alfanuméricos ASCII de 7 bits + `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`. |
| generationId |obrigatória, somente leitura |Uma cadeia de caracteres que diferencia maiúsculas de minúsculas com até 128 caracteres gerada pelo Hub IoT. Esse valor é usado para distinguir os dispositivos com a mesma **deviceId** quando são excluídos e recriados. |
| etag |obrigatória, somente leitura |Uma cadeia de caracteres que representa uma ETag fraca para a identidade do dispositivo, de acordo com [RFC7232][lnk-rfc7232]. |
| auth |opcional |Um objeto composto que contém as informações de autenticação e os materiais de segurança. |
| auth.symkey |opcional |Um objeto composto que contém as chaves primária e secundária, armazenadas no formato base64. |
| status |obrigatório |Um indicador de acesso. Pode estar **Habilitado** ou **Desabilitado**. Se estiver **Habilitado**, o dispositivo terá permissão para se conectar. Se estiver **Desabilitado**, este dispositivo não poderá acessar qualquer ponto de extremidade voltado para o dispositivo. |
| statusReason |opcional |Uma cadeia de caracteres com 128 caracteres que armazena o motivo do status de identidade do dispositivo. Todos os caracteres UTF-8 são permitidos. |
| statusUpdateTime |somente leitura |Um indicador temporal, mostrando a data e hora da última atualização de status. |
| connectionState |somente leitura |Um campo indicando o status da conexão: **Conectado** ou **Desconectado**. Esse campo representa a exibição do Hub IoT do status de conexão do dispositivo. **Importante**: esse campo deve ser usado apenas para fins de desenvolvimento/depuração. O estado da conexão é atualizado somente nos dispositivos que usam MQTT ou AMQP. Além disso, ele se baseia nos pings do nível de protocolo (pings MQTT ou AMQP) e pode ter um atraso máximo de apenas cinco minutos. Por esses motivos, pode haver falsos positivos, como dispositivos relatados como conectados, mas que estão desconectados. |
| connectionStateUpdatedTime |somente leitura |Um indicador temporal, mostrando a data e a hora da última atualização do estado da conexão. |
| lastActivityTime |somente leitura |Um indicador temporal, mostrando a data e hora da última vez em que o dispositivo se conectou, recebeu ou enviou uma mensagem. |

> [!NOTE]
> O estado da conexão pode representar apenas a visão do Hub IoT do status da conexão. As atualizações para esse estado podem ser atrasadas, dependendo das configurações e das condições da rede.

## <a name="additional-reference-material"></a>Material de referência adicional

Outros tópicos de referência no Guia do desenvolvedor do Hub IoT incluem:

* [Pontos de extremidade do Hub IoT][lnk-endpoints] descreve os vários pontos de extremidade que cada Hub IoT expõe para operações de tempo de execução e de gerenciamento.
* [Limitação e cotas][lnk-quotas] descreve as cotas que se aplicam ao serviço Hub IoT e o comportamento de limitação esperado ao usar o serviço.
* [SDKs de dispositivo e serviço IoT do Azure][lnk-sdks] lista os vários SDKs de linguagem que você pode usar no desenvolvimento de aplicativos de dispositivo e de serviço que interagem com o Hub IoT.
* [Linguagem de consulta do Hub IoT para dispositivos gêmeos, trabalhos e roteamento de mensagens][lnk-query] descreve a linguagem de consulta do Hub IoT que você pode usar para recuperar informações do Hub IoT sobre dispositivos gêmeos e trabalhos.
* [Suporte ao MQTT do Hub IoT][lnk-devguide-mqtt] fornece mais informações sobre o suporte do Hub IoT para o protocolo MQTT.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a usar o Registro de identidade do Hub IoT, pode ser interessante ler os seguintes tópicos do Guia do desenvolvedor do Hub IoT:

* [Controlar o acesso ao Hub IoT][lnk-devguide-security]
* [Usar dispositivos gêmeos para sincronizar o estado e as configurações][lnk-devguide-device-twins]
* [Invocar um método direto em um dispositivo][lnk-devguide-directmethods]
* [Agendar trabalhos em vários dispositivos][lnk-devguide-jobs]

Se você quiser experimentar alguns dos conceitos descritos neste artigo, talvez se interesse pelo seguinte tutorial de Hub IoT:

* [Introdução ao Hub IoT do Azure][lnk-getstarted-tutorial]

<!-- Links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-resource-provider-apis]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-guidance-provisioning]: iot-hub-devguide-identity-registry.md#device-provisioning
[lnk-guidance-heartbeat]: iot-hub-devguide-identity-registry.md#device-heartbeat
[lnk-rfc7232]: https://tools.ietf.org/html/rfc7232
[lnk-bulk-identity]: iot-hub-bulk-identity-mgmt.md
[lnk-export]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities
[lnk-devguide-opmon]: iot-hub-operations-monitoring.md

[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md

[lnk-getstarted-tutorial]: iot-hub-csharp-csharp-getstarted.md

