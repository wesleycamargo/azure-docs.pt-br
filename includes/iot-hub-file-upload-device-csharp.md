## Carregar um arquivo por meio de um dispositivo simulado

Nesta seção, você modificará o aplicativo do dispositivo simulado criado em [Enviar mensagens da nuvem para o dispositivo com o Hub IoT] para receber mensagens da nuvem para o dispositivo do hub IoT.

1. No Visual Studio, clique com o botão direito do mouse no projeto **SimulatedDevice** e em **Gerenciar Pacotes NuGet...**. 

    Isto mostra a janela Gerenciar Pacotes NuGet.

2. Pesquise por `WindowsAzure.Storage`, clique em **Instalar** e aceite os termos de uso.

    Isso baixa, instala e adiciona uma referência ao [SDK do Armazenamento do Microsoft Azure](https://www.nuget.org/packages/WindowsAzure.Storage/).

3. No arquivo **Program.cs**, adicione as seguintes instruções na parte superior do arquivo:

        using System.IO;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;

4. Na classe **Program**, altere o método **ReceiveC2dAsync** da seguinte maneira:
         
        private static async void ReceiveC2dAsync()
        {
            Console.WriteLine("\nReceiving cloud to device messages from service");
            while (true)
            {
                Message receivedMessage = await deviceClient.ReceiveAsync();
                if (receivedMessage == null) continue;

                if (receivedMessage.Properties.ContainsKey("command") && receivedMessage.Properties["command"] == "FileUpload")
                {
                    UploadFileToBlobAsync(receivedMessage);
                    continue;
                }

                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received message: {0}", Encoding.ASCII.GetString(receivedMessage.GetBytes()));
                Console.ResetColor();
            }
        }

    Isso faz com que **ReceiveC2dAsync** diferencie mensagens com a propriedade `command` definida como `FileUpload`, que será tratada pelo método **UploadFileToBlobAsync**.

    Adicione o método abaixo para manipular os comandos de upload de arquivo.
   
        private static async Task UploadFileToBlobAsync(Message fileUploadCommand)
        {
            var fileUri = fileUploadCommand.Properties["fileUri"];
            var blob = new CloudBlockBlob(new Uri(fileUri));

            byte[] data = new byte[10 * 1024 * 1024];
            Random rng = new Random();
            rng.NextBytes(data);

            MemoryStream msWrite = new MemoryStream(data);
            msWrite.Position = 0;
            using (msWrite)
            {
                await blob.UploadFromStreamAsync(msWrite);
            }
            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Uploaded file to: {0}", fileUri);
            Console.ResetColor();

            await deviceClient.CompleteAsync(fileUploadCommand);
        }

    Esse método usa o SDK do Armazenamento do Azure para carregar um blob de 10 MB gerado aleatoriamente para o URI especificado. Veja [Armazenamento do Azure - Como usar blobs] para obter mais informações sobre como carregar blobs.

> [AZURE.NOTE]É importante lembrar que essa implementação do dispositivo simulado conclui a mensagem da nuvem para o dispositivo somente depois que o blob for carregado. Essa abordagem simplifica o processamento dos arquivos carregados no back-end, pois a confirmação de entrega representa a disponibilidade do arquivo carregado para processamento. No entanto, como explicado no [Guia do Desenvolvedor do Hub IoT][IoT Hub Developer Guide - C2D], uma mensagem que não seja concluída antes do *tempo limite de visibilidade* (geralmente 1 minuto) é colocada de volta na fila de dispositivo e o método **ReceiveAsync()** a receberá novamente. Para cenários em que o upload de arquivo possa levar mais tempo, talvez seja preferível que o dispositivo simulado mantenha um armazenamento durável dos atuais trabalhos de upload. Isso permite que o dispositivo simulado conclua a mensagem da nuvem para o dispositivo antes de concluir o upload do arquivo e, em seguida, envie uma mensagem do dispositivo para a nuvem notificando o back-end da conclusão.

<!-- Links -->
[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d
[Armazenamento do Azure - Como usar blobs]: https://azure.microsoft.com/pt-BR/documentation/articles/storage-dotnet-how-to-use-blobs/#upload-a-blob-into-a-container

<!-- Images -->

<!---HONumber=Oct15_HO1-->