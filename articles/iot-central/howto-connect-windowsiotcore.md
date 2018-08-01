---
title: Conectar um dispositivo Windows IoT Core ao aplicativo Azure IoT Central | Microsoft Docs
description: Como um desenvolvedor de dispositivos, saiba como conectar um dispositivo MXChip IoT DevKit ao aplicativo Azure IoT Central.
author: miriambrus
ms.author: mriamb
ms.date: 04/09/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 39eb6f137750f7f741c88dcdf9a55f34d24eaa59
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2018
ms.locfileid: "39205741"
---
# <a name="connect-a-windows-iot-core-device-to-your-azure-iot-central-application"></a>Conectar um dispositivo Windows IoT Core ao aplicativo Azure IoT Central

Este artigo descreve como um desenvolvedor de dispositivos conecta um dispositivo Windows IoT Core ao aplicativo Microsoft Azure IoT Central.

## <a name="before-you-begin"></a>Antes de começar

Para concluir as etapas neste artigo, você precisa do seguinte:

1. Um aplicativo Azure IoT Central criado a partir do modelo de aplicativo de **Devkits de Exemplo**. Para obter mais informações, consulte [Criar o aplicativo Azure IoT Central](howto-create-application.md).
2. Um dispositivo executando o sistema operacional Windows 10 IoT Core. Para este passo a passo, vamos usar um Raspberry Pi.


## <a name="sample-devkits-application"></a>Aplicativo **Devkits de exemplo**

Um aplicativo criado a partir do modelo de aplicativo de **Devkits de Exemplo** inclui um modelo de dispositivo **Windows IoT Core** com as características a seguir: 

- Telemetria que contém as medidas para o dispositivo **Umidade**, **Temperatura** e **Pressão**. 
- Configurações mostrando a **Velocidade do Ventilador**.
- Propriedades contendo a propriedade de dispositivo **número do dado** e a propriedade de nuvem **local**.


