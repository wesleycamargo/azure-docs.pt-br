## Associar uma conta do Armazenamento do Azure ao Hub IoT
Como o dispositivo simulado carrega um arquivo em um blob do Armazenamento do Azure, é necessário ter uma conta do Armazenamento do Azure associada ao Hub IoT. Você pode usar uma conta de armazenamento existente ou seguir as instruções em [Sobre o Armazenamento do Azure] para criar uma nova. É possível associar uma conta do Armazenamento do Azure ao Hub IoT seguindo as instruções em [Gerenciar hubs IoT por meio do portal do Azure].

## Carregar um arquivo por meio de um dispositivo simulado
Nesta seção, você modificará o aplicativo do dispositivo simulado criado em [Enviar mensagens da nuvem para o dispositivo com o Hub IoT] para receber mensagens da nuvem para o dispositivo do hub IoT.

1. No Visual Studio, clique com o botão direito do mouse no projeto **SimulatedDevice**, clique em **Adicionar** e em **Item Existente**. Navegue até um arquivo de imagem e inclua-o no projeto. Este tutorial pressupõe que a imagem é denominada `image.jpg`.
2. Clique com o botão direito do mouse na imagem e clique em **Propriedades**. Verifique se **Copiar para Diretório de Saída** está definido como **Copiar sempre**.
   
    ![][1]
3. No arquivo **Program.cs**, adicione as seguintes instruções na parte superior do arquivo:
   
        using System.IO;
4. Adicione o seguinte método à classe **Programa**:
   
        private static async void SendToBlobAsync()
        {
            string fileName = "image.jpg";
            Console.WriteLine("Uploading file: {0}", fileName);
            var watch = System.Diagnostics.Stopwatch.StartNew();
   
            using (var sourceData = new FileStream(@"image.jpg", FileMode.Open))
            {
                await deviceClient.UploadToBlobAsync(fileName, sourceData);
            }
   
            watch.Stop();
            Console.WriteLine("Time to upload file: {0}ms\n", watch.ElapsedMilliseconds);
        }
   
    O método `UploadToBlobAsync` usa o nome de arquivo e a fonte de transmissão do arquivo a ser carregado e manipula o upload para o armazenamento. O aplicativo de console exibe o tempo necessário para carregar o arquivo.
5. Adicione o seguinte método ao método **Main**, logo antes da linha `Console.ReadLine()`:
   
        SendToBlobAsync();

> [!NOTE]
> Para simplificar, este tutorial não implementa nenhuma política de repetição. No código de produção, implemente políticas de repetição (como uma retirada exponencial), como sugerido no artigo [Transient Fault Handling] \(Tratamento de falhas transitórias) do MSDN.
> 
> 

<!-- Links -->
[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Sobre o Armazenamento do Azure]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Gerenciar hubs IoT por meio do portal do Azure]: ../articles/iot-hub-manage-through-portal/#file-upload

<!-- Images -->
[1]: ../articles/iot-hub/media/iot-hub-csharp-csharp-file-upload/image-properties.png

<!---HONumber=AcomDC_0622_2016-->