<properties
 pageTitle="Lista dos SDKs do Hub IoT do Azure | Microsoft Azure"
 description="Informações e links para os vários dispositivos do Hub IoT do Azure e SDKs de serviço."
 services="iot-hub"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="06/23/2016"
 ms.author="dobett"/>

# SDKs do Hub IoT

Este artigo fornece informações sobre os vários [SDKs da Microsoft Azure IoT][] junto com links para recursos adicionais.

## SDKs do dispositivo do Hub IoT

Os SDKs do dispositivo IoT do Microsoft Azure contêm código que facilita a criação de dispositivos e aplicativos que se conectam e são gerenciados pelos serviços do Hub Iot do Azure.

O seguinte dispositivo de SDKs IoT estão disponíveis para download no GitHub:

- [Dispositivo SDK do dispositivo IoT do Azure para C][] gravado em ANSI C (C99) para portabilidade e compatibilidade ampla da plataforma.
- [SDK do dispositivo IoT do Azure para .NET][]
- [SDK do dispositivo IoT do Azure para Java][]
- [SDK do dispositivo IoT do Azure para Node.js][]
- [SDK do dispositivo IoT do Microsoft Azure para Python 2.7][]

### Compatibilidade de plataformas de SO e hardware

Para saber mais sobre a compatibilidade com dispositivos de hardware específicos, confira os seguintes artigos:

- [Compatibilidade de plataformas de SO e hardware com SDKs de dispositivo][OS Platforms and hardware compatibility]
- [Programa Microsoft Azure Certified para IoT][].

## SDKs do serviço de Hub IoT

Os SDKs do serviço de IoT do Microsoft Azure contêm código que facilita a criação aplicativos que interagem diretamente com o Hub IoT a fim de gerenciar dispositivos e segurança.

Os SDKs do serviço IoT a seguir estão disponíveis para download no GitHub:

- [SDK do serviço IoT do Azure para Node.js][]
- [SDK do serviço IoT do Azure para Java][]

## SDK de Gateway do Azure IoT

Esse SDK de Gateway do Azure IoT contém a infraestrutura e os módulos para criar soluções de gateway IoT. Você pode estender o SDK para criar gateways adaptados a qualquer cenário completo.

Você pode baixar o [SDK de Gateway do Azure IoT][] do GitHub.

## Documentação de referência online de API

A seguir, uma lista dos links para documentação de referência da API online para as bibliotecas de dispositivo, de serviço e de gateway de IoT do Azure:

- [Internet das Coisas (IoT) .NET][]
- [REST do Hub IoT][]
- [SDK do dispositivo IoT do Microsoft Azure para C][]
- [SDK do dispositivo IoT do Microsoft Azure para Java][]
- [SDK do serviço IoT do Microsoft Azure para Java][]
- [SDK do dispositivo IoT do Microsoft Azure para Node.js][]
- [SDK do serviço IoT do Microsoft Azure para Node.js][]
- [SDK de gateway do Microsoft Azure IoT][]

## Próximas etapas

Para explorar melhor as funcionalidades do Hub IoT, consulte:

- [Projetar sua solução][lnk-design]
- [Explorar o gerenciamento de dispositivo usando a interface do usuário de exemplo][lnk-dmui]
- [Simular um dispositivo com o SDK do Gateway][lnk-gateway]
- [Usar o Portal do Azure para gerenciar o Hub IoT][lnk-portal]

[SDKs da Microsoft Azure IoT]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[Dispositivo SDK do dispositivo IoT do Azure para C]: https://github.com/Azure/azure-iot-sdks/blob/master/c/readme.md
[SDK do dispositivo IoT do Azure para .NET]: https://github.com/Azure/azure-iot-sdks/blob/master/csharp/device/readme.md
[SDK do dispositivo IoT do Azure para Java]: https://github.com/Azure/azure-iot-sdks/blob/master/java/device/readme.md
[SDK do serviço IoT do Azure para Java]: https://github.com/Azure/azure-iot-sdks/blob/master/java/service/readme.md
[SDK do dispositivo IoT do Azure para Node.js]: https://github.com/Azure/azure-iot-sdks/blob/master/node/device/readme.md
[SDK do serviço IoT do Azure para Node.js]: https://github.com/Azure/azure-iot-sdks/blob/master/node/service/README.md
[SDK do dispositivo IoT do Microsoft Azure para Python 2.7]: https://github.com/Azure/azure-iot-sdks/blob/master/python/device/readme.md
[OS Platforms and hardware compatibility]: iot-hub-tested-configurations.md
[Programa Microsoft Azure Certified para IoT]: iot-hub-tested-configurations.md#microsoft-azure-certified-for-iot
[SDK de Gateway do Azure IoT]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/README.md

[Internet das Coisas (IoT) .NET]: https://msdn.microsoft.com/library/mt488521.aspx
[SDK do dispositivo IoT do Microsoft Azure para C]: http://azure.github.io/azure-iot-sdks/c/api_reference/index.html
[SDK do dispositivo IoT do Microsoft Azure para Java]: http://azure.github.io/azure-iot-sdks/java/device/api_reference/index.html
[SDK do dispositivo IoT do Microsoft Azure para Node.js]: http://azure.github.io/azure-iot-sdks/node/api_reference/azure-iot-device/1.0.8/index.html
[REST do Hub IoT]: https://msdn.microsoft.com/library/mt548492.aspx
[SDK do serviço IoT do Microsoft Azure para Java]: http://azure.github.io/azure-iot-sdks/java/service/api_reference/index.html
[SDK do serviço IoT do Microsoft Azure para Node.js]: http://azure.github.io/azure-iot-sdks/node/api_reference/azure-iothub/1.0.10/index.html
[SDK de gateway do Microsoft Azure IoT]: http://azure.github.io/azure-iot-gateway-sdk/api_reference/c/html/

[lnk-design]: iot-hub-guidance.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0713_2016-->