---
title: Entenda como um dispositivo Azure IoT Edge pode ser usado como um gateway para outros dispositivos | Microsoft Docs
description: "Use o Azure IoT Edge para criar um dispositivo de gateway transparente, opaco ou de proxy que envia dados de vários dispositivos downstream para a nuvem ou os processa localmente."
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 11/27/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 3f2f9258b97d4886f41a2b991ff4de7e16379245
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2018
---
# <a name="how-an-iot-edge-device-can-be-used-as-a-gateway---preview"></a>Como um dispositivo IoT Edge pode ser usado como um gateway – versão prévia

A finalidade dos gateways em soluções de IoT é específica para a solução e combinação de conectividade do dispositivo com a análise de borda. O Azure IoT Edge pode ser usado para atender a todas as necessidades de um gateway de IoT, independentemente de se elas estão relacionadas à conectividade, identidade ou análise de borda. Padrões de gateway, neste artigo, referem-se apenas às características de conectividade de dispositivo downstream e identidade de dispositivo, não como dados de dispositivo são processados no gateway.

## <a name="patterns"></a>Padrões
Há três padrões de uso de um dispositivo IoT Edge como um gateway: transparente, conversão de protocolo e tradução de identidade:
* **Transparente** – dispositivos que teoricamente podem se conectar ao Hub IoT podem, em vez disso, conectar-se a um dispositivo de gateway. Isso implica que os dispositivos downstream têm suas próprias identidades de Hub IoT e estão usando qualquer um dos protocolos MQTT, AMQP ou HTTP. O gateway simplesmente passa as comunicações entre os dispositivos e o Hub IoT. Os dispositivos não sabem que estão se comunicando com a nuvem por meio de um gateway, e um usuário interagindo com os dispositivos no Hub IoT não sabe que há um dispositivo de gateway intermediário. Assim, o gateway é transparente. Consulte o tutorial sobre como [Criar um gateway transparente][lnk-iot-edge-as-transparent-gateway] para obter informações específicas sobre como usar um dispositivo IoT Edge como um gateway transparente.
* **Conversão de protocolo** – os dispositivos que não dão suporte a MQTT, AMQP ou HTTP usam um dispositivo de gateway para enviar dados para o Hub IoT. O gateway é inteligente o suficiente para entender o protocolo usado pelos dispositivos downstream, porém, é o único dispositivo que tem identidade no Hub IoT. Todas as informações parecem estar vindo de um dispositivo, o gateway. Isso implica que dispositivos downstream devem inserir informações de identificação adicionais em suas mensagens se aplicativos de nuvem quiserem entender os dados em uma base por dispositivo. Além disso, Hub IoT primitivos como gêmeos e métodos estão disponíveis apenas para o dispositivo de gateway, não para dispositivos downstream.
* **Conversão de identidade** – dispositivos que não conseguem se conectar ao Hub IoT conectam-se a um dispositivo de gateway que fornece identidade e conversão de protocolo ao Hub IoT em nome dos dispositivos downstream. O gateway é inteligente o suficiente para entender o protocolo usado por dispositivos downstream, fornecer identidade a eles e converter Hub IoT primitivos. Dispositivos downstream aparecem no Hub IoT como dispositivos de primeira classe com gêmeos e métodos. Um usuário pode interagir com os dispositivos do Hub IoT e não tem ciência do dispositivo de gateway intermediário.

![Diagramas de padrões de gateway][1]

## <a name="use-cases"></a>Casos de uso
Todos padrões de gateway fornecem os seguintes benefícios:
* **Análise de borda** – use serviços de IA localmente para processar dados provenientes de dispositivos downstream sem enviar telemetria de fidelidade completa à nuvem. Encontre e reaja a insights localmente e só envie um subconjunto de dados para o Hub IoT. 
* **Isolamento de dispositivo downstream** – o dispositivo de gateway pode proteger todos os dispositivos downstream da exposição à internet. Ele pode ser colocado entre uma rede OT que não tem conectividade e uma rede de TI que fornece acesso à Web. 
* **Multiplexação de conexão** – todos os dispositivos que se conectam ao Hub IoT por meio de um dispositivo do IoT Edge usarão a mesma conexão subjacente.
* **Suavização de tráfego** – o dispositivo do IoT Edge implementará automaticamente a retirada exponencial em caso de limitação do Hub IoT, enquanto as mensagens persistem localmente. Isso tornará sua solução resiliente a picos no tráfego.
* **Suporte offline limitado** – o dispositivo de gateway armazenará localmente as mensagens que não podem ser entregues ao Hub IoT.

Um gateway que faz a conversão de protocolo também pode executar análise de borda, isolamento de dispositivo, suavização de tráfego e dar suporte offline para dispositivos existentes e novos que tenham restrição de recursos. Muitos dispositivos existentes estão produzindo dados que podem fomentar insights de negócios, porém, não foram projetados com a conectividade de nuvem em mente. Gateways opacos permitem que esses dados possam ser desbloqueados e utilizados em uma solução de IoT de ponta a ponta.

Um gateway que faz a conversão de identidade fornece os benefícios de conversão de protocolo e também permite a capacidade de gerenciamento completa de dispositivos downstream da nuvem. Todos os dispositivos na sua solução IoT aparecem no Hub IoT, independentemente do protocolo com o qual conversam.

## <a name="cheat-sheet"></a>Roteiro
Aqui está um roteiro rápido que compara os Hub IoT primitivos ao usarem gateways transparentes, opacos e de proxy.

| &nbsp; | Gateway transparente | Conversão de protocolo | Conversão de identidade |
|--------|-------------|--------|--------|
| Identidades armazenadas no registro de identidade do Hub IoT | Identidades de todos os dispositivos conectados | Somente a identidade do dispositivo de gateway | Identidades de todos os dispositivos conectados |
| Dispositivo gêmeo | Cada dispositivo conectado tem seu próprio dispositivo gêmeo | Somente o gateway tem um dispositivo e módulo gêmeos | Cada dispositivo conectado tem seu próprio dispositivo gêmeo |
| Métodos diretos e mensagens da nuvem para o dispositivo | A nuvem pode lidar com cada dispositivo conectado individualmente | A nuvem pode lidar somente com o dispositivo de gateway | A nuvem pode lidar com cada dispositivo conectado individualmente |
| [Cotas e limitações do Hub IoT][lnk-iothub-throttles-quotas] | Aplica-se a cada dispositivo | Aplica-se ao dispositivo de gateway | Aplica-se a cada dispositivo |

Ao usar um padrão de gateway opaco (conversão de protocolo), todos os dispositivos que se conectam por meio do gateway compartilham a mesma fila da nuvem para o dispositivo, que pode conter no máximo 50 mensagens. Devido a isso, o padrão de gateway opaco deve ser usado somente quando muito poucos dispositivos estão se conectando por meio de cada gateway de campo e seu tráfego da nuvem para o dispositivo é baixo.

## <a name="next-steps"></a>Próximas etapas
Use um dispositivo IoT Edge como um [gateway transparente][lnk-iot-edge-as-transparent-gateway] 

[lnk-iot-edge-as-transparent-gateway]: ./how-to-create-transparent-gateway.md
[lnk-iothub-throttles-quotas]: ../iot-hub/iot-hub-devguide-quotas-throttling.md

[1]: ./media/iot-edge-as-gateway/edge-as-gateway.png
