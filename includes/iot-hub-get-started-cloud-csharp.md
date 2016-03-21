## Criar uma identidade do dispositivo

Nesta seção, você cria um aplicativo do console do Windows que cria uma nova identidade do dispositivo no registro de identidade em seu hub IoT. Um dispositivo não pode se conectar ao Hub IoT, a menos que ele tenha uma entrada no registro de identidade do dispositivo. Veja a seção **Registro de identidade do dispositivo** do [Guia do Desenvolvedor do Hub IoT][lnk-devguide-identity] para obter mais informações. Quando você executa esse aplicativo de console, ele gera uma ID e uma chave de dispositivo exclusivas com que seu dispositivo poderá se identificar ao enviar mensagens de dispositivo para a nuvem para o Hub IoT.

1. No Visual Studio, adicione um novo projeto de Área de Trabalho Clássica do Windows no Visual C# para a solução atual usando o modelo de projeto do **Aplicativo do Console**. Nomeie o projeto **CreateDeviceIdentity**.

	![][10]

2. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **CreateDeviceIdentity** e clique em **Gerenciar Pacotes NuGet**.

3. Na janela **Gerenciador de Pacotes NuGet**, procure **Dispositivos do Microsoft Azure**, clique em **Instalar** para instalar o pacote **Microsoft.Azure.Devices** e aceite os termos de uso.

	![][11]

4. Isso baixa, instala e adiciona uma referência ao pacote NuGet do [SDK de Serviço IoT do Microsoft Azure][lnk-nuget-service-sdk].

4. Adicione as instruções `using` abaixo na parte superior do arquivo **Program.cs**:

		using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Common.Exceptions;

5. Adicione os campos abaixo à classe **Program**, substituindo os valores do espaço reservado pela cadeia de conexão para o Hub IoT criado na seção anterior:

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

	Esse método cria uma nova identidade do dispositivo com a ID **myFirstDevice** (se essa ID de dispositivo já existir no registro, o código simplesmente recupera as informações de dispositivo existentes). Em seguida, o aplicativo exibe a chave primária dessa identidade. Você usa essa chave no dispositivo simulado para se conectar ao hub IoT.

7. Por fim, adicione as seguintes linhas ao método **Principal**:

		registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddDeviceAsync().Wait();
        Console.ReadLine();

8. Execute este aplicativo e anote a chave do dispositivo.

    ![][12]

> [AZURE.NOTE] O registro de identidade do Hub IoT armazena apenas as identidades de dispositivo para habilitar o acesso seguro ao hub. Ele armazena as IDs e as chaves de dispositivo para usar como credenciais de segurança e um sinalizador habilitado/desabilitado que permite que você desabilite o acesso para um dispositivo individual. Se seu aplicativo precisar armazenar outros metadados específicos do dispositivo, ele deverá usar um repositório específico do aplicativo. Veja o [Guia do Desenvolvedor do Hub IoT][lnk-devguide-identity] para saber mais.

## Receber mensagens do dispositivo para a nuvem

Nesta seção, você cria um aplicativo do console do Windows que lê mensagens do dispositivo para a nuvem do Hub IoT. Um hub IoT expõe um ponto de extremidade compatível com os [Hubs de Eventos][lnk-event-hubs-overview] para permitir que você leia mensagens do dispositivo para a nuvem. Para simplificar, este tutorial cria um leitor básico que não é adequado para uma implantação de alta taxa de transferência. O tutorial [Processar mensagens do dispositivo para a nuvem][lnk-processd2c-tutorial] mostra como processar mensagens do dispositivo para a nuvem em escala. O tutorial [Introdução aos Hubs de Eventos][lnk-eventhubs-tutorial] oferece informações adicionais sobre como processar mensagens de Hubs de Eventos e se aplica aos pontos de extremidade compatíveis com o Hub de Eventos Hub IoT.

1. No Visual Studio, adicione um novo projeto de Área de Trabalho Clássica do Windows no Visual C# para a solução atual usando o modelo de projeto do **Aplicativo do Console**. Nomeie o projeto **ReadDeviceToCloudMessages**.

    ![][10]

2. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **ReadDeviceToCloudMessages** e clique em **Gerenciar Pacotes NuGet**.

3. Na janela **Gerenciador de Pacotes NuGet**, procure **WindowsAzure.ServiceBus**, clique em **Instalar** e aceite os termos de uso.

    Isso baixa, instala e adiciona uma referência ao [Barramento de Serviço do Azure][lnk-servicebus-nuget] com todas as dependências.

4. Adicione a seguinte instrução `using` na parte superior do arquivo **Program.cs**:

        using Microsoft.ServiceBus.Messaging;

5. Adicione os campos a seguir à classe **Program**, substituindo os valores do espaço reservado pela cadeia de conexão para o Hub IoT criado na seção *Criar um hub IoT*:

        static string connectionString = "{iothub connection string}";
        static string iotHubD2cEndpoint = "messages/events";
        static EventHubClient eventHubClient;

6. Adicione o seguinte método à classe **Programa**:

        private async static Task ReceiveMessagesFromDeviceAsync(string partition)
        {
            var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow);
            while (true)
            {
                EventData eventData = await eventHubReceiver.ReceiveAsync();
                if (eventData == null) continue;

                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                Console.WriteLine(string.Format("Message received. Partition: {0} Data: '{1}'", partition, data));
            }
        }

    Esse método usa uma instância de **EventHubReceiver** para receber de todas as partições de recebimento do dispositivo para a nuvem do hub IoT. Observe como você passa um parâmetro `DateTime.Now` quando cria o objeto **EventHubReceiver** para que ele receba apenas as mensagens enviadas após ele ser iniciado. Isso será útil em um ambiente de teste para que você possa ver o conjunto de mensagens atual, mas em um ambiente de produção, seu código deverá garantir o processamento de todas as mensagens - veja o tutorial [Como processar mensagens do dispositivo para a nuvem do Hub IoT][lnk-processd2c-tutorial] para saber mais.

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

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide.md#identityregistry
[lnk-servicebus-nuget]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-processd2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

<!-- Images -->
[10]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp1.png
[11]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp2.png
[12]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp3.png

<!---HONumber=AcomDC_0309_2016-->