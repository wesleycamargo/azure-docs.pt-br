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
ms.openlocfilehash: e9c2d18a518bd5c98fcc35efdb0dff36970a49b2
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34629058"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-python"></a>Conectar um Raspberry Pi ao aplicativo Azure IoT Central (Python)

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

Este artigo descreve como um desenvolvedor de dispositivos conecta um Raspberry Pi ao aplicativo Microsoft Azure IoT Central usando a linguagem de programação Python.

## <a name="before-you-begin"></a>Antes de começar

Para concluir as etapas neste artigo, você precisa do seguinte:

* Um aplicativo Azure IoT Central criado a partir do modelo de aplicativo de **Devkits de Exemplo**. Para obter mais informações, consulte [Criar o aplicativo Azure IoT Central](howto-create-application.md).
* Um dispositivo Raspberry Pi executando o sistema operacional Raspbian. É necessário ter um monitor, teclado e mouse conectado ao Raspberry Pi para acessar o ambiente da GUI. O Raspberry Pi deve ser capaz de [conectar-se à Internet](https://www.raspberrypi.org/learning/software-guide/wifi/).
* Opcionalmente, um complemento [Sense Hat](https://www.raspberrypi.org/products/sense-hat/) para o Raspberry Pi. Esta placa coleta dados de telemetria de vários sensores para enviar ao aplicativo Azure IoT Central. Caso não tenha uma placa **Sense Hat**, você poderá usar um emulador.

Um aplicativo criado a partir do modelo de aplicativo de **Devkits de Exemplo** inclui um modelo de dispositivo **Raspberry Pi** com as características a seguir:

### <a name="telemetry-measurements"></a>Medidas de telemetria

| Nome do campo     | Unidades  | Mínimo | Máximo | Casas decimais |
| -------------- | ------ | ------- | ------- | -------------- |
| umidade       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |
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

| type            | Nome de exibição | Nome do campo | Tipo de dados |
| --------------- | ------------ | ---------- | --------- |
| Propriedade de dispositivo | Número impresso   | dieNumber  | número    |
| Texto            | Local padrão     | location   | N/D       |

### <a name="add-a-real-device"></a>Adicionar um dispositivo real

No aplicativo Azure IoT Central, adicione um dispositivo real do modelo de dispositivo **Raspberry Pi** e anote a cadeia de conexão do dispositivo. Para obter mais informações, consulte [Adicionar um dispositivo real ao aplicativo Azure IoT Central](tutorial-add-device.md).

## <a name="configure-the-raspberry-pi"></a>Configurar o Raspberry Pi

As etapas a seguir descrevem como baixar e configurar o aplicativo Python de exemplo do GitHub. Este aplicativo de exemplo:

* Envia dados de telemetria e valores de propriedade para o Azure IoT Central.
* Responde a alterações de configuração feitas no Azure IoT Central.

> [!NOTE]
> Para obter mais informações sobre o exemplo Python do Raspberry Pi, consulte o arquivo [Leiame](https://github.com/Microsoft/microsoft-iot-central-firmware/blob/master/RaspberryPi/README.md) no GitHub.

1. Utilize o navegador da Web na área de trabalho do Raspberry Pi para navegar até a página de [versões do firmware do Azure IoT Central](https://github.com/Microsoft/microsoft-iot-central-firmware/releases).

1. Baixe o arquivo zip que contém o firmware mais recente para a pasta base no Raspberry Pi. O nome do arquivo é semelhante a `RaspberryPi-IoTCentral-X.X.X.zip`.

1. Para descompactar o arquivo de firmware, use o **Gerenciador de Arquivos** na área de trabalho do Raspberry Pi. Clique com o botão direito do mouse no arquivo zip e escolha **Extrair aqui**. Essa operação cria uma pasta chamada `RaspberryPi-IoTCentral-X.X.X` na pasta base.

1. Caso não tenha uma placa **Sense Hat** anexada ao Raspberry Pi, será necessário habilitar o emulador:
    1. Em **Gerenciador de Arquivos**, na pasta `RaspberryPi-IoTCentral-X.X.X` clique com o botão direito do mouse em **config.iot** e escolha **Editor de Texto**.
    1. Altere a linha `"simulateSenseHat": false,` para `"simulateSenseHat": true,`.
    1. Salve as alterações e feche o **Editor de Texto**.

1. Inicie uma sessão de **Terminal** e use o comando `cd` para navegar até a pasta que você criou na etapa anterior.

1. Para iniciar o aplicativo de exemplo em execução, digite `./start.sh` na janela do **Terminal**. Se você estiver usando o **Emulador Sense HAT**, a GUI será exibida. É possível usar a GUI para alterar os valores de telemetria enviados ao aplicativo Azure IoT Central.

1. A janela do **Terminal** exibe uma mensagem semelhante a `Device information being served at http://192.168.0.60:8080`. A URL pode ser diferente no ambiente. Copie a URL e use o navegador da Web para navegar até a página de configuração:

    ![Configurar dispositivo](media/howto-connect-raspberry-pi-python/configure.png)

1. Insira uma cadeia de conexão do dispositivo que você anotou ao adicionar um dispositivo real ao aplicativo Azure IoT Central. Em seguida, escolha **Configurar Dispositivo**. Você verá uma mensagem **Dispositivo configurado, o dispositivo deve iniciar o envio de dados para o Azure IoT Central momentaneamente**.

1. No aplicativo Azure IoT Central, você observa como o código em execução no Raspberry Pi interage com o aplicativo:

    * Na página **Medidas** do dispositivo real, é possível ver a telemetria enviada do Raspberry Pi. Se estiver usando o **Emulador Sense HAT**, será possível modificar os valores de telemetria na GUI no Raspberry Pi.
    * Na página **Propriedades**, é possível ver o valor da propriedade do **Número Impresso**.
    * Na página **Configurações**, é possível alterar várias configurações no Raspberry Pi, como tensão e velocidade da ventoinha. Quando o Raspberry Pi reconhece a alteração, a configuração é mostrada como **sincronizada** no Azure IoT Central.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a conectar um Raspberry Pi ao aplicativo Azure IoT Central, as próximas etapas sugeridas são apresentadas:

* [Conectar um aplicativo cliente Node.js genérico ao Azure IoT Central](howto-connect-nodejs.md)