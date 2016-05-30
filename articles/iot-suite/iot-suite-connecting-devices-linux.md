<properties
   pageTitle="Conectar um dispositivo usando o C no Linux | Microsoft Azure"
   description="Descreve como conectar um dispositivo à solução pré-configurada de monitoramento remoto do Azure IoT Suite usando um aplicativo escrito em C em execução no Linux."
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
   ms.date="04/26/2016"
   ms.author="dobett"/>


# Conectar seu dispositivo à solução pré-configurada de monitoramento remoto (Linux)

[AZURE.INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## Compilar e executar a solução de exemplo do C no Linux

1. Para clonar o repositório GitHub dos *SDKs do Microsoft Azure IoT* e instalar o *SDK de dispositivo de IoT do Microsoft Azure para C* em seu ambiente de área de trabalho do Linux, siga as instruções em [Configurar um ambiente de desenvolvimento do Linux][lnk-setup-linux].

2. Abra o arquivo **c/serializer/samples/serializer/remote\_monitoring.c** em um editor de texto.

3. Localize o código a seguir no arquivo:

    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
    ```

4. Substitua **[ID do Dispositivo]** e **[Chave do Dispositivo]** pelos valores do seu dispositivo no painel da solução de monitoramento remoto.

5. Use o **Nome de host do Hub IoT** no painel para substituir **[Nome do Hub IoT]** e **[Sufixo do Hub IoT, ou seja, azure-devices.net]**. Por exemplo, se o seu **Nome de host do Hub IoT** for **contoso.azure devices.net**, substitua **[Nome do Hub IoT]** por **contoso** e substitua **[Sufixo do Hub IoT, ou seja, azure-devices.net]** por **azure devices.net**, como mostrado abaixo:

    ```
    static const char* deviceId = "mydevice";
    static const char* deviceKey = "mykey";
    static const char* hubName = "Contoso";
    static const char* hubSuffix = "azure-devices.net";
    ```

6. Salve suas alterações e compile os exemplos. Para criar o exemplo, você pode executar o script build.sh no diretório **c/build\_all/linux**. O script de compilação cria uma pasta **cmake** para armazenar os programas de exemplo compilados.

7. Execute o aplicativo de exemplo **cmake/serializer/samples/remote\_monitoring/remote\_monitoring**.

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[lnk-setup-linux]: https://github.com/azure/azure-iot-sdks/blob/develop/c/doc/devbox_setup.md#linux

<!---HONumber=AcomDC_0518_2016-->