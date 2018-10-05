---
title: Entenda os módulos do Azure IoT Edge | Microsoft Docs
description: Saiba mais sobre os módulos do Azure IoT Edge e como eles são configurados
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 09/21/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5d80b6438569e74ee254d27e0061443a87efc6ce
ms.sourcegitcommit: 42405ab963df3101ee2a9b26e54240ffa689f140
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47423384"
---
# <a name="understand-azure-iot-edge-modules"></a>Entenda os módulos do Azure IoT Edge

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

Na implementação, as imagens de módulos existem como imagens de contêiner em um repositório, e as instâncias de módulo são contêineres em dispositivos. 

<!--
As use cases for Azure IoT Edge grow, new types of module images and instances will be created. For example, resource constrained devices cannot run containers so may require module images that exist as dynamic link libraries and instances that are executables. 
-->

## <a name="module-identities"></a>Identidades de módulo

Quando uma nova instância de módulo é criada pelo tempo de execução do IoT Edge, a instância é associada a uma identidade de módulo correspondente. A identidade de módulo é armazenada no Hub IoT e utilizada como o escopo de endereçamento e segurança para todas as comunicações locais e de nuvem dessa instância específica de módulo.
A identidade associada a uma instância de módulo depende da identidade do dispositivo no qual a instância está em execução, e do nome fornecido para esse módulo em sua solução. Por exemplo, se você chamar `insight` um módulo que usa um Azure Stream Analytics e implantá-lo em um dispositivo chamado `Hannover01`, o tempo de execução do IoT Edge criará uma identidade de módulo correspondente chamada `/devices/Hannover01/modules/insight`.

Claramente, em cenários nos quais você precisa implantar uma imagem de módulo várias vezes no mesmo dispositivo, é possível implantar a mesma imagem várias vezes com nomes diferentes.

![As identidades de módulo são exclusivas][2]

## <a name="module-twins"></a>Módulos gêmeos

Cada instância de módulo também tem um módulo gêmeo correspondente que você pode usar para configurar a instância de módulo. A instância e o gêmeo são associados entre si por meio da identidade de módulo. 

O módulo gêmeo é um documento JSON que armazena as propriedades de configuração e as informações do módulo. Esse conceito é comparável ao conceito de [dispositivo gêmeo][lnk-device-twin] do Hub IoT. A estrutura de um módulo gêmeo é exatamente igual a de um dispositivo gêmeo. As APIs usadas para interagir com os dois tipos de gêmeos também são as mesmas. A única diferença entre os dois é a identidade usada para instanciar o SDK do cliente. 

```csharp
// Create a ModuleClient object. This ModuleClient will act on behalf of a 
// module since it is created with a module’s connection string instead 
// of a device connection string. 
ModuleClient client = new ModuleClient.CreateFromEnvironmentAsync(settings); 
await client.OpenAsync(); 
 
// Get the module twin 
Twin twin = await client.GetTwinAsync(); 
```

## <a name="offline-capabilities"></a>Funcionalidades offline

O Azure IoT Edge tem suporte para operações offline nos seus dispositivos IoT Edge. Esses recursos são limitados por enquanto. 

Módulos IoT Edge podem estar offline por períodos estentidos por tempo indeterminado enquanto os requisitos seguintes forem cumpridos: 

* **A mensagem de TTL (vida útil) não expirou**. O valor padrão para mensagem TTL é de duas horas, mas pode ser mudado para maior ou menor na Store e outras configurações nas configurações do IoT Edge Hub. 
* **Módulos não precisam reautenticar com o hub do IoT Edge quando estiverem offline**. Módulos podem apenas autenticar com Hubs Edge que têm uma conexão ativa com um Hub do IoT. Módulos precisam reautenticar se eles forem reiniciados por qualquer razão. Módulos podem ainda enviar mensagens ao Hub Edge depois que seu token SAS expirar. Quando a conectividade voltar, o Hub Edge solicita um novo token do módulo e valida com o Hub IoT. Se for bem sucedido, o Hub Edge envia ao módulo mensagens que ele armazenou, mesmo as mensagens que foram enviadas enquanto o token do módulo estava expirado. 
* **O módulo que enviar as mensagens enquanto offline ainda é funcional quando a conectividade volta**. Ao reconectar para Hub IoT, o Hub Edge precisa validar um novo token de módulo (se o anterior expirou) antes que possa enviar as mensagens de módulo. Se o módulo não estiver disponível para fornecer um novo token, o Hub Edge não pode agir mas mensagens armazenadas no módulo. 
* **O Hub Edge tem espaço de disco para armazenar as mensagens**. Por padrão, mensagens são armazenadas no Hub Edge do sistema de arquivos do contêiner. Há uma opção de configuração para especificar um volume montado para armazenar as mensagens. Em qualquer caso, precisa ter espaço disponível para armazenar as mensagens para a entrega designada ao Hub IoT.  

Recursos off-line adicionais estão disponíveis na visualização pública. Para obter mais informações, consulte [compreender estendido recursos offline para o IoT Edge dispositivos, módulos e dispositivos filho](offline-capabilities.md).

## <a name="next-steps"></a>Próximas etapas
 - [Entender os requisitos e as ferramentas para desenvolvimento de módulos do IoT Edge][lnk-mod-dev]
 - [Entenda o tempo de execução do Azure IoT Edge e sua arquitetura][lnk-runtime]

<!-- Images -->
[1]: ./media/iot-edge-modules/image_instance.png
[2]: ./media/iot-edge-modules/identity.png

<!-- Links -->
[lnk-device-identity]: ../iot-hub/iot-hub-devguide-identity-registry.md
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-runtime]: iot-edge-runtime.md
[lnk-mod-dev]: module-development.md
