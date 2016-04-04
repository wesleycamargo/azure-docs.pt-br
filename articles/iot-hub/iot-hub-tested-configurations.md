<properties
	pageTitle="Compatibilidade de plataformas de sistema operacional| Microsoft Azure"
	description="Resume a compatibilidade do SDK do dispositivo IoT com plataformas de sistema operacional."
	services="iot-hub"
	documentationCenter=""
	authors="hegate"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="02/28/2016"
     ms.author="hegate"/>

# Compatibilidade de plataformas de sistema operacional com SDKs do dispositivo

Este documento descreve a compatibilidade do SDK com diferentes plataformas de sistema operacional. Se não tiver certeza de qual dispositivo usar, dê uma olhada na seção sobre compatibilidade da [Plataforma de sistemas operacionais e bibliotecas](#os-platforms).

## Programa Microsoft Azure Certified para IoT

Se já tiver um dispositivo, examine a lista de dispositivos incluídos no [programa Microsoft Azure Certified para IoT][lnk-certified] para encontrar informações específicas ao dispositivo sobre compatibilidade. O certificado para o IoT do Microsoft Azure é o parceiro do programa que conecta o ecossistema IoT mais amplo ao Microsoft Azure de forma que os desenvolvedores e arquitetos entendem os cenários de compatibilidade. Especificamente, fornece uma lista confiável de combinações de dispositivo/sistema operacional para ajudá-lo a se familiarizar rapidamente com um projeto IoT – seja em uma prova de conceito ou fase piloto

## Plataformas de SO

As bibliotecas do Azure IoT foram testadas nas seguintes plataformas do sistema operacional:


|Plataformas dos sistemas operacionais Linux/Unix | Versão|
|:---------------|:------------:|
|Debian Linux| 7\.5|
|Fedora Linux|20|
|Raspbian Linux| 3\.18 |
|Ubuntu Linux| 14\.04 |
|Yocto Linux|2,1 |

|Plataformas do sistema operacional Windows | Versão|
|:---------------|:------------:|
|Área de trabalho do Windows| 10 |
|Windows IoT Core| 10 |
|Windows Server| 2012 R2|

|Outras plataformas | Versão|
|:---------------|:------------:|
|mbed | 2,0 |
|TI-RTOS | 2\. x |



## Bibliotecas C

O [SDK do dispositivo IoT do Microsoft Azure](https://github.com/Azure/azure-iot-sdks/blob/master/c/readme.md) foi testado nas seguintes configurações:

|Plataforma do SO| Versão|Protocolos|
|:---------|:----------:|:----------:|
|Debian Linux| 7\.5 | HTTPS, AMQP, MQTT, AMQP sobre WebSockets |
|Fedora Linux| 20 | HTTPS, AMQP, MQTT, AMQP sobre WebSockets |
|sistema operacional mbed| 2,0 | HTTPS, AMQP |
|TI-RTOS| 2\. x | HTTPS |
|Ubuntu Linux| 14\.04 | HTTPS, AMQP, MQTT, AMQP sobre WebSockets |
|Área de trabalho do Windows| 10 | HTTPS, AMQP, MQTT, AMQP sobre WebSockets |
|Yocto Linux|2,1 | HTTPS, AMQP|



## Bibliotecas do Node. js

O [SDK do dispositivo IoT do Microsoft Azure para Node.js](https://github.com/Azure/azure-iot-sdks/blob/master/node/device/readme.md) foi testado nas seguintes configurações:


|Tempo de execução| Versão|Protocolos|
|:---------|:----------:|:----:|
|Node.js| 4\.1.0 | HTTPS, AMQP, MQTT, AMQP sobre WebSockets |



## Bibliotecas Java

O [SDK do dispositivo IoT do Microsoft Azure para Java](https://github.com/Azure/azure-iot-sdks/blob/master/java/device/readme.md) foi testado nas seguintes configurações:

|Tempo de execução| Versão|Protocolos|
|:---------|:----------:|----|
|Java SE (Windows)| 1\.8 | HTTPS, AMQP, MQTT |
|Java SE (Linux)| 1\.8 | HTTPS, AMQP, MQTT|

O SDK do serviço IoT do Microsoft Azure para Java foi testado nas seguintes configurações:

|Tempo de execução| Versão|Protocolos|
|:---------|:----------:|:-----|
|Java SE| 1\.8 | HTTPS, AMQP, MQTT |


## CSharp

O [SDK do dispositivo IoT do Microsoft Azure para .NET](https://github.com/Azure/azure-iot-sdks/blob/master/csharp/device/readme.md) foi testado nas seguintes configurações:

|Plataforma do SO| Versão|Protocolos|
|:---------|:----------:|:----------:|
|Área de trabalho do Windows| 10 | HTTPS, AMQP, MQTT, AMQP sobre WebSockets |
|Windows IoT Core|10 | HTTPS |

O código de agente gerenciado requer o Microsoft .NET Framework 4.5


## Próximas etapas

- Saiba mais sobre o programa [Microsoft Azure Certified para IoT][lnk-certified].
- Saiba mais sobre como desenvolver soluções usando [Certificado para os dispositivos IoT](http://azure.com/iotdev).


[lnk-iot-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-certified]: iot-hub-certified-devices-linux-c.md

<!---HONumber=AcomDC_0323_2016-->