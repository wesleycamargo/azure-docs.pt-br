---
title: "Gerenciamento de dispositivos IoT do Azure com a extensão de IoT para a CLI do Azure 2.0 | Microsoft Docs"
description: "Use a ferramenta extensão de IoT para a CLI do Azure 2.0 para o gerenciamento de dispositivos Hub IoT do Azure, que contam com métodos Diretos e as opções de gerenciamento de propriedades desejadas do Gêmeo."
services: iot-hub
documentationcenter: 
author: chrissie926
manager: timlt
tags: 
keywords: gerenciamento de dispositivos iot do azure, gerenciamento de dispositivos hub iot do azure, iot de gerenciamento de dispositivos, gerenciamento de dispositivos hub iot
ms.assetid: b34f799a-fc14-41b9-bf45-54751163fffe
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/16/2018
ms.author: menchi
ms.openlocfilehash: 760a6a30513308aa59c5e253e3b91e28cf9e3241
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/18/2018
---
# <a name="use-the-iot-extension-for-azure-cli-20-for-azure-iot-hub-device-management"></a>Use a extensão de IoT da CLI do Azure 2.0 para gerenciamento de dispositivo Hub IoT do Azure

![Diagrama de ponta a ponta](media/iot-hub-get-started-e2e-diagram/2.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[A extensão de IoT para a CLI do Azure 2.0](https://github.com/Azure/azure-iot-cli-extension) é uma nova extensão IoT de código aberto que adiciona aos recursos da [CLI do Azure 2.0](https://docs.microsoft.com/en-us/cli/azure/overview?view=azure-cli-latest) inclui comandos para interagir com pontos de extremidade de gerenciamento de gerente de recursos do Azure. A CLI do Azure 2.0 inclui comandos para interagir com pontos de extremidade de gerenciamento e gerente de recursos do Azure. Por exemplo, é possível usar a CLI do Azure 2.0 para criar um Hub IoT ou uma VM do Azure. Uma extensão CLI permite que um serviço do Azure amplie a CLI do Azure, fornecendo acesso a recursos específicos de serviços adicionais. A extensão de IoT fornece aos desenvolvedores de IoT acesso de linha de comando para todos os Hub IoT, IoT Edge, e recursos do Serviço de Provisionamento de Dispositivos no Hub IoT.

| Opção de gerenciamento          | Tarefa                                                                                                                            |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------|
| Métodos diretos             | Faça com que um dispositivo comece ou pare de enviar mensagens ou seja reinicializado.                                        |
| Propriedades desejadas do gêmeo    | Coloque um dispositivo em alguns estados, como definir um LED como verde ou definir o intervalo de envio de telemetria como 30 minutos.         |
| Propriedades relatadas do gêmeo   | Obtenha o estado relatado de um dispositivo. Por exemplo, o dispositivo relata que o LED está piscando no momento.                                    |
| Marcações do gêmeo                  | Armazene os metadados específicos do dispositivo na nuvem. Por exemplo, o local de implantação de uma máquina de vendas.                         |
| Consultas de dispositivo gêmeo        | Consulte todos os dispositivos gêmeos para recuperar aqueles com condições arbitrárias, como identificar os dispositivos que estão disponíveis para uso. |

Para obter explicações mais detalhadas sobre as diferenças e diretrizes sobre como usar essas opções, consulte [Diretrizes de comunicação do dispositivo para a nuvem](iot-hub-devguide-d2c-guidance.md) e [Diretrizes de comunicação da nuvem para o dispositivo](iot-hub-devguide-c2d-guidance.md).

> [!NOTE]
> Dispositivos gêmeos são documentos JSON que armazenam informações do estado do dispositivo (metadados, configurações e condições). O Hub IoT persiste um dispositivo gêmeo para cada dispositivo que você conecta a ele. Para obter mais informações sobre dispositivos gêmeos, consulte [Introdução aos dispositivos gêmeos](iot-hub-node-node-twin-getstarted.md).

## <a name="what-you-learn"></a>O que você aprenderá

Você aprenderá a usar a extensão de IoT para a CLI do Azure 2.0 com várias opções de gerenciamento em seu computador de desenvolvimento.

## <a name="what-you-do"></a>O que fazer

Execute a CLI do Azure 2.0 e a extensão do IoT para a CLI do Azure 2.0 com várias opções de gerenciamento.

## <a name="what-you-need"></a>O que você precisa

- Tutorial [Configurar seu dispositivo](iot-hub-raspberry-pi-kit-node-get-started.md) concluído que aborda os seguintes requisitos:
  - Uma assinatura ativa do Azure.
  - Um hub IoT do Azure em sua assinatura.
  - O aplicativo cliente que envia mensagens para o hub IoT do Azure.

- Verifique se o dispositivo está sendo executado com o aplicativo cliente durante este tutorial.

- [Python 2.7x ou Python 3.x](https://www.python.org/downloads/)

- Instale a CLI do Azure 2.0. Uma maneira simples de instalar no Windows é fazer o download e instalar o [MSI](https://aka.ms/InstallAzureCliWindows). Você também pode seguir as instruções de instalação no [Microsoft Docs](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) para configurar a CLI do Azure 2.0 no seu ambiente. A versão 2.0 da CLI do Azure deve ser 2.0.24 ou superior. Use `az –version` para validar. 

- Instalar a extensão de IoT. A maneira mais simples é executar `az extension add --name azure-cli-iot-ext`. [O Leiame da extensão de IoT](https://github.com/Azure/azure-iot-cli-extension/blob/master/README.md) descreve várias maneiras de instalar a extensão.


## <a name="login-to-your-azure-account"></a>Faça logon em sua conta do Azure

Faça logon na sua conta do Azure executando o comando a seguir:

```bash
az login
```

## <a name="use-the-iot-extension-for-azure-cli-20-with-direct-methods"></a>Use a extensão de IoT da CLI do Azure 2.0 com métodos diretos

```bash
az iot hub invoke-device-method --device-id <your device id> --hub-name <your hub name> --method-name <the method name> --method-payload <the method payload>
```

## <a name="use-the-iot-extension-for-azure-cli-20-with-twins-desired-properties"></a>Use a extensão de IoT da CLI do Azure 2.0 com propriedades desejadas duplas

Defina um intervalo de propriedade desejado = 3000 executando o seguinte comando:

```bash
az iot hub device-twin update -n <your hub name> -d <your device id> --set properties.desired.interval = 3000
```

Essa propriedade pode ser lido do seu dispositivo.

## <a name="use-the-iot-extension-for-azure-cli-20-with-twins-reported-properties"></a>Use a extensão de IoT da CLI do Azure 2.0 com propriedades reportadas duplas

Obtenha as propriedades relatadas do dispositivo executando o seguinte comando:

```bash
az iot hub device-twin update -n <your hub name> -d <your device id> --set properties.reported.interval = 3000
```

Uma das propriedades é $metadata.$lastUpdated, que mostra a última vez que esse dispositivo enviou ou recebeu uma mensagem.

## <a name="use-the-iot-extension-for-azure-cli-20-with-twins-tags"></a>Use a extensão de IoT da CLI do Azure 2.0 com marcas duplas

Exiba as marcações e as propriedades do dispositivo executando o seguinte comando:

```bash
az iot hub device-twin show --hub-name <your hub name> --device-id <your device id>
```

Adicione uma função de campo = temperature&humidity ao dispositivo executando o seguinte comando:

```bash
az iot hub device-twin update --hub-name <your hub name> --device-id <your device id> --set tags = '{"role":"temperature&humidity"}}'
```

## <a name="use-the-iot-extension-for-azure-cli-20-with-device-twins-queries"></a>Use a extensão de IoT para a CLI do Azure 2.0 com consultas duplas de dispositivos

Consulte dispositivos com uma marcação de função = 'temperature&humidity' executando o seguinte comando:

```bash
az iot hub query --hub-name <your hub name> --query-command "SELECT * FROM devices WHERE tags.role = 'temperature&humidity'"
```

Consulte todos os dispositivos, exceto aqueles com uma marcação de função = 'temperature&humidity', executando o seguinte comando:

```bash
az iot hub query --hub-name <your hub name> --query-command "SELECT * FROM devices WHERE tags.role != 'temperature&humidity'"
```

## <a name="next-steps"></a>Próximas etapas

Você aprendeu como monitorar mensagens do dispositivo para a nuvem e enviar mensagens da nuvem para o dispositivo entre o dispositivo IoT e o Hub IoT do Azure.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]