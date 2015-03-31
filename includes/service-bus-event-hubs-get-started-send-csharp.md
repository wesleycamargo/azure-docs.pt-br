## Enviar mensagens ao Hub de Eventos
Nesta seção, você escreverá um aplicativo de console do Windows para enviar eventos para o hub de eventos.

1. No Visual Studio, crie um novo projeto de aplicativo de área de trabalho do Visual C# usando o modelo de projeto de **Aplicativo de Console.** Nomeie o projeto como **Remetente**.

   	![][7]

2. No Gerenciador de Soluções, clique com o botão direito do mouse na solução e, então, clique em **Gerenciar Pacotes NuGet para Solução...**. 

	Isto mostra a caixa de diálogo Gerenciar Pacotes NuGet.

3. Pesquise por `Microsoft Azure Service Bus`, clique em **Instalar** e aceite os termos de uso. 

	![][8]

	Ele faz o download, instala e adiciona uma referência ao <a href="https://www.nuget.org/packages/WindowsAzure.ServiceBus/">pacote NuGet de biblioteca do Barramento de serviço do Azure</a>.

4. Adicione a seguinte instrução `using` na parte superior do arquivo **Program.cs**:

		using Microsoft.ServiceBus.Messaging;

5. Adicione os seguintes campos `static` à classe **Program**, substituindo os valores com o nome do hub de eventos que você criou na seção anterior, e a cadeia de conexão com direitos de **envio**:

		static string eventHubName = "{event hub name}";
        static string connectionString = "{send connection string}";

6. Adicionar o método a seguir à classe **Program**:

		static async Task SendingRandomMessages()
        {
            var eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, eventHubName);
            while (true)
            {
                try
                {
                    var message = Guid.NewGuid().ToString();
                    Console.WriteLine("{0} > Sending message: {1}", DateTime.Now.ToString(), message);
                    await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
                }
                catch (Exception exception)
                {
                    Console.ForegroundColor = ConsoleColor.Red;
                    Console.WriteLine("{0} > Exception: {1}", DateTime.Now.ToString(), exception.Message);
                    Console.ResetColor();
                }

                await Task.Delay(200);
            }
        }

	Esse método enviará continuamente os eventos ao seu hub de eventos com um atraso de 200 ms.

7. Por fim, adicione as seguintes linhas ao método **Main**:

		Console.WriteLine("Press Ctrl-C to stop the sender process");
        Console.WriteLine("Press Enter to start now");
        Console.ReadLine();
        SendingRandomMessages().Wait();


<!-- Images -->
[7]: ./media/service-bus-event-hubs-getstarted/create-sender-csharp1.png
[8]: ./media/service-bus-event-hubs-getstarted/create-sender-csharp2.png
<!--HONumber=47-->
