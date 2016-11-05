---
title: Guia do desenvolvedor - Noções básicas dos dispositivos gêmeos | Microsoft Docs
description: Guia de desenvolvedor do Hub IoT do Azure - Use dispositivos gêmeos para sincronizar os dados de estado e de configuração entre os dispositivos e o Hub IoT
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2016
ms.author: elioda

---
# <a name="understand-device-twins-preview"></a>Noções básicas dos dispositivos gêmeos - visualização
## <a name="overview"></a>Visão geral
*Dispositivos gêmeos* são documentos JSON que armazenam informações do estado do dispositivo (metadados, configurações e condições). O Hub IoT persiste um dispositivo gêmeo para cada dispositivo que você conecta ao Hub IoT. Este artigo descreverá:

* a estrutura do dispositivo gêmeo: *marcas*, *propriedades desejadas* e *relatadas* e
* as operações que os aplicativos e back-ends de dispositivo podem executar em dispositivos gêmeos.

> [!NOTE]
> Neste momento, os dispositivos gêmeos podem ser acessados somente de dispositivos que se conectam ao Hub IoT usando o protocolo MQTT. Confira o artigo do [suporte a MQTT][lnk-devguide-mqtt] para obter instruções sobre como converter o aplicativo de dispositivo existente para usar MQTT.
> 
> 

### <a name="when-to-use"></a>Quando usar
Use os dispositivos gêmeos para:

* Armazenar metadados específicos do dispositivo na nuvem, por exemplo, o local da implantação de uma máquina de vendas.
* Relatar informações de estado atual, como os recursos disponíveis e as condições de seu aplicativo de dispositivo, por exemplo, um dispositivo que se conecta por meio de celular ou wi-fi.
* Sincronizar o estado dos fluxos de trabalho de longa duração entre o back-end e o aplicativo do dispositivo, por exemplo, o back-end especificando a nova versão do firmware para instalação, e o aplicativo do dispositivo reportando os diversos estágios do processo de atualização.
* Consultar os metadados, a configuração ou o estado do seu dispositivo.

Use [mensagens do dispositivo para a nuvem][lnk-d2c] para sequências de eventos com carimbo de data e hora, como a série temporal dos dados ou alarmes do sensor. Use [métodos da nuvem para o dispositivo][lnk-methods] para controle interativo de dispositivos, como ativar um ventilador.

## <a name="device-twins"></a>Dispositivos gêmeos
Dispositivos gêmeos armazenam informações relacionadas ao dispositivo que:

* O dispositivo e os back-ends podem usar para sincronizar a configuração e as condições do dispositivo.
* O back-end do aplicativo pode usar para consultar e direcionar operações demoradas.

O ciclo de vida de um dispositivo gêmeo está vinculado à [identidade do dispositivo][lnk-identity] correspondente. Gêmeos são criados e excluídos implicitamente quando uma nova identidade de dispositivo é criada ou excluída no Hub IoT.

Um dispositivo gêmeo é um documento JSON que inclui:

* **Marcas**. Um documento JSON lido e gravado pelo back-end. As marcas não ficam visíveis para os aplicativos do dispositivo.
* **Propriedades desejadas**. Usadas em conjunto com as propriedades reportadas para sincronizar a configuração ou condição do dispositivo. As propriedades desejadas só podem ser definidas pelo back-end do aplicativo e podem ser lidas pelo aplicativo do dispositivo. O aplicativo do dispositivo também pode ser notificado em tempo real sobre as alterações nas propriedades desejadas.
* **Propriedades reportadas**. Usadas em conjunto com as propriedades desejadas para sincronizar a configuração ou condição do dispositivo. As propriedades reportadas só podem ser definidas pelo aplicativo do dispositivo e podem ser lidas e consultadas pelo back-end do aplicativo.

Além disso, a raiz do dispositivo gêmeo contém as propriedades somente leitura da identidade correspondente, contidas no [registro de identidade do dispositivo][lnk-identity].

![][img-twin]

Veja um exemplo de um documento JSON de dispositivo gêmeo:

        {
            "deviceId": "devA",
            "generationId": "123",
            "status": "enabled",
            "statusReason": "provisioned",
            "connectionState": "connected",
            "connectionStateUpdatedTime": "2015-02-28T16:24:48.789Z",
            "lastActivityTime": "2015-02-30T16:24:48.789Z",

            "tags": {
                "$etag": "123",
                "deploymentLocation": {
                    "building": "43",
                    "floor": "1"
                }
            },
            "properties": {
                "desired": {
                    "telemetryConfig": {
                        "sendFrequency": "5m"
                    },
                    "$metadata" : {...},
                    "$version": 1
                },
                "reported": {
                    "telemetryConfig": {
                        "sendFrequency": "5m",
                        "status": "success"
                    }
                    "batteryLevel": 55,
                    "$metadata" : {...},
                    "$version": 4
                }
            }
        }

