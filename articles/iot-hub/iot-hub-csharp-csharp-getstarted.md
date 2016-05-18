<properties
	pageTitle="Introdução ao Hub IoT do Azure para C# | Microsoft Azure"
	description="Tutorial de introdução ao Hub IoT do Azure para C#. Use o Hub IoT do Azure e C# com SDKs de IoT do Microsoft Azure para implementar uma solução de Internet das coisas."
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
     ms.date="03/22/2016"
     ms.author="dobett"/>

# Introdução ao Hub IoT do Azure para .NET

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

## Introdução

O Hub IoT do Azure é um serviço totalmente gerenciado que permite comunicações bidirecionais confiáveis e seguras entre milhões de dispositivos IoT (Internet das coisas) e um back-end da solução. Um dos maiores desafios enfrentados por projetos IoT é como conectar dispositivos de forma confiável e segura para a solução de back-end. Para enfrentar esse desafio, o hub IoT:

- Oferece, de forma confiável, mensagens em larga escala do dispositivo para a nuvem e da nuvem para o dispositivo.
- Permite proteger as comunicações usando credenciais de segurança e controle de acesso por dispositivo.
- Inclui bibliotecas de dispositivo para as plataformas e idiomas mais populares.

Este tutorial mostra como:

- Use o portal do Azure para criar um hub IoT.
- Crie uma identidade de dispositivo em seu hub IoT.
- Crie um dispositivo simulado que envia a telemetria para o back-end de nuvem e recebe comandos de seu back-end de nuvem.

Ao fim deste tutorial, você terá três aplicativos de console do Windows:

* **CreateDeviceIdentity**, que cria uma identidade do dispositivo e uma chave de segurança associada para conectar o dispositivo simulado.
* **ReadDeviceToCloudMessages**, que exibe a telemetria enviada pelo dispositivo simulado.
* **SimulatedDevice**, que se conecta ao hub IoT com a identidade do dispositivo criada anteriormente e envia uma mensagem de telemetria a cada segundo usando o protocolo AMQPS.

> [AZURE.NOTE] O artigo [SDKs do hub IoT][lnk-hub-sdks] fornece informações sobre vários SDKs que você pode usar para criar aplicativos executados em dispositivos e no back-end da solução.

Para concluir este tutorial, você precisará do seguinte:

+ Microsoft Visual Studio 2015.

+ Uma conta ativa do Azure. <br/>Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos Para obter detalhes, consulte [Avaliação gratuita do Azure][lnk-free-trial].

