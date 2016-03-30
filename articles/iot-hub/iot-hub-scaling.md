<properties
 pageTitle="Dimensionamento no Hub IoT do Azure | Microsoft Azure"
 description="Descreve como dimensionar o Hub IoT do Azure."
 services="iot-hub"
 documentationCenter=""
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="03/14/2016"
 ms.author="elioda"/>

# Dimensionamento do Hub IoT

O Hub do IoT do Azure pode oferecer suporte a até um milhão de dispositivos conectados ao mesmo tempo. Para saber mais, confira [Preço do Hub IoT][lnk-pricing]. Cada unidade do Hub IoT permite uma quantidade de mensagens diárias.

Para dimensionar corretamente sua solução, considere sua utilização específica do Hub IoT. Em particular, considere a taxa de transferência de pico necessária para as seguintes categorias de operações:

* Mensagens do dispositivo para a nuvem
* Mensagens da nuvem para o dispositivo
* Operações de registro de identidade

Além dessas informações sobre produtividade, confira as [Cotas e limites do Hub IoT][] e crie sua solução adequadamente.

## Taxa de transferência de mensagens do dispositivo para a nuvem e da nuvem para o dispositivo

A melhor maneira de dimensionar uma solução do Hub IoT é avaliar o tráfego de acordo com a unidade.

As mensagens do dispositivo para a nuvem seguem estas diretrizes de taxa de transferência sustentada.

| Camada | Taxa de transferência sustentada | Taxa de envio sustentada |
| ---- | -------------------- | ------------------- |
| S1 | Até 1111 KB/minuto por unidade<br/>(1,5 GB/dia/unidade) | Média de 278 mensagens/minuto por unidade<br/>(400.000 mensagens/dia por unidade) |
| S2 | Até 16 MB/minuto por unidade<br/>(22,8 GB/dia/unidade) | Média de 4167 mensagens/minuto por unidade<br/>(6 milhões de mensagens/dia por unidade) |

O desempenho de mensagens da nuvem para o dispositivo é dimensionado por dispositivo, e cada dispositivo recebe até cinco mensagens por minuto.

## Taxa de transferência de operações de registro de identidade

As operações de registro de identidade do Hub IoT não devem ser operações em tempo de execução, pois estão relacionadas principalmente com provisionamento do dispositivo.

Consulte [Cotas e limites do Hub IoT][] para obter números específicos de desempenho de intermitência.

## Fragmentação

Enquanto um único Hub IoT pode ser dimensionado para milhões de dispositivos, às vezes sua solução exigirá características específicas de desempenho que um único Hub IoT não pode garantir. Nesse caso, é recomendável que você particione seus dispositivos em vários Hubs IoT. Vários hubs IoT suavizam picos de tráfego e obtêm a taxa de transferência ou as taxas de operação necessárias.

## Próximas etapas

Para saber mais sobre o Hub IoT do Azure, siga estes links:

- [Introdução ao Hub IoT (Tutorial)][lnk-get-started]
- [O que é o Hub IoT do Azure?][]

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[Cotas e limites do Hub IoT]: iot-hub-devguide.md#throttling

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[O que é o Hub IoT do Azure?]: iot-hub-what-is-iot-hub.md

<!---HONumber=AcomDC_0316_2016-->