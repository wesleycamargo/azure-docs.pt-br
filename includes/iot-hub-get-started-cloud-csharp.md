## Criar uma identidade do dispositivo

Nesta seção, você escreverá um aplicativo do console do Windows que cria uma nova identidade do dispositivo em seu hub IoT. Veja a seção **Registro de identidade do dispositivo** do [Guia do Desenvolvedor do Hub IoT][IoT Hub Developer Guide - Identity Registry] para obter mais informações. Depois de executar este aplicativo de console, você terá uma ID e uma chave para usar como a identidade do dispositivo para enviar mensagens do dispositivo para a nuvem ao Hub IoT.

1. No Visual Studio, crie um novo projeto de aplicativo de área de trabalho do Visual C# usando o modelo de projeto de **Aplicativo de Console**. Nomeie o projeto **CreateDeviceIdentity**.

   	![][10]

2. No Gerenciador de Soluções, clique com o botão direito do mouse na solução e, então, clique em **Gerenciar Pacotes NuGet para Solução...**.

	Isto mostra a janela Gerenciar Pacotes NuGet.

3. Pesquise por `Microsoft Azure Devices`, clique em **Instalar** e aceite os termos de uso.

	Isso baixa, instala e adiciona uma referência ao [pacote NuGet do SDK de Serviço - IoT do Azure].

4. Adicione a seguinte instrução `using` na parte superior do arquivo **Program.cs**:

		using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Common.Exceptions;

5. Adicione os seguintes campos à classe **Programa**, substituindo os valores do espaço reservado pelo nome do Hub IoT que você criou na seção anterior e pela sua cadeia de conexão:

		static RegistryManager registryManager;
        static string connectionString = "{iothub connection string}";

6. Adicione o seguinte método à classe **Programa**:

		private async static Task AddDeviceAsync()
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

	Este método criará uma nova identidade do dispositivo com a ID **myFirstDevice** (no caso de uma identidade já existir com a mesma ID, ele apenas a recuperará). Em seguida, o aplicativo exibe a chave primária dessa identidade. Essa chave será usada pelo dispositivo simulado para se conectar ao Hub IoT.

7. Por fim, adicione as seguintes linhas ao método **Principal**:

		registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddDeviceAsync().Wait();
        Console.ReadLine();

8. Execute este aplicativo e anote a chave do dispositivo.

    ![][12]

> [AZURE.NOTE]É importante lembrar que o Registro de identidade do hub IoT só é usado para armazenar identidades de dispositivo com a finalidade de proteger o acesso, ou seja, armazenar as credenciais de segurança e habilitar/desabilitar o acesso do dispositivo individual. Os metadados de aplicativo do dispositivo devem ser armazenados em um repositório específico do aplicativo. Veja o [Guia do Desenvolvedor do Hub IoT][IoT Hub Developer Guide - Identity Registry] para obter mais informações.

## Receber mensagens do dispositivo para a nuvem
Nesta seção, você criará um aplicativo do console do Windows que lê mensagens do dispositivo para a nuvem do Hub IoT. O Hub IoT expõe um ponto de extremidade compatível com os [Hubs de Eventos][Event Hubs Overview] para ler mensagens do dispositivo para a nuvem. Para simplificar, este tutorial usa e cria um leitor simplificado que não é adequado para a implantação com uma alta taxa de transferência. Mais informações sobre como processar mensagens do dispositivo para a nuvem do Hub IoT podem ser encontradas no tutorial [Processar mensagens do dispositivo para a nuvem]. Para obter mais informações sobre como processar mensagens dos Hubs de Eventos, confira o tutorial [Introdução aos Hubs de Eventos].

1. Na atual solução do Visual Studio, clique em **Arquivo->Adicionar->Projeto** para criar um novo projeto de Aplicativo da Área de Trabalho do Visual C# usando o modelo de projeto do **Aplicativo do Console**. Nomeie o projeto **ReadDeviceToCloudMessages**.

    ![][10]

2. No Gerenciador de Soluções, clique com o botão direito do mouse na solução e, em seguida, clique em **Gerenciar Pacotes NuGet**.

    A caixa de diálogo **Gerenciar Pacotes NuGet** será exibida.

3. Pesquise por `WindowsAzure.ServiceBus`, clique em **Instalar** e aceite os termos de uso.

    Isso baixa, instala e adiciona uma referência ao [Barramento de Serviço do Azure](https://www.nuget.org/packages/WindowsAzure.ServiceBus), com todas as suas dependências.

4. Adicione a seguinte instrução `using` na parte superior do arquivo **Program.cs**:

        using Microsoft.Azure.Devices.Common;
        using Microsoft.ServiceBus.Messaging;

5. Adicione os seguintes campos à classe **Programa**, substituindo os valores do espaço reservado pelo nome do Hub IoT que você criou na seção anterior e pela sua cadeia de conexão:

        static string connectionString = "{iothub connection string}";
        static string iotHubD2cEndpoint = "messages/events";
        static EventHubClient eventHubClient;

6. Adicione o seguinte método à classe **Programa**:

        private async static Task ReceiveMessagesFromDeviceAsync(string partition)
        {
            var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.Now);
            while (true)
            {
                EventData eventData = await eventHubReceiver.ReceiveAsync();
                if (eventData == null) continue;

                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                Console.WriteLine(string.Format("Message received. Partition: {0} Data: '{1}'", partition, data));
            }
        }

    Esse método usa um cliente EventHub para receber de todas as partições de recebimento do dispositivo para a nuvem de seu hub IoT. Observe como, ao criar um EventHubReceiver, um parâmetro `DateTime.Now` é transmitido. Isso cria um receptor que receberá somente as mensagens que são enviadas depois que ele for iniciado.

7. Por fim, adicione as seguintes linhas ao método **Principal**:

        Console.WriteLine("Receive messages\n");
        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, iotHubD2cEndpoint);

        var d2cPartitions = eventHubClient.GetRuntimeInformation().PartitionIds;

        foreach (string partition in d2cPartitions)
        {
           ReceiveMessagesFromDeviceAsync(partition);
        }  
        Console.ReadLine();


<!-- Links -->

[pacote NuGet do SDK de Serviço - IoT do Azure]: https://www.nuget.org/packages/Microsoft.Azure.Devices/

[Introdução aos Hubs de Eventos]: event-hubs-csharp-ephcs-getstarted.md
[IoT Hub Developer Guide - Identity Registry]: iot-hub-devguide.md#identityregistry

[Event Hubs Overview]: event-hubs-overview.md
[Scaled out event processing]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Event-Hub-45f43fc3
[Azure Storage account]: storage-create-storage-account.md
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx

[Azure Preview Portal]: https://portal.azure.com/


<!-- Images -->
[10]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp1.png
[12]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp3.png

<!---HONumber=Oct15_HO1-->