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
ms.openlocfilehash: b86132b42aef981e6218b27e271e6db645d14071
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2018
---
# <a name="connecting-iot-devices-to-azure-iot-hub-and-event-hubs"></a>Conectar Dispositivos IoT ao Azure: Hub IoT e Hubs de Eventos

O Azure fornece serviços desenvolvidos especificamente para diferentes tipos de conectividade e comunicação para ajudá-lo a conectar seus dados ao poder da nuvem. Tanto o Hub IoT como os Hubs de Eventos do Azure são serviços de nuvem que podem ingerir grandes quantidades de dados e processar ou armazenar esses dados para informações de negócios. Os dois serviços são semelhantes, pois ambos dão suporte à ingestão de dados com baixa latência e alta confiabilidade, mas eles são projetados para finalidades diferentes. O Hub IoT foi desenvolvido especificamente para atender aos requisitos exclusivos de conexão de dispositivos IoT, em escala, à nuvem do Azure, enquanto os Hubs de Eventos foram projetados para streaming de Big Data. Este é o motivo pelo qual a Microsoft recomenda o uso do Hub IoT para conectar dispositivos IoT ao Azure

O Azure Hub IoT é o gateway de nuvem que conecta dispositivos IoT e coleta dados para conduzir informações de negócios e automação. Além disso, o Hub IoT inclui recursos que enriquecem a relação entre os dispositivos e seus sistemas de back-end. Os recursos de comunicação bidirecional significam que, embora você receba dados de dispositivos, também é possível enviar comandos e políticas de volta aos dispositivos, por exemplo, para atualizar as propriedades ou chamar ações de gerenciamento de dispositivos.  Essa conectividade de nuvem a dispositivo também habilita o recurso importante do fornecimento de inteligência de nuvem para seus dispositivos de borda com o Azure IoT Edge. A identidade exclusiva no nível de dispositivo fornecida pelo Hub IoT ajuda a proteger melhor a sua solução de IoT contra ataques potenciais. 

[Hubs de Eventos do Azure][Azure Event Hubs] é o serviço de streaming de Big Data do Azure. Ele foi projetado para cenários de streaming de dados de alta taxa de transferência em que os clientes podem enviar bilhões de solicitações por dia. Os Hubs de Eventos usam um modelo de consumidor particionado para expandir sua transmissão e estão integrados aos serviços de big data e análise do Azure, incluindo Databricks, Stream Analytics, ADLS e HDInsight. Com recursos como a Captura e Aumento Automático de Hubs de Eventos, este serviço é criado para dar suporte a aplicativos e soluções de big data. Além disso, o Hub IoT utiliza Hubs de Eventos para seu caminho de fluxo de telemetria, para que sua solução de IoT também se beneficie do grande poder dos Hubs de Eventos.

Para resumir, embora ambas as soluções são projetadas para ingestão de dados em grande escala, somente o Hub IoT fornece recursos específicos de IoT avançados que são projetados para maximizar o valor de negócios da conexão de seus dispositivos IoT à nuvem do Azure.  Se sua jornada IoT está apenas começando, começar com o Hub IoT para dar suporte a cenários de ingestão de dados garantirá que você tenha acesso instantâneo aos recursos de IoT completos quando suas necessidades comerciais e técnicas o exigir.

A tabela a seguir fornece detalhes sobre como as duas camadas do Hub IoT comparam-se aos Hubs de Eventos quando avaliá-las para os recursos de IoT. Para obter mais informações sobre as camadas Standard e Básica do Hub IoT, consulte [Como escolher a camada certa do Hub IoT][lnk-scaling].

| Funcionalidade do IoT | Camada Standard do Hub IoT | Camada Básica do Hub IoT | Hubs de evento |
| --- | --- | --- | --- |
| Mensagens do dispositivo para a nuvem | ![Verificação][1] | ![Verificação][1] | ![Verificação][1] |
| Protocolos: HTTPS, AMQP, AMQP sobre webSockets | ![Verificação][1] | ![Verificação][1] | ![Verificação][1] |
| Protocolos: MQTT, MQTT sobre webSockets | ![Verificação][1] | ![Verificação][1] |  |
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
