## <a name="create-a-device-identity"></a>Criar uma identidade do dispositivo
Nesta seção, você use uma ferramenta Node chamada [Gerenciador do Hub IoT][iot-hub-explorer] para criar uma identidade de dispositivo para este tutorial.

1. Execute o seguinte em seu ambiente de linha de comando:
   
    npm install -g iothub-explorer@latest
2. Em seguida, execute o seguinte comando para fazer logon em seu hub, lembrando de substituir `{service connection string}` pela cadeia de conexão do Hub IoT copiado anteriormente:
   
    iothub-explorer login "{cadeia de conexão do serviço}"
3. Finalmente, crie uma nova identidade de dispositivo chamada `myDeviceId` com o comando:
   
    iothub-explorer create myDeviceId --connection-string

Anote a cadeia de conexão do dispositivo no resultado. Essa cadeia de conexão é usada pelo aplicativo do dispositivo para se conectar ao seu Hub IoT como um dispositivo.

![][img-identity]

Consulte [Introdução ao Hub IoT][lnk-getstarted] para conferir uma forma de criar identidades de dispositivos por meio de programação.

<!-- images and links -->
[img-identity]: media/iot-hub-get-started-create-device-identity/devidentity.png

[iot-hub-explorer]: https://github.com/Azure/azure-iot-sdks/tree/master/tools/iothub-explorer

[lnk-getstarted]: ../articles/iot-hub/iot-hub-csharp-csharp-getstarted.md


<!--HONumber=Nov16_HO3-->


