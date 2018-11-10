---
title: Entender os SDKs do IoT Azure | Microsoft Docs
description: Guia do desenvolvedor ‑ Informações e links para os vários SDKs de serviço e de dispositivo do IoT do Azure que você pode usar para criar os aplicativos do dispositivo e de back-end.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: dobett
ms.openlocfilehash: 9daca1876a291cb1f726e7c0eb4840c57f5c54c8
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/01/2018
ms.locfileid: "50741310"
---
# <a name="understand-and-use-azure-iot-hub-sdks"></a>Entender e usar os SDKs de Hub IoT do Azure

Há três categorias de SDKs (Kits de desenvolvimento de software) para trabalhar com o Hub IoT:

* **SDKs do dispositivo** permitem que você crie aplicativos que são executados em seus dispositivos de IoT usando o cliente de dispositivo ou módulo. Esses aplicativos enviam telemetria ao Hub IoT e, opcionalmente, recebem mensagens, trabalho, método ou atualizações de gêmeos de seu Hub IoT.  Você também pode usar o cliente do módulo para criar [módulos](../iot-edge/iot-edge-modules.md) para [tempo de execução do Azure IoT Edge](../iot-edge/about-iot-edge.md).

* Os **Serviço SDKs** permitem gerenciar seu hub IoT e, como opção, enviar mensagens, agendar trabalhos, invocar métodos diretos ou enviar atualizações de propriedade desejadas para os dispositivos IoT ou módulos.

* **SDKs do Dispositivo de Provisionamento** permitem que você provisione dispositivos ao Hub IoT usando o [serviço de provisionamento de dispositivo](../iot-dps/about-iot-dps.md).

