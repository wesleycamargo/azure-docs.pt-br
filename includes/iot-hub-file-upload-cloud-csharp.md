## Provisionar uma conta do Armazenamento do Azure
Já que o dispositivo simulado carregará um arquivo em um blob do Armazenamento do Azure, é necessário ter uma conta do Armazenamento do Azure. Você pode usar uma já existente ou seguir as instruções em [Sobre o Armazenamento do Azure] para criar uma nova. Anote a cadeia de conexão da conta de armazenamento.

## Enviar um URI do blob do Azure para o dispositivo simulado

Nesta seção, você modificará o aplicativo do console **SendCloudtoDevice** criado em [Enviar mensagens da nuvem para o dispositivo com o Hub IoT] para incluir um URI do blob do Azure com uma assinatura de acesso compartilhado. Isso permite que o back-end de nuvem conceda acesso de gravação ao blob apenas para o destinatário da mensagem da nuvem para o dispositivo.

1. No Visual Studio, clique com o botão direito do mouse no projeto **SendCloudtoDevice** e em **Gerenciar Pacotes NuGet...**. 

    Isto mostra a janela Gerenciar Pacotes NuGet.

2. Pesquise por `WindowsAzure.Storage`, clique em **Instalar** e aceite os termos de uso.

    Isso baixa, instala e adiciona uma referência ao [SDK do Armazenamento do Microsoft Azure](https://www.nuget.org/packages/WindowsAzure.Storage/).

3. No arquivo **Program.cs**, adicione as seguintes instruções na parte superior do arquivo:

        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;

4. Na classe **Program**, adicione os campos de classe a seguir, substituindo a cadeia de conexão de sua conta de armazenamento.

        static string storageConnectionString = "{storage connection string}";

    Em seguida, adicione o seguinte método (você pode substituir qualquer nome de contêiner de blob; este tutorial usa **iothubfileuploadtutorial**):
   
        private static async Task<string> GenerateBlobUriAsync()
        {
            var storageAccount = CloudStorageAccount.Parse(storageConnectionString);
            var blobClient = storageAccount.CreateCloudBlobClient();
            var blobContainer = blobClient.GetContainerReference("iothubfileuploadtutorial");
            await blobContainer.CreateIfNotExistsAsync();

            var blobName = String.Format("deviceUpload_{0}", Guid.NewGuid().ToString());
            CloudBlockBlob blob = blobContainer.GetBlockBlobReference(blobName);

            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
            sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
            sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
            sasConstraints.Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write;
            string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

            return blob.Uri + sasBlobToken;
        }

    Esse método cria uma nova referência de blob e gera um URI de assinatura de acesso compartilhado, como descrito em [Criar e usar uma SAS com o serviço Blob](https://azure.microsoft.com/pt-BR/documentation/articles/storage-dotnet-shared-access-signature-part-2/). Vale lembrar que o método acima gera um URI de assinatura que é válido por 24 horas. Se o dispositivo de destino precisar de mais tempo para carregar o arquivo (por exemplo, ele se conecta com pouca frequência ou ele tem uma conectividade não confiável para carregar um arquivo grande), você pode considerar tempos de expiração mais longos para as assinaturas.

5. Modifique **SendCloudToDeviceMessageAsync** da seguinte maneira:

        private async static Task SendCloudToDeviceMessageAsync()
        {
            var commandMessage = new Message();
            commandMessage.Properties["command"] = "FileUpload";
            commandMessage.Properties["fileUri"] = await GenerateBlobUriAsync();
            commandMessage.Ack = DeliveryAcknowledgement.Full;

            await serviceClient.SendAsync("myFirstDevice", commandMessage);
        }

    Esse método envia uma mensagem da nuvem para o dispositivo que contém duas propriedades de aplicativo: uma identificando essa mensagem como um comando para carregar um arquivo e a outra contendo o URI do blob. Ele também solicita confirmações de entrega completa. Vale lembrar que as informações nas duas propriedades de aplicativo podem ser serializadas no corpo da mensagem, mas isso incorreria em processamento adicional para serializar e desserializar as informações.

<!-- Links -->

[Sobre o Armazenamento do Azure]: https://azure.microsoft.com/pt-BR/documentation/articles/storage-create-storage-account/#create-a-storage-account

[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d
[Azure IoT - Service SDK NuGet package]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[Transient Fault Handling]: https://msdn.microsoft.com/pt-BR/library/hh680901(v=pandp.50).aspx
[Get started with IoT Hub]: iot-hub-csharp-csharp-getstarted.md

<!-- Images -->

<!---HONumber=Oct15_HO3-->