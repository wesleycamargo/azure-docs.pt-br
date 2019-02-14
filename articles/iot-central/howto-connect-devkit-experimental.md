---
title: Conectar um dispositivo DevKit ao aplicativo Azure IoT Central | Microsoft Docs
description: Como um desenvolvedor de dispositivos, saiba como conectar um dispositivo MXChip IoT DevKit ao aplicativo Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 02/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 1097d14c824bcdebb9062fcf63b04e9837b0d170
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55811606"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>Conectar um dispositivo MXChip IoT DevKit ao aplicativo Azure IoT Central

Este artigo descreve como um desenvolvedor de dispositivos conecta um dispositivo MXChip IoT DevKit (DevKit) ao aplicativo Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Antes de começar

Para concluir as etapas neste artigo, você precisa do seguinte:

1. Um aplicativo Azure IoT Central criado a partir do modelo de aplicativo de **Devkits de Exemplo**. Para obter mais informações, consulte o [Criar um início rápido de aplicativo](quick-deploy-iot-central-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).
1. Um dispositivo DevKit. Para adquirir um dispositivo DevKit, visite [MXChip IoT DevKit](http://mxchip.com/az3166).

## <a name="sample-devkits-application"></a>Aplicativo Devkits de exemplo

Um aplicativo criado a partir do modelo de aplicativo de **Devkits de Exemplo** inclui um modelo de dispositivo **MXChip** com as características a seguir:

- Telemetria que contém as medidas para o dispositivo **Umidade**, **Temperatura**, **Pressão**, **Magnetômetro** (medido ao longo dos eixos X, Y e Z), **Acelerômetro** (medido ao longo dos eixos X, Y e Z) e **Giroscópio** (medido ao longo dos eixos X, Y, Z).
- Estado que contém uma medida de exemplo para **Estado do Dispositivo**.
- Medição de evento com um evento de **Botão B Pressionado**. 
- Configurações mostrando **Tensão**, **Corrente**, **Velocidade do Ventilador** e uma alternância de **IV**.
- As propriedades contendo a propriedade de dispositivo **número de dado** e **local do dispositivo**, que é uma propriedade local, bem como em uma propriedade de nuvem **Fabricados em**. 

Para obter detalhes completos sobre a configuração, veja [Detalhes do modelo de Dispositivo MXChip](#mxchip-device-template-details)


## <a name="add-a-real-device"></a>Adicionar um dispositivo real

No aplicativo Azure IoT Central, adicione um dispositivo real do modelo de dispositivo **MXChip** e anote os detalhes da conexão do dispositivo (**ID do escopo, ID do dispositivo e Chave Primária**).

1. Adicione um **dispositivo real** do Device Explorer, clique em **+Novo > Real** para adicionar um dispositivo real.

    * Insira a ID do Dispositivo **<span style="color:Red">(deve estar em minúsculas)</span>** ou use a ID do Dispositivo sugerida.
    * Insira o nome do dispositivo ou use o nome sugerido

    ![Adicionar dispositivo](media/howto-connect-devkit-experimental/add-device.png)

1. Obtenha detalhes da conexão como a **ID do escopo, ID do dispositivo e Chave Primária** do dispositivo adicionado, clicando em **Conectar** na página do dispositivo.

    ![Detalhes da conexão](media/howto-connect-devkit-experimental/device-connect.png)

1. Salvar esses detalhes, pois a Internet será desconectada temporariamente enquanto você prepara o dispositivo DevKit.

### <a name="prepare-the-devkit-device"></a>Preparar o dispositivo DevKit

> [!NOTE]
> Se você já tiver usado o dispositivo antes, tiver credenciais de WiFi armazenadas e quiser reconfigurar o dispositivo para usar uma rede WiFi, uma cadeia de conexão ou uma medida de telemetria diferente, pressione os botões **A** e **B** no painel simultaneamente. Se isso não funcionar, pressione o botão **reiniciar** e tente novamente.

#### <a name="to-prepare-the-devkit-device"></a>Para preparar o dispositivo DevKit

1. Baixe o último firmware do Azure IoT Central pré-criado para o MXChip na página [versões](https://aka.ms/iotcentral-docs-MXChip-releases) no GitHub.
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
    > Se a tela exibir qualquer outra diferente dessa, redefina o dispositivo e pressione os botões  **A** e **B** no dispositivo ao mesmo tempo para reinicializar o dispositivo.

1. O dispositivo agora está no modo de AP (Ponto de Acesso). É possível conectar esse ponto de acesso WiFi a partir do computador ou dispositivo móvel.

1. No computador, telefone ou tablet, conecte o nome da rede WiFi exibido na tela do dispositivo. Quando essa rede for conectada, não haverá acesso à Internet. Esse estado é esperado e somente ficará conectado a essa rede por um curto período de tempo, enquanto você configura o dispositivo.

1. Abra o navegador da Web e navegue até [http://192.168.0.1/start](http://192.168.0.1/start). A página da Web a seguir é exibida:

    ![Página de configuração do dispositivo](media/howto-connect-devkit-experimental/configpage.png)

    Na página da Web: 
    - adicione o nome da rede WiFi 
    - a senha da rede WiFi
    - CÓDIGO PIN mostrado no LCD do dispositivo 
    - os detalhes da conexão **ID do escopo, ID do dispositivo e Chave primária** do dispositivo (você já deve ter salvado isso seguindo as etapas)      
    - Selecione todas as medidas de telemetria disponíveis! 

1. Após escolher **Configurar Dispositivo**, você verá esta página:

    ![Dispositivo configurado](media/howto-connect-devkit-experimental/deviceconfigured.png)

1. Pressione o botão **Reiniciar** no dispositivo.


## <a name="view-the-telemetry"></a>Exibir a Telemetria

Quando o dispositivo DevKit for reiniciado, a tela no dispositivo exibirá:

* O número de mensagens de telemetria enviadas.
* O número de falhas.
* O número de propriedades desejadas recebidas e o número de propriedades relatadas enviadas.

> [!NOTE]
> Se o dispositivo parece estar em looping durante a conexão, verifique se o dispositivo está *Bloqueado* no IoT Central e *Desbloqueie* o dispositivo para que ele possa conectar-se ao aplicativo.

Agite o dispositivo para incrementar o número de propriedades relatadas enviadas. O dispositivo envia um número aleatório como a propriedade de dispositivo **Número impresso**.

É possível exibir as medidas de telemetria e os valores das propriedades relatadas e definir as configurações no Azure IoT Central:

1. Use **Device Explorer** para navegar até a página **Medidas** do dispositivo MXChip real que você adicionou:

    ![Navegue para o dispositivo real](media/howto-connect-devkit-experimental/realdevicenew.png)

1. Na página **Medidas**, é possível ver a telemetria proveniente do dispositivo MXChip:

    ![Exibir telemetria do dispositivo real](media/howto-connect-devkit-experimental/devicetelemetrynew.png)

1. Na página **Propriedades**, é possível ver o último número impresso e a localização do dispositivo relatados pelo dispositivo:

    ![Exibir propriedades do dispositivo](media/howto-connect-devkit-experimental/devicepropertynew.png)

1. Na página **Configurações**, é possível atualizar as configurações no dispositivo MXChip:

    ![Exibir as configurações do dispositivo](media/howto-connect-devkit-experimental/devicesettingsnew.png)

1. Na página **Painel**, você pode ver o mapa do local

    ![Exibir o painel do dispositivo](media/howto-connect-devkit-experimental/devicedashboardnew.png)


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

![Visual Studio Code](media/howto-connect-devkit-experimental/vscodeview.png)

Para ver como a telemetria é enviada ao aplicativo Azure IoT Central, abra o arquivo **main_telemetry.cpp** na pasta de origem.

A função `buildTelemetryPayload` cria a carga de telemetria JSON usando dados dos sensores no dispositivo.

A função `sendTelemetryPayload` chama `sendTelemetry` no **iotHubClient.cpp** para enviar a carga JSON ao Hub IoT que o aplicativo Azure IoT Central usa.

Para ver como os valores de propriedade são relatados para o aplicativo Azure IoT Central, abra o arquivo **main_telemetry.cpp** na pasta de origem.

A função `telemetryLoop` envia a propriedade relatada **doubleTap** quando o acelerômetro detecta um toque duplo. Ele usa a função `sendReportedProperty` no arquivo de origem **iotHubClient.cpp**.

O código no arquivo de origem **iotHubClient.cpp** usa funções de [ SDKs do Microsoft Azure IoT e bibliotecas para C](https://github.com/Azure/azure-iot-sdk-c) para interagir com o Hub IoT.

Para obter informações sobre como modificar, compilar e carregar o código de exemplo no dispositivo, consulte o arquivo **readme.md** na pasta `AZ3166`.

## <a name="mxchip-device-template-details"></a>Detalhes do modelo de Dispositivo de MXChip

Um aplicativo criado com base no modelo de aplicativo de Devkits de Exemplo inclui um modelo de dispositivo MXChip com as características a seguir:

### <a name="measurements"></a>Medidas

#### <a name="telemetry"></a>Telemetria 

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


#### <a name="states"></a>Estados 
| NOME          | Nome de exibição   | NORMAL | CUIDADO | PERIGO | 
| ------------- | -------------- | ------ | ------- | ------ | 
| DeviceState   | Estado do dispositivo   | Verde  | Laranja  | Vermelho    | 

#### <a name="events"></a>Eventos 
| NOME             | Nome de exibição      | 
| ---------------- | ----------------- | 
| ButtonBPressed   | Botão B Pressionado  | 

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

| Type            | Nome de exibição | Nome do campo | Tipo de dados |
| --------------- | ------------ | ---------- | --------- |
| Propriedade de dispositivo | Número impresso   | dieNumber  | número    |
| Propriedade de dispositivo | Localização do dispositivo   | location  | location    |
| Texto            | Fabricado em     | manufacturedIn   | N/D       |



## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como conectar um dispositivo DevKit ao aplicativo Azure IoT Central, as próximas etapas sugeridas são apresentadas:

* [Preparar e conectar um Raspberry Pi](howto-connect-raspberry-pi-python.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