No objeto raiz estão as propriedades do sistema e os objetos de contêiner para `tags`, além de `reported` e `desired properties`. O contêiner `properties` contém alguns elementos somente leitura (`$metadata`, `$etag` e `$version`) que são descritos respectivamente nas seções [Metadados gêmeos][lnk-twin-metadata] e [Simultaneidade otimista][lnk-concurrency].

### <a name="reported-property-example"></a>Exemplo da propriedade reportada
No exemplo acima, o dispositivo gêmeo contém uma propriedade `batteryLevel` que é reportada pelo aplicativo do dispositivo. Essa propriedade torna possível a consultar e operação em dispositivos com base no último nível da bateria reportado. Outro exemplo teria os recursos de dispositivo de relatório do aplicativo do dispositivo ou as opções de conectividade.

Observe como as propriedades reportadas simplificam cenários nos quais o back-end está interessado no último valor conhecido de uma propriedade. Use [mensagens do dispositivo para a nuvem][lnk-d2c] se o back-end precisar processar telemetria do dispositivo na forma de sequências de eventos com carimbo de data e hora, como uma série temporal.

### <a name="desired-configuration-example"></a>Exemplo de configuração desejada
No exemplo acima, a `telemetryConfig` desejada e as propriedades reportadas são usadas pelo aplicativo do dispositivo e pelo back-end para sincronizar a configuração de telemetria para este dispositivo. Por exemplo:

1. O back-end do aplicativo define a propriedade desejada com o valor de configuração desejado. Aqui está a parte do documento com a propriedade desejada:
   
        ...
        "desired": {
            "telemetryConfig": {
                "sendFrequency": "5m"
            },
            ...
        },
        ...
2. O aplicativo do dispositivo é notificado sobre a alteração imediatamente, se estiver conectado, ou na primeira reconexão. Em seguida, o aplicativo do dispositivo reporta a configuração atualizada (ou uma condição de erro usando a propriedade `status`). Esta é a parte das propriedades reportadas:
   
        ...
        "reported": {
            "telemetryConfig": {
                "sendFrequency": "5m",
                "status": "success"
            }
            ...
        }
        ...
3. O back-end do aplicativo pode manter o controle dos resultados da operação de configuração em vários dispositivos [consultando][lnk-query] gêmeos.

> [!NOTE]
> Os trechos acima são exemplos, otimizados para facilitar a leitura, de uma forma possível de codificar uma configuração de dispositivo e seu status. O Hub IoT não impõem um esquema específico para as propriedades desejadas e reportadas nos dispositivos gêmeos.
> 
> 

Em muitos casos, os gêmeos são usados para sincronizar operações de longa duração, como atualizações de firmware. Confira [Usar propriedades desejadas para configurar dispositivos][lnk-twin-properties] para saber mais sobre como usar propriedades para sincronizar e acompanhar operações de longa execução nos dispositivos.

## <a name="back-end-operations"></a>Operações de back-end
O back-end funciona no gêmeo usando as seguintes operações atômicas, expostas por meio de HTTP:

1. **Recuperar gêmeo por ID**. Essa operação retorna o conteúdo do documento do gêmeo, incluindo marcas e propriedades desejadas, reportadas e do sistema.
2. **Atualizar parcialmente o gêmeo**. Essa operação permite que o back-end atualize parcialmente as marcas ou propriedades desejadas do gêmeo. A atualização parcial é expressa como um documento JSON que adiciona ou atualiza qualquer propriedade mencionada. As propriedades definidas como `null` foram removidas. Por exemplo, o seguinte cria uma nova propriedade desejada com o valor `{"newProperty": "newValue"}`, substitui o valor existente de `existingProperty` por `"otherNewValue"`, e remove `otherOldProperty` completamente. Nenhuma alteração ocorre em outras propriedades desejadas ou marcas existentes:
   
        {
            "properties": {
                "desired": {
                    "newProperty": {
                        "nestedProperty": "newValue"
                    },
                    "existingProperty": "otherNewValue",
                    "otherOldProperty": null
                }
            }
        }
3. **Substituir propriedades desejadas**. Esta operação permite que o back-end substitua completamente todas as suas propriedades desejadas e substitui um novo documento JSON para `properties/desired`.
4. **Substituir marcas**. De forma análoga à substituição das propriedades desejadas, essa operação permite que o back-end substitua completamente todas as marcas existentes e substitua um novo documento JSON para `tags`.

Todas as operações acima oferecem suporte à [Simultaneidade otimista][lnk-concurrency] e exigem a permissão **ServiceConnect**, conforme definido no artigo [Segurança][lnk-security].

