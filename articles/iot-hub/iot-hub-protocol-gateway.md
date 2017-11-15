---
title: Gateway de protocolo IoT do Azure | Microsoft Docs
description: Como usar um gateway de protocolo IoT do Azure para estender os recursos do Hub IoT e o suporte a protocolos para permitir que dispositivos se conectem ao seu hub usando protocolos sem suporte nativo no Hub IoT.
services: iot-hub
documentationcenter: 
author: fsautomata
manager: timlt
editor: 
ms.assetid: 555e59ae-3136-4533-8ba8-f3a3b6acf648
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/11/2017
ms.author: elioda
ms.openlocfilehash: 1ed8ec28b95bbc91b731fd7bb7b3f1f6654e7fcf
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/14/2017
---
# <a name="support-additional-protocols-for-iot-hub"></a>Suporte a protocolos adicionais para Hub IoT
O Hub IoT do Azure dá suporte nativo à comunicação pelos protocolos MQTT, AMQP e HTTPS. Em alguns casos, os dispositivos ou gateways de campo poderão não conseguir usar um desses protocolos padrão e exigirão a adaptação do protocolo. Nesses casos, você pode usar um gateway personalizado. Um gateway personalizado habilita a adaptação de protocolo para pontos de extremidade do Hub IoT conectando o tráfego de e para o Hub IoT. Você pode usar o [gateway de protocolo IoT do Azure](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md) como um gateway personalizado para habilitar a adaptação de protocolo para o Hub IoT.

## <a name="azure-iot-protocol-gateway"></a>gateway de protocolo IoT do Azure
O gateway de protocolo do IoT do Azure é uma estrutura para a adaptação do protocolo feito para uma comunicação com o dispositivo bidirecional de alta escala com o Hub IoT. O gateway de protocolo é um componente de passagem que aceita conexões de dispositivo usando um protocolo específico. Ele preenche o tráfego para o Hub IoT sobre AMQP 1.0. 

Você pode implantar o gateway de protocolo no Azure de maneira altamente escalonável usando o Azure Service Fabric, as funções de trabalho dos Serviços de Nuvem do Azure ou as Máquinas Virtuais do Windows. Além disso, o gateway de protocolo pode ser implantado em ambientes locais, como gateways de campo.

O gateway de protocolo IoT do Azure inclui um adaptador de protocolo MQTT que permite a personalização do comportamento do protocolo MQTT, se necessário. Como o Hub IoT fornece suporte interno ao protocolo MQTT v 3.1.1, você deverá considerar o uso do adaptador de protocolo MQTT apenas se tiver a necessidade de personalizações de protocolo ou requisitos específicos para funcionalidade adicional.

O adaptador MQTT também demonstra o modelo de programação para a criação de adaptadores de protocolo para outros protocolos. Além disso, o modelo de programação do gateway de protocolo IoT do Azure permite que você conecte componentes personalizados para processamento especializados, como autenticação personalizada, transformações de mensagens, compactação/descompactação ou criptografia/descriptografia de tráfego entre os dispositivos e Hub IoT.

Para a flexibilidade, o gateway de protocolo de IoT do Azure e a implementação de MQTT são fornecidos em um projeto de software livre. Você pode usar o projeto de software livre para adicionar suporte para vários protocolos e versões de protocolo ou personalizar a implementação para seu cenário. 

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o gateway de protocolo IoT do Azure e como usar e implantá-lo como parte de sua solução de IoT, confira:

* [Repositório de gateway de protocolo IoT do Azure no GitHub](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md)
* [Guia de desenvolvedor do gateway do protocolo IoT do Azure](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/docs/DeveloperGuide.md)

Para saber mais sobre como planejar sua implantação do Hub IoT, consulte:

* [Comparar com Hubs de Eventos][lnk-compare]
* [Colocação em escala, alta disponibilidade e recuperação de desastres][lnk-scaling]
* [Guia do desenvolvedor do Hub IoT][lnk-devguide]

[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
