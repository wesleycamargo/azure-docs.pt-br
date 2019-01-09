---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: iot-accelerators
author: dominicbetts
ms.service: iot-accelerators
ms.topic: include
ms.date: 07/26/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 0b2b6814ad0b55d8f56998f6d4c9b6bb88663e04
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/15/2018
ms.locfileid: "53443958"
---
## <a name="state-behavior"></a>Comportamento de Estado

A seção de [Simulação](../articles/iot-accelerators/iot-accelerators-remote-monitoring-device-schema.md#simulation) do esquema de modelo do dispositivo define o estado interno de um dispositivo simulado:

- `InitialState` define valores iniciais para todas as propriedades do objeto de estado do dispositivo.
- `Script` identifica um arquivo JavaScript que executa em uma programação para atualizar o estado do dispositivo.

O exemplo a seguir mostra a definição do objeto de estado do dispositivo para um dispositivo resfriador simulado:

```json
"Simulation": {
  "InitialState": {
    "online": true,
    "temperature": 75.0,
    "temperature_unit": "F",
    "humidity": 70.0,
    "humidity_unit": "%",
    "pressure": 150.0,
    "pressure_unit": "psig",
    "simulation_state": "normal_pressure"
  },
  "Interval": "00:00:05",
  "Scripts": {
    "Type": "javascript",
    "Path": "chiller-01-state.js"
  }
}
```

O estado do dispositivo simulado, conforme definido na seção `InitialState`, é mantido na memória pelo serviço de simulação. As informações de estado são passadas como entrada para a função `main` definida em **chiller-01-state.js**. Neste exemplo, o serviço de simulação executa o arquivo **chiller-01-state.js** a cada cinco segundos. O script pode modificar o estado do dispositivo simulado.

A seguir, a estrutura de uma função `main` típica:

```javascript
function main(context, previousState, previousProperties) {

  // Use the previous device state to
  // generate the new device state
  // returned by the function.

  return state;
}
```

O parâmetro `context` tem as seguintes propriedades:

- `currentTime`como uma cadeia de caracteres com formato `yyyy-MM-dd'T'HH:mm:sszzz`
- `deviceId`, por exemplo `Simulated.Chiller.123`
- `deviceModel`, por exemplo `Chiller`

O `state` parâmetro contém o estado do dispositivo mantido pelo serviço de simulação do dispositivo. Esse valor é o `state` objeto retornado pela chamada anterior para `main`.

O exemplo a seguir mostra uma implementação típica do `main` método para controlar o estado do dispositivo mantido pelo serviço de simulação:

```javascript
// Default state
var state = {
  online: true,
  temperature: 75.0,
  temperature_unit: "F",
  humidity: 70.0,
  humidity_unit: "%",
  pressure: 150.0,
  pressure_unit: "psig",
  simulation_state: "normal_pressure"
};

function restoreState(previousState) {
  // If the previous state is null, force a default state
  if (previousState !== undefined && previousState !== null) {
      state = previousState;
  } else {
      log("Using default state");
  }
}

function main(context, previousState, previousProperties) {

  restoreState(previousState);

  // Update state

  return state;
}
```

A exemplo a seguir mostra como o `main` método pode simular valores de telemetria que variam ao longo do tempo:

```javascript
/**
 * Simple formula generating a random value
 * around the average and between min and max
 */
function vary(avg, percentage, min, max) {
  var value = avg * (1 + ((percentage / 100) * (2 * Math.random() - 1)));
  value = Math.max(value, min);
  value = Math.min(value, max);
  return value;
}


function main(context, previousState, previousProperties) {

    restoreState(previousState);

    // 75F +/- 5%,  Min 25F, Max 100F
    state.temperature = vary(75, 5, 25, 100);

    // 70% +/- 5%,  Min 2%, Max 99%
    state.humidity = vary(70, 5, 2, 99);

    log("Simulation state: " + state.simulation_state);
    if (state.simulation_state === "high_pressure") {
        // 250 psig +/- 25%,  Min 50 psig, Max 300 psig
        state.pressure = vary(250, 25, 50, 300);
    } else {
        // 150 psig +/- 10%,  Min 50 psig, Max 300 psig
        state.pressure = vary(150, 10, 50, 300);
    }

    return state;
}
```

Você pode exibir o [chiller-01-state.js](https://github.com/Azure/device-simulation-dotnet/blob/master/Services/data/devicemodels/scripts/chiller-01-state.js) completo no GitHub.

## <a name="method-behavior"></a>Comportamento do método

A seção [CloudToDeviceMethods](../articles/iot-accelerators/iot-accelerators-remote-monitoring-device-schema.md#cloudtodevicemethods) do esquema de modelo do dispositivo define os métodos que um dispositivo simulado responde.

O exemplo a seguir mostra a lista de métodos suportados por um dispositivo resfriador simulado:

```json
"CloudToDeviceMethods": {
  "Reboot": {
    "Type": "javascript",
    "Path": "Reboot-method.js"
  },
  "FirmwareUpdate": {
    "Type": "javascript",
    "Path": "FirmwareUpdate-method.js"
  },
  "EmergencyValveRelease": {
    "Type": "javascript",
    "Path": "EmergencyValveRelease-method.js"
  },
  "IncreasePressure": {
    "Type": "javascript",
    "Path": "IncreasePressure-method.js"
  }
}
```

Cada método tem um arquivo JavaScript associado que implementa o comportamento do método.

O estado do dispositivo simulado, conforme definido na seção `InitialState` do esquema, é mantido na memória pelo serviço de simulação. As informações de estado são passadas como entrada para a função `main` definida no arquivo JavaScript quando o método é chamado. O script pode modificar o estado do dispositivo simulado.

A seguir, a estrutura de uma função `main` típica:

```javascript
function main(context, previousState, previousProperties) {

}
```

O parâmetro `context` tem as seguintes propriedades:

- `currentTime`como uma cadeia de caracteres com formato `yyyy-MM-dd'T'HH:mm:sszzz`
- `deviceId`, por exemplo `Simulated.Chiller.123`
- `deviceModel`, por exemplo `Chiller`

O `state` parâmetro contém o estado do dispositivo mantido pelo serviço de simulação do dispositivo.

O parâmetro `properties` contém as propriedades do dispositivo que são gravadas como propriedades relatadas para o dispositivo gêmeo do Hub IoT.

Há três funções globais que você pode usar para ajudar a implementar o comportamento do método:

- `updateState` atualizar o estado mantido pelo serviço de simulação.
- `updateProperty` para atualizar a propriedade de um único dispositivo.
- `sleep` pausar a execução para simular uma tarefa demorada.

O exemplo a seguir mostra uma versão abreviada do script **IncreasePressure-method.js** usado pelos dispositivos resfriadores simulados:

```javascript
function main(context, previousState, previousProperties) {

    log("Starting 'Increase Pressure' method simulation (5 seconds)");

    // Pause the simulation and change the simulation mode so that the
    // temperature will fluctuate at ~250 when it resumes
    var state = {
        simulation_state: "high_pressure",
        CalculateRandomizedTelemetry: false
    };
    updateState(state);

    // Increase
    state.pressure = 210;
    updateState(state);
    log("Pressure increased to " + state.pressure);
    sleep(1000);

    // Increase
    state.pressure = 250;
    updateState(state);
    log("Pressure increased to " + state.pressure);
    sleep(1000);

    // Resume the simulation
    state.CalculateRandomizedTelemetry = true;
    updateState(state);

    log("'Increase Pressure' method simulation completed");
}
```

## <a name="debugging-script-files"></a>Depuração dos arquivos de script

Não é possível anexar um depurador ao interpretador Javascript usado pelo serviço de simulação do dispositivo para executar os scripts de estado e método. No entanto, você pode registrar informações no log de serviço. O função `log()` interna permite que você salve informações para controlar e depurar a execução da função.

Se houver um erro de sintaxe, o interpretador falha e grava uma entrada `Jint.Runtime.JavaScriptException` no log do serviço.

O artigo [executando o serviço localmente](https://github.com/Azure/device-simulation-dotnet#running-the-service-locally-eg-for-development-tasks) no GitHub mostra como executar o serviço de simulação de dispositivo localmente. Executar o serviço localmente torna mais fácil depurar seus dispositivos simulados antes de implantá-los à nuvem.