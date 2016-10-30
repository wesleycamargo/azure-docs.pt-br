<properties
 pageTitle="Guia do desenvolvedor – métodos diretos | Microsoft Azure"
 description="Guia de desenvolvedor de Hub IoT do Azure – use métodos diretos para invocar código em seus dispositivos"
 services="iot-hub"
 documentationCenter=".net"
 authors="nberdy"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="nberdy"/>


# <a name="invoke-a-direct-method-on-a-device-(preview)"></a>Invocar um método direto em um dispositivo (visualização)

## <a name="overview"></a>Visão geral

O Hub IoT oferece a capacidade de invocar métodos em dispositivos da nuvem. Os métodos representam uma interação de solicitação-resposta com um dispositivo semelhante a uma chamada HTTP no sentido de que é bem sucedida ou falha imediatamente (após um tempo limite especificado pelo usuário) para permitir que o usuário saiba o status da chamada. Isso é útil para cenários em que a ação imediata é diferente dependendo da capacidade de resposta do dispositivo, como enviar uma ativação por SMS para um dispositivo se o dispositivo está offline (com o SMS sendo mais caro do que uma chamada de método).

Você pode pensar em um método como uma chamada de procedimento remota diretamente para o dispositivo. Somente métodos que foram implementados em um dispositivo podem ser chamados da nuvem. Se a nuvem tentar invocar um método em um dispositivo que não tem esse método definido, a chamada do método falhará.

Cada método de dispositivo tem como alvo um único dispositivo. [Trabalhos][lnk-devguide-jobs] fornecem uma maneira de invocar métodos em vários dispositivos e colocar em fila a invocação de método para dispositivos desconectados.

Qualquer pessoa com permissões de **conectar serviço** no Hub IoT pode invocar um método em um dispositivo.

### <a name="when-to-use"></a>Quando usar

Métodos de dispositivo são semelhantes a [mensagens de nuvem para dispositivos][lnk-devguide-messages] no sentido de que ambos habilitam o back-end de nuvem a passar informações para um dispositivo, mas há diferenças fundamentais entre os dois. Conceitualmente, métodos são síncronos e não duráveis, enquanto mensagens de nuvem para dispositivos são assíncronas e têm até 48 horas de durabilidade.

Os métodos seguem um padrão de solicitação-resposta e não são duráveis. A falta de durabilidade fornece dois benefícios imediatos quando você está comandando dispositivos:

- **Comentários imediatos sobre a execução do método** significa que não é necessário gerenciar a correlação entre a solicitação e resposta.
- **Maior taxa de transferência** significa que as operações podem ser executadas mais rapidamente porque o Hub IoT não fornece nenhuma durabilidade. O Hub IoT permite mais chamadas de método por unidade do que mensagens de nuvem para dispositivos.

As mensagens de nuvem para dispositivo não são necessariamente comandos para o dispositivo, mas representam um serviço de nuvem que passa algumas informações para o dispositivo para que receba conforme desejado, e as quais o dispositivo pode ou não responder. Mensagens de nuvem para dispositivos têm um tempo limite maior (de até 48 horas), enquanto os métodos expiram muito mais rapidamente.

Use métodos de dispositivo para invocação de comando imediata em um dispositivo e trabalhos para invocação agendada de um comando em um dispositivo.

## <a name="method-lifecycle"></a>Ciclo de vida do método

Métodos são implementados no dispositivo e podem precisar de zero ou mais entradas na carga do método para instanciar corretamente. Você invoca um método direto por meio de um URI voltado para serviços (`{iot hub}/twins/{device id}/methods/`). Um dispositivo recebe métodos diretos por meio de um tópico MQTT específico ao dispositivo (`$iothub/methods/POST/{method name}/`). Podemos dar suporte a métodos em protocolos de rede do lado do dispositivo no futuro.

> [AZURE.NOTE] Quando você invoca um método direto em um dispositivo, os valores e nomes de propriedade só podem conter caracteres alfanuméricos imprimíveis US-ASCII, exceto pelo seguinte conjunto: ``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}``.

Métodos são síncronos e obtêm êxito ou falham após o tempo limite (padrão: 30 segundos, configurável para até 3600 segundos). Métodos são úteis em cenários interativos em que você deseja que um dispositivo atue somente se o dispositivo estiver online e recebendo comandos, como acender uma luz usando um telefone. Nesses cenários, você deseja ver uma falha ou êxito imediatamente, para que o serviço de nuvem possa atuar quanto ao resultado o mais rápido possível. O dispositivo pode retornar algum corpo de mensagem como resultado do método, mas não é obrigatório que o método faça isso. Não há nenhuma garantia quanto à ordenação ou semântica de simultaneidade nas chamadas de método.

