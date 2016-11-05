---
title: Dimensionamento no Hub IoT do Azure | Microsoft Docs
description: Descreve como dimensionar o Hub IoT do Azure.
services: iot-hub
documentationcenter: ''
author: fsautomata
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2016
ms.author: elioda

---
# Dimensionamento do Hub IoT
O Hub do IoT do Azure pode oferecer suporte a até um milhão de dispositivos conectados ao mesmo tempo. Para saber mais, confira [Preço do Hub IoT][lnk-pricing]. Cada unidade do Hub IoT permite uma determinada quantidade de mensagens diárias.

Para dimensionar corretamente sua solução, considere sua utilização específica do Hub IoT. Em particular, considere a taxa de transferência de pico necessária para as seguintes categorias de operações:

* Mensagens do dispositivo para a nuvem
* Mensagens da nuvem para o dispositivo
* Operações de registro de identidade

Além dessas informações sobre produtividade, confira as [Cotas e limites do Hub IoT][Cotas e limites do Hub IoT] e crie sua solução adequadamente.

## Taxa de transferência de mensagens do dispositivo para a nuvem e da nuvem para o dispositivo
A melhor maneira de dimensionar uma solução do Hub IoT é avaliar o tráfego de acordo com a unidade.

As mensagens do dispositivo para a nuvem seguem estas diretrizes de taxa de transferência sustentada.

| Camada | Taxa de transferência sustentada | Taxa de envio sustentada |
| --- | --- | --- |
| S1 |Até 1111 KB/minuto por unidade<br/>(1,5 GB/dia/unidade) |Média de 278 mensagens/minuto por unidade<br/>(400.000 mensagens/dia por unidade) |
| S2 |Até 16 MB/minuto por unidade<br/>(22,8 GB/dia/unidade) |Média de 4167 mensagens/minuto por unidade<br/>(6 milhões de mensagens/dia por unidade) |
| S3 |Até 814 MB/minuto por unidade<br/>(1144,4 GB/dia/unidade) |Média de 208,333 mensagens/minuto por unidade<br/>(300 milhões de mensagens/dia por unidade) |

## Taxa de transferência de operações de registro de identidade
As operações de registro de identidade do Hub IoT não devem ser operações em tempo de execução, pois estão relacionadas principalmente com provisionamento do dispositivo.

Consulte [Cotas e limites do Hub IoT][Cotas e limites do Hub IoT] para obter números específicos de desempenho de intermitência.

## Fragmentação
Enquanto um único Hub IoT pode ser dimensionado para milhões de dispositivos, às vezes sua solução exigirá características específicas de desempenho que um único Hub IoT não pode garantir. Nesse caso, é recomendável que você particione seus dispositivos em vários Hubs IoT. Vários hubs IoT suavizam picos de tráfego e obtêm a taxa de transferência ou as taxas de operação necessárias.

## Próximas etapas
Para explorar melhor as funcionalidades do Hub IoT, consulte:

* [Guia do desenvolvedor][lnk-devguide]
* [Explorar o gerenciamento de dispositivo usando a interface do usuário de exemplo][lnk-dmui]
* [Simular um dispositivo com o SDK do Gateway][lnk-gateway]
* [Gerenciar Hubs IoT por meio do portal do Azure][lnk-portal]

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[Cotas e limites do Hub IoT]: iot-hub-devguide.md#throttling

[lnk-design]: iot-hub-guidance.md
[lnk-mqtt]: iot-hub-mqtt-support.md
[lnk-devices]: iot-hub-tested-configurations.md
[lnk-protocols]: iot-hub-protocol-gateway.md
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0921_2016-->