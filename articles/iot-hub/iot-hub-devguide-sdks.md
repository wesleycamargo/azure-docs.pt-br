---
title: Entender os SDKs do IoT Azure | Microsoft Docs
description: Guia do desenvolvedor ‑ Informações e links para os vários SDKs de serviço e de dispositivo do IoT do Azure que você pode usar para criar os aplicativos do dispositivo e de back-end.
services: iot-hub
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: c5c9a497-bb03-4301-be2d-00edfb7d308f
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/12/2018
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2380e72083ec0c9453db991be5fbcc89310cee17
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2018
---
# <a name="understand-and-use-azure-iot-hub-sdks"></a>Entender e usar os SDKs de Hub IoT do Azure

Há duas categorias de SDKs (Kits de desenvolvimento de software) para trabalhar com o Hub IoT:

* **SDKs de dispositivo** permitem a compilação de aplicativos que são executados em dispositivos IoT. Esses aplicativos enviam telemetria ao Hub IoT e, opcionalmente, recebem mensagens, trabalho, método ou atualizações de gêmeos de seu Hub IoT.

* Os **Serviço SDKs** permitem gerenciar seu hub IoT e, como opção, enviar mensagens, agendar trabalhos, invocar métodos diretos ou enviar atualizações de propriedade desejadas para os dispositivos IoT.

Saiba mais sobre os benefícios do desenvolvimento usando SDKs de IoT do Azure [aqui][lnk-benefits-blog].

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="azure-iot-device-sdks"></a>SDKs do dispositivo IoT do Azure

Os SDKs do dispositivo IoT do Microsoft Azure contêm código que facilita a criação de dispositivos e aplicativos que se conectam e são gerenciados pelos serviços do Hub Iot do Azure.

SDK do dispositivo de Hub IoT do Azure para .NET: 
* Instalar do [Nuget][lnk-nuget-csharp-device]
* [Código-fonte][lnk-dotnet-sdk]
* [Referência de API][lnk-dotnet-ref]

Dispositivo SDK do dispositivo de Hub IoT do Azure para C: gravado em ANSI C (C99) para portabilidade e compatibilidade ampla da plataforma
* Instalar a partir de [apt-get, MBED, Arduino IDE ou Nuget][lnk-c-package]
* [Código-fonte][lnk-c-sdk]
* [Referência de API][lnk-c-ref]

SDK do dispositivo de Hub IoT do Azure para Java: 
* Adicionar ao projeto [Maven][lnk-maven-device]
* [Código-fonte][lnk-java-sdk]
* [Referência de API][lnk-java-ref]

SDK do dispositivo de Hub IoT do Azure para Node.js: 
* Instalar do arquivo [npm][lnk-npm-device]
* [Código-fonte][lnk-node-sdk]
* [Referência de API][lnk-node-ref]

SDK do dispositivo de Hub IoT do Azure para Python: 
* Instalar do arquivo [pip][lnk-pip-device]
* [Código-fonte][lnk-python-sdk]

> [!NOTE]
> Consulte os arquivos Leiame nos repositórios GitHub para obter informações sobre como usar os gerenciadores de pacotes específicos à linguagem e à plataforma para instalar binários e dependências em seu computador de desenvolvimento.
> 
> 

### <a name="os-platform-and-hardware-compatibility"></a>Compatibilidade de plataforma do sistema operacional e hardware

Para saber mais sobre a compatibilidade do SDK com dispositivos de hardware específicos, consulte o [Catálogo de dispositivos certificados pelo Azure para IoT][lnk-certified] ou repositório individual.

## <a name="azure-iot-service-sdks"></a>SDKs do serviço IoT do Azure

Os SDKs do serviço de IoT do Azure contêm código para facilitar a criação aplicativos que interagem diretamente com o Hub IoT para gerenciar dispositivos e a segurança.

SDK de serviço de Hub IoT do Azure para .NET:
* Fazer download do [Nuget][lnk-nuget-csharp-service]
* [Código-fonte][lnk-dotnet-sdk]
* [Referência de API][lnk-dotnet-service-ref]

SDK de serviço de Hub IoT do Azure para Java: 
* Adicionar ao projeto [Maven][lnk-maven-service]
* [Código-fonte][lnk-java-sdk]
* [Referência de API][lnk-java-service-ref]

SDK de serviço de Hub IoT do Azure para Node.js: 
* Fazer download do [npm][lnk-npm-service]
* [Código-fonte][lnk-node-sdk]
* [Referência de API][lnk-node-service-ref]

SDK de serviço de Hub IoT do Azure para Python: 
* Fazer download do [pip][lnk-pip-service]
* [Código-fonte][lnk-python-sdk]

SDK de serviço de Hub IoT do Azure para C: 
* Faça o download de [apt-get, MBED, Arduino IDE ou Nuget][lnk-c-package]
* [Código-fonte][lnk-c-sdk]

> [!NOTE]
> Consulte os arquivos Leiame nos repositórios GitHub para obter informações sobre como usar os gerenciadores de pacotes específicos à linguagem e à plataforma para instalar binários e dependências em seu computador de desenvolvimento.


## <a name="next-steps"></a>Próximas etapas

Outros tópicos de referência neste Guia do desenvolvedor do Hub IoT incluem:

* [Pontos de extremidade do Hub IoT][lnk-devguide-endpoints]
* [Linguagem de consulta do Hub IoT para dispositivos gêmeos, trabalhos e roteamento de mensagens][lnk-devguide-query]
* [Cotas e limitações][lnk-devguide-quotas]
* [Suporte ao MQTT do Hub IoT][lnk-devguide-mqtt]
* [Referência de API REST do Hub IoT][lnk-rest-ref]

<!-- Links and images -->

[lnk-c-sdk]: https://github.com/Azure/azure-iot-sdk-c
[lnk-dotnet-sdk]: https://github.com/Azure/azure-iot-sdk-csharp
[lnk-java-sdk]: https://github.com/Azure/azure-iot-sdk-java
[lnk-node-sdk]: https://github.com/Azure/azure-iot-sdk-node
[lnk-python-sdk]: https://github.com/Azure/azure-iot-sdk-python
[lnk-certified]: https://catalog.azureiotsuite.com/

[lnk-dotnet-ref]: https://docs.microsoft.com/dotnet/api/microsoft.azure.devices?view=azure-dotnet
[lnk-dotnet-service-ref]: https://docs.microsoft.com/dotnet/api/microsoft.azure.devices
[lnk-c-ref]: https://azure.github.io/azure-iot-sdk-c/index.html
[lnk-java-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device
[lnk-node-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device
[lnk-rest-ref]: https://docs.microsoft.com/rest/api/iothub/
[lnk-java-service-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service
[lnk-node-service-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service

[lnk-maven-device]: https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk
[lnk-maven-service]: https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk
[lnk-npm-device]: https://www.npmjs.com/package/azure-iot-device
[lnk-npm-service]: https://www.npmjs.com/package/azure-iothub
[lnk-nuget-csharp-device]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-csharp-service]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-c-package]: https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md
[lnk-pip-device]: https://pypi.python.org/pypi/azure-iothub-device-client/
[lnk-pip-service]: https://pypi.python.org/pypi/azure-iothub-service-client/


[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-benefits-blog]: https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/
