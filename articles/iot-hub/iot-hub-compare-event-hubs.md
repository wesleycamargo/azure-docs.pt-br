---
title: Comparar o Hub IoT do Azure ao Hubs de Eventos do Azure | Microsoft Docs
description: Uma comparação dos serviços do Hub IoT e de Eventos do Azure destacando diferenças funcionais e casos de uso. A comparação inclui protocolos com suporte, gerenciamento de dispositivos, monitoramento e uploads de arquivos.
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
editor: ''
ms.assetid: aeddea62-8302-48e2-9aad-c5a0e5f5abe9
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/01/2018
ms.author: kgremban
ms.openlocfilehash: 303a2bde0a1e0b25ca6eb145e7b0cd6c91fff351
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2018
---
# <a name="comparison-of-azure-iot-hub-and-azure-event-hubs"></a>Comparação do Hub IoT do Azure e Hubs de Eventos do Azure

Tanto o Hub IoT como os Hubs de Eventos do Azure são serviços de nuvem que podem ingerir grandes quantidades de dados e processar ou armazenar esses dados para informações de negócios. Os dois serviços são semelhantes, pois ambos dão suporte a dados de eventos e telemetria de processos com baixa latência e alta confiabilidade. No entanto, somente o Hub IoT foi desenvolvido com os recursos específicos necessários para dar suporte a cenários de Internet em escala. 

O Hub IoT é o gateway de nuvem que conecta dispositivos e coleta dados para automação e informações de negócios. Ele facilita a transmissão de dados para a nuvem e o gerenciamento dos dispositivos em escala. Um importante diferenciador entre o Hub IoT e outros serviços de ingestão de dados é que o Hub IoT inclui recursos que enriquecem o relacionamento entre os dispositivos e os sistemas de back-end. Os recursos de comunicação bidirecional significam que, embora você receba dados de dispositivos, também é possível enviar mensagens de volta aos dispositivos para atualizar as propriedades ou chamar uma ação. A identidade no nível do dispositivo ajuda a proteger o sistema. A computação distribuída move a lógica do serviço de nuvem para dispositivos de borda.

[Hubs de Eventos do Azure][Azure Event Hubs] é um serviço de processamento de eventos que pode processar e armazenar grandes quantidades de dados e telemetria. Os Hubs de Eventos são projetados para ingestão de eventos em massa, tanto no contexto de cenários inter-datacenter e intra-datacenter, mas não fornecem os recursos específicos de IoT avançados que estão disponíveis no Hub IoT. Por esse motivo, não é recomendável Hubs de Eventos para as soluções de IoT. 

A tabela a seguir fornece detalhes sobre como as duas camadas do Hub IoT comparam-se aos Hubs de Eventos quando avaliá-las para os recursos de IoT. Para obter mais informações sobre as camadas Standard e Básica do Hub IoT, consulte [Como escolher a camada certa do Hub IoT][lnk-scaling].

| Funcionalidade do IoT | Camada Standard do Hub IoT | Camada Básica do Hub IoT | Hubs de evento |
| --- | --- | --- | --- |
| Mensagens do dispositivo para a nuvem | ![Verificação][1] | ![Verificação][1] | ![Verificação][1] |
| Protocolos: HTTPS, AMQP, AMQP sobre websockets | ![Verificação][1] | ![Verificação][1] | ![Verificação][1] |
| Protocolos: MQTT, MQTT sobre websockets | ![Verificação][1] | ![Verificação][1] |  |
| Identidade por dispositivo | ![Verificação][1] | ![Verificação][1] |  |
| Upload de arquivo de dispositivos | ![Verificação][1] | ![Verificação][1] |  |
| Serviço de provisionamento de dispositivos | ![Verificação][1] | ![Verificação][1] |  |
| Mensagens da nuvem para o dispositivo | ![Verificação][1] |  |  |
| Gerenciamento de dispositivo, dispositivo gêmeo | ![Verificação][1] |  |  |
| IoT Edge | ![Verificação][1] |  |  |

Mesmo se o único caso de uso for ingestão de dados de dispositivo para nuvem, é altamente recomendável usar o Hub IoT, pois ele fornece um serviço projetado para conectividade do dispositivo do IoT. 

### <a name="next-steps"></a>Próximas etapas

Para explorar ainda mais as funcionalidade do Hub IoT, consulte o [Guia do desenvolvedor do Hub IoT][lnk-devguide]


[Azure Event Hubs]: ../event-hubs/event-hubs-what-is-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md

<!--Image references-->
[1]: ./media/iot-hub-compare-event-hubs/ic195031.png