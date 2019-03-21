---
title: Suporte da plataforma de SDKs do dispositivo IoT do Azure | Microsoft Docs
description: Conceitos - lista de plataformas com suporte a SDKs do dispositivo IoT do Azure
author: yzhong94
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: yizhon
ms.openlocfilehash: 7bcc1bf6b734abe202c5fec5d515604f4bf8e4a7
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/22/2019
ms.locfileid: "56669356"
---
# <a name="azure-iot-sdks-platform-support"></a>Suporte da plataforma de SDKs do IoT do Azure

Os [Azure IoT SDKs](iot-hub-devguide-sdks.md) são um conjunto de bibliotecas para interagir com o Hub IoT e o Serviço de Provisionamento de Dispositivos com amplo suporte a linguagens e plataformas. Os SDKs são executados nas plataformas mais comuns e os desenvolvedores podem portar o SDK C para plataformas específicas, seguindo as [Diretrizes de Portabilidade](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md). 

A Microsoft dá suporte a vários sistemas operacionais/plataformas/frameworks e pode ser estendida usando o SDK C do Azure IoT. Alguns têm suporte oficialmente pela equipe, agrupados em camadas que representam o nível de suporte que os usuários podem esperar. *Plataformas com suporte total* significa que a Microsoft:

- Compila e executa testes de ponta a ponta continuamente no mestre e nas versões com suporte LTS.  Para fornecer cobertura de teste entre diferentes versões, geralmente podemos testar em relação a versão mais recente do LTS e a versão mais popular.  Outras versões da mesma plataforma podem ter suporte por meio de compatibilidade de versão da plataforma.
- Fornece diretrizes de instalação ou pacotes, se aplicável.
- Com suporte total para plataformas no GitHub.