Além dessas operações, o back-end pode consultar os gêmeos usando uma [linguagem de consulta][lnk-query] tipo SQL, e executar operações em conjuntos grandes de gêmeos usando [trabalhos][lnk-jobs].

## <a name="device-operations"></a>Operações de dispositivo
O aplicativo do dispositivo opera no gêmeo usando as seguintes operações atômicas:

1. **Recuperar gêmeo**. Essa operação retorna o conteúdo do documento do gêmeo (incluindo marcas e propriedades desejadas, reportadas e do sistema) para o dispositivo conectado no momento.
2. **Atualizar parcialmente as propriedades reportadas**. Essa operação permite a atualização parcial das propriedades reportadas do dispositivo conectado no momento. Ela usa o mesmo formato de atualização JSON que o back-end ao passar pela atualização parcial das propriedades desejadas.
3. **Observar as propriedades desejadas**. O dispositivo conectado no momento pode optar por ser notificado sobre atualizações para as propriedades desejadas, assim que elas ocorrem. O dispositivo recebe a mesma forma de atualização (substituição total ou parcial) executada pelo back-end.

Todas as operações acima exigem a permissão **DeviceConnect**, conforme definido no artigo [Segurança][lnk-security].

Os [SDKs do dispositivo IoT do Azure][lnk-sdks] facilitam o uso das operações acima a partir de várias linguagens e plataformas. Encontre mais informações sobre os detalhes dos primitivos de Hub IoT para sincronização de propriedades desejadas em [Fluxo de reconexão do dispositivo][lnk-reconnection].

> [!NOTE]
> No momento, os dispositivos gêmeos podem ser acessados somente de dispositivos que se conectam ao Hub IoT usando o protocolo MQTT.
> 
> 

## <a name="reference"></a>Referência
### <a name="tags-and-properties-format"></a>Formato de marcas e propriedades
Marcas, propriedades desejadas e reportadas são objetos JSON com as seguintes restrições:

* Todas as chaves em objetos JSON são cadeias UNICODE de 128 caracteres que diferencia maiúsculas de minúsculas. Os caracteres permitidos excluir caracteres de controle UNICODE (segmentos C0 e C1) e `'.'`, `' '` e `'$'`.
* Todos os valores no objeto JSON podem ser dos tipos de JSON a seguir: booliano, número, cadeia de caracteres, objeto. Não há permissão para matrizes.

### <a name="twin-size"></a>Tamanho do gêmeo
O Hub IoT impõe um limite de tamanho de 8 KB nos valores de `tags`, `properties/desired` e `properties/reported`, excluindo elementos somente leitura.
O tamanho é calculado pela contagem de todos os caracteres, exceto caracteres de controle UNICODE (segmentos C0 e C1) e espaço `' '`, quando aparece fora de uma constante de cadeia de caracteres.
O Hub IoT rejeitará com erro todas as operações que podem aumentar o tamanho desses documentos acima do limite.

### <a name="twin-metadata"></a>Metadados do gêmeo
O Hub IoT mantém o carimbo de data e hora da última atualização de cada objeto JSON nas propriedades desejadas e reportadas. Os carimbos de data e hora estão em UTC e são codificados no formato [ISO8601]`YYYY-MM-DDTHH:MM:SS.mmmZ`.
Por exemplo:

        {
            ...
            "properties": {
                "desired": {
                    "telemetryConfig": {
                        "sendFrequency": "5m"
                    },
                    "$metadata": {
                        "telemetryConfig": {
                            "sendFrequency": {
                                "$lastUpdated": "2016-03-30T16:24:48.789Z"
                            },
                            "$lastUpdated": "2016-03-30T16:24:48.789Z"
                        },
                        "$lastUpdated": "2016-03-30T16:24:48.789Z"
                    },
                    "$version": 23
                },
                "reported": {
                    "telemetryConfig": {
                        "sendFrequency": "5m",
                        "status": "success"
                    }
                    "batteryLevel": "55%",
                    "$metadata": {
                        "telemetryConfig": {
                            "sendFrequency": "5m",
                            "status": {
                                "$lastUpdated": "2016-03-31T16:35:48.789Z"
                            },
                            "$lastUpdated": "2016-03-31T16:35:48.789Z"
                        }
                        "batteryLevel": {
                            "$lastUpdated": "2016-04-01T16:35:48.789Z"
                        },
                        "$lastUpdated": "2016-04-01T16:24:48.789Z"
                    },
                    "$version": 123
                }
            }
            ...
        }

Essas informações são armazenadas em cada nível (não apenas nas folhas da estrutura JSON) a fim de preservar as atualizações que removem chaves de objeto.

