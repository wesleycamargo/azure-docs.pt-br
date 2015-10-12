<properties
   pageTitle="Gateway de protocolo IoT do Azure | Microsoft Azure"
   description="Descreve como usar o gateway do protocolo IoT do Azure para aumentar as capacidades do Hub IoT do Azure"
   services="iot-hub"
   documentationCenter=""
   authors="kdotchkoff"
   manager="kevinmil"
   editor=""/>

<tags
   ms.service="iot-hub"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/29/2015"
   ms.author="kdotchkoff"/>

# Suporte a protocolos adicionais para Hub IoT

O Hub IoT nativamente dá suporte à comunicação pelos protocolos HTTPS e AMQP. Em alguns casos os dispositivos ou gateways de campo não poderão usar um desses protocolos padrão em exigirão a adaptação do protocolo. Nesses casos, um gateway personalizado pode habilitar a adaptação de protocolo para pontos de extremidade do Hub IoT preenchendo o tráfego para e do Hub IoT. O gateway de protocolo do IoT do Azure possibilita a adaptação do protocolo para o Hub IoT e implementa um adaptador de protocolo MQTT para habilitar a comunicação entre o dispositivo IoT e o Hub IoT sobre o protocolo MQTT.

## Gateway de protocolo IoT do Azure

O gateway de protocolo do IoT do Azure é uma estrutura para a adaptação do protocolo feito para uma comunicação com o dispositivo bi-direcional de alta escala com o Hub IoT. O gateway de protocolo é um componente de passagem que aceita conexões de dispositivo por meio de um protocolo específico e ponte de tráfego para o Hub IoT sobre o AMQP 1.0. O gateway de protocolo IoT está disponível como um projeto de software livre (OSS) para fornecer flexibilidade adicionando suporte para uma variedade de protocolos e versões de protocolo.

O gateway de protocolo pode ser implantado no Azure de maneira altamente escalonável usando as funções de trabalho de Serviços de Nuvem. Além disso, o gateway de protocolo pode ser implantado em ambientes locais como gateways de campo.

O gateway de protocolo IoT do Azure inclui um adaptador MQTT para facilitar a comunicação com os dispositivos sobre o protocolo MQTT v3.1.1. O gateway de protocolo e a implementação de MQTT são fornecidos como projeto OSS para flexibilidade para permitir a personalização da implementação conforme necessário.
   
O adaptador MQTT também demonstra o modelo de programação para a criação de adaptadores de protocolo para outros protocolos. Além disso, o modelo de programação do gateway de protocolo IoT permite conectar componentes personalizados para processamento especializados como autenticação personalizada, transformações de mensagens, compactação/descompactação ou criptografia/descriptografia de tráfego entre os dispositivos e Hub IoT.

## Próximas etapas 

Para saber mais sobre o gateway de protocolo IoT do Azure e como usar e implantá-lo como parte de sua solução de IoT, consulte:

* [Repositório de gateway de protocolo IoT do Azure no GitHub](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md)
* [Guia de desenvolvedor do gateway do protocolo IoT do Azure](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/docs/DeveloperGuide.md)

<!---HONumber=Oct15_HO1-->