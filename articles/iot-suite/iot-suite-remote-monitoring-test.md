---
title: "Simulação de dispositivo na solução de monitoramento remoto – Azure | Microsoft Docs"
description: "Este tutorial mostra como usar o simulador de dispositivo com a solução de monitoramento remoto pré-configurada."
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 09/16/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 732ec45003481b0e2f2eca03b6ae13772d325ef1
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2017
---
# <a name="test-your-solution-with-simulated-devices"></a>Testar sua solução com dispositivos simulados

Este tutorial mostra como personalizar o microsserviço do simulador de dispositivo com a solução de monitoramento remoto pré-configurada. Para mostrar os recursos do simulador de dispositivo, este tutorial usa dois cenários no aplicativo de IoT da Contoso.

No primeiro cenário, a Contoso deseja testar um novo dispositivo de lâmpada inteligente. Para executar os testes, você pode criar um novo dispositivo simulado com as seguintes características:

*Propriedades*

| Nome                     | Valores                      |
| ------------------------ | --------------------------- |
| Cor                    | White, Red, Blue            |
| Brilho               | 0 a 100                    |
| Vida útil restante estimada | Contagem regressiva de 10.000 horas |

*Telemetria*

A tabela a seguir mostra os dados que a lâmpada relata para a nuvem como um fluxo de dados:

| Nome   | Valores      |
| ------ | ----------- |
| Status | "on", "off" |

*Métodos*

A tabela a seguir mostra as ações com suporte no novo dispositivo:

| Nome        |
| ----------- |
| Ligar   |
| Desligar  |

*Estado inicial*

A tabela a seguir mostra o status inicial do dispositivo:

| Nome                     | Valores |
| ------------------------ | -------|
| Cor inicial            | Branco  |
| Brilho inicial       | 75     |
| Vida útil restante inicial   | 10.000 |
| Status de telemetria inicial | "on"   |

No segundo cenário, você adiciona um novo tipo de telemetria no dispositivo **Resfriador** existente da Contoso.

Este tutorial mostra como usar o simulador de dispositivo com a solução de monitoramento remoto pré-configurada:

Neste tutorial, você aprenderá como:

>[!div class="checklist"]
> * Criar um novo tipo de dispositivo
> * Simular o comportamento do dispositivo personalizado
> * Adicionar um novo tipo de dispositivo no painel
> * Enviar telemetria personalizada de um tipo de dispositivo existente

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este tutorial, você precisará de uma instância implantada de solução de monitoramento remoto na sua assinatura do Azure.

Se você ainda não implantou a solução de monitoramento remoto, conclua o tutorial [Deploy the remote monitoring preconfigured solution](iot-suite-remote-monitoring-deploy.md) (Implantar a solução de monitoramento remoto pré-configurada).

<!-- Dominic please this use as your reference https://github.com/Azure/device-simulation-dotnet/wiki/Device-Models -->

## <a name="the-device-simulation-service"></a>O serviço de simulação de dispositivo

O serviço de simulação de dispositivo na solução pré-configurada permite que você faça alterações nos tipos de dispositivo simulados internos e crie novos tipos de dispositivo simulados. Você pode usar tipos de dispositivo personalizados para testar o comportamento da solução de monitoramento remoto antes de conectar os dispositivos físicos à solução.

## <a name="create-a-simulated-device-type"></a>Criar um tipo de dispositivo simulado

A maneira mais fácil de criar um novo tipo de dispositivo no microsserviço de simulação é copiar e modificar um tipo existente. As etapas a seguir mostram como copiar o dispositivo **Resfriador** interno para criar um novo dispositivo **Lâmpada**:

1. Use o seguinte comando para clonar o repositório **device-simulation** do GitHub para o computador local:

    ```cmd/sh
    git clone https://github.com/Azure/device-simulation-dotnet.git
    ```

