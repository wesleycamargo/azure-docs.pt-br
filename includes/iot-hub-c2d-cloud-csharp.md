## Enviar uma mensagem da nuvem para o dispositivo por meio do back-end do aplicativo

Nesta seção, você escreverá um aplicativo do console do Windows que envia mensagens da nuvem para o dispositivo ao aplicativo do dispositivo simulado.

1. Na atual solução do Visual Studio, crie um novo projeto de Aplicativo da Área de Trabalho do Visual C# usando o modelo de projeto do **Aplicativo do Console**. Nomeie o projeto **SendCloudToDevice**.

   	![][20]

2. No Gerenciador de Soluções, clique com o botão direito do mouse na solução e, então, clique em **Gerenciar Pacotes NuGet para Solução...**.

	Isto mostra a janela Gerenciar Pacotes NuGet.

3. Pesquise por `Microsoft Azure Devices`, clique em **Instalar** e aceite os termos de uso.

	Isso baixa, instala e adiciona uma referência ao [pacote NuGet do SDK de Serviço - IoT do Azure].

4. Adicione a seguinte instrução `using` na parte superior do arquivo **Program.cs**:

		using Microsoft.Azure.Devices;

5. Adicione os seguintes campos à classe **Programa**, substituindo os valores de espaço reservado pelo nome e cadeia de conexão do hub IoT da seção [Introdução ao Hub IoT]\:

		static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";

6. Adicione o seguinte método à classe **Programa**:

		private async static Task SendCloudToDeviceMessageAsync()
        {
            var commandMessage = new Message(Encoding.ASCII.GetBytes("Cloud to device message."));
            await serviceClient.SendAsync("myFirstDevice", commandMessage);
        }

	Esse método enviará uma nova mensagem da nuvem para o dispositivo ao dispositivo com a ID `myFirstDevice`. Altere este parâmetro de acordo, caso tenha modificado aquele usado em [Introdução ao Hub IoT].

7. Por fim, adicione as seguintes linhas ao método **Principal**:

        Console.WriteLine("Send Cloud-to-Device message\n");
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);

        Console.WriteLine("Press any key to send a C2D message.");
        Console.ReadLine();
        SendCloudToDeviceMessageAsync().Wait();
        Console.ReadLine();

8. No Visual Studio, clique com o botão direito do mouse na solução e selecione **Definir projetos de inicialização...**. Selecione **Vários projetos de inicialização** e, em seguida, selecione a ação **Iniciar** para **ProcessDeviceToCloudMessages**, **SimulatedDevice** e **SendCloudToDevice**.

9.  Pressione **F5** para ver todos os três aplicativos serem iniciados. Selecione a janela **SendCloudToDevice** e pressione **Enter**: você deve ver a mensagem que está sendo recebida pelo aplicativo simulado.

    ![][21]

## Recebendo comentários de entrega
É possível solicitar a confirmação de entrega (ou expiração) de Hub IoT para cada mensagem da nuvem para o dispositivo. Isso permite que o back-end de nuvem para informar facilmente a lógica de repetição ou de compensação. Veja o [Guia do Desenvolvedor do Hub IoT][IoT Hub Developer Guide - C2D] para obter mais informações sobre comentários da nuvem para o dispositivo.

Nesta seção, você modificará o aplicativo **SendCloudToDevice** para solicitar comentários e recebê-los do Hub IoT.

1. No Visual Studio, no projeto **SendCloudToDevice**, adicione o método a seguir à classe **Program**.
   
        private async static void ReceiveFeedbackAsync()
        {
            var feedbackReceiver = serviceClient.GetFeedbackReceiver();

            Console.WriteLine("\nReceiving c2d feedback from service");
            while (true)
            {
                var feedbackBatch = await feedbackReceiver.ReceiveAsync();
                if (feedbackBatch == null) continue;

                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received feedback: {0}", string.Join(", ", feedbackBatch.Records.Select(f => f.StatusCode)));
                Console.ResetColor();

                await feedbackReceiver.CompleteAsync(feedbackBatch);
            }
        }

    É importante lembrar que o padrão de recebimento é o mesmo usado para receber mensagens da nuvem para o dispositivo do aplicativo do dispositivo.

2. Adicione o seguinte método no método **Main**, logo após a linha `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)`:

        ReceiveFeedbackAsync();

3. Para solicitar comentários para a entrega da mensagem da nuvem para o dispositivo, você deve especificar uma propriedade no método **SendCloudToDeviceMessageAsync**. Adicione a seguinte linha, logo após a linha `var commandMessage = new Message(...);`:

        commandMessage.Ack = DeliveryAcknowledgement.Full;

4.  Execute os aplicativos pressionando **F5** para ver todos os três aplicativos serem iniciados. Selecione a janela **SendCloudToDevice** e pressione **Enter**: você deve ver a mensagem recebida pelo aplicativo simulado e, depois de alguns segundos, a mensagem de comentários recebida pelo seu aplicativo **SendCloudToDevice**.

    ![][22]

> [AZURE.NOTE]Para simplificar, este tutorial não implementa nenhuma política de repetição. No código de produção, é recomendado implementar políticas de repetição (por exemplo, retirada exponencial), como sugerido no artigo do MSDN [Tratamento de falhas transitórias].

<!-- Links -->

[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d
[pacote NuGet do SDK de Serviço - IoT do Azure]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[Tratamento de falhas transitórias]: https://msdn.microsoft.com/pt-BR/library/hh680901(v=pandp.50).aspx
[Introdução ao Hub IoT]: iot-hub-csharp-csharp-getstarted.md

<!-- Images -->
[20]: ./media/iot-hub-c2d-cloud-csharp/create-identity-csharp1.png
[21]: ./media/iot-hub-c2d-cloud-csharp/sendc2d1.png
[22]: ./media/iot-hub-c2d-cloud-csharp/sendc2d2.png

<!---HONumber=Oct15_HO1-->