Além disso, uma lista de parceiros transportou nosso SDK C para mais plataformas e estão mantendo a PAL (Camada de Abstração de Plataforma). O [Catálogo de dispositivo do Microsoft Azure Certified para IoT](https://catalog.azureiotsolutions.com/) também apresenta uma lista de plataformas de SO em que os vários SDKs foram testados. Os SDKs também criam regularmente nessas plataformas, com testes e suporte limitados:

* MBED2
* Arduino
* Windows CE 2013 (preterido em outubro de 2018)
* .NET Standard 1.3 com .NET Core 2.1 e .NET Framework 4.7
* Xamarin iOS, Android, UWP

## <a name="supported-platforms"></a>Plataformas com suporte

Há várias plataformas com suporte.

### <a name="c-sdk"></a>SDK C

| SO                  | Arch | Compilador             | Biblioteca TLS       |
|---------------------|------|----------------------|-------------------|
| Ubuntu 16.04 LTS    | X64  | gcc-5.4.0            | openssl  - 1.0.2g |
| Ubuntu 18.04 LTS    | X64  | gcc-7.3              | WolfSSL – 1.13    |
| Ubuntu 18.04 LTS    | X64  | Clang 6.0.X          | Openssl – 1.1.0g  |
| OSX 10.13.4         | x64  | XCode 9.4.1          | OSX nativo        |
| Windows Server 2016 | x64  | Visual Studio 14.0.X | SChannel          |
| Windows Server 2016 | x86  | Visual Studio 14.0.X | SChannel          |
| Debian 9 Stretch    | x64  | gcc-7.3              | Openssl – 1.1.0f  |

### <a name="python-sdk"></a>SDK do Python

| SO                  | Arch | Compilador   | Biblioteca TLS |
|---------------------|------|------------|-------------|
| Windows Server 2016 | x86  | Python 2,7 | openssl     |
| Windows Server 2016 | x64  | Python 2,7 | openssl     |
| Windows Server 2016 | x86  | Python 3.5 | openssl     |
| Windows Server 2016 | x64  | Python 3.5 | openssl     |
| Ubuntu 18.04 LTS    | x86  | Python 2,7 | openssl     |
| Ubuntu 18.04 LTS    | x86  | Python 3.4 | openssl     |
| MacOS High Sierra   | x64  | Python 2,7 | openssl     |

### <a name="net-sdk"></a>SDK .NET

| SO                  | Arch | Framework            | Standard          |
|---------------------|------|----------------------|-------------------|
| Ubuntu 16.04 LTS    | X64  | .NET Core 2.1        | .NET Standard 2.0 |
| Windows Server 2016 | X64  | .NET Core 2.1        | .NET Standard 2.0 |
| Windows Server 2016 | X64  | .NET Framework 4.7   | .NET Standard 2.0 |
| Windows Server 2016 | X64  | .NET Framework 4.5.1 | N/D               |

### <a name="nodejs-sdk"></a>SDK do Node.js

| SO                                           | Arch | Versão do nó |
|----------------------------------------------|------|--------------|
| Ubuntu 16.04 LTS (usando imagem de docker do nó 6) | X64  | Nó 6       |
| Windows Server 2016                          | X64  | Nó 6       |

### <a name="java-sdk"></a>Java SDK

| SO                  | Arch | Versão Java |
|---------------------|------|--------------|
| Ubuntu 16.04 LTS    | X64  | Java 8       |
| Windows Server 2016 | X64  | Java 8       |
| Android API 28 | X64  | Java 8       |
| Coisas Android | X64  | Java 8      |

## <a name="partner-supported-platforms"></a>Plataformas com suporte de parceiros

Os clientes podem estender o nosso suporte de plataforma ao portar o SDK do Azure IoT C, especificamente, criando a camada de abstração de plataforma (PAL) do SDK. A Microsoft trabalha com parceiros para fornecer suporte estendido. Uma lista dos parceiros compatibilizou o SDK do C para mais plataformas, mantendo a PAL.

| Parceiro             | Dispositivos                            | Link                     | Suporte |
|---------------------|------------------------------------|--------------------------|---------|
| Espressif           | ESP32 <br/> ESP8266                              | [Esp-azure](https://github.com/espressif/esp-azure)                | [GitHub](https://github.com/espressif/esp-azure)  
| Qualcomm            | Qualcomm MDM9206 LTE IoT Modem     | [Qualcomm LTE para SDK de IoT](https://developer.qualcomm.com/software/lte-iot-sdk) | [Fórum](https://developer.qualcomm.com/forums/software/lte-iot-sdk)   |
| ST Microelectronics | Série STM32L4 <br/> Série STM32F4 <br/>  Série STM32F7 <br/>  Kit de descoberta STM32L4 para nó de IoT    | [X-CUBE-CLOUD](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32cube-expansion-packages/x-cube-cloud.html) <br/> [X-CUBE-AZURE](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32cube-expansion-packages/x-cube-azure.html) <br/> [P-NUCLEO-AZURE](https://www.st.com/content/st_com/en/products/evaluation-tools/solution-evaluation-tools/communication-and-connectivity-solution-eval-boards/p-nucleo-azure1.html) <br/> [FP-CLD-AZURE](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32-ode-function-pack-sw/fp-cld-azure1.html)            | [Suporte](https://www.st.com/content/st_com/en/support/support-home.html)
| Texas Instruments   | Launchpad CC3220SF <br/> Launchpad CC3220S <br/> Launchpad MSP432E4      | [Plugin do IoT do Azure para SimpleLink](https://github.com/TexasInstruments/azure-iot-pal-simplelink) | [Fórum de TI E2E](https://e2e.ti.com) <br/> [Fórum de TI E2E para CC3220](https://e2e.ti.com/support/wireless_connectivity/simplelink_wifi_cc31xx_cc32xx/) <br/> [Fórum de TI E2E para MSP432E4](https://e2e.ti.com/support/microcontrollers/msp430/) |

## <a name="next-steps"></a>Próximas etapas

* [SDKs de serviço e dispositivo](iot-hub-devguide-sdks.md)
* [Diretrizes de Portabilidade](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
