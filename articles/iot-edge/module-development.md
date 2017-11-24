---
title: "Desenvolver módulos para o Azure IoT Edge | Microsoft Docs"
description: "Saiba como criar módulos personalizados para o Azure IoT Edge"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 10/05/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 95b1d5d4e5e11f96b6abb17f0aeba935cc65512d
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2017
---
# <a name="understand-the-requirements-and-tools-for-developing-iot-edge-modules---preview"></a>Entender os requisitos e as ferramentas para desenvolvimento de módulos do IoT Edge - versão prévia

Este artigo explica quais funcionalidades estão disponíveis ao escrever aplicativos que são executados como módulo do IoT Edge e como aproveitar esses recursos.

## <a name="iot-edge-runtime-environment"></a>Ambiente de tempo de execução do IoT Edge
O tempo de execução do IoT Edge fornece a infraestrutura para integrar a funcionalidade de vários módulos do IoT Edge e implantá-los nos dispositivos IoT Edge. Em um nível alto, qualquer programa pode ser empacotado como um módulo do IoT Edge. No entanto, para aproveitar ao máximo as funcionalidades de gerenciamento e comunicação do IoT Edge, um programa em execução em um módulo pode se conectar ao hub do IoT Edge local, integrado no tempo de execução do IoT Edge.

## <a name="using-the-iot-edge-hub"></a>Usar o hub do IoT Edge
O hub do IoT Edge fornece duas funcionalidades principais: proxy para o Hub IoT e comunicações locais.

### <a name="iot-hub-primitives"></a>Primitivos do Hub IoT
O Hub IoT enxerga um módulo de instância de maneira semelhante a um dispositivo, no sentido de que ele:

* possui um módulo gêmeo, que é distinto e isolado do [dispositivo gêmeo][lnk-devicetwin] e dos outros módulos gêmeos do dispositivo;
* pode enviar [mensagens do dispositivo para a nuvem][lnk-iothub-messaging];
* pode receber [métodos diretos][lnk-methods] destinados especificamente à sua identidade.

No momento, um módulo não pode receber mensagens de nuvem para dispositivo nem usar o recurso de carregamento de arquivo.

Ao escrever um módulo, você pode simplesmente usar o [SDK do Dispositivo Azure IoT] [ lnk-devicesdk] para se conectar ao hub do IoT Edge e usar a funcionalidade acima como faria ao usar o Hub IoT com um aplicativo de dispositivo, a única diferença é que, a partir do back-end do seu aplicativo, você tem que referir-se à identidade do módulo, em vez da identidade do dispositivo.

Consulte [Desenvolver e implantar um módulo do IoT Edge em um dispositivo simulado][lnk-tutorial2] para obter um exemplo de um aplicativo de módulo que envia mensagens do dispositivo para nuvem e usa o módulo gêmeo.

### <a name="device-to-cloud-messages"></a>Mensagens do dispositivo para a nuvem
Para habilitar o processamento complexo de mensagens de dispositivo para nuvem, o hub do IoT Edge fornece o roteamento declarativo de mensagens entre módulos e entre módulos e Hub IoT.
Isso permite que os módulos interceptem e processem as mensagens enviadas por outros módulos e as propaguem em pipelines compleoas.
O artigo [Composição do módulo][lnk-module-comp] explica como compor os módulos em pipelines complexos usando rotas.

Um módulo do IoT Edge, diferentemente de um aplicativo de dispositivo do Hub IoT normal, pode receber mensagens de dispositivo para nuvem que estão sendo enviadas por proxy pelo seu hub do IoT Edge local, para processá-las.

O hub do IoT Edge propaga as mensagens para o módulo com base nas rotas declarativas descritas no artigo [Composição do módulo][lnk-module-comp]. Ao desenvolver um módulo do IoT Edge, você pode receber essas mensagens definindo manipuladores de mensagens, conforme mostrado no tutorial [Desenvolver e implantar um módulo do IoT Edge em um dispositivo simulado][lnk-tutorial2].

Para simplificar a criação de rotas, o IoT Edge adiciona o conceito de pontos de extremidade de *entrada* e *saída* do módulo. Um módulo pode receber todas as mensagens de dispositivo para nuvem roteadas a ele sem especificar nenhuma entrada e pode enviar mensagens de dispositivo para nuvem sem especificar nenhuma saída.
Usar entradas e saídas explícitas, porém, torna as regras de roteamento mais simples de entender. Consulte [Composição do módulo][lnk-module-comp] para obter mais informações sobre regras de roteamento e pontos de extremidade de entrada e saídos para módulos.

Por fim, as mensagens de dispositivo para nuvem tratadas pelo hub do Edge são marcadas com as seguintes propriedades de sistema:

| Propriedade | Descrição |
| -------- | ----------- |
| $connectionDeviceId | A ID do dispositivo do cliente que enviou a mensagem |
| $connectionDeviceId | A ID do módulo que enviou a mensagem |
| $inputName | A entrada que recebeu esta mensagem. Pode ser um vazia. |
| $outputName | A saída usada para enviar a mensagem. Pode ser um vazia. |

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>Conectar-se ao hub do IoT Edge a partir de um módulo
Conectar-se ao hub do IoT Edge local a partir de um módulo envolve duas etapas: usar a cadeia de conexão fornecida pelo tempo de execução do IoT Edge quando o módulo é iniciado e verificar se seu aplicativo aceita o certificado apresentado pelo hub do IoT Edge nesse dispositivo.

A cadeia de conexão a ser usada é injetada pelo tempo de execução do IoT Edge na variável de ambiente `EdgeHubConnectionString`. Isso a disponibiliza para qualquer programa que deseje usá-la.

De maneira semelhante, o certificado a ser usado para validar a conexão de hub do IoT Edge é injetado pelo tempo de execução do IoT Edge em um arquivo cujo caminho está disponível na variável de ambiente `EdgeModuleCACertificateFile`.

O tutorial [Desenvolver e implantar um módulo do IoT Edge em um dispositivo simulado][lnk-tutorial2] mostra como certificar-se de que o certificado está no repositório do computador no aplicativo do seu módulo. Obviamente, qualquer outro método para confiar nas conexões usando esse certificado funcionará.

## <a name="packaging-as-an-image"></a>Empacotar como uma imagem
Os módulos do IoT Edge são empacotados como imagens do Docker.
Você pode usar uma cadeia de ferramentas do Docker diretamente ou o Visual Studio Code, conforme mostrado no tutorial [Desenvolver e implantar um módulo do IoT Edge em um dispositivo simulado][lnk-tutorial2].

## <a name="next-steps"></a>Próximas etapas

Depois de desenvolver um módulo, saiba como [Implantar e monitorar os módulos do IoT Edge em escala][lnk-howto-deploy].

[lnk-devicesdk]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-devicetwin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-iothub-messaging]: ../iot-hub/iot-hub-devguide-messaging.md
[lnk-methods]: ../iot-hub/iot-hub-devguide-direct-methods.md
[lnk-tutorial2]: tutorial-csharp-module.md
[lnk-module-comp]: module-composition.md
[lnk-howto-deploy]: how-to-deploy-monitor.md
