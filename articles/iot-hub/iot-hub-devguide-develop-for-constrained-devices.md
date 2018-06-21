---
title: Hub IoT desenvolvido para dispositivos restritos| Microsoft Docs
description: Guia do desenvolvedor - diretrizes sobre como desenvolver usando SDKs do IoT do Azure para dispositivos restritos.
services: iot-hub
documentationcenter: c
author: yzhong94
manager: timlt
editor: ''
ms.assetid: 979136db-c92d-4288-870c-f305e8777bdd
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/24/2018
ms.author: yizhon
ms.openlocfilehash: 62065b78e3f8191c6423ba9dba4a8f7d16fad114
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34658843"
---
# <a name="develop-for-constrained-devices-using-azure-iot-sdks"></a>Desenvolver para dispositivos restritos usando SDKs do IoT do Azure

## <a name="develop-using-azure-iot-hub-c-sdk"></a>Desenvolver usando SDK C do Hub IoT
O SDK C do Hub IoT é gravado em ANSI C (C99), tornando-o adequado para operar uma variedade de plataformas com pequeno volume de memória e disco.  A RAM recomendada é de pelo menos 64 KB, mas o tamanho exato do volume de memória depende do protocolo usado, do número de conexões abertas e da plataforma de destino.

O SDK C está disponível em formulário de pacote do apt-get, NuGet e MBED.  Para direcionar dispositivos restritos, convém compilar o SDK localmente para a plataforma de destino. Esta documentação demonstra como remover determinados recursos para reduzir o volume do SDK C usando [cmake][lnk-cmake].  Além disso, esta documentação aborda os modelos de programação de melhores práticas para trabalhar com dispositivos restritos.

### <a name="building-the-c-sdk-for-constrained-devices"></a>Compilar o SDK C para dispositivos restritos
#### <a name="prerequisites"></a>pré-requisitos
Siga este [guia de configuração][lnk-devbox-setup] para preparar o ambiente de desenvolvimento para compilar o SDK C.  Antes de chegar à etapa de compilação com cmake, é possível invocar sinalizadores cmake para remover recursos não utilizados.

#### <a name="remove-additional-protocol-libraries"></a>Remover bibliotecas de protocolo adicionais
Atualmente, o SDK C dá suporte a cinco protocolos: MQTT, MQTT sobre WebSocket, AMQPs, AMQP sobre WebSocket e HTTPS.    A maioria dos cenários requer um ou dois protocolos em execução em um cliente, portanto, é possível remover a biblioteca de protocolos que não está usando do SDK.  Informações adicionais sobre como escolher o protocolo de comunicação apropriado para seu cenário podem ser localizadas neste [documento][lnk-choosing-protocol].  Por exemplo, o MQTT é um protocolo leve que geralmente é mais adequado para dispositivos restritos.

É possível remover bibliotecas AMQP e HTTP usando o comando cmake a seguir:
```
cmake -Duse_amqp=OFF -Duse_http=OFF <Path_to_cmake>
```

#### <a name="remove-sdk-logging-capability"></a>Remover o recurso de log do SDK
O SDK C fornece log extensivo para ajudar na depuração. É possível remover o recurso de log para dispositivos de produção usando o comando cmake a seguir:
```
cmake -Dno_logging=OFF <Path_to_cmake>
```

#### <a name="remove-upload-to-blob-capability"></a>Remover upload para o recurso de blob
É possível carregar arquivos grandes no Armazenamento do Microsoft Azure usando o recurso interno do SDK.  O Hub IoT atua como um dispatcher para uma conta de Armazenamento do Microsoft Azure associada.  É possível usar esse recurso para enviar arquivos de mídia, grandes lotes de telemetria e logs.  Saiba mais sobre carregamento de arquivos com o Hub IoT neste [documento][lnk-hub-file-upload].  Se o aplicativo não exigir essa funcionalidade, você poderá remover esse recurso usando o comando cmake a seguir:
```
cmake -Ddont_use_uploadtoblob=ON <Path_to_cmake>
```
#### <a name="running-strip-on-linux-environment"></a>Execução de faixa no ambiente Linux
Se os binários executarem no sistema Linux, você poderá utilizar o [comando strip][lnk-strip] para reduzir o tamanho do aplicativo final após a compilação.
```
strip -s <Path_to_executable>
```

### <a name="programming-models-for-constrained-devices"></a>Modelos de programação para dispositivos restritos
#### <a name="avoid-using-the-serializer"></a>Evite usar o Serializador
O SDK C possui um [serializador][lnk-serializer], que permite usar tabelas de mapeamento declarativas para definir métodos e propriedades de dispositivo gêmeo.  O serializador foi projetado para simplificar o desenvolvimento, mas adiciona sobrecarga, o que não é ideal para dispositivos restritos.  Nesse caso, considere usar APIs de cliente primitivo e analisar json usando um analisador leve, como [parson][lnk-parson].

#### <a name="use-the-lower-layer-ll"></a>Use a camada inferior (_LL_)
O SDK C dá suporte a dois modelos de programação.  Um conjunto tem APIs com um infixo _LL_, que significa camada inferior.  Esse conjunto de APIs é mais leve e não ativa segmentos de trabalho, o que significa que o usuário deve controlar manualmente o agendamento.  Por exemplo, para o cliente de dispositivo, as APIs _LL_ podem ser encontradas neste [arquivo de cabeçalho](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client_ll.h).  Outro conjunto de APIs sem o índice _LL_ é chamado de camada de conveniência, em que um thread de trabalho é girado automaticamente.  Por exemplo, as APIs da camada de conveniência para o cliente de dispositivo podem ser encontradas neste [arquivo de cabeçalho](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client.h).  Para dispositivos restritos em que cada thread extra pode receber uma porcentagem substancial de recursos do sistema, considere o uso de APIs _LL_.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre a arquitetura de SDK C do IoT do Azure:
-   [Código-fonte do SDK C do IoT do Azure](https://github.com/Azure/azure-iot-sdk-c/)
-   [SDK do dispositivo IoT do Azure para introdução C](https://docs.microsoft.com/azure/iot-hub/iot-hub-device-sdk-c-intro)

------
[lnk-cmake]: https://cmake.org/
[lnk-devbox-setup]:  https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md
[lnk-choosing-protocol]: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-protocols
[lnk-hub-file-upload]: https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-devguide-file-upload
[lnk-strip]: https://en.wikipedia.org/wiki/Strip_(Unix)
[lnk-serializer]: https://github.com/Azure/azure-iot-sdk-c/tree/master/serializer
[lnk-parson]: https://github.com/kgabis/parson