## <a name="create-a-device-identity"></a>Criar uma identidade do dispositivo

Nesta seção, você cria um aplicativo do console do .NET que cria uma identidade do dispositivo no registro de identidade em seu Hub IoT. Um dispositivo não pode se conectar ao Hub IoT, a menos que ele tenha uma entrada no Registro de identidade. Para obter mais informações, consulte a seção "Registro de identidade" do [Guia do Desenvolvedor do Hub IoT][lnk-devguide-identity]. Quando você executa esse aplicativo de console, ele gera uma ID e chave de dispositivo exclusivas. O dispositivo usa esses valores para se identificar ao enviar mensagens de dispositivo para nuvem para o Hub IoT. As IDs de Dispositivo diferenciam maiúsculas de minúsculas.

1. No Visual Studio, adicione um projeto da Área de Trabalho Clássica do Windows no Visual C# a uma nova solução usando o modelo de projeto **Aplicativo do Console (.NET Framework)**. Verifique se a versão do .NET Framework é 4.5.1 ou posterior. Nomeie o projeto **CreateDeviceIdentity** e a solução **IoTHubGetStarted**.

    ![Novo projeto da Área de Trabalho Clássica do Windows no Visual C#][10]

1. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **CreateDeviceIdentity** e clique em **Gerenciar Pacotes Nuget**.

1. Na janela **Gerenciador de Pacotes Nuget**, selecione **Procurar**, procure **microsoft.azure.devices**, selecione **Instalar** para instalar o pacote **Microsoft.Azure.Devices** e aceite os termos de uso. O procedimento baixa, instala e adiciona uma referência ao [pacote Nuget do SDK do Dispositivo IoT do Azure][lnk-nuget-service-sdk] e suas dependências.

    ![Janela do Gerenciador de Pacotes NuGet][11]

1. Adicione as instruções `using` abaixo na parte superior do arquivo **Program.cs** :

    ```csharp
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Common.Exceptions;
    ```

1. Adicione os seguintes campos à classe **Program** . Substitua o valor do espaço reservado pela cadeia de conexão do Hub IoT criado na seção anterior.

    ```csharp
    static RegistryManager registryManager;
    static string connectionString = "{iot hub connection string}";
    ```

1. Adicione o seguinte método à classe **Programa** :

    ```csharp
    private static async Task AddDeviceAsync()
    {
      string deviceId = "myFirstDevice";
      Device device;
      try
      {
          device = await registryManager.AddDeviceAsync(new Device(deviceId));
      }
      catch (DeviceAlreadyExistsException)
      {
          device = await registryManager.GetDeviceAsync(deviceId);
      }
      Console.WriteLine("Generated device key: {0}", device.Authentication.SymmetricKey.PrimaryKey);
    }
    ```

    Esse método cria uma identidade do dispositivo com a ID **myFirstDevice**. (se essa ID do dispositivo já existir no registro de identidade, o código simplesmente irá recuperar as informações do dispositivo existentes.) Em seguida, o aplicativo exibe a chave primária dessa identidade. Você usa essa chave no aplicativo de dispositivo simulado para se conectar ao Hub IoT.
[!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

1. Por fim, adicione as seguintes linhas ao método **Principal** :

    ```csharp
    registryManager = RegistryManager.CreateFromConnectionString(connectionString);
    AddDeviceAsync().Wait();
    Console.ReadLine();
    ```

1. Execute este aplicativo e anote a chave do dispositivo.

    ![Chave do dispositivo gerada pelo aplicativo][12]

> [!NOTE]
> O Registro de identidade do Hub IoT armazena apenas as identidades de dispositivo para habilitar o acesso seguro ao Hub IoT. O registro de identidade armazena IDs de dispositivo e chaves para usar como credenciais de segurança. O registro de identidade também armazena um sinalizador de habilitado/desabilitado para cada dispositivo que você pode usar para desabilitar o acesso ao dispositivo. Se seu aplicativo precisar armazenar outros metadados específicos do dispositivo, ele deverá usar um repositório específico do aplicativo. Para saber mais, confira [Guia de Desenvolvedor do Hub IoT][lnk-devguide-identity].

<!-- Images. -->
[10]: ./media/iot-hub-get-started-create-device-identity-csharp/create-identity-csharp1.png
[11]: ./media/iot-hub-get-started-create-device-identity-csharp/create-identity-csharp2.png
[12]: ./media/iot-hub-get-started-create-device-identity-csharp/create-identity-csharp3.png

<!-- Links -->
[lnk-devguide-identity]: ../articles/iot-hub/iot-hub-devguide-identity-registry.md
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
