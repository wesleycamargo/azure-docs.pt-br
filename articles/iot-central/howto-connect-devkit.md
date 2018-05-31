---
title: Conectar um dispositivo DevKit ao aplicativo Azure IoT Central | Microsoft Docs
description: Como um desenvolvedor de dispositivos, saiba como conectar um dispositivo MXChip IoT DevKit ao aplicativo Azure IoT Central.
services: iot-central
author: tanmaybhagwat
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: 4c7074e5e7d3858919f3fc17005fea4f8dce1560
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34200734"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>Conectar um dispositivo MXChip IoT DevKit ao aplicativo Azure IoT Central

Este artigo descreve como um desenvolvedor de dispositivos conecta um dispositivo MXChip IoT DevKit (DevKit) ao aplicativo Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Antes de começar

Para concluir as etapas neste artigo, você precisa do seguinte:

1. Um aplicativo Azure IoT Central criado a partir do modelo de aplicativo de **Devkits de Exemplo**. Para obter mais informações, consulte [Criar o aplicativo Azure IoT Central](howto-create-application.md).
1. Um dispositivo DevKit. Para adquirir um dispositivo DevKit, visite [MXChip IoT DevKit](http://mxchip.com/az3166).

Um aplicativo criado a partir do modelo de aplicativo de **Devkits de Exemplo** inclui um modelo de dispositivo **MXChip** com as características a seguir:

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

### <a name="states"></a>Estados 

| NOME          | Nome de exibição   | NORMAL | CUIDADO | PERIGO | 
| ------------- | -------------- | ------ | ------- | ------ | 
| DeviceState   | Estado do dispositivo   | Verde  | Laranja  | Vermelho    | 

### <a name="events"></a>Eventos 

| NOME             | Nome de exibição      | 
| ---------------- | ----------------- | 
| ButtonBPressed   | Botão B Pressionado  | 

### <a name="add-a-real-device"></a>Adicionar um dispositivo real

No aplicativo Azure IoT Central, adicione um dispositivo real do modelo de dispositivo **MXChip** e anote a cadeia de conexão do dispositivo. Para obter mais informações, consulte [Adicionar um dispositivo real ao aplicativo Azure IoT Central](tutorial-add-device.md).

## <a name="prepare-the-devkit-device"></a>Preparar o dispositivo DevKit

> [!TIP]
> Para obter diretrizes de solução de problemas de dispositivo DevKit, consulte [Introdução ao IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/get-started/).

Para preparar o dispositivo DevKit:

1. Baixe o último firmware do Azure IoT Central pré-criado para o MXChip na página [versões](https://github.com/Azure/iot-central-firmware/releases) no GitHub. O nome de arquivo de download na página de versões é semelhante a `AZ3166-IoT-Central-X.X.X.bin`.

1. Conecte o dispositivo DevKit ao computador de desenvolvimento usando um cabo USB. No Windows, uma janela do explorador de arquivos abre em uma unidade mapeada para o armazenamento no dispositivo DevKit. Por exemplo, a unidade pode ser chamada **AZ3166 (D:)**.

1. Arraste o arquivo **iotCentral.bin** para a janela da unidade. Quando a cópia for concluída, o dispositivo será reiniciado com o novo firmware.

1. Quando o dispositivo DevKit for reiniciado, a tela a seguir será exibida:

    ```
    Connect HotSpot:
    AZ3166_??????
    go-> 192.168.0.1 
    PIN CODE xxxxx
    ```

    > [!NOTE]
    > Se a tela exibir qualquer outra coisa, pressione o botão **Reiniciar** no dispositivo. 

1. O dispositivo agora está no modo de AP (Ponto de Acesso). É possível conectar esse ponto de acesso WiFi a partir do computador ou dispositivo móvel.

1. No computador, telefone ou tablet, conecte o nome da rede WiFi exibido na tela do dispositivo. Quando essa rede for conectada, não haverá acesso à Internet. Esse estado é esperado e somente ficará conectado a essa rede por um curto período de tempo, enquanto você configura o dispositivo.

1. Abra o navegador da Web e navegue até [http://192.168.0.1/start](http://192.168.0.1/start). A página da Web a seguir é exibida:

    ![Página de configuração do dispositivo](media/howto-connect-devkit/configpage.png)

    Na página da Web: 
    - adicione o nome da rede WiFi 
    - a senha da rede WiFi 
    - CÓDIGO PIN mostrado no LCD do dispositivo 
    - a cadeia de conexão do dispositivo. 
      É possível localizar uma cadeia de conexão @ `https://apps.iotcentral.com` -> `Device Explorer` -> `Device` -> `Select or Create a new Real Device` -> `Connect this device` (no canto superior direito) 
    - Selecione todas as medidas de telemetria disponíveis! 

1. Após escolher **Configurar Dispositivo**, você verá esta página:

    ![Dispositivo configurado](media/howto-connect-devkit/deviceconfigured.png)

1. Pressione o botão **Reiniciar** no dispositivo.

> [!NOTE]
> Para reconfigurar o dispositivo para usar uma rede WiFi, uma cadeia de conexão ou uma medida de telemetria diferente, pressione os botões **A** e **B** no painel simultaneamente. Se isso não funcionar, pressione o botão **reiniciar** e tente novamente. 

## <a name="view-the-telemetry"></a>Exibir a Telemetria

Quando o dispositivo DevKit for reiniciado, a tela no dispositivo exibirá:

* O número de mensagens de telemetria enviadas.
* O número de falhas.
* O número de propriedades desejadas recebidas e o número de propriedades relatadas enviadas.

Agite o dispositivo para incrementar o número de propriedades relatadas enviadas. O dispositivo envia um número aleatório como a propriedade de dispositivo **Número impresso**.

É possível exibir as medidas de telemetria e os valores das propriedades relatadas e definir as configurações no Azure IoT Central:

1. Use **Device Explorer** para navegar até a página **Medidas** do dispositivo MXChip real que você adicionou:

    ![Navegue para o dispositivo real](media/howto-connect-devkit/realdevice.png)

1. Na página **Medidas**, é possível ver a telemetria proveniente do dispositivo MXChip:

    ![Exibir telemetria do dispositivo real](media/howto-connect-devkit/realtelemetry.png)

1. Na página **Propriedades**, é possível ver o último número impresso relatado pelo dispositivo:

    ![Exibir propriedades do dispositivo](media/howto-connect-devkit/deviceproperties.png)

1. Na página **Configurações**, é possível atualizar as configurações no dispositivo MXChip:

    ![Exibir as configurações do dispositivo](media/howto-connect-devkit/settings.png)

## <a name="download-the-source-code"></a>Fazer o download do código-fonte

Se você quiser explorar e modificar o código do dispositivo, poderá baixá-lo do GitHub. Se você planeja modificar o código, siga essas instruções para [preparar o ambiente de desenvolvimento](https://microsoft.github.io/azure-iot-developer-kit/docs/get-started/#step-5-prepare-the-development-environment) do sistema operacional de área de trabalho.

Para baixar o código-fonte, execute o comando a seguir no computador desktop:

```cmd/sh
git clone https://github.com/Azure/iot-central-firmware
```

O comando anterior baixa o código-fonte para uma pasta chamada `iot-central-firmware`. 

> [!NOTE]
> Se o **git** não estiver instalado no ambiente de desenvolvimento, será possível baixá-lo a partir de [https://git-scm.com/download](https://git-scm.com/download).

## <a name="review-the-code"></a>Examine o código

Use o Visual Studio Code, que foi instalado quando você preparou o ambiente de desenvolvimento, para abrir a pasta `AZ3166` pasta em `iot-central-firmware`: 

![Visual Studio Code](media/howto-connect-devkit/vscodeview.png)

Para ver como a telemetria é enviada ao aplicativo Azure IoT Central, abra o arquivo **main_telemetry.cpp** na pasta de origem.

A função `buildTelemetryPayload` cria a carga de telemetria JSON usando dados dos sensores no dispositivo.

A função `sendTelemetryPayload` chama `sendTelemetry` no **iotHubClient.cpp** para enviar a carga JSON ao Hub IoT que o aplicativo Azure IoT Central usa.

Para ver como os valores de propriedade são relatados para o aplicativo Azure IoT Central, abra o arquivo **main_telemetry.cpp** na pasta de origem.

A função `telemetryLoop` envia a propriedade relatada **doubleTap** quando o acelerômetro detecta um toque duplo. Ele usa a função `sendReportedProperty` no arquivo de origem **iotHubClient.cpp**.

O código no arquivo de origem **iotHubClient.cpp** usa funções de [ SDKs do Microsoft Azure IoT e bibliotecas para C](https://github.com/Azure/azure-iot-sdk-c) para interagir com o Hub IoT.

Para obter informações sobre como modificar, compilar e carregar o código de exemplo no dispositivo, consulte o arquivo **readme.md** na pasta `AZ3166`.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como conectar um dispositivo DevKit ao aplicativo Azure IoT Central, as próximas etapas sugeridas são apresentadas:

* [Preparar e conectar um Raspberry Pi](howto-connect-raspberry-pi-python.md)