1. Cada tipo de dispositivo tem um arquivo de modelo JSON e scripts associados na pasta `Services/data/devicemodels`. Copie os arquivos do **Resfriador** para criar os arquivos da **Lâmpada**, conforme é mostrado na tabela a seguir:

    | Fonte                      | Destino                   |
    | --------------------------- | ----------------------------- |
    | chiller-01.json             | lightbulb-01.json             |
    | scripts/chiller-01-state.js | scripts/lightbulb-01-state.js |
    | scripts/reboot-method.js    | scripts/SwitchOn-method.js    |

### <a name="define-the-characteristics-of-the-new-device-type"></a>Definir as características do novo tipo de dispositivo

O arquivo `lightbulb-01.json` define as características do tipo, como a telemetria que ele gera e os métodos com suporte. As seguintes etapas atualizam o arquivo `lightbulb-01.json` para definir o dispositivo **Lâmpada**:

1. No arquivo `lightbulb-01.json`, atualize os metadados do dispositivo, conforme é mostrado no trecho a seguir:

    ```json
    "SchemaVersion": "1.0.0",
    "Id": "lightbulb-01",
    "Version": "0.0.1",
    "Name": "Lightbulb",
    "Description": "Smart lightbulb device.",
    "Protocol": "MQTT",
    ```

1. No arquivo `lightbulb-01.json`, atualize as definições de simulação, conforme é mostrado no trecho a seguir:

    ```json
    "Simulation": {
      "InitialState": {
        "online": true,
        "status": "on"
      },
      "Script": {
        "Type": "javascript",
        "Path": "lightbulb-01-state.js",
        "Interval": "00:00:20"
      }
    },
    ```

1. No arquivo `lightbulb-01.json`, atualize as propriedades do tipo de dispositivo, conforme é mostrado no trecho a seguir:

    ```json
    "Properties": {
      "Type": "Lightbulb",
      "Color": "White",
      "Brightness": 75,
      "EstimatedRemainingLife": 10000
    },
    ```

1. No arquivo `lightbulb-01.json`, atualize as definições de telemetria do tipo de dispositivo, conforme é mostrado no trecho a seguir:

    ```json
    "Telemetry": [
      {
        "Interval": "00:00:20",
        "MessageTemplate": "{\"status\":\"${status}\"}",
        "MessageSchema": {
          "Name": "lightbulb-status;v1",
          "Format": "JSON",
          "Fields": {
            "status": "text"
          }
        }
      }
    ],
    ```

1. No arquivo `lightbulb-01.json`, atualize os métodos do tipo de dispositivo, conforme é mostrado no trecho a seguir:

    ```json
    "CloudToDeviceMethods": {
      "SwitchOn": {
        "Type": "javascript",
        "Path": "SwitchOn-method.js"
      },
      "SwitchOff": {
        "Type": "javascript",
        "Path": "SwitchOff-method.js"
      },
    }
    ```

1. Salve o arquivo `lightbulb-01.json`.

### <a name="simulate-custom-device-behavior"></a>Simular o comportamento do dispositivo personalizado

O arquivo `scripts/lightbulb-01-state.js` define o comportamento de simulação do tipo **Lâmpada**. As seguintes etapas atualizam o arquivo `scripts/lightbulb-01-state.js` para definir o comportamento do dispositivo **Lâmpada**:

1. Edite a definição de estado no arquivo `scripts/lightbulb-01-state.js`, conforme é mostrado no trecho a seguir:

    ```js
    // Default state
    var state = {
      online: true,
      status: "on"
    };
    ```

1. Substitua a função **vary** pela o seguinte função **flip**:

    ```js
    /**
    * Simple formula that sometimes flips the status of the lightbulb
    */
    function flip(value) {
      if (Math.random() < 0.2) {
        return (value == "on") ? "off" : "on"
      }
      return value;
    }
    ```

1. Edite a função **main** para implementar o comportamento, conforme é mostrado no trecho a seguir:

    ```js
    function main(context, previousState) {

      // Restore the global state before generating the new telemetry, so that
      // the telemetry can apply changes using the previous function state.
      restoreState(previousState);

      // Make this flip every so often
      state.status = flip(state.status);

      return state;
    }
    ```