### <a name="optimistic-concurrency"></a>Simultaneidade otimista
Marcas, propriedades desejadas e reportadas oferecem suporte à simultaneidade otimista.
Marcas tem uma etag, de acordo com [RFC7232], que representa a representação JSON da marca. Você pode usar isso em operações de atualização condicionais do back-end para garantir a consistência.

As propriedades desejadas e reportadas não tem as etags, mas tem um valor `$version` com garantia de ser incremental. De forma análoga e uma etag, a versão pode ser usada pela parte como que está atualizando (como um aplicativo de dispositivo para uma propriedade reportada ou o back-end para uma propriedade desejada) para impor a consistência das atualizações.

Versões também são úteis quando um agente observador (por exemplo, o aplicativo do dispositivo que observa as propriedades desejadas) precisa reconciliar corridas entre o resultado de uma operação de recuperação e uma notificação de atualização. A seção [Fluxo de reconexão do dispositivo][lnk-reconnection] fornece mais informações.

### <a name="device-reconnection-flow"></a>Fluxo de reconexão do dispositivo
O Hub IoT não preserva notificações de atualização de propriedades desejadas para dispositivos desconectados. Acontece que um dispositivo que está se conectando deve recuperar o documento completo de propriedades desejadas, além de se inscrever para receber notificações de atualização. Considerando a possibilidade de corridas entre notificações de atualização e recuperação completa, o seguinte fluxo deve ser garantido:

1. O aplicativo de dispositivo conecta-se a um Hub IoT.
2. O aplicativo de dispositivo se inscreve para as notificações de atualização de propriedades desejadas .
3. O aplicativo de dispositivo recupera o documento completo para as propriedades desejadas.

O aplicativo de dispositivo pode ignorar todas as notificações com `$version` menor ou igual à versão do documento recuperado completo. Isso é possível porque o Hub IoT garante que as versões sempre aumentam.

> [!NOTE]
> Essa lógica já está implementada nos [SDKs do dispositivo IoT do Azure][lnk-sdks]. Essa descrição será útil somente se o aplicativo de dispositivo não puder usar qualquer um dos SDKs do dispositivo IoT do Azure, e deve programar a interface MQTT diretamente.
> 
> 

### <a name="additional-reference-material"></a>Material de referência adicional
Outros tópicos de referência no Guia do desenvolvedor incluem:

* [onto de extremidade do Hub IoT][lnk-endpoints]descreve os vários pontos de extremidade que cada Hub IoT expõe para operações de tempo de execução e de gerenciamento.
* [Limitação e cotas][lnk-quotas] descreve as cotas que se aplicam ao serviço Hub IoT e o comportamento de limitação esperado ao usar o serviço.
* [SDKs do dispositivo e serviço do Hub IoT][lnk-sdks] lista os vários SDKs de linguagem que você pode usar no desenvolvimento de aplicativos de dispositivo e serviço que interagem com o Hub IoT.
* [Linguagem de consulta para trabalhos, métodos e gêmeos][lnk-query] descreve a linguagem de consulta que você pode usar para recuperar informações do Hub IoT sobre gêmeos de dispositivo, métodos e trabalhos.
* [Suporte a MQTT do Hub IoT][lnk-devguide-mqtt] fornece mais informações sobre o suporte a Hub IoT para o protocolo MQTT.

## <a name="next-steps"></a>Próximas etapas
Agora que você aprendeu sobre dispositivos gêmeos, talvez você se interesse pelos seguintes tópicos do Guia do desenvolvedor:

* [Invocar um método direto em um dispositivo][lnk-methods]
* [Agendar trabalhos em vários dispositivos][lnk-jobs]

Se você quiser experimentar alguns dos conceitos descritos neste artigo, talvez se interesse pelos seguintes tutoriais de Hub IoT:

* [Como usar o dispositivo gêmeo][lnk-twin-tutorial]
* [Como usar propriedades gêmeas][lnk-twin-properties]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-identity]: iot-hub-devguide-identity-registry.md
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-security]: iot-hub-devguide-security.md

[ISO8601]: https://en.wikipedia.org/wiki/ISO_8601
[RFC7232]: https://tools.ietf.org/html/rfc7232
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-twin-tutorial]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-properties]: iot-hub-node-node-twin-how-to-configure.md
[lnk-twin-metadata]: iot-hub-devguide-device-twins.md#twin-metadata
[lnk-concurrency]: iot-hub-devguide-device-twins.md#optimistic-concurrency
[lnk-reconnection]: iot-hub-devguide-device-twins.md#device-reconnection-flow

[img-twin]: media/iot-hub-devguide-device-twins/twin.png


<!--HONumber=Oct16_HO2-->


