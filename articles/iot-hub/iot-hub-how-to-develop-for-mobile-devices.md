---
title: Desenvolvimento para dispositivos móveis usando SDKs do IoT do Azure | Microsoft Docs
description: Guia do desenvolvedor - Saiba mais sobre como desenvolver para dispositivos móveis usando SDKs do Hub IoT do Azure.
author: yzhong94
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/16/2018
ms.author: yizhon
ms.openlocfilehash: 9c4b4a61698556f14a6362984b04e5cc409b5763
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34634940"
---
# <a name="develop-for-mobile-devices-using-azure-iot-sdks"></a>Desenvolvimento para dispositivos móveis usando SDKs IoT do Azure

As coisas na Internet das Coisas podem se referir a uma ampla variedade de dispositivos com capacidade variável: sensores, microcontroladores, dispositivos inteligentes, gateways industriais e até mesmo dispositivos móveis.  Um dispositivo móvel pode ser um dispositivo de IoT, em que ele envia a telemetria do dispositivo para nuvem e é gerenciado pela nuvem.  Ele também pode ser o dispositivo que executa um aplicativo de serviço de back-end, que gerencia outros dispositivos de IoT.  Em ambos os casos, [SDKs do Hub IoT do Azure][lnk-sdk-overview] podem ser usados para desenvolver aplicativos que funcionam para dispositivos móveis.  

## <a name="develop-for-native-ios-platform"></a>Desenvolver para a plataforma de iOS nativo

Os SDKs do Hub IoT do Azure fornecem suporte à plataforma de iOS nativo por meio do C SDK do Azure IoT Hub.  Você pode pensar nela como um SDK do iOS que você pode incorporar ao seu projeto Swift ou Objective C XCode.  Há duas maneiras de usar o C SDK no iOS:
- Use as bibliotecas de CocoaPod no projeto XCode diretamente.  
- Faça o download do código-fonte para o C SDK e compile para a plataforma iOS após a [instrução de build][lnk-c-devbox] para MacOS.  

O C SDK do Hub IoT do Azure é escrito em C99 para portabilidade máxima para várias plataformas.  O processo de portabilidade envolve a escrita de uma camada de adoção fina para os componentes específicos de plataforma, o que pode ser encontrado aqui para [iOS][lnk-ios-pal].  Os recursos no C SDK podem ser utilizados na plataforma iOS, incluindo os primitivos do Hub IoT do Azure com suporte e os recursos específicos do SDK, como política de novas tentativas para confiabilidade da rede.  A interface para SDK do iOS também é semelhante à interface do C SDK do Hub IoT do Azure.  

Essas documentações explicam como desenvolver um aplicativo de dispositivo ou serviço em um dispositivo iOS:
- [Início Rápido: enviar telemetria de um dispositivo para um hub de IoT][lnk-device-ios-quickstart]  
- [Enviar mensagens de nuvem para seu dispositivo com o hub de IoT][lnk-service-ios-quickstart]  

### <a name="develop-with-azure-iot-hub-cocoapod-libraries"></a>Desenvolver com bibliotecas CocoaPod do Hub IoT do Azure

Os SDKs do Hub IoT do Azure liberam um conjunto de bibliotecas Objective-C CocoaPod para desenvolvimento do iOS.  Para ver a lista mais recente das bibliotecas de CocoaPod, consulte [CocoaPods para Microsoft Azure IoT][lnk-cocoapod-list].  Depois que as bibliotecas relevantes forem incorporadas ao seu projeto XCode, haverá duas maneiras de escrever o código relacionado ao Hub IoT:
- Função Objective C: se o projeto for escrito em Objective-C, você poderá chamar APIs do C SDK do Hub IoT do Azure diretamente.  Se o seu projeto for escrito em Swift, você pode chamar ``@objc func`` antes de criar a função e continuar escrevendo todas as lógicas relacionadas ao Hub IoT do Azure usando código C ou Objective-C.  Um conjunto de exemplos que demonstram ambos pode ser encontrado no [repositório de exemplo][lnk-ios-samples-repo].  
- Incorporar exemplos de C: se tiver escrito um aplicativo de dispositivo C, você poderá referenciá-lo diretamente no seu projeto XCode:
    - Adicione o arquivo sample.c ao seu projeto XCode a partir do XCode.  
    - Adicione o arquivo de cabeçalho à dependência.  Um arquivo de cabeçalho está incluído no [repositório de exemplo][lnk-ios-samples-repo] como exemplo.  Para obter mais informações, visite a página de documentação da Apple para [Objective-C](https://developer.apple.com/documentation/objectivec).

## <a name="next-steps"></a>Próximas etapas
* [Referência de API REST do Hub IoT][lnk-rest-ref]
* [Código-fonte do C SDK do Azure IoT][lnk-c-sdk]

[lnk-sdk-overview]: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks
[lnk-c-devbox]: https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md
[lnk-device-ios-quickstart]:https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-ios
[lnk-service-ios-quickstart]: https://docs.microsoft.com/azure/iot-hub/iot-hub-ios-swift-c2d
[lnk-cocoapod-list]: https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/ios/CocoaPods.md
[lnk-ios-samples-repo]: https://github.com/Azure-Samples/azure-iot-samples-ios
[lnk-ios-pal]: https://github.com/Azure/azure-c-shared-utility/tree/master/pal/ios-osx
[lnk-c-sdk]: https://github.com/Azure/azure-iot-sdk-c
[lnk-rest-ref]: https://docs.microsoft.com/rest/api/iothub/
