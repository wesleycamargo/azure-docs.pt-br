<properties
	pageTitle="Introdução ao Hub IoT do Azure para C# | Microsoft Azure"
	description="Tutorial de introdução ao Hub IoT do Azure para C#. Use o Hub IoT do Azure e C# com os SDKs de IoT do Microsoft Azure para implementar uma solução IoT (Internet das Coisas)."
	services="iot-hub"
	documentationCenter=".net"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="06/16/2016"
     ms.author="dobett"/>

# Introdução ao Hub IoT do Azure para .NET

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

No final deste tutorial, você terá três aplicativos de console do Windows:

* **CreateDeviceIdentity**, que cria uma identidade do dispositivo e uma chave de segurança associada para conectar o dispositivo simulado.
* **ReadDeviceToCloudMessages**, que exibe a telemetria enviada pelo dispositivo simulado.
* **SimulatedDevice**, que se conecta ao hub IoT com a identidade do dispositivo criada anteriormente e envia uma mensagem de telemetria a cada segundo usando o protocolo AMQPS.

> [AZURE.NOTE] Para obter informações sobre os vários SDKs que você pode usar para criar os aplicativos para executar nos dispositivos e o back-end de solução, consulte [SDKs do Hub IoT][lnk-hub-sdks].

Para concluir este tutorial, você precisará do seguinte:

+ Microsoft Visual Studio 2015.

+ Uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação Gratuita do Azure][lnk-free-trial].)

