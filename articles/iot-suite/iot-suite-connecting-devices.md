<properties
   pageTitle="Conectar um dispositivo usando C no Windows | Microsoft Azure"
   description="Descreve como conectar um dispositivo à solução pré-configurada de monitoramento remoto do Azure IoT Suite usando um aplicativo escrito em C em execução no Windows."
   services=""
   suite="iot-suite"
   documentationCenter="na"
   authors="dominicbetts"
   manager="timlt"
   editor=""/>

<tags
   ms.service="iot-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/10/2015"
   ms.author="dobett"/>


# Conectar seu dispositivo à solução pré-configurada de monitoramento remoto do IoT Suite

[AZURE.INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## Compilar e executar a solução de exemplo C no Windows

1. Para clonar o repositório de *SDKs do Microsoft Azure IoT* no GitHub e instalar o *SDK de dispositivo do Microsoft Azure IoT para C* em seu ambiente de área de trabalho do Windows, execute as instruções em [Configurando um ambiente de desenvolvimento do Windows][lnk-setup-windows].

2. No Visual Studio 2015, abra a solução **remote\_monitoring.sln** na pasta **c\\serializer\\samples\\remote\_monitoring\\windows** em sua cópia local do repositório.

3. No **Gerenciador de Soluções**, no projeto **remote\_monitoring**, abra o arquivo **remote\_monitoring.c**.

4. Localize o código a seguir no arquivo:

    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
    ```

5. Substitua **[ID do Dispositivo]** e **[Chave do Dispositivo]** pelos valores do seu dispositivo no painel da solução de monitoramento remoto.

6. Use o **Nome de host do Hub IoT** no painel para substituir **[Nome do Hub IoT]** e **[Sufixo do Hub IoT, ou seja, azure-devices.net]**. Por exemplo, se o seu **Nome de host do Hub IoT** for **contoso.azure devices.net**, substitua **[Nome do Hub IoT]** por **contoso** e substitua **[Sufixo do Hub IoT, ou seja, azure-devices.net]** por **azure devices.net**, como mostrado abaixo:

    ```
    static const char* deviceId = "mydevice";
    static const char* deviceKey = "mykey";
    static const char* hubName = "contoso";
    static const char* hubSuffix = "azure-devices.net";
    ```

7. Em **Solution Explorer**, clique com o botão direito do mouse no projeto **remote\_monitoring**, clique em **Depurar** e em **Iniciar nova instância** para compilar e executar o exemplo. O console exibe mensagens à medida que o aplicativo envia a telemetria de exemplo para Hub IoT.

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[lnk-setup-windows]: https://github.com/azure/azure-iot-sdks/blob/develop/c/doc/devbox_setup.md#windows

<!---HONumber=AcomDC_0128_2016-->