Chamadas de método de dispositivo são somente HTTP do lado da nuvem e somente MQTT do lado do dispositivo.

## <a name="reference"></a>Referência

### <a name="service-facing"></a>Voltada para o serviço

#### <a name="method-invocation"></a>Invocação de método

Invocações de método direto em um dispositivo são chamadas HTTP, que compreendem:

- O *URI* específico para o dispositivo (`{iot hub}/twins/{device id}/methods/`)
- A *método* POST
- *Cabeçalhos* que contêm a autorização, ID da solicitação, tipo de conteúdo e codificação de conteúdo
- Um *corpo* JSON transparente no seguinte formato:

```
{
    "methodName": "reboot",
    "timeoutInSeconds": 200,
    "payload": {
        "input1": "someInput",
        "input2": "anotherInput"
    }
}
```

  Tempo limite em segundos. Se o tempo limite não tiver sido definido, o padrão será 30 segundos.
  
#### <a name="response"></a>Resposta

O back-end recebe uma resposta que inclui:

- *Código de status HTTP*, que é usado para erros provenientes do Hub IoT, incluindo um erro 404 para dispositivos que não estão conectados
- *Cabeçalhos* que contêm a etag, ID da solicitação, tipo de conteúdo e codificação de conteúdo
- Um *corpo* JSON no seguinte formato:

```
{
    "status" : "OK",
    "payload" : {...}
}
```
  
   `status` e `body` são fornecidos pelo dispositivo e usados para responder com o código de status e/ou descrição do dispositivo.

### <a name="device-facing"></a>Voltado para o dispositivo

#### <a name="method-invocation"></a>Invocação de método

Os dispositivos recebem solicitações de método direto sobre o tópico MQTT: `$iothub/methods/POST/{method name}/?$rid={request id}`

O corpo que o dispositivo recebe está no seguinte formato:

```
{
    "input1": "someInput",
    "input2": "anotherInput"
}
```

As solicitações de método são QoS 0.

#### <a name="response"></a>Resposta

O dispositivo envia as respostas para `$iothub/methods/res/{status}/?$rid={request id}`, em que:

 - A propriedade `status` é o status de execução fornecido pelo dispositivo da execução do método.
 - A propriedade `$rid` é a ID de solicitação de invocação do método recebida do Hub IoT.

O corpo é definido pelo dispositivo e pode ter qualquer status.

### <a name="additional-reference-material"></a>Material de referência adicional

Outros tópicos de referência no Guia do desenvolvedor incluem:

- [Pontos de extremidade do Hub IoT][lnk-endpoints] descreve os vários pontos de extremidade que cada Hub IoT expõe para operações de tempo de execução e de gerenciamento.
- [Limitação e cotas][lnk-quotas] descreve as cotas que se aplicam ao serviço Hub IoT e o comportamento de limitação esperado ao usar o serviço.
- [SDKs de dispositivo e serviço do Hub IoT][lnk-sdks] lista os vários SDKs de linguagem que você pode usar no desenvolvimento de aplicativos de dispositivo e serviço que interagem com o Hub IoT.
- [Linguagem de consulta para gêmeos, métodos e trabalhos][lnk-query] descreve a linguagem de consulta que você pode usar para recuperar informações do Hub IoT sobre gêmeos de dispositivo, métodos e trabalhos.
- [Suporte ao MQTT do Hub IoT][lnk-devguide-mqtt] fornece mais informações sobre o suporte do Hub IoT para o protocolo MQTT.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a usar métodos diretos, você pode estar interessado no seguinte tópicos do Guia do desenvolvedor:

- [Agendar trabalhos em vários dispositivos][lnk-devguide-jobs]

Se você quiser experimentar alguns dos conceitos descritos neste artigo, talvez se interesse pelo seguinte tutorial de Hub IoT:

- [Usar métodos de nuvem para dispositivo][lnk-methods-tutorial]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-methods-tutorial]: iot-hub-c2d-methods.md
[lnk-devguide-messages]: iot-hub-devguide-messaging.md



<!--HONumber=Oct16_HO2-->


