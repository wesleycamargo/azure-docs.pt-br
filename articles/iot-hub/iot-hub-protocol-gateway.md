---
title: Gateway de protocolo IoT do Azure | Microsoft Docs
description: Descreve como usar o gateway do protocolo IoT do Azure para aumentar as capacidades e suporte de protocolo do Hub IoT do Azure.
services: iot-hub
documentationcenter: ''
author: kdotchkoff
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/23/2016
ms.author: kdotchko

---
# Suporte a protocolos adicionais para Hub IoT
O Hub IoT do Azure dá suporte nativo à comunicação pelos protocolos AMQP, MQTT e HTTP/1. Em alguns casos, os dispositivos ou gateways de campo não poderão usar um desses protocolos padrão e exigirão a adaptação do protocolo. Nesses casos, você pode usar um gateway personalizado. Nesses casos, um gateway personalizado pode habilitar a adaptação de protocolo para pontos de extremidade do Hub IoT preenchendo o tráfego para e do Hub IoT. Você pode usar o [gateway de protocolo IoT do Azure](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md) como um gateway personalizado para habilitar a adaptação de protocolo para o Hub IoT.

## Gateway de protocolo IoT do Azure
O gateway de protocolo do IoT do Azure é uma estrutura para a adaptação do protocolo feito para uma comunicação com o dispositivo bidirecional de alta escala com o Hub IoT. O gateway de protocolo é um componente de passagem que aceita conexões de dispositivo usando um protocolo específico. Ele preenche o tráfego para o Hub IoT sobre AMQP 1.0. O gateway de protocolo IoT está disponível como um projeto de software livre para flexibilizar a adição de suporte para uma variedade de protocolos e versões de protocolo.

Você pode implantar o gateway de protocolo no Azure de maneira altamente escalonável usando as funções de trabalho dos Serviços de Nuvem. Além disso, o gateway de protocolo pode ser implantado em ambientes locais, como gateways de campo.

O gateway de protocolo IoT do Azure inclui um adaptador de protocolo MQTT que permite a personalização do comportamento do protocolo MQTT, se for necessário. Como Hub IoT fornece suporte interno ao protocolo MQTT v 3.1.1, você deve considerar o uso do adaptador de protocolo MQTT apenas se tiver uma necessidade de personalizações de protocolo ou requisitos específicos para recursos adicionais.

O adaptador MQTT também demonstra o modelo de programação para a criação de adaptadores de protocolo para outros protocolos. Além disso, o modelo de programação do gateway de protocolo IoT permite que você conecte componentes personalizados para processamento especializados, como autenticação personalizada, transformações de mensagens, compactação/descompactação ou criptografia/descriptografia de tráfego entre os dispositivos e Hub IoT.

Para manter a flexibilidade, o gateway de protocolo e a implementação de MQTT são fornecidos em um projeto de software livre. Isso permite que você personalize a implementação conforme a necessidade.

## Próximas etapas
Para saber mais sobre o gateway de protocolo IoT do Azure e como usar e implantá-lo como parte de sua solução de IoT, confira:

* [Repositório de gateway de protocolo IoT do Azure no GitHub](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md)
* [Guia de desenvolvedor do gateway do protocolo IoT do Azure](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/docs/DeveloperGuide.md)

Para saber mais sobre como planejar sua implantação do Hub IoT, consulte:

* [Comparar com Hubs de Eventos][lnk-compare]
* [Escala, alta disponibilidade e recuperação de desastre][lnk-scaling]

Para explorar melhor as funcionalidades do Hub IoT, consulte:

* [Guia do desenvolvedor][lnk-devguide]
* [Explorar o gerenciamento de dispositivo usando a interface do usuário de exemplo][lnk-dmui]
* [Simular um dispositivo com o SDK do Gateway][lnk-gateway]
* [Usar o Portal do Azure para gerenciar o Hub IoT][lnk-portal]

[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0831_2016-->