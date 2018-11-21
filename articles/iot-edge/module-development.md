---
title: Desenvolver módulos para o Azure IoT Edge | Microsoft Docs
description: Saiba como criar módulos personalizados para o Azure IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/05/2017
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: cb97e2cf6d554753f64afc76de84f43e38443909
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2018
ms.locfileid: "51567223"
---
# <a name="understand-the-requirements-and-tools-for-developing-iot-edge-modules"></a>Entender os requisitos e as ferramentas para desenvolvimento de módulos do IoT Edge

Este artigo explica quais funcionalidades estão disponíveis ao escrever aplicativos que são executados como módulo do IoT Edge e como aproveitar esses recursos.

## <a name="iot-edge-runtime-environment"></a>Ambiente de tempo de execução do IoT Edge
O tempo de execução do IoT Edge fornece a infraestrutura para integrar a funcionalidade de vários módulos do IoT Edge e implantá-los nos dispositivos IoT Edge. Em um nível alto, qualquer programa pode ser empacotado como um módulo do IoT Edge. No entanto, para aproveitar ao máximo as funcionalidades de gerenciamento e comunicação do IoT Edge, um programa em execução em um módulo pode se conectar ao hub do IoT Edge local, integrado no tempo de execução do IoT Edge.

## <a name="using-the-iot-edge-hub"></a>Usar o hub do IoT Edge
O hub do IoT Edge fornece duas funcionalidades principais: proxy para o Hub IoT e comunicações locais.

### <a name="iot-hub-primitives"></a>Primitivos do Hub IoT
O Hub IoT vê uma instância de módulo de forma análoga a um dispositivo, no sentido de que:

* ele tem um módulo gêmeo que é distinto e isolado do [dispositivo gêmeo](../iot-hub/iot-hub-devguide-device-twins.md) e dos outros módulos gêmeos desse dispositivo;
* ele pode enviar [mensagens de dispositivo para nuvem](../iot-hub/iot-hub-devguide-messaging.md);
* ele pode receber [métodos diretos](../iot-hub/iot-hub-devguide-direct-methods.md) direcionados especificamente para sua identidade.

No momento, um módulo não pode receber mensagens de nuvem para dispositivo nem usar o recurso de carregamento de arquivo.

Ao gravar um módulo, você pode usar o [Azure IoT Device SDK](../iot-hub/iot-hub-devguide-sdks.md) para se conectar ao hub IoT Edge e usar a funcionalidade acima como faria ao usar o IoT Hub com um aplicativo de dispositivo, a única diferença sendo que, de seu back-end de aplicativo, você deve se referir à identidade do módulo em vez da identidade do dispositivo.

Consulte [Desenvolver e implantar um módulo IoT Edge em um dispositivo simulado](tutorial-csharp-module.md) para obter um exemplo de um aplicativo de módulo que envia mensagens de dispositivo para nuvem e usa o módulo gêmeo.

### <a name="device-to-cloud-messages"></a>Mensagens do dispositivo para a nuvem
Para habilitar o processamento complexo de mensagens de dispositivo para nuvem, o hub do IoT Edge fornece o roteamento declarativo de mensagens entre módulos e entre módulos e Hub IoT. O roteamento declarativo permite que os módulos interceptem e processem mensagens enviadas por outros módulos e os propaguem em pipelines complexos. O artigo [Composição do módulo](module-composition.md) explica como compor módulos em pipelines complexos usando rotas.

Um módulo IoT Edge, em oposição a um aplicativo de dispositivo Hub IoT normal, pode receber mensagens de dispositivo para nuvem que estão sendo intermediadas por proxy por seu hub IoT Edge local para processá-las.

O hub IoT Edge propaga as mensagens para o seu módulo com base nas rotas declarativas descritas no artigo [Module composition](module-composition.md). Ao desenvolver um módulo IoT Edge, você pode receber essas mensagens definindo manipuladores de mensagens.

Para simplificar a criação de rotas, o IoT Edge adiciona o conceito de pontos de extremidade de *entrada* e *saída* do módulo. Um módulo pode receber todas as mensagens de dispositivo para nuvem roteadas a ele sem especificar nenhuma entrada e pode enviar mensagens de dispositivo para nuvem sem especificar nenhuma saída.
Usar entradas e saídas explícitas, porém, torna as regras de roteamento mais simples de entender. Para obter mais informações sobre regras de roteamento e pontos de extremidade de entrada e saídos para os módulos, consulte [composição do módulo](module-composition.md).

Por fim, as mensagens de dispositivo para nuvem tratadas pelo hub do Edge são marcadas com as seguintes propriedades de sistema:

| Propriedade | DESCRIÇÃO |
| -------- | ----------- |
| $connectionDeviceId | A ID do dispositivo do cliente que enviou a mensagem |
| $connectionDeviceId | A ID do módulo que enviou a mensagem |
| $inputName | A entrada que recebeu esta mensagem. Pode ser um vazia. |
| $outputName | A saída usada para enviar a mensagem. Pode ser um vazia. |

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>Conectar-se ao hub do IoT Edge a partir de um módulo
Conectar-se ao hub IoT Edge local a partir de um módulo envolve duas etapas: 
1. Use a cadeia de conexão fornecida pelo tempo de execução do IoT Edge quando o módulo é iniciado.
2. Verifique se que seu aplicativo aceita o certificado apresentado pelo hub do IoT Edge nesse dispositivo.

A cadeia de conexão a ser usada é injetada pelo tempo de execução do IoT Edge na variável de ambiente `EdgeHubConnectionString`. Isso a disponibiliza para qualquer programa que deseje usá-la.

De maneira semelhante, o certificado a ser usado para validar a conexão de hub do IoT Edge é injetado pelo tempo de execução do IoT Edge em um arquivo cujo caminho está disponível na variável de ambiente `EdgeModuleCACertificateFile`.

## <a name="next-steps"></a>Próximas etapas

Depois de desenvolver um módulo, aprenda como [Implantar e monitorar os módulos do IoT Edge na escala](how-to-deploy-monitor.md).

