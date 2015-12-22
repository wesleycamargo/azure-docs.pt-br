## Criar um aplicativo de dispositivo simulado

Nesta seção, você criará um aplicativo do console do Windows que simula um dispositivo que envia mensagens do dispositivo para a nuvem para um hub IoT.

1. No Visual Studio, adicione um novo projeto de Área de Trabalho Clássica do Windows no Visual C# para a solução atual usando o modelo de projeto do **Aplicativo do Console**. Chame o projeto de **SimulatedDevice**.

   	![][30]

2. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **SimulatedDevice** e clique em **Gerenciar Pacotes NuGet**.

3. Na janela **Gerenciador de Pacotes NuGet**, verifique se a opção **Incluir pré-lançamento** está marcada. Procure **Cliente de Dispositivos do Microsoft Azure**, clique em **Instalar** e aceite os termos de uso.

	Isso baixa, instala e adiciona uma referência ao [pacote NuGet do SDK do Dispositivo - IoT do Azure][lnk-device-nuget].

4. Adicione a seguinte instrução `using` na parte superior do arquivo **Program.cs**:

		using Microsoft.Azure.Devices.Client;
        using Newtonsoft.Json;
        using System.Threading;

5. Adicione os seguintes campos à classe **Program**, substituindo os valores de espaço reservado pelo nome de host do hub IoT recuperado na seção *Criar um hub IoT* e a chave de dispositivo recuperada na seção *Criar uma identidade do dispositivo*:

		static DeviceClient deviceClient;
        static string iotHubUri = "{iot hub hostname}";
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

	Esse método envia uma nova mensagem de dispositivo para a nuvem a cada segundo. A mensagem contém um objeto serializado por JSON com a deviceId e um número gerado aleatoriamente para simular um sensor de velocidade do vento.

7. Por fim, adicione as seguintes linhas ao método **Principal**:

        Console.WriteLine("Simulated device\n");
        deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey("myFirstDevice", deviceKey));

        SendDeviceToCloudMessagesAsync();
        Console.ReadLine();

  Por padrão, o método **Create** cria um **DeviceClient** que usa o protocolo AMQP para se comunicar com o Hub IoT. Para usar o protocolo HTTPS, use a substituição do método **Create** que permite especificar o protocolo. Se você optar por usar o protocolo HTTPS, você também deve adicionar o pacote NuGet **Microsoft.AspNet.WebApi.Client** ao seu projeto para incluir o namespace **System.Net.Http.Formatting**.


> [AZURE.NOTE]Para simplificar, este tutorial não implementa nenhuma política de repetição. No código de produção, implemente políticas de repetição (como uma retirada exponencial), como sugerido no artigo [Tratamento de falhas transitórias][lnk-transient-faults] do MSDN.

<!-- Links -->

[lnk-device-nuget]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-transient-faults]: https://msdn.microsoft.com/pt-BR/library/hh680901(v=pandp.50).aspx

<!-- Images -->
[30]: ./media/iot-hub-getstarted-device-csharp/create-identity-csharp1.png

<!---HONumber=AcomDC_1217_2015-->