Saiba mais sobre os [benefícios do desenvolvimento usando SDKs de IoT do Azure ](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="azure-iot-device-sdks"></a>SDKs do dispositivo IoT do Azure

Os SDKs do dispositivo IoT do Microsoft Azure contêm código que facilita a criação de dispositivos e aplicativos que se conectam e são gerenciados pelos serviços do Hub Iot do Azure.

SDK do dispositivo de Hub IoT do Azure para .NET: 

* Instalar do [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/)
* [Código-fonte](https://github.com/Azure/azure-iot-sdk-csharp)
* [Referência de API](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices?view=azure-dotnet)
* [Referência do módulo](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet)

Dispositivo SDK do dispositivo de Hub IoT do Azure para C: gravado em ANSI C (C99) para portabilidade e compatibilidade ampla da plataforma:

* Instalar a partir de [apt-get, MBED, Arduino IDE ou Nuget](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md)
* [Código-fonte](https://github.com/Azure/azure-iot-sdk-c)
* [Referência de API](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
* [Referência do módulo](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_module_client.h)

SDK do dispositivo de Hub IoT do Azure para Java: 

* Adicionar ao projeto [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk)
* [Código-fonte](https://github.com/Azure/azure-iot-sdk-java)
* [Referência de API](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device)
* [Referência do módulo](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device._module_client?view=azure-java-stable)

SDK do dispositivo de Hub IoT do Azure para Node.js: 

* Instalar do arquivo [npm](https://www.npmjs.com/package/azure-iot-device)
* [Código-fonte](https://github.com/Azure/azure-iot-sdk-node)
* [Referência de API](https://docs.microsoft.com/javascript/api/azure-iot-device/?view=azure-iot-typescript-latest)
* [Referência do módulo](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest)

SDK do dispositivo de Hub IoT do Azure para Python: 

* Instalar do arquivo [pip](https://pypi.python.org/pypi/azure-iothub-device-client/)
* [Código-fonte](https://github.com/Azure/azure-iot-sdk-python)
* Referência da API: consulte [referência da API C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)

SDK do dispositivo do Hub IoT para iOS: 

* Instalar de [CocoaPod](https://cocoapods.org/pods/AzureIoTHubClient)
* [Exemplos](https://github.com/Azure-Samples/azure-iot-samples-ios)
* Referência da API: consulte [referência da API C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)

> [!NOTE]
> Consulte os arquivos Leiame nos repositórios GitHub para obter informações sobre como usar os gerenciadores de pacotes específicos à linguagem e à plataforma para instalar binários e dependências em seu computador de desenvolvimento.
> 
> 

### <a name="os-platform-and-hardware-compatibility"></a>Compatibilidade de plataforma do sistema operacional e hardware

Plataformas com suporte para os SDKs que podem ser encontradas no [suporte de plataforma de SDKs de IoT do Azure](iot-hub-device-sdk-platform-support.md).

Para saber mais sobre a compatibilidade do SDK com dispositivos de hardware específicos, consulte o [Catálogo de dispositivos certificados pelo Azure para IoT](https://catalog.azureiotsuite.com/) ou repositório individual.

## <a name="azure-iot-service-sdks"></a>SDKs do serviço IoT do Azure

Os SDKs do serviço de IoT do Azure contêm código para facilitar a criação aplicativos que interagem diretamente com o Hub IoT para gerenciar dispositivos e a segurança.

SDK de serviço de Hub IoT do Azure para .NET:

* Fazer o download do [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Devices/)
* [Código-fonte](https://github.com/Azure/azure-iot-sdk-csharp)
* [Referência de API](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices)

SDK de serviço de Hub IoT do Azure para Java: 

* Adicionar ao projeto [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk
)
* [Código-fonte](https://github.com/Azure/azure-iot-sdk-java)
* [Referência de API](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service)

SDK de serviço de Hub IoT do Azure para Node.js: 

* Fazer download do [npm](https://www.npmjs.com/package/azure-iothub)
* [Código-fonte](https://github.com/Azure/azure-iot-sdk-node)
* [Referência de API](https://docs.microsoft.com/javascript/api/azure-iothub/?view=azure-iot-typescript-latest)

SDK de serviço de Hub IoT do Azure para Python: 

* Fazer download do [pip](https://pypi.python.org/pypi/azure-iothub-service-client/)
* [Código-fonte](https://github.com/Azure/azure-iot-sdk-python)

SDK de serviço de Hub IoT do Azure para C: 

* Faça o download de [apt-get, MBED, Arduino IDE ou Nuget](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md)
* [Código-fonte](https://github.com/Azure/azure-iot-sdk-c)

SDK do serviço do Hub IoT para iOS: 

* Instalar de [CocoaPod](https://cocoapods.org/pods/AzureIoTHubServiceClient)
* [Exemplos](https://github.com/Azure-Samples/azure-iot-samples-ios)

> [!NOTE]
> Consulte os arquivos Leiame nos repositórios GitHub para obter informações sobre como usar os gerenciadores de pacotes específicos à linguagem e à plataforma para instalar binários e dependências em seu computador de desenvolvimento.

## <a name="device-provisioning-sdks"></a>SDKs de provisionamento de dispositivos

As **SDKs de provisionamento do Microsoft Azure** permitem que você provisione dispositivos ao Hub IoT usando o [serviço de provisionamento de dispositivo](../iot-dps/about-iot-dps.md).

Dispositivo de Provisionamento do Azure e SDKs de serviço para C#:

* [SDK do cliente de dispositivo de provisionamento](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/provisioning/device)
* [SDK do cliente de dispositivo de provisionamento](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/provisioning/service)

Dispositivo de Provisionamento do Azure e SDKs de serviço para Java:

* [SDK do cliente de dispositivo de provisionamento](https://github.com/Azure/azure-iot-sdk-java/blob/master/provisioning/provisioning-device-client)
* [SDK do cliente de dispositivo de provisionamento](https://github.com/Azure/azure-iot-sdk-java/blob/master/provisioning/provisioning-service-client)

Dispositivo de Provisionamento do Azure e SDKs de serviço para Node.js:

* [SDK do cliente de dispositivo de provisionamento](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/device)
* [SDK do cliente de dispositivo de provisionamento](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service)

Dispositivo de Provisionamento do Azure e SDKs de serviço para Python:

* [Provisionamento da SDK do cliente do dispositivo](https://github.com/Azure/azure-iot-sdk-python/blob/master/provisioning_device_client)
* [SDK do cliente de dispositivo de provisionamento](https://github.com/Azure/azure-iot-sdk-python/tree/master/provisioning_service_client)

Dispositivo de Provisionamento do Azure e SDKs de serviço para C:

* [SDK do cliente de dispositivo de provisionamento](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client)
* [SDK do cliente de dispositivo de provisionamento](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_service_client)

## <a name="next-steps"></a>Próximas etapas

Os SDKs de IoT do Azure também fornecem um conjunto de ferramentas para ajudá-lo com o desenvolvimento:
* [iothub-diagnostics](https://github.com/Azure/iothub-diagnostics): uma ferramenta de linha de comando de plataforma cruzada para ajudar a diagnosticar problemas relacionados à conexão com o IoT Hub.
* [Gerenciador de dispositivos](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer): um aplicativo de desktop do Windows para se conectar ao seu IoT Hub.

Outros tópicos de referência neste Guia do desenvolvedor do Hub IoT incluem:

* [Pontos de extremidade do Hub IoT](iot-hub-devguide-endpoints.md)
* [Linguagem de consulta do Hub IoT para dispositivos gêmeos, trabalhos e roteamento de mensagens](iot-hub-devguide-query-language.md)
* [Cotas e limitação](iot-hub-devguide-quotas-throttling.md)
* [Suporte ao MQTT do Hub IoT](iot-hub-mqtt-support.md)
* [Referência de API REST do Hub IoT](/rest/api/iothub/)
* [Suporte de plataforma do SDK do IoT do Azure](iot-hub-device-sdk-platform-support.md)
