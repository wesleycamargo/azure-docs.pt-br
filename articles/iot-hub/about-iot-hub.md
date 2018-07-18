---
title: Introdução ao Hub IoT do Azure | Microsoft Docs
description: Saiba mais sobre o Hub IoT do Azure. Esse serviço IoT é construído para a ingestão de dados escalonável, gerenciamento de dispositivo e segurança.
author: nberdy
ms.author: nberdy
ms.date: 07/04/2018
ms.topic: overview
ms.custom: mvc
ms.service: iot-hub
services: iot-hub
manager: briz
ms.openlocfilehash: 0e54c48eb63c5b70626e72f83de6d3676710d4c4
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2018
ms.locfileid: "37860149"
---
# <a name="what-is-azure-iot-hub"></a>O que é o Hub IoT do Azure?

O Hub IoT é um serviço gerenciado e hospedado na nuvem que atua como um hub central de mensagem para a comunicação bidirecional entre o aplicativo de IoT e os dispositivos que ele gerencia. Você pode usar o Hub IoT do Azure para criar soluções de IoT com comunicação segura e confiável entre milhões de dispositivos de IoT e um back-end de solução de nuvem hospedado. Você pode se conectar praticamente qualquer dispositivo ao Hub IoT.

O Hub IoT dá suporte a comunicações do dispositivo para a nuvem e da nuvem para o dispositivo. O Hub IoT dá suporte a vários padrões de mensagens, como telemetria do dispositivo para nuvem, o carregamento de arquivo de dispositivos e métodos de solicitação-resposta para controlar os dispositivos pela nuvem. O monitoramento do Hub IoT ajuda a manter a integridade de sua solução pelo rastreamento de eventos, como criação de dispositivo, falhas do dispositivo e conexões de dispositivo.

Os recursos do Hub IoT ajudarão a criar soluções de IoT escalonáveis e completas, como o gerenciamento de equipamentos industriais usados na produção, no controle de ativos valiosos na área de saúde e monitoramento de uso de prédios.

## <a name="scale-your-solution"></a>Escalar sua solução

O Hub IoT pode ser dimensionado para milhões de dispositivos conectados simultaneamente, além de milhões de eventos por segundo para dar suporte a suas cargas de trabalho de IoT. O Hub IoT oferece vários níveis de serviço para atender a suas necessidades de escalabilidade. [Saiba mais](https://azure.microsoft.com/pricing/details/iot-hub/).

## <a name="secure-your-communications"></a>Proteja suas comunicações

O Hub IoT fornece um canal de comunicação seguro para seus dispositivos enviarem dados.

* A autenticação por dispositivo permite que cada dispositivo se conecte com segurança ao Hub IoT e a cada dispositivo para ser gerenciado com segurança.
* Você tem controle total sobre o acesso ao dispositivo e pode controlar conexões no nível de cada dispositivo.
* O [Serviço de Provisionamento de Dispositivos no Hub IoT](https://docs.microsoft.com/azure/iot-dps/) provisiona automaticamente os dispositivos para o hub IoT correto quando o dispositivo é inicializado pela primeira vez.
* Vários tipos de autenticação oferecem suporte a uma variedade de recursos do dispositivo:
  * Autenticação baseada em token SAS para começar a usar rapidamente sua solução de IoT.
  * Autenticação de certificado X.509 individual para autenticação segura e baseada em padrões.
  * Autenticação de autoridade de certificação X.509 para registro simples e baseado em padrões.

## <a name="route-device-data"></a>Rotear dados do dispositivo

A funcionalidade interna de roteamento de mensagem oferece flexibilidade para configurar a divisão automática de mensagens baseada em regras:

* Use o roteamento de mensagens para controlar onde seu hub envia telemetria do dispositivo.
* Não há nenhum custo adicional para rotear mensagens para vários pontos de extremidade.
* As regras de roteamento sem código assumem o lugar de código de dispatcher de mensagens personalizadas.

## <a name="integrate-with-other-services"></a>Integrar com outros serviços

Você pode integrar o Hub IoT com outros serviços do Azure para criar soluções completas de ponta a ponta. Por exemplo, use:

* A [Grade de Eventos do Azure](https://docs.microsoft.com/azure/event-grid/) permite que sua empresa reaja rapidamente a eventos críticos de maneira segura, escalonável e confiável.

* [Aplicativos Lógicos do Azure](https://docs.microsoft.com/azure/logic-apps/) para automatizar processos de negócios.

* [Aprendizado de máquina do Azure](https://docs.microsoft.com/azure/machine-learning/) para adicionar o aprendizado de máquina e modelos de AI a sua solução.

* [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/) para executar cálculos de análise em tempo real nos dados que fluem de seus dispositivos.

## <a name="configure-and-control-your-devices"></a>Configurar e controlar seus dispositivos

Você pode gerenciar seus dispositivos conectados ao Hub IoT com uma matriz de funcionalidade interna.

* Armazene, sincronize e consulte informações de metadados e o estado do dispositivo para todos os seus dispositivos.
* Defina o estado do dispositivo por dispositivo ou com base nas características comuns dos dispositivos.
* Responda automaticamente a uma alteração de estado do dispositivo relatado com a integração do roteamento da mensagem.

## <a name="make-your-solution-highly-available"></a>Torne sua solução altamente disponível

Há um [Contrato de Nível de Serviço para Hub IoT](https://azure.microsoft.com/support/legal/sla/iot-hub/) de 99,9%. O [SLA completo do Azure](https://azure.microsoft.com/support/legal/sla/) explica a disponibilidade garantida do Azure como um todo.

## <a name="connect-your-devices"></a>Conecte seus dispositivos

Use as bibliotecas do [SDK do dispositivo IoT do Azure](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) para criar aplicativos que são executados em seus dispositivos e interagem com o Hub IoT. As plataformas com suporte incluem várias distribuições de Linux, Windows e sistemas operacionais em tempo real. As linguagens com suporte incluem:

* C
* C#
* Java
* Python
* Node.js.

O Hub IoT e os SDKs do dispositivo dão suporte aos seguintes protocolos para conectar dispositivos:

* HTTPS
* AMQP
* AMQP sobre WebSockets
* MQTT
* MQTT sobre WebSockets

Se sua solução não puder usar as bibliotecas de dispositivos, os dispositivos poderão usar os protocolos MQTT v3.1.1, HTTPS 1.1 ou AMQP 1.0 para conectar-se nativamente ao seu hub.

Se sua solução não puder usar um dos protocolos com suporte, você poderá estender o Hub IoT para dar suporte a protocolos personalizados:

* Use o [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/) para criar um gateway de campo para executar a translação do protocolo na borda.
* Personalize o [gateway de protocolo do Azure IoT](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md) para realizar a translação do protocolo na nuvem.

## <a name="quotas-and-limits"></a>Cotas e limites

Cada assinatura do Azure tem limites de cota em vigor para impedir o abuso do serviço e esses limites podem afetar o escopo de sua solução de IoT. O limite atual por assinatura é de 10 hubs IoT por assinatura. Você pode solicitar um aumento de cota contatando o suporte. Para obter mais detalhes sobre os limites de cota:

* [Limites de serviço da assinatura do Azure](../azure-subscription-service-limits.md)
* [Limitação do Hub IoT e você](https://azure.microsoft.com/blog/iot-hub-throttling-and-you/)

## <a name="next-steps"></a>Próximas etapas

Para testar uma solução de IoT de ponta a ponta, confira o início rápido do Hub IoT:

* [Início Rápido: enviar telemetria de um dispositivo para um hub de IoT](quickstart-send-telemetry-node.md)