Para obter detalhes completos sobre a configuração do modelo de dispositivo, veja os [Detalhes do modelo de Dispositivo do Windows IoT Core](howto-connect-windowsiotcore.md#windows-iot-core-device-template-details)

## <a name="add-a-real-device"></a>Adicionar um dispositivo real

No aplicativo Azure IoT Central, adicione um dispositivo real do modelo de dispositivo **Windows IoT Core** e anote a cadeia de conexão do dispositivo. Para obter mais informações, consulte [Adicionar um dispositivo real ao aplicativo Azure IoT Central](tutorial-add-device.md).

### <a name="prepare-the-windows-iot-core-device"></a>Preparar o dispositivo Windows IoT Core

Para configurar um dispositivo Windows IoT Core, siga o guia passo a passo em [Configurar um dispositivo Windows IoT Core] (https://github.com/Microsoft/microsoft-iot-central-firmware/tree/master/WindowsIoT#setup-a-physical-device).

### <a name="add-a-real-device"></a>Adicionar um dispositivo real

No aplicativo Azure IoT Central, adicione um dispositivo real do modelo de dispositivo **Windows IoT Core** e anote a cadeia de conexão do dispositivo. Para obter mais informações, consulte [Adicionar um dispositivo real ao aplicativo Azure IoT Central](tutorial-add-device.md).

## <a name="prepare-the-windows-10-iot-core-device"></a>Preparar o dispositivo Windows 10 IoT Core

### <a name="what-youll-need"></a>O que será necessário

Para configurar um dispositivo físico do Windows 10 IoT Core, você precisará primeiro ter um dispositivo executando o Windows 10 IoT Core. Saiba como configurar um dispositivo do Windows 10 IoT Core [aqui](https://developer.microsoft.com/en-us/windows/iot/getstarted/prototype/setupdevice).

Além disso, será necessário um aplicativo cliente que possa comunicar-se com o Azure IoT Central. É possível criar seu próprio aplicativo personalizado utilizando a SDK do Azure e implantá-lo no dispositivo usando o Visual Studio, ou pode baixar a partir de um [exemplo pré-criado](https://developer.microsoft.com/en-us/windows/iot/samples) e simplesmente implantá-lo e executá-lo no dispositivo. 

### <a name="deploying-the-sample-client-application"></a>Implantar o aplicativo cliente de exemplo

Para implantar o aplicativo cliente da etapa anterior no dispositivo Windows 10 IoT para prepará-lo:

**Assegure-se de que a cadeia de conexão esteja armazenada no dispositivo para o aplicativo cliente usar**
* Na área de trabalho, salve a cadeia de conexão em um arquivo de texto nomeado connection.string.iothub.
* Copie o arquivo de texto para a pasta de documentos do dispositivo: `[device-IP-address]\C$\Data\Users\DefaultAccount\Documents\connection.string.iothub`

Após fazer isso, será necessário abrir o [Portal de Dispositivos do Windows](https://docs.microsoft.com/en-us/windows/iot-core/manage-your-device/deviceportal), digitando em http://[device-IP-address]:8080 em qualquer navegador.

A partir disso e, conforme mostrado abaixo, poderá ser conveniente:
1. Expandir o nó "Aplicativos" à esquerda.
2. Clicar em "Exemplos de execução rápida".
3. Clicar em "Cliente Hub IoT".
4. Clicar em "Implantar e executar".

![Gif do Cliente Hub IoT no Portal de Dispositivos do Windows](./media/howto-connect-windowsiotcore/iothubapp.gif)

Quando concluído com êxito, o aplicativo iniciará no dispositivo e será semelhante a:

![Captura de tela do aplicativo Cliente Hub IoT](./media/howto-connect-windowsiotcore/IoTHubForegroundClientScreenshot.png)

No Azure IoT Central, é possível ver como o código em execução no Raspberry Pi interage com o aplicativo:

* Na página **Medidas** do dispositivo real, é possível ver a telemetria.
* Na página **Propriedades**, é possível ver o valor da propriedade do Número Impresso relatado.
* Na página **Configurações**, é possível alterar várias configurações no Raspberry Pi, como voltagem e velocidade da ventoinha.

## <a name="download-the-source-code"></a>Fazer o download do código-fonte

Se você quiser explorar e modificar o código-fonte do aplicativo cliente, será possível baixá-lo do GitHub [aqui](https://github.com/Microsoft/Windows-iotcore-samples/tree/develop/Samples/Azure/IoTHubClients). Se você planeja modificar o código, será necessário seguir essa instruções no arquivo Leiame [aqui](https://github.com/Microsoft/Windows-iotcore-samples) do sistema operacional de área de trabalho.

> [!NOTE]
> Se o **git** não estiver instalado no ambiente de desenvolvimento, será possível baixá-lo a partir de [https://git-scm.com/download](https://git-scm.com/download).

## <a name="windows-iot-core-device-template-details"></a>Detalhes do modelo do dispositivo Windows IoT Core

Um aplicativo criado a partir do modelo de aplicativo de **Devkits de Exemplo** inclui um modelo de dispositivo **Windows IoT Core** com as características a seguir:

### <a name="telemetry-measurements"></a>Medidas de telemetria

| Nome do campo     | Unidades  | Mínimo | Máximo | Casas decimais |
| -------------- | ------ | ------- | ------- | -------------- |
| umidade       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| pressão       | hPa    | 260     | 1260    | 0              |

### <a name="settings"></a>Configurações

Configurações numéricas

| Nome de exibição | Nome do campo | Unidades | Casas decimais | Mínimo | Máximo | Inicial |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Velocidade da ventoinha    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |


### <a name="properties"></a>propriedades

| Tipo            | Nome de exibição | Nome do campo | Tipo de dados |
| --------------- | ------------ | ---------- | --------- |
| Propriedade de dispositivo | Número impresso   | dieNumber  | número    |
| Texto            | Localização     | location   | N/D       |
