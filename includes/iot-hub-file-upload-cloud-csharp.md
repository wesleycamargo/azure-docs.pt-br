## Receber uma notificação de upload de arquivo
Nesta seção, você escreverá um aplicativo de console do Windows que recebe mensagens de notificação de upload de arquivo do Hub IoT.

1. Na atual solução do Visual Studio, crie um novo projeto do Visual C# do Windows usando o modelo de projeto do **Aplicativo do Console**. Nomeie o projeto **ReadFileUploadNotification**.
   
    ![Novo projeto no Visual Studio][2]
2. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **ReadFileUploadNotification** e clique em **Gerenciar Pacotes NuGet**.
   
    Isto mostra a janela Gerenciar Pacotes NuGet.
3. Pesquise por `Microsoft.Azure.Devices`, clique em **Instalar** e aceite os termos de uso.
   
    Esta ação baixa, instala e adiciona uma referência ao [pacote NuGet do SDK de Serviço – Azure IoT] no projeto **ReadFileUploadNotification**.
4. No arquivo **Program.cs**, adicione as seguintes instruções na parte superior do arquivo:
   
        using Microsoft.Azure.Devices;
5. Adicione os seguintes campos à classe **Program**. Substitua os valores de espaço reservado pela cadeia de conexão do Hub IoT da [Introdução ao Hub IoT]\:
   
        static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";
6. Adicione o seguinte método à classe **Programa**:
   
        private async static Task ReceiveFileUploadNotificationAsync()
        {
            var notificationReceiver = serviceClient.GetFileNotificationReceiver();
   
            Console.WriteLine("\nReceiving file upload notification from service");
            while (true)
            {
                var fileUploadNotification = await notificationReceiver.ReceiveAsync();
                if (fileUploadNotification == null) continue;
   
                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received file upload noticiation: {0}", string.Join(", ", fileUploadNotification.BlobName));
                Console.ResetColor();
   
                await notificationReceiver.CompleteAsync(fileUploadNotification);
            }
        }
   
    É importante lembrar que o padrão de recebimento é o mesmo usado para receber mensagens da nuvem para o dispositivo do aplicativo do dispositivo.
7. Por fim, adicione as seguintes linhas ao método **Principal**:
   
        Console.WriteLine("Receive file upload notifications\n");
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
        ReceiveFileUploadNotificationAsync().Wait();
        Console.ReadLine();

<!-- Links -->

[IoT Hub Developer Guide - C2D]: ../articles/iot-hub/iot-hub-devguide.md#c2d
[pacote NuGet do SDK de Serviço – Azure IoT]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Introdução ao Hub IoT]: ../articles/iot-hub/iot-hub-csharp-csharp-getstarted.md

<!-- Images -->
[2]: ./media/iot-hub-c2d-cloud-csharp/create-identity-csharp1.png

<!---HONumber=AcomDC_0622_2016-->