[AZURE.INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Agora, você criou o hub IoT e tem o nome do host e a cadeia de conexão necessários para concluir este tutorial.

## Criar uma identidade do dispositivo

Nesta seção, você cria um aplicativo do console do Windows que cria uma nova identidade do dispositivo no registro de identidade em seu hub IoT. Um dispositivo não pode se conectar ao Hub IoT, a menos que ele tenha uma entrada no registro de identidade do dispositivo. Para obter mais informações, consulte a seção "Registro da identidade do dispositivo" do [Guia do Desenvolvedor do Hub IoT][lnk-devguide-identity]. Quando você executar esse aplicativo de console, ele irá gerar uma ID e chave do dispositivo exclusivas com as quais seu dispositivo poderá identificar-se ao enviar mensagens entre o dispositivo e a nuvem para o Hub IoT.

1. No Visual Studio, adicione um novo projeto da Área de Trabalho Clássica do Windows no Visual C# à solução atual usando o modelo de projeto **Aplicativo do Console**. Verifique se a versão do .NET Framework é 4.5.1 ou posterior. Nomeie o projeto **CreateDeviceIdentity**.

	![Novo projeto da Área de Trabalho Clássica do Windows no Visual C#][10]

2. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **CreateDeviceIdentity** e clique em **Gerenciar Pacotes NuGet**.

3. Na janela **Gerenciador de Pacotes NuGet**, selecione **Procurar**, procure **microsoft.azure.devices**, selecione **Instalar** para instalar o pacote **Microsoft.Azure.Devices** e aceite os termos de uso. Isso baixa, instala e adiciona uma referência ao pacote NuGet do [SDK de Serviço IoT do Microsoft Azure][lnk-nuget-service-sdk] e suas dependências.

	![Janela do Gerenciador de Pacotes NuGet][11]

4. Adicione as instruções `using` abaixo na parte superior do arquivo **Program.cs**:

		using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Common.Exceptions;

5. Adicione os seguintes campos à classe **Program**. Substitua o valor do espaço reservado pela cadeia de conexão do hub IoT criado na seção anterior.

		static RegistryManager registryManager;
        static string connectionString = "{iot hub connection string}";

6. Adicione o seguinte método à classe **Programa**:

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

	Esse método cria uma nova identidade do dispositivo com a ID **myFirstDevice**. (se essa ID do dispositivo já existir no registro, o código simplesmente irá recuperar as informações do dispositivo existentes.) Em seguida, o aplicativo exibe a chave primária dessa identidade. Você usa essa chave no dispositivo simulado para se conectar ao hub IoT.

7. Por fim, adicione as seguintes linhas ao método **Principal**:

		registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddDeviceAsync().Wait();
        Console.ReadLine();

8. Execute este aplicativo e anote a chave do dispositivo.

    ![Chave do dispositivo gerada pelo aplicativo][12]

> [AZURE.NOTE] O registro de identidade do Hub IoT armazena apenas as identidades de dispositivo para habilitar o acesso seguro ao hub. Ele armazena as IDs e as chaves do dispositivo a usar como credenciais de segurança e um sinalizador habilitado/desabilitado que você poderá usar para desabilitar o acesso de um dispositivo individual. Se seu aplicativo precisar armazenar outros metadados específicos do dispositivo, ele deverá usar um repositório específico do aplicativo. Para saber mais, confira [Guia de Desenvolvedor do Hub IoT][lnk-devguide-identity].

## Receber mensagens do dispositivo para a nuvem

Nesta seção, você cria um aplicativo do console do Windows que lê mensagens do dispositivo para a nuvem do Hub IoT. Um hub IoT expõe um ponto de extremidade compatível com os [Hubs de Eventos do Azure][lnk-event-hubs-overview] para permitir que você leia as mensagens entre o dispositivo e a nuvem. Para simplificar, este tutorial cria um leitor básico que não é adequado para uma implantação de alta taxa de transferência. Para saber como processar as mensagens entre o dispositivo e a nuvem em escala, consulte o tutorial [Processar as mensagens entre o dispositivo e a nuvem][lnk-process-d2c-tutorial]. Para saber mais sobre como processar as mensagens dos Hubs de Eventos, consulte o tutorial [Introdução aos Hubs de Eventos][lnk-eventhubs-tutorial]. (Este tutorial é aplicável aos pontos de extremidade compatíveis com os Hubs de Eventos do Hub IoT).

> [AZURE.NOTE] O ponto de extremidade compatível com os Hubs de Eventos para ler mensagens do dispositivo para a nuvem sempre usa o protocolo AMQPS.

1. No Visual Studio, adicione um novo projeto da Área de Trabalho Clássica do Windows no Visual C# à solução atual usando o modelo de projeto **Aplicativo do Console**. Verifique se a versão do .NET Framework é 4.5.1 ou posterior. Nomeie o projeto **ReadDeviceToCloudMessages**.

    ![Novo projeto da Área de Trabalho Clássica do Windows no Visual C#][10]

2. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **ReadDeviceToCloudMessages** e clique em **Gerenciar Pacotes NuGet**.

3. Na janela **Gerenciador de Pacotes NuGet**, procure **WindowsAzure.ServiceBus**, selecione **Instalar** e aceite os termos de uso. Isso baixa, instala e adiciona uma referência ao [Barramento de Serviço do Azure][lnk-servicebus-nuget] com todas as suas dependências. Este pacote permite que o aplicativo conecte o ponto de extremidade compatível com o Hubs de Eventos em seu Hub IoT.

4. Adicione as instruções `using` abaixo na parte superior do arquivo **Program.cs**:

        using Microsoft.ServiceBus.Messaging;
        using System.Threading;

5. Adicione os seguintes campos à classe **Program**. Substitua o valor do espaço reservado pela cadeia de conexão do hub IoT criado na seção "Criar um hub IoT".

        static string connectionString = "{iothub connection string}";
        static string iotHubD2cEndpoint = "messages/events";
        static EventHubClient eventHubClient;

6. Adicione o seguinte método à classe **Programa**:

        private static async Task ReceiveMessagesFromDeviceAsync(string partition, CancellationToken ct)
        {
            var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow);
            while (true)
            {
                if (ct.IsCancellationRequested) break;
                EventData eventData = await eventHubReceiver.ReceiveAsync();
                if (eventData == null) continue;

                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                Console.WriteLine("Message received. Partition: {0} Data: '{1}'", partition, data);
            }
        }

    Esse método usa uma instância de **EventHubReceiver** para receber de todas as partições de recebimento do dispositivo para a nuvem do hub IoT. Observe como você passa um parâmetro `DateTime.Now` quando cria o objeto **EventHubReceiver** para que ele receba apenas as mensagens enviadas após iniciar. Isso é útil em um ambiente de teste para que você possa ver o conjunto atual de mensagens, mas em um ambiente de produção, seu código deve garantir o processamento de todas as mensagens. Para obter mais informações, confira o tutorial [Como processar as mensagens entre o dispositivo e a nuvem do Hub IoT][lnk-process-d2c-tutorial].

7. Por fim, adicione as seguintes linhas ao método **Principal**:

        Console.WriteLine("Receive messages. Ctrl-C to exit.\n");
        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, iotHubD2cEndpoint);

        var d2cPartitions = eventHubClient.GetRuntimeInformation().PartitionIds;

        CancellationTokenSource cts = new CancellationTokenSource();

        System.Console.CancelKeyPress += (s, e) =>
        {
          e.Cancel = true;
          cts.Cancel();
          Console.WriteLine("Exiting...");
        };

        var tasks = new List<Task>();
        foreach (string partition in d2cPartitions)
        {
           tasks.Add(ReceiveMessagesFromDeviceAsync(partition, cts.Token));
        }  
        Task.WaitAll(tasks.ToArray());

## Criar um aplicativo de dispositivo simulado

Nesta seção, você criará um aplicativo do console do Windows que simula um dispositivo que envia mensagens do dispositivo para a nuvem para um hub IoT.

1. No Visual Studio, adicione um novo projeto da Área de Trabalho Clássica do Windows no Visual C# à solução atual usando o modelo de projeto **Aplicativo do Console**. Verifique se a versão do .NET Framework é 4.5.1 ou posterior. Chame o projeto de **SimulatedDevice**.

    ![Novo projeto da Área de Trabalho Clássica do Windows no Visual C#][10]

2. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **SimulatedDevice** e clique em **Gerenciar Pacotes NuGet**.

3. Na janela **Gerenciador de Pacotes NuGet**, selecione **Procurar**, procure **Microsoft.Azure.Devices.Client**, selecione **Instalar** para instalar o pacote **Microsoft.Azure.Devices.Client** e aceite os termos de uso. Isso baixa, instala e adiciona uma referência ao [pacote NuGet do SDK do Dispositivo - IoT do Azure][lnk-device-nuget] e suas dependências.

4. Adicione a seguinte instrução `using` na parte superior do arquivo **Program.cs**:

		using Microsoft.Azure.Devices.Client;
        using Newtonsoft.Json;

5. Adicione os seguintes campos à classe **Program**. Substitua os valores do espaço reservado pelo nome de host do hub IoT recuperado na seção "Criar um hub IoT" e a chave do dispositivo recuperada na seção "Criar uma identidade do dispositivo".

		static DeviceClient deviceClient;
        static string iotHubUri = "{iot hub hostname}";
        static string deviceKey = "{device key}";

6. Adicione o seguinte método à classe **Programa**:

		private static async void SendDeviceToCloudMessagesAsync()
        {
            double avgWindSpeed = 10; // m/s
            Random rand = new Random();

            while (true)
            {
                double currentWindSpeed = avgWindSpeed + rand.NextDouble() * 4 - 2;

                var telemetryDataPoint = new
                {
                    deviceId = "myFirstDevice",
                    windSpeed = currentWindSpeed
                };
                var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
                var message = new Message(Encoding.ASCII.GetBytes(messageString));

                await deviceClient.SendEventAsync(message);
                Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, messageString);

                Task.Delay(1000).Wait();
            }
        }

	Esse método envia uma nova mensagem de dispositivo para a nuvem a cada segundo. A mensagem contém um objeto serializado por JSON com a ID do dispositivo e um número gerado aleatoriamente para simular um sensor de velocidade do vento.

7. Por fim, adicione as seguintes linhas ao método **Principal**:

        Console.WriteLine("Simulated device\n");
        deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey("myFirstDevice", deviceKey));

        SendDeviceToCloudMessagesAsync();
        Console.ReadLine();

  Por padrão, o método **Create** cria uma instância **DeviceClient** que usa o protocolo AMQP para se comunicar com o Hub IoT. Para usar o protocolo HTTPS, use a substituição do método **Create** que permite especificar o protocolo. Se você usar o protocolo HTTPS, também deverá adicionar o pacote NuGet **Microsoft.AspNet.WebApi.Client** ao seu projeto para incluir o namespace **System.Net.Http.Formatting**.

