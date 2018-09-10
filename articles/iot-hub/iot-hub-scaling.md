---
title: Dimensionamento do Hub IoT do Azure | Microsoft Docs
description: Como dimensionar o Hub IoT para dar suporte à taxa de transferência de mensagem antecipada e os recursos desejados. Inclui um resumo das taxas de transferência com suporte para cada camada e opções de fragmentação.
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/02/2018
ms.author: kgremban
ms.openlocfilehash: 6ae0217ed4b8833eb42a4719a1f2525461f9dcdd
ms.sourcegitcommit: a1140e6b839ad79e454186ee95b01376233a1d1f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2018
ms.locfileid: "43143641"
---
# <a name="choose-the-right-iot-hub-tier-for-your-solution"></a>Escolher a camada certa do Hub IoT para a solução

Cada solução IoT é diferente, portanto, o Hub IoT oferece várias opções com base em preço e escala. Este artigo destina-se a ajudá-lo a avaliar suas necessidades de Hub IoT. Para obter informações sobre preços das camadas do Hub IoT, consulte [Preços do Hub IoT](https://azure.microsoft.com/pricing/details/iot-hub). 

Para decidir qual camada do Hub IoT é ideal para a solução, faça duas perguntas:

**Quais recursos planejo usar?**
O Hub IoT oferece duas camadas, Básica e Standard, que diferem no número de recursos que podem dar suporte. Se a solução de IoT baseia-se na coleta de dados de dispositivos e na análise central, a camada básica provavelmente é a certa para você. Se você quiser usar configurações mais avançadas para controlar dispositivos IoT remotamente ou distribuir algumas de suas cargas de trabalho nos próprios dispositivos, considere a camada Standard. Para uma análise detalhada de quais recursos estão incluídos em cada camada, continue em [Camadas Básica e Standard](#basic-and-standard-tiers).

**Quantos dados planejo mover diariamente?**
Cada camada do Hub IoT está disponível em três tamanhos, com base na quantidade de taxa de transferência que pode ser manipulada em um determinado dia. Esses tamanhos são identificados numericamente como 1, 2 e 3. Por exemplo, cada unidade de um Hub IoT de nível 1 pode manipular 400 mil mensagens por dia, enquanto uma unidade de nível 3 pode lidar com 300 milhões. Para mais detalhes sobre as diretrizes de dados, continue com [Taxa de transferência da mensagem](#message-throughput).

## <a name="basic-and-standard-tiers"></a>Camadas Básica e Standard

A camada Standard do Hub IoT permite todos os recursos e é necessária para qualquer solução de IoT que pretende usar os recursos de comunicação bidirecional. A camada Básica permite um subconjunto dos recursos e destina-se a soluções de IoT que precisam apenas de comunicação unidirecional dos dispositivos para a nuvem. Ambas as camadas oferecem os mesmos recursos de segurança e autenticação.

Após criar o Hub IoT, será possível atualizar da camada Básica para a camada Standard sem interromper suas operações existentes. Para obter mais informações, consulte [Como fazer upgrade do Hub IoT](iot-hub-upgrade.md). Observe que o limite máximo de partição para o Hub IoT de camada básica é 8 e para a camada padrão é 32. A maioria dos Hubs IoT precisa apenas de 4 partições. O limite de partição é escolhido quando o Hub IoT é criado e se relaciona as mensagens de dispositivo para a nuvem com o número de leitores simultâneos dessas mensagens. Esse valor permanecerá inalterado quando você migrar da camada Básica para a camada Standard. Observe também que apenas um tipo de [edição](https://azure.microsoft.com/pricing/details/iot-hub/) dentro de uma camada pode ser escolhida pelo Hub IoT. Por exemplo, você pode criar um Hub IoT com várias unidades do S1, mas não com uma mistura de unidades de edições diferentes, como S1 e B3, ou S1 e S2.

| Recurso | Camada básica | Camada padrão |
| ---------- | ---------- | ------------- |
| [Telemetria de dispositivo para nuvem](iot-hub-devguide-messaging.md) | SIM | SIM |
| [Identidade por dispositivo](iot-hub-devguide-identity-registry.md) | SIM | SIM |
| [Roteamento de mensagens](iot-hub-devguide-messages-read-custom.md) e [Integração de Grade de Eventos](iot-hub-event-grid.md) | SIM | SIM |
| [Protocolos HTTP, AMQP e MQTT](iot-hub-devguide-protocols.md) | SIM | SIM |
| [Serviço de provisionamento de dispositivos](../iot-dps/about-iot-dps.md) | SIM | SIM |
| [Monitoramento e diagnósticos](iot-hub-monitor-resource-health.md) | SIM | SIM |
| [Mensagens de nuvem para dispositivo](iot-hub-devguide-c2d-guidance.md) |   | SIM |
| [Dispositivos gêmeos](iot-hub-devguide-device-twins.md), [Módulos gêmeos](iot-hub-devguide-module-twins.md) e [Gerenciamento de dispositivo](iot-hub-device-management-overview.md) |   | SIM |
| [Azure IoT Edge](../iot-edge/about-iot-edge.md) |   | SIM |

O Hub IoT também oferece uma camada gratuita destinado a testes e avaliação. Ela tem todos os recursos da camada Standard, mas provisões limitadas de mensagens. Não é possível fazer upgrade da camada gratuita para Básica ou Standard. 

### <a name="iot-hub-rest-apis"></a>APIs REST do Hub IoT

A diferença nos recursos com suporte entre as camadas Básica e Standard do Hub IoT significa que algumas chamadas de API não funcionam com hubs de camada Básica. A tabela a seguir mostra quais APIs estão disponíveis: 

| API | Camada básica | Camada padrão |
| --- | ---------- | ------------- |
| [Excluir dispositivo](https://docs.microsoft.com/rest/api/iothub/service/deletedevice) | SIM | SIM |
| [Obter dispositivo](https://docs.microsoft.com/rest/api/iothub/service/getdevice) | SIM | SIM |
| Excluir módulo | SIM | SIM |
| Obter módulo | SIM | SIM |
| [Obter estatísticas de registro](https://docs.microsoft.com/rest/api/iothub/service/getdeviceregistrystatistics) | SIM | SIM |
| [Obter estatísticas de serviços](https://docs.microsoft.com/rest/api/iothub/service/getservicestatistics) | SIM | SIM |
| [Criar Ou Atualizar Dispositivo](https://docs.microsoft.com/rest/api/iothub/service/createorupdatedevice) | SIM | SIM |
| Colocar módulo | SIM | SIM |
| [Consulta Hub IoT](https://docs.microsoft.com/rest/api/iothub/service/queryiothub) | SIM | SIM |
| Consultar módulos | SIM | SIM |
| [Criar URI de SAS de upload de arquivo](https://docs.microsoft.com/rest/api/iothub/device/createfileuploadsasuri) | SIM | SIM |
| [Receber notificação de limite de dispositivo](https://docs.microsoft.com/rest/api/iothub/device/receivedeviceboundnotification) | SIM | SIM |
| [Enviar evento de dispositivo](https://docs.microsoft.com/rest/api/iothub/device/senddeviceevent) | SIM | SIM |
| Enviar eventos de módulo | SIM | SIM |
| [Atualizar o status de upload de arquivo](https://docs.microsoft.com/rest/api/iothub/device/updatefileuploadstatus) | SIM | SIM |
| [Operação de dispositivo em massa](https://docs.microsoft.com/rest/api/iot-dps/deviceenrollment/bulkoperation) | Sim, exceto pelos recursos do Azure IoT Edge | SIM | 
| [Limpar fila de comandos](https://docs.microsoft.com/rest/api/iothub/service/purgecommandqueue) |   | SIM |
| [Obter dispositivo gêmeo](https://docs.microsoft.com/rest/api/iothub/service/gettwin) |   | SIM |
| Obter módulo gêmeo |   | SIM |
| [Invocar um método de dispositivo](https://docs.microsoft.com/rest/api/iothub/service/invokedevicemethod) |   | SIM |
| [Atualizar dispositivo gêmeo](https://docs.microsoft.com/rest/api/iothub/service/updatetwin) |   | SIM | 
| Atualizar módulo gêmeo |   | SIM | 
| [Abandonar notificação de limite de dispositivo](https://docs.microsoft.com/rest/api/iothub/device/abandondeviceboundnotification) |   | SIM |
| [Completar notificação de limite de dispositivo](https://docs.microsoft.com/rest/api/iothub/device/completedeviceboundnotification) |   | SIM |
| [Cancelar trabalho](https://docs.microsoft.com/rest/api/iothub/service/canceljob) |   | SIM |
| [Criar trabalho](https://docs.microsoft.com/rest/api/iothub/service/createjob) |   | SIM |
| [Obter Trabalho](https://docs.microsoft.com/rest/api/iothub/service/getjob) |   | SIM |
| [Trabalhos de consulta](https://docs.microsoft.com/rest/api/iothub/service/queryjobs) |   | SIM |

## <a name="message-throughput"></a>Taxa de transferência da mensagem

A melhor maneira de dimensionar uma solução do Hub IoT é avaliar o tráfego de acordo com a unidade. Em particular, considere a taxa de transferência de pico necessária para as seguintes categorias de operações:

* Mensagens do dispositivo para a nuvem
* Mensagens da nuvem para o dispositivo
* Operações de registro de identidade

O tráfego é medido por unidade, não por hub. Uma instância do Hub IoT de nível 1 ou 2 pode ter até 200 unidades associadas a ela. Uma instância do Hub IoT de nível 3 pode ter até 10 unidades. Após criar o Hub IoT, será possível alterar o número de unidades ou mover entre os tamanhos 1, 2 e 3 em uma camada específica sem interromper as operações existentes. Para obter mais informações, consulte [Como fazer upgrade do Hub IoT](iot-hub-upgrade.md).

Como um exemplo dos recursos de tráfego de cada camada, as mensagens de dispositivo para nuvem seguem estas diretrizes de taxa de transferência sustentada:

| Camada | Taxa de transferência sustentada | Taxa de envio sustentada |
| --- | --- | --- |
| B1, S1 |Até 1111 KB/minuto por unidade<br/>(1,5 GB/dia/unidade) |Média de 278 mensagens/minuto por unidade<br/>(400.000 mensagens/dia por unidade) |
| B2, S2 |Até 16 MB/minuto por unidade<br/>(22,8 GB/dia/unidade) |Média de 4.167 mensagens/minuto por unidade<br/>(6 milhões de mensagens/dia por unidade) |
| B3, S3 |Até 814 MB/minuto por unidade<br/>(1144,4 GB/dia/unidade) |Média de 208,333 mensagens/minuto por unidade<br/>(300 milhões de mensagens/dia por unidade) |

Além dessas informações sobre produtividade, confira as [Cotas e limites do Hub IoT][IoT Hub quotas and throttles] e crie sua solução segundo com tais informações.

### <a name="identity-registry-operation-throughput"></a>Taxa de transferência de operações de registro de identidade
As operações de Registro de identidade do Hub IoT não devem ser operações em tempo de execução, pois estão relacionadas principalmente com provisionamento do dispositivo.

Para ver número de pico de desempenho específicos, consulte [Cotas e limites do Hub IoT][IoT Hub quotas and throttles].

## <a name="auto-scale"></a>Dimensionamento automático
Se você está se aproximando o limite permitido de mensagens no Hub IoT, você pode usar essas [etapas para dimensionar automaticamente](https://azure.microsoft.com/resources/samples/iot-hub-dotnet-autoscale/) para incrementar uma unidade de Hub IoT na mesma camada do Hub IoT.

## <a name="sharding"></a>Fragmentação
Enquanto um único Hub IoT pode ser dimensionado para milhões de dispositivos, às vezes sua solução exigirá características específicas de desempenho que um único Hub IoT não pode garantir. Nesse caso, é possível particionar os dispositivos em vários Hubs IoT. Vários hubs IoT suavizam picos de tráfego e obtêm a taxa de transferência ou as taxas de operação necessárias.

## <a name="next-steps"></a>Próximas etapas

* Para obter informações adicionais sobre recursos e detalhes de desempenho do Hub IoT, consulte [Preços do Hub IoT][link-pricing] ou [Cotas e limitações do Hub IoT][IoT Hub quotas and throttles].
* Para alterar a camada do Hub IoT, siga as etapas em [Fazer upgrade do Hub IoT](iot-hub-upgrade.md).

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[IoT Hub quotas and throttles]: iot-hub-devguide-quotas-throttling.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
