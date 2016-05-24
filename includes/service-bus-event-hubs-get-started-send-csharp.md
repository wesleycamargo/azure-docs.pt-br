## Enviar mensagens ao Hub de Eventos

Nesta seção, você escreverá um aplicativo de console do Windows para enviar eventos para o hub de eventos.

1. No Visual Studio, crie um novo projeto de aplicativo de área de trabalho do Visual C# usando o modelo de projeto de **Aplicativo de Console**. Nomeie o projeto como **Remetente**.

	![][7]

2. No Gerenciador de Soluções, clique com o botão direito na solução e clique em **Gerenciar Pacotes NuGet para Solução**.

3. Clique na guia **Procurar** e pesquise `Microsoft Azure Service Bus`. Verifique se o nome do projeto (**Remetente**) está especificado na caixa **Versão(ões)**. Clique em **Instalar** e aceite os termos de uso.

	![][8]

	Ele faz o download, instala e adiciona uma referência ao <a href="https://www.nuget.org/packages/WindowsAzure.ServiceBus/">pacote NuGet de biblioteca do Barramento de Serviço do Azure</a>.

4. Adicione as instruções `using` abaixo na parte superior do arquivo **Program.cs**:

	```
	using System.Threading;
	using Microsoft.ServiceBus.Messaging;
	```

5. Adicione os seguintes campos à classe **Programa**, substituindo os valores do espaço reservado pelo nome do Hub de Eventos criado na seção anterior e a cadeia de conexão no nível do namespace que você salvou anteriormente.

	```
	static string eventHubName = "{Event Hub name}";
	static string connectionString = "{send connection string}";
	```

6. Adicione o seguinte método à classe **Programa**:

	```
	static void SendingRandomMessages()
	{
	    var eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, eventHubName);
	    while (true)
	    {
	        try
	        {
	            var message = Guid.NewGuid().ToString();
	            Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, message);
	            eventHubClient.Send(new EventData(Encoding.UTF8.GetBytes(message)));
	        }
	        catch (Exception exception)
	        {
	            Console.ForegroundColor = ConsoleColor.Red;
	            Console.WriteLine("{0} > Exception: {1}", DateTime.Now, exception.Message);
	            Console.ResetColor();
	        }

	        Thread.Sleep(200);
	    }
	}
	```

	Esse método envia continuamente os eventos ao seu hub de eventos com um atraso de 200 ms.

7. Por fim, adicione as seguintes linhas ao método **Principal**:

	```
	Console.WriteLine("Press Ctrl-C to stop the sender process");
	Console.WriteLine("Press Enter to start now");
	Console.ReadLine();
	SendingRandomMessages();
	```


<!-- Images -->
[7]: ./media/service-bus-event-hubs-getstarted/create-sender-csharp1.png
[8]: ./media/service-bus-event-hubs-getstarted/create-sender-csharp2.png