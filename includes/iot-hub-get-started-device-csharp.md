## Criar um aplicativo de dispositivo simulado

Nesta seção, você escreverá um aplicativo do console do Windows que simula um dispositivo que envia mensagens do dispositivo para a nuvem para um hub IoT.

1. Na atual solução do Visual Studio, clique em **Arquivo->Adicionar->Projeto** para criar um novo projeto de Aplicativo da Área de Trabalho do Visual C# usando o modelo de projeto do **Aplicativo do Console**. Nomeie o projeto **SimulatedDevice**.

   	![][30]

2. No Gerenciador de Soluções, clique com o botão direito do mouse na solução e, então, clique em **Gerenciar Pacotes NuGet para Solução...**.

	Isto mostra a janela Gerenciar Pacotes NuGet.

3. Pesquise por `Microsoft Azure Devices Client`, clique em **Instalar** e aceite os termos de uso.

	Isso baixa, instala e adiciona uma referência ao [pacote NuGet do SDK de Dispositivo - IoT do Azure].

4. Adicione a seguinte instrução `using` na parte superior do arquivo **Program.cs**:

		using Microsoft.Azure.Devices.Client;
        using Newtonsoft.Json;
        using System.Threading;

5. Adicione os seguintes campos à classe **Programa**, substituindo os valores de espaço reservado pelo URI do hub IoT e pela chave do dispositivo recuperada nas seções **Criar um hub IoT** e **Criar uma identidade do dispositivo**, respectivamente:

		static DeviceClient deviceClient;
        static string iotHubUri = "{iot hub URI}";
        static string deviceKey = "{deviceKey}";

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

                Thread.Sleep(1000);
            }
        }

	Esse método enviará uma nova mensagem do dispositivo para a nuvem a cada segundo, contendo um objeto serializado pelo JSON com a ID do dispositivo e um número gerado aleatoriamente, que representa um sensor de velocidade eólica simulado.

7. Por fim, adicione as seguintes linhas ao método **Principal**:

        Console.WriteLine("Simulated device\n");
        deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey("myFirstDevice", deviceKey));

        SendDeviceToCloudMessagesAsync();
        Console.ReadLine();

> [AZURE.NOTE]Para simplificar, este tutorial não implementa nenhuma política de repetição. No código de produção, é recomendado implementar políticas de repetição (por exemplo, retirada exponencial), como sugerido no artigo do MSDN [Tratamento de falhas transitórias].

<!-- Links -->

[pacote NuGet do SDK de Dispositivo - IoT do Azure]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[Tratamento de falhas transitórias]: https://msdn.microsoft.com/pt-BR/library/hh680901(v=pandp.50).aspx

<!-- Images -->
[30]: ./media/iot-hub-getstarted-device-csharp/create-identity-csharp1.png

<!---HONumber=Oct15_HO1-->