---
title: Conectar um Raspberry Pi ao aplicativo Azure IoT Central (Python) | Microsoft Docs
description: Como um desenvolvedor de dispositivos, saiba como conectar um Raspberry Pi ao aplicativo Azure IoT Central usando Python.
author: dominicbetts
ms.author: dobett
ms.date: 01/23/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: b5632db57e902eef76860f85de6e76f85861090a
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/17/2018
ms.locfileid: "45728956"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-python"></a>Conectar um Raspberry Pi ao aplicativo Azure IoT Central (Python)

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

Este artigo descreve como um desenvolvedor de dispositivos conecta um Raspberry Pi ao aplicativo Microsoft Azure IoT Central usando a linguagem de programação Python.

## <a name="before-you-begin"></a>Antes de começar

Para concluir as etapas neste artigo, você precisa do seguinte:

* Um aplicativo Azure IoT Central criado a partir do modelo de aplicativo de **Devkits de Exemplo**. Para obter mais informações, consulte [Criar o aplicativo Azure IoT Central](howto-create-application.md).
* Um dispositivo Raspberry Pi executando o sistema operacional Raspbian. É necessário ter um monitor, teclado e mouse conectado ao Raspberry Pi para acessar o ambiente da GUI. O Raspberry Pi deve ser capaz de [conectar-se à Internet](https://www.raspberrypi.org/learning/software-guide/wifi/).
* Opcionalmente, um complemento [Sense Hat](https://www.raspberrypi.org/products/sense-hat/) para o Raspberry Pi. Esta placa coleta dados de telemetria de vários sensores para enviar ao aplicativo Azure IoT Central. Se não tiver uma placa **Sense Hat**, você poderá usar um emulador (disponível como parte da imagem do Raspberry Pi).

## <a name="sample-devkits-application"></a>Aplicativo **Devkits de exemplo**

Um aplicativo criado a partir do modelo de aplicativo de **Devkits de Exemplo** inclui um modelo de dispositivo **Raspberry Pi** com as características a seguir: 

- Telemetria que contém as medidas para o dispositivo **Umidade**, **Temperatura**, **Pressão**, **Magnômetro** (medido ao longo dos eixos X, Y e Z), **Acelerômetro** (medido ao longo dos eixos X, Y e Z) e **Giroscópio** (medido ao longo dos eixos X, Y, Z).
- Configurações mostrando **Tensão**, **Corrente**,**Velocidade do Ventilador** e uma alternância de **IV**.
- Propriedades contendo a propriedade de dispositivo **número do dado** e a propriedade de nuvem **local**.


Para obter detalhes completos sobre a configuração do modelo de dispositivo, veja os [detalhes do modelo de Dispositivo do Raspberry PI](howto-connect-raspberry-pi-python.md#raspberry-pi-device-template-details)
    

## <a name="add-a-real-device"></a>Adicionar um dispositivo real

No aplicativo Azure IoT Central, adicione um dispositivo real do modelo de dispositivo **Raspberry Pi** e anote os detalhes da conexão do dispositivo (**ID do escopo, ID do dispositivo, Chave Primária**). Para obter mais informações, consulte [Adicionar um dispositivo real ao aplicativo Azure IoT Central](tutorial-add-device.md).


### <a name="configure-the-raspberry-pi"></a>Configurar o Raspberry Pi

As etapas a seguir descrevem como baixar e configurar o aplicativo Python de exemplo do GitHub. Este aplicativo de exemplo:

* Envia dados de telemetria e valores de propriedade para o Azure IoT Central.
* Responde a alterações de configuração feitas no Azure IoT Central.

Para configurar o dispositivo [siga as instruções passo a passo no GitHub.](http://aka.ms/iotcentral-docs-Raspi-releases)


> [!NOTE]
> Para obter mais informações sobre o exemplo Python do Raspberry Pi, consulte o arquivo [Leiame](http://aka.ms/iotcentral-docs-Raspi-releases) no GitHub.


1. Depois que o dispositivo estiver configurado, o dispositivo deverá começar a enviar dados para o Azure IoT Central momentaneamente.
1. No aplicativo Azure IoT Central, você observa como o código em execução no Raspberry Pi interage com o aplicativo:

    * Na página **Medidas** do dispositivo real, é possível ver a telemetria enviada do Raspberry Pi. Se estiver usando o **Emulador Sense HAT**, será possível modificar os valores de telemetria na GUI no Raspberry Pi.
    * Na página **Propriedades**, é possível ver o valor da propriedade do **Número Impresso**.
    * Na página **Configurações**, é possível alterar várias configurações no Raspberry Pi, como tensão e velocidade da ventoinha. Quando o Raspberry Pi reconhece a alteração, a configuração é mostrada como **sincronizada** no Azure IoT Central.


## <a name="raspberry-pi-device-template-details"></a>Detalhes de modelo do Dispositivo Raspberry PI

Um aplicativo criado a partir do modelo de aplicativo de **Devkits de Exemplo** inclui um modelo de dispositivo **Raspberry Pi** com as características a seguir:

### <a name="telemetry-measurements"></a>Medidas de telemetria

| Nome do campo     | Unidades  | Mínimo | Máximo | Casas decimais |
| -------------- | ------ | ------- | ------- | -------------- |
| umidade       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| pressão       | hPa    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | mg     | -2000   | 2000    | 0              |
| accelerometerY | mg     | -2000   | 2000    | 0              |
| accelerometerZ | mg     | -2000   | 2000    | 0              |
| gyroscopeX     | mdps   | -2000   | 2000    | 0              |
| gyroscopeY     | mdps   | -2000   | 2000    | 0              |
| gyroscopeZ     | mdps   | -2000   | 2000    | 0              |

### <a name="settings"></a>Configurações

Configurações numéricas

| Nome de exibição | Nome do campo | Unidades | Casas decimais | Mínimo | Máximo | Inicial |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Voltagem      | setVoltage | Volts | 0              | 0       | 240     | 0       |
| Atual      | setCurrent | Amps  | 0              | 0       | 100     | 0       |
| Velocidade da ventoinha    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

Configurações de alternância

| Nome de exibição | Nome do campo | Texto ativado | Texto desativado | Inicial |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | ATIVADO      | DESATIVADO      | Desativar     |

### <a name="properties"></a>propriedades

| Tipo            | Nome de exibição | Nome do campo | Tipo de dados |
| --------------- | ------------ | ---------- | --------- |
| Propriedade de dispositivo | Número impresso   | dieNumber  | número    |
| Texto            | Local padrão     | location   | N/D       |

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a conectar um Raspberry Pi ao aplicativo Azure IoT Central, as próximas etapas sugeridas são apresentadas:

* [Conectar um aplicativo cliente Node.js genérico ao Azure IoT Central](howto-connect-nodejs.md)