Este tutorial apresenta as etapas para criar um cliente de dispositivo do Hub IoT. Você também pode usar a extensão Visual Studio do [Serviço Conectado para o Hub IoT do Azure][lnk-connected-service] para adicionar o código necessário ao aplicativo cliente de seu dispositivo.


> [AZURE.NOTE] Para simplificar, este tutorial não implementa nenhuma política de repetição. No código de produção, implemente políticas de repetição (como uma retirada exponencial), como sugerido no artigo [Tratamento de falhas transitórias][lnk-transient-faults] do MSDN.

## Executar os aplicativos

Agora você está pronto para executar os aplicativos.

1.	No Visual Studio, no Gerenciador de Soluções, clique com o botão direito na solução e clique em **Definir Projetos de Inicialização**. Selecione **Vários projetos de inicialização** e selecione **Iniciar** como a ação para os projetos **ReadDeviceToCloudMessages** e **SimulatedDevice**.

   	![Propriedades do projeto de inicialização][41]

2.	Pressione **F5** para iniciar ambos os aplicativos em execução. A saída do console do aplicativo **SimulatedDevice** mostra as mensagens que seu dispositivo simulado envia para o hub IoT. A saída do console do aplicativo **ReadDeviceToCloudMessages** mostra as mensagens que seu hub IoT recebe.

   	![Saída de console dos aplicativos][42]

3. O bloco **Uso** no [portal do Azure][lnk-portal] mostra o número de mensagens enviadas para o hub:

    ![Bloco Uso do portal do Azure][43]


## Próximas etapas

Neste tutorial, você configurou um novo hub IoT no portal e depois criou uma identidade do dispositivo no registro de identidade do hub. Você usou essa identidade do dispositivo para habilitar o aplicativo do dispositivo simulado para enviar as mensagens entre o dispositivo e a nuvem para o hub. Você também criou um aplicativo que exibe as mensagens recebidas pelo hub.

Para continuar a introdução ao Hub IoT e explorar outros cenários de IoT, confira:

- [Conectando o dispositivo][lnk-connect-device]
- [Introdução ao gerenciamento de dispositivo][lnk-device-management]
- [Introdução ao SDK do Gateway][lnk-gateway-SDK]

Para saber como estender sua solução IoT e processar as mensagens entre o dispositivo e a nuvem em escala, consulte o tutorial [Processar as mensagens entre o dispositivo e a nuvem][lnk-process-d2c-tutorial].

<!-- Images. -->
[41]: ./media/iot-hub-csharp-csharp-getstarted/run-apps1.png
[42]: ./media/iot-hub-csharp-csharp-getstarted/run-apps2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png
[10]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp1.png
[11]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp2.png
[12]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp3.png

<!-- Links -->
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-sdks-summary.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide.md#identityregistry
[lnk-servicebus-nuget]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-device-nuget]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-connected-service]: https://visualstudiogallery.msdn.microsoft.com/e254a3a5-d72e-488e-9bd3-8fee8e0cd1d6
[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/

<!---HONumber=AcomDC_0831_2016-->