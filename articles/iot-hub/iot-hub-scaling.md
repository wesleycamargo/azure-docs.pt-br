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
 ms.date="10/02/2015"
 ms.author="elioda"/>

# Dimensionamento do Hub IoT

O Hub IoT pode dar suporte a até um milhão de dispositivos conectados simultaneamente, aumentando para 2,000 o número de unidades S1 ou S2 do Hub IoT. Para saber mais, consulte [Preços do Hub IoT][lnk-pricing].

Cada unidade do Hub IoT permite um certo número de identidades de dispositivo no registro, que podem estar simultaneamente conectadas, e um número de mensagens diárias.

Para dimensionar corretamente sua solução, considere sua utilização específica do Hub IoT. Em particular, considere a taxa de transferência de pico necessária para as seguintes categorias de operações:

* Mensagens do dispositivo para a nuvem
* Mensagens da nuvem para o dispositivo
* Operações de registro de identidade

Além dessas informações sobre produtividade, lembre-se de consultar as [Cotas e limites do Hub IoT][] e crie sua solução adequadamente.

## Taxa de transferência de mensagens do dispositivo para a nuvem, e da nuvem para o dispositivo

A melhor maneira de dimensionar uma solução do Hub IoT é avaliar o tráfego de acordo com o dispositivo.

As mensagens do dispositivo para a nuvem seguem estas diretrizes de taxa de transferência sustentada.

| Camada | Taxa de transferência sustentada | Taxa de envio sustentada |
| ---- | -------------------- | ------------------- |
| S1 | até 8 kb/hora por dispositivo | média de quatro mensagens por hora por dispositivo |
| S2 | até 4 kb/minuto por dispositivo | média de duas mensagens por minuto por dispositivo |

Ao receber mensagens do dispositivo para a nuvem, o back-end de aplicativo pode esperar taxa de transferência máxima a seguir (entre todos os leitores).

| Camada | Taxa de transferência sustentada |
| ---- | -------------------- |
| S1 | até 120 Kb/minuto por unidade com 2 Mb/s, no mínimo. |
| S2 | até 4 Mb/minuto por unidade com 2 Mb/s, no mínimo. |

O desempenho de mensagens da nuvem para o dispositivo é dimensionado por dispositivo, e cada dispositivo recebe até cinco mensagens por minuto.

## Taxa de transferência de operações de registro de identidade

As operações de registro de identidade do Hub IoT não devem ser operações em tempo de execução, pois estão relacionadas principalmente com provisionamento do dispositivo.

Consulte [Cotas e limites do Hub IoT][] para obter números específicos de desempenho de intermitência.

## Fragmentação

Enquanto um único Hub IoT pode ser dimensionado para milhões de dispositivos, às vezes sua solução exigirá características específicas de desempenho que um único Hub IoT não pode garantir. Nesse caso, recomendamos o particionamento de seus dispositivos em vários Hubs IoT, a fim de suavizar os picos de tráfego e obter as taxas de produtividade ou de operação necessárias.

## Próximas etapas

Para saber mais sobre o Hub IoT do Azure, siga estes links:

- [Introdução aos Hubs IoT (Tutorial)][lnk-get-started]
- [O que é o Hub IoT do Azure?][]

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[Cotas e limites do Hub IoT]: iot-hub-devguide.md#throttling

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[O que é o Hub IoT do Azure?]: iot-hub-what-is-iot-hub.md

<!---HONumber=Oct15_HO3-->