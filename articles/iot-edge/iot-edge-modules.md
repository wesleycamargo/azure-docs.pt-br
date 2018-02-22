---
title: "Entenda os módulos do Azure IoT Edge | Microsoft Docs"
description: "Saiba mais sobre os módulos do Azure IoT Edge e como eles são configurados"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 02/15/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 0f3ce7496427b6975eb4ac476e7d1737321ed2e9
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="understand-azure-iot-edge-modules---preview"></a>Entenda os módulos do Azure IoT Edge - versão prévia

O Azure IoT Edge permite que você implante e gerencie a lógica de negócios no Edge na forma de *módulos*. Os módulos do Azure IoT Edge são a menor unidade de computação gerenciada pelo IoT Edge e podem conter serviços do Azure (por exemplo, o Azure Stream Analytics) ou seu próprio código específico à solução. Para entender como os módulos são desenvolvidos, implantados e mantidos, é útil pensar em quatro partes conceituais que compõem um módulo:

* Uma **imagem de módulo** é um pacote contendo o software que define um módulo.
* Uma **instância de módulo** é a unidade específica de computação que executa a imagem de módulo em um dispositivo com IoT Edge. A instância de módulo é iniciada pelo tempo de execução do IoT Edge.
* Uma **identidade de módulo** são informações (incluindo as credenciais de segurança) armazenadas no Hub IoT, que estão associadas a cada instância de módulo.
* Um **módulo gêmeo** é um documento JSON armazenado no Hub IoT, e contém informações de estado para uma instância de módulo, incluindo metadados, configurações e condições. 

## <a name="module-images-and-instances"></a>Instâncias e imagens de módulo

As imagens de módulo do IoT Edge contêm aplicativos que tiram proveito dos recursos de comunicação, segurança e gerenciamento do tempo de execução do IoT Edge. Você pode desenvolver suas próprias imagens de módulo ou exportar uma de um serviço do Azure com suporte, como o Azure Stream Analytics.
As imagens existem na nuvem e podem ser atualizadas, alteradas e implantadas em diferentes soluções. Por exemplo, um módulo que usa aprendizado de máquina para prever a saída da linha de produção como uma imagem separada, em comparação com um módulo que usa a visão do computador para controlar um drone. 

Sempre que uma imagem de módulo é implantada em um dispositivo e iniciada pelo tempo de execução do IoT Edge, uma nova instância desse módulo é criada. Dois dispositivos em diferentes partes do mundo poderiam usar a mesma imagem de módulo. No entanto, cada um teria sua própria instância de módulo quando o módulo fosse iniciado no dispositivo. 

![Imagens de módulo na nuvem - instâncias de módulo em dispositivos][1]

Na implementação, as imagens de módulos existem como imagens de contêiner em um repositório, e as instâncias de módulo são contêineres em dispositivos. À medida que os casos de uso do Azure IoT Edge aumentam, novos tipos de imagens e instâncias de módulo serão criados. Por exemplo, dispositivos com restrição de recursos não podem executar contêineres e, portanto, poderão exigir imagens de módulo que existam como bibliotecas de vínculo dinâmico e instâncias executáveis. 

## <a name="module-identities"></a>Identidades de módulo

Quando uma nova instância de módulo é criada pelo tempo de execução do IoT Edge, a instância é associada a uma identidade de módulo correspondente. A identidade de módulo é armazenada no Hub IoT e utilizada como o escopo de endereçamento e segurança para todas as comunicações locais e de nuvem dessa instância específica de módulo.
A identidade associada a uma instância de módulo depende da identidade do dispositivo no qual a instância está em execução, e do nome fornecido para esse módulo em sua solução. Por exemplo, se você chamar `insight` um módulo que usa um Azure Stream Analytics e implantá-lo em um dispositivo chamado `Hannover01`, o tempo de execução do IoT Edge criará uma identidade de módulo correspondente chamada `/devices/Hannover01/modules/insight`.

Claramente, em cenários nos quais você precisa implantar uma imagem de módulo várias vezes no mesmo dispositivo, é possível implantar a mesma imagem várias vezes com nomes diferentes.

![As identidades de módulo são exclusivas][2]

## <a name="module-twins"></a>Módulos gêmeos

Cada instância de módulo também tem um módulo gêmeo correspondente que você pode usar para configurar a instância de módulo. A instância e o gêmeo são associados entre si por meio da identidade de módulo. 

O módulo gêmeo é um documento JSON que armazena as propriedades de configuração e as informações do módulo. Esse conceito é comparável ao conceito de [dispositivo gêmeo][lnk-device-twin] do Hub IoT. A estrutura de um módulo gêmeo é exatamente igual a de um dispositivo gêmeo. As APIs usadas para interagir com os dois tipos de gêmeos também são as mesmas. A única diferença entre os dois é a identidade usada para instanciar o SDK do cliente. 

```csharp
// Create a DeviceClient object. This DeviceClient will act on behalf of a 
// module since it is created with a module’s connection string instead 
// of a device connection string. 
DeviceClient client = new DeviceClient.CreateFromConnectionString(moduleConnectionString, settings); 
await client.OpenAsync(); 
 
// Get the model twin 
Twin twin = await client.GetTwinAsync(); 
```

## <a name="next-steps"></a>Próximas etapas
 - [Entenda o tempo de execução do Azure IoT Edge e sua arquitetura][lnk-runtime]

<!-- Images -->
[1]: ./media/iot-edge-modules/image_instance.png
[2]: ./media/iot-edge-modules/identity.png

<!-- Links -->
[lnk-device-identity]: ../iot-hub/iot-hub-devguide-identity-registry.md
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-runtime]: iot-edge-runtime.md