1. Salve o arquivo `scripts/lightbulb-01-state.js`.

O arquivo `scripts/SwitchOn-method.js` implementa o método **Ligar** em um dispositivo **Lâmpada**. As seguintes etapas atualizam o arquivo `scripts/SwitchOn-method.js`:

1. Edite a definição de estado no arquivo `scripts/SwitchOn-method.js`, conforme é mostrado no trecho a seguir:

    ```js
    var state = {
       status: "on"
    };
    ```

1. Para ligar a lâmpada, edite a função **main** da seguinte maneira:

    ```js
    function main(context, previousState) {
        log("Executing lightbulb Switch On method.");
        state.status = "on";
        updateState(state);
    }
    ```

1. Salve o arquivo `scripts/SwitchOn-method.js`.

1. Faça uma cópia do arquivo `scripts/SwitchOn-method.js` chamada `scripts/SwitchOff-method.js`.

1. Para desligar a lâmpada, edite a função **main** no arquivo `scripts/SwitchOff-method.js` da seguinte maneira:

    ```js
    function main(context, previousState) {
        log("Executing lightbulb Switch Off method.");
        state.status = "off";
        updateState(state);
    }
    ```

1. Salve o arquivo `scripts/SwitchOff-method.js`.

### <a name="test-the-lightbulb-device-type"></a>Testar o tipo de dispositivo Lâmpada

Para testar o tipo de dispositivo **Lâmpada**, primeiro você pode testar se o tipo de dispositivo se comporta como esperado ao executar uma cópia local do serviço **device-simulation**. Depois de testar e depurar o novo tipo de dispositivo localmente, você poderá recriar o contêiner e reimplantar o serviço **device-simulation** no Azure.

Para testar e depurar as alterações localmente, consulte [Device simulation overview](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md) (Visão geral da simulação de dispositivo).

Configure o projeto para copiar os novos arquivos do dispositivo **Lâmpada** para o diretório de saída.

Para testar o novo dispositivo em uma solução implantada, consulte um destes artigos:

* [Deploying containers from custom docker-hub account](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#deploying-containers-from-custom-docker-hub-account) (Implantando contêineres da conta docker-hub personalizada)
* [Atualizar um contêiner implantado por meio de cópia manual](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#update-a-deployed-container-via-manual-copy)

Na página **Dispositivos**, você pode provisionar instâncias de seu novo tipo:

![Exibir a lista de simulações disponíveis](media/iot-suite-remote-monitoring-test/devicesmodellist.png)

Você pode exibir a telemetria do dispositivo simulado:

![Exibir telemetria da lâmpada](media/iot-suite-remote-monitoring-test/devicestelemetry.png)

Você pode chamar os métodos **SwitchOn** e **SwitchOff** em seu dispositivo:

![Chamar métodos da lâmpada](media/iot-suite-remote-monitoring-test/devicesmethods.png)

Para criar uma imagem do Docker com o novo tipo de dispositivo para implantação no Azure, consulte [Building a customized Docker image](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md#building-a-customized-docker-image) (Criar uma imagem do Docker personalizada).

## <a name="add-a-new-telemetry-type"></a>Adicionar um novo tipo de telemetria

Esta seção descreve como modificar um tipo de dispositivo simulado existente para dar suporte a um novo tipo de telemetria.

### <a name="locate-the-chiller-device-type-files"></a>Localizar os arquivos do tipo de dispositivo Resfriador

As etapas a seguir mostram como localizar os arquivos que definem o dispositivo **Resfriador** interno:

1. Se você ainda não tiver feito isso, use o seguinte comando para clonar o repositório **device-simulation** do GitHub para o computador local:

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet.git
    ```

1. Cada tipo de dispositivo tem um arquivo de modelo JSON e scripts associados na pasta `data/devicemodels`. Os arquivos que definem o tipo de dispositivo do **Resfriador** simulado são:
    * `data/devicemodels/chiller-01.json`
    * `data/devicemodels/scripts/chiller-01-state.js`

### <a name="specify-the-new-telemetry-type"></a>Especificar o novo tipo de telemetria

As etapas a seguir mostram como adicionar um novo tipo **Temperatura Interna** no tipo de dispositivo **Resfriador**:

1. Abra o arquivo `chiller-01.json` .

1. Atualize o valor de **SchemaVersion** da seguinte maneira:

    ```json
    "SchemaVersion": "1.1.0",
    ```

1. Na seção **InitialState**, adicione as duas definições a seguir:

    ```json
    "internal_temperature": 65.0,
    "internal_temperature_unit": "F",
    ```

1. Na matriz **Telemetria**, adicione a seguinte definição:

    ```json
    {
      "Interval": "00:00:05",
      "MessageTemplate": "{\"internal_temperature\":${internal_temperature},\"internal_temperature_unit\":\"${internal_temperature_unit}\"}",
      "MessageSchema": {
        "Name": "chiller-internal-temperature;v1",
        "Format": "JSON",
        "Fields": {
          "temperature": "double",
          "temperature_unit": "text"
        }
      }
    },
    ```

1. Salve o arquivo `chiller-01.json`.

1. Abra o arquivo `scripts/chiller-01-state.js` .

1. Adicione os seguintes campos à variável **state**:

    ```js
    internal_temperature: 65.0,
    internal_temperature_unit: "F",
    ```

1. Adicione a seguinte linha à função **main**:

    ```js
    state.internal_temperature = vary(65, 2, 15, 125);
    ```

1. Salve o arquivo `scripts/chiller-01-state.js`.

### <a name="test-the-chiller-device-type"></a>Testar o tipo de dispositivo Resfriador

Para testar o tipo de dispositivo **Resfriador** atualizado, primeiro você pode testar se o tipo de dispositivo se comporta como esperado ao executar uma cópia local do serviço **device-simulation**. Depois de testar e depurar o tipo de dispositivo atualizado localmente, você poderá recriar o contêiner e reimplantar o serviço **device-simulation** no Azure.

Quando você executa o serviço **device-simulation** localmente, ele envia a telemetria para a solução de monitoramento remoto. Na página **Dispositivos**, você pode provisionar instâncias de seu tipo atualizado.

Para testar e depurar as alterações localmente, consulte [Running the service with Visual Studio](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md#running-the-service-with-visual-studio) (Executando o serviço com o Visual Studio) ou [Build and Run from the command line](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md#build-and-run-from-the-command-line) (Criar e executar usando a linha de comando).

Para testar o novo dispositivo em uma solução implantada, consulte um destes artigos:

* [Deploying containers from custom docker-hub account](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#deploying-containers-from-custom-docker-hub-account) (Implantando contêineres da conta docker-hub personalizada)
* [Atualizar um contêiner implantado por meio de cópia manual](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#update-a-deployed-container-via-manual-copy)

Na página **Dispositivos**, você pode provisionar instâncias de seu tipo atualizado:

![Adicionar Resfriador atualizado](media/iot-suite-remote-monitoring-test/devicesupdatedchiller.png)

Você pode exibir a nova telemetria **Temperatura interna** do dispositivo simulado.

Para criar uma imagem do Docker com o novo tipo de dispositivo para implantação no Azure, consulte [Building a customized Docker image](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md#building-a-customized-docker-image) (Criar uma imagem do Docker personalizada).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, nós mostramos como:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Criar um novo tipo de dispositivo
> * Simular o comportamento do dispositivo personalizado
> * Adicionar um novo tipo de dispositivo no painel
> * Enviar telemetria personalizada de um tipo de dispositivo existente

Agora que você aprendeu a usar o serviço de simulação de dispositivo, a próxima etapa sugerida é saber como [conectar um dispositivo físico à sua solução de monitoramento remoto](iot-suite-connecting-devices-node.md).

Para obter informações para o desenvolvedor sobre a solução de monitoramento remoto, consulte:

* [Guia de Referência do Desenvolvedor](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Guia de Solução de Problemas do Desenvolvedor](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->