[AZURE.INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Agora você criou o hub IoT e tem o nome do hosto e a cadeia de conexão de que precisa para concluir este tutorial.

## Criar uma identidade do dispositivo

Nesta seção, você cria um aplicativo do console do Windows que cria uma nova identidade do dispositivo no registro de identidade em seu hub IoT. Um dispositivo não pode se conectar ao Hub IoT, a menos que ele tenha uma entrada no registro de identidade do dispositivo. Veja a seção **Registro de identidade do dispositivo** do [Guia do Desenvolvedor do Hub IoT][lnk-devguide-identity] para obter mais informações. Quando você executa esse aplicativo de console, ele gera uma ID e uma chave de dispositivo exclusivas com que seu dispositivo poderá se identificar ao enviar mensagens de dispositivo para a nuvem para o Hub IoT.

1. No Visual Studio, adicione um novo projeto de Área de Trabalho Clássica do Windows no Visual C# para a solução atual usando o modelo de projeto do **Aplicativo do Console**. Verifique se a versão do .NET Framework é 4.5.1 ou posterior. Nomeie o projeto **CreateDeviceIdentity**.

	![][10]

2. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **CreateDeviceIdentity** e clique em **Gerenciar Pacotes NuGet**.

3. Na janela **Gerenciador de Pacotes NuGet**, selecione **Procurar**, procure **microsoft.azure.devices**, clique em **Instalar** para instalar o pacote **Microsoft.Azure.Devices** e aceite os termos de uso.

	![][11]

4. Isso baixa, instala e adiciona uma referência ao pacote NuGet do [SDK de Serviço IoT do Microsoft Azure][lnk-nuget-service-sdk] e suas dependências.

4. Adicione as instruções `using` abaixo na parte superior do arquivo **Program.cs**:

		using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Common.Exceptions;

5. Adicione os campos abaixo à classe **Program**, substituindo os valores do espaço reservado pela cadeia de conexão para o Hub IoT criado na seção anterior:

		static RegistryManager registryManager;
        static string connectionString = "{iothub connection string}";

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

	Esse método cria uma nova identidade do dispositivo com a ID **myFirstDevice** (se essa ID de dispositivo já existir no registro, o código simplesmente recupera as informações de dispositivo existentes). Em seguida, o aplicativo exibe a chave primária dessa identidade. Você usa essa chave no dispositivo simulado para se conectar ao hub IoT.

7. Por fim, adicione as seguintes linhas ao método **Principal**:

		registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddDeviceAsync().Wait();
        Console.ReadLine();

8. Execute este aplicativo e anote a chave do dispositivo.

    ![][12]

> [AZURE.NOTE] O registro de identidade do Hub IoT armazena apenas as identidades de dispositivo para habilitar o acesso seguro ao hub. Ele armazena as IDs e as chaves de dispositivo para usar como credenciais de segurança e um sinalizador habilitado/desabilitado que permite que você desabilite o acesso para um dispositivo individual. Se seu aplicativo precisar armazenar outros metadados específicos do dispositivo, ele deverá usar um repositório específico do aplicativo. Veja o [Guia do Desenvolvedor do Hub IoT][lnk-devguide-identity] para saber mais.

## Receber mensagens do dispositivo para a nuvem

Nesta seção, você cria um aplicativo do console do Windows que lê mensagens do dispositivo para a nuvem do Hub IoT. Um hub IoT expõe um ponto de extremidade compatível com os [Hubs de Eventos][lnk-event-hubs-overview] para permitir que você leia mensagens do dispositivo para a nuvem. Para simplificar, este tutorial cria um leitor básico que não é adequado para uma implantação de alta taxa de transferência. O tutorial [Processar mensagens do dispositivo para a nuvem][lnk-process-d2c-tutorial] mostra como processar as mensagens do dispositivo para a nuvem em escala. O tutorial [Introdução aos Hubs de Eventos][lnk-eventhubs-tutorial] oferece informações adicionais sobre como processar as mensagens dos Hubs de Eventos e é aplicável aos pontos de extremidade compatíveis com o Hub de Eventos do Hub IoT.

> [AZURE.NOTE] O ponto de extremidade compatível com os Hubs de Eventos para ler mensagens do dispositivo para a nuvem sempre usa o protocolo AMQPS.

1. No Visual Studio, adicione um novo projeto de Área de Trabalho Clássica do Windows no Visual C# para a solução atual usando o modelo de projeto do **Aplicativo do Console**. Verifique se a versão do .NET Framework é 4.5.1 ou posterior. Nomeie o projeto **ReadDeviceToCloudMessages**.

    ![][10]

2. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **ReadDeviceToCloudMessages** e clique em **Gerenciar Pacotes NuGet**.

3. Na janela **Gerenciador de Pacotes NuGet**, procure **WindowsAzure.ServiceBus**, clique em **Instalar** e aceite os termos de uso.

    Isso baixa, instala e adiciona uma referência ao [Barramento de Serviço do Azure][lnk-servicebus-nuget] com todas as dependências. Este pacote permite que o aplicativo conecte o ponto de extremidade compatível com o Hub de Eventos em seu Hub IoT.

4. Adicione as instruções `using` abaixo na parte superior do arquivo **Program.cs**:

        using Microsoft.ServiceBus.Messaging;
        using System.Threading;

5. Adicione os campos a seguir à classe **Program**, substituindo os valores do espaço reservado pela cadeia de conexão para o Hub IoT criado na seção *Criar um hub IoT*:

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

    Esse método usa uma instância de **EventHubReceiver** para receber de todas as partições de recebimento do dispositivo para a nuvem do hub IoT. Observe como você passa um parâmetro `DateTime.Now` quando cria o objeto **EventHubReceiver** para que ele receba apenas as mensagens enviadas após ele ser iniciado. Isso será útil em um ambiente de teste para que você possa ver o conjunto de mensagens atual, mas em um ambiente de produção, seu código deverá garantir o processamento de todas as mensagens. Consulte o tutorial [Como processar as mensagens do dispositivo para a nuvem do Hub IoT][lnk-process-d2c-tutorial] para saber mais.

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

1. No Visual Studio, adicione um novo projeto de Área de Trabalho Clássica do Windows no Visual C# para a solução atual usando o modelo de projeto do **Aplicativo do Console**. Verifique se a versão do .NET Framework é 4.5.1 ou posterior. Chame o projeto de **SimulatedDevice**.

   	![][10]

2. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **SimulatedDevice** e clique em **Gerenciar Pacotes NuGet**.

3. Na janela **Gerenciador de Pacotes NuGet**, selecione **Procurar**, procure **Microsoft.Azure.Devices.Client**, clique em **Instalar** para instalar o pacote **Microsoft.Azure.Devices.Client** e aceite os termos de uso.

	Isso baixa, instala e adiciona uma referência ao [pacote NuGet do SDK do Dispositivo - IoT do Azure][lnk-device-nuget] e suas dependências.

4. Adicione a seguinte instrução `using` na parte superior do arquivo **Program.cs**:

		using Microsoft.Azure.Devices.Client;
        using Newtonsoft.Json;


5. Adicione os seguintes campos à classe **Program**, substituindo os valores de espaço reservado pelo nome de host do hub IoT recuperado na seção *Criar um hub IoT* e a chave de dispositivo recuperada na seção *Criar uma identidade do dispositivo*:

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

	Esse método envia uma nova mensagem de dispositivo para a nuvem a cada segundo. A mensagem contém um objeto serializado por JSON com a deviceId e um número gerado aleatoriamente para simular um sensor de velocidade do vento.

7. Por fim, adicione as seguintes linhas ao método **Principal**:

        Console.WriteLine("Simulated device\n");
        deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey("myFirstDevice", deviceKey));

        SendDeviceToCloudMessagesAsync();
        Console.ReadLine();

  Por padrão, o método **Create** cria uma instância **DeviceClient** que usa o protocolo AMQP para se comunicar com o Hub IoT. Para usar o protocolo HTTPS, use a substituição do método **Create** que permite especificar o protocolo. Se você optar por usar o protocolo HTTPS, você também deve adicionar o pacote NuGet **Microsoft.AspNet.WebApi.Client** ao seu projeto para incluir o namespace **System.Net.Http.Formatting**.

Este tutorial apresenta as etapas para criar um cliente de dispositivo do Hub IoT. Como alternativa, você pode usar a extensão Visual Studio do [Serviço Conectado para o Hub IoT do Azure][lnk-connected-service] para adicionar o código necessário ao aplicativo cliente de seu dispositivo.


> [AZURE.NOTE] Para simplificar, este tutorial não implementa nenhuma política de repetição. No código de produção, implemente políticas de repetição (como uma retirada exponencial), como sugerido no artigo [Tratamento de falhas transitórias][lnk-transient-faults] do MSDN.

## Executar os aplicativos

Agora você está pronto para executar os aplicativos.

1.	No Visual Studio, no Gerenciador de Soluções, clique com o botão direito do mouse na solução e clique em **Definir projetos de Inicialização**. Selecione **Vários projetos de inicialização** e selecione a ação **Iniciar** como a **Ação** para os projetos **ReadDeviceToCloudMessages** e **SimulatedDevice**.

   	![][41]

2.	Pressione **F5** para iniciar ambos os aplicativos em execução. A saída do console do aplicativo **SimulatedDevice** mostra as mensagens que o dispositivo simulado envia para o hub IoT, e a saída do console do aplicativo **ReadDeviceToCloudMessages** mostra as mensagens recebidas pelo hub IoT.

   	![][42]

3. O bloco **Uso** no [portal do Azure][lnk-portal] mostra o número de mensagens enviadas para o hub:

    ![][43]


## Próximas etapas

Neste tutorial, você configurou um novo hub IoT no portal e depois criou uma identidade de dispositivo no registro de identidade do hub. Você usou essa identidade de dispositivo para habilitar o aplicativo do dispositivo simulado para enviar mensagens entre o dispositivo e a nuvem para o hub e criou um aplicativo que exibe as mensagens recebidas pelo hub. Você pode continuar a explorar os recursos do hub IoT e outros cenários de IoT nestes tutoriais:

- [Enviar mensagens da Nuvem para o Dispositivo com o Hub IoT][lnk-c2d-tutorial] mostra como enviar mensagens para dispositivos e como processar os comentários de entrega produzidos pelo Hub IoT.
- [Processar mensagens do Dispositivo para a Nuvem][lnk-process-d2c-tutorial] mostra como processar de forma confiável a telemetria e as mensagens interativas vindas dos dispositivos.
- [Carregando arquivos de dispositivos][lnk-upload-tutorial] descreve um padrão que usa mensagens da nuvem para o dispositivo para facilitar o carregamento de arquivo dos dispositivos.

<!-- Images. -->
[41]: ./media/iot-hub-csharp-csharp-getstarted/run-apps1.png
[42]: ./media/iot-hub-csharp-csharp-getstarted/run-apps2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png
[10]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp1.png
[11]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp2.png
[12]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp3.png

<!-- Links -->
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
[lnk-upload-tutorial]: iot-hub-csharp-csharp-file-upload.md

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

<!---HONumber=AcomDC_0511_2016-->