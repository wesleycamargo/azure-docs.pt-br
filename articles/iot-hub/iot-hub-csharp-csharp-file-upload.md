---
title: Carregar arquivos de dispositivos usando o Hub IoT do Azure | Microsoft Docs
description: "Como carregar arquivos de um dispositivo para a nuvem usando o SDK do dispositivo IoT do Azure para .NET. Os arquivos carregados são armazenados em um contêiner de Azure Storage Blob."
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: 
ms.assetid: 4759d229-f856-4526-abda-414f8b00a56d
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: elioda
translationtype: Human Translation
ms.sourcegitcommit: a243e4f64b6cd0bf7b0776e938150a352d424ad1
ms.openlocfilehash: ddc181bde6a154cb3fb35254da6b76a91450ba6b


---
# <a name="upload-files-from-devices-to-the-cloud-with-iot-hub"></a>Carregar arquivos de dispositivos para a nuvem com o Hub IoT
## <a name="introduction"></a>Introdução
O Hub IoT do Azure é um serviço totalmente gerenciado que permite comunicações bidirecionais confiáveis e seguras entre milhões de dispositivos e um back-end da solução. Os tutoriais anteriores ([Introdução ao Hub IoT] e [Enviar mensagens da nuvem para o dispositivo com o Hub IoT]) ilustram a funcionalidade básica de troca de mensagens do dispositivo para a nuvem e da nuvem para o dispositivo do Hub IoT, e o tutorial [Como processar mensagens de dispositivo para nuvem] descreve uma maneira de armazenar com segurança mensagens do dispositivo para a nuvem no armazenamento de Blobs do Azure. No entanto, em alguns cenários você não pode mapear facilmente os dados que seus dispositivos enviam em mensagens relativamente menores do dispositivo para a nuvem que o Hub IoT aceita. Exemplos incluem arquivos grandes que contenham imagens, vídeos, exemplos de dados de vibração em alta frequência ou que contenham alguma forma de dados pré-processados. Esses arquivos normalmente são processados em lote na nuvem usando ferramentas como o [Azure Data Factory] ou a pilha do [Hadoop]. Quando o modo de preferência para enviar eventos é o carregamento de arquivo, você ainda pode usar a funcionalidade de segurança e confiabilidade do Hub IoT.

Este tutorial se baseia no código do tutorial [Enviar mensagens da nuvem para o dispositivo com o Hub IoT] para mostrar como usar os recursos de carregamento de arquivo do Hub IoT. Ele mostra como:

- Fornecer com segurança um URI de blob do Azure a um dispositivo para carregamento de um arquivo.
- Usar as notificações de carregamento de arquivo do Hub IoT para disparar o processamento do arquivo no back-end do aplicativo.

No final deste tutorial, você executará dois aplicativos do console .NET:

* **SimulatedDevice**, uma versão modificada do aplicativo criado no tutorial [Enviar mensagens da nuvem para o dispositivo com o Hub IoT]. Esse aplicativo carrega um arquivo no armazenamento usando um URI SAS fornecido pelo seu Hub IoT.
* **ReadFileUploadNotification**, que recebe notificações de carregamento de arquivo de seu Hub IoT.

> [!NOTE]
> O Hub IoT é compatível com muitas plataformas de dispositivo e linguagens (incluindo C, Java e Javascript) por meio dos SDKs do dispositivo IoT do Azure. Confira a [Central de Desenvolvedores do IoT do Azure] para obter instruções passo a passo sobre como conectar seu dispositivo ao Hub IoT do Azure.
> 
> 

Para concluir este tutorial, você precisará do seguinte:

* Microsoft Visual Studio 2015,
* Uma conta ativa do Azure. (Se você não tem uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.)

## <a name="associate-an-azure-storage-account-to-iot-hub"></a>Associar uma conta do Armazenamento do Azure ao Hub IoT
Como o aplicativo do dispositivo simulado carrega um arquivo em um blob, você deve ter uma conta de [Armazenamento do Azure] associada ao Hub IoT. Quando você associa uma conta de Armazenamento do Azure a um Hub IoT, este pode gerar um URI SAS que um dispositivo pode usar para carregar com segurança um arquivo em um contêiner de blobs. O serviço de Hub IoT e os SDKs do dispositivo coordenam o processo que gera o URI de SAS e o torna disponível para um dispositivo a ser usado para carregar um arquivo.

Siga as instruções em [Configurar carregamentos de arquivo usando o Portal do Azure][lnk-configure-upload] para associar uma conta de Armazenamento do Azure ao seu Hub IoT.

## <a name="upload-a-file-from-a-simulated-device-app"></a>Carregar um arquivo de um aplicativo do dispositivo simulado
Nesta seção, você modifica o aplicativo do dispositivo simulado criado em [Enviar mensagens da nuvem para o dispositivo com o Hub IoT] para receber mensagens da nuvem para o dispositivo do Hub IoT.

1. No Visual Studio, clique com o botão direito no projeto **SimulatedDevice**, clique em **Adicionar** e, em seguida, clique em **Item Existente**. Navegue até um arquivo de imagem e inclua-o no projeto. Este tutorial pressupõe que a imagem é denominada `image.jpg`.
2. Clique com o botão direito do mouse na imagem e clique em **Propriedades**. Verifique se **Copiar para o Diretório de Saída** está definido como **Copiar sempre**.
   
    ![][1]
3. No arquivo **Program.cs** , adicione as seguintes instruções na parte superior do arquivo:
   
        using System.IO;
4. Adicione o seguinte método à classe **Programa** :
   
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
> Para simplificar, este tutorial não implementa nenhuma política de repetição. No código de produção, implemente políticas de repetição (como uma retirada exponencial), conforme sugestão no artigo [Transient Fault Handling](Tratamento de Falhas Transitórias) do MSDN.
> 
> 

## <a name="receive-a-file-upload-notification"></a>Receber uma notificação de upload de arquivo
Nesta seção, você escreverá um aplicativo de console do .NET que recebe mensagens de notificação de upload de arquivo do Hub IoT.

1. Na solução atual do Visual Studio, crie um projeto do Visual C# do Windows usando o modelo de projeto do **Aplicativo do Console** . Nomeie o projeto **ReadFileUploadNotification**.
   
    ![Novo projeto no Visual Studio][2]
2. No Gerenciador de Soluções, clique com o botão direito no projeto **ReadFileUploadNotification** e, em seguida, clique em **Gerenciar Pacotes NuGet**.
   
    Essa ação mostra a janela Gerenciar Pacotes NuGet.
3. Pesquise por `Microsoft.Azure.Devices`, clique em **Instalar**e aceite os termos de uso. 
   
    Essa ação baixa, instala e adiciona uma referência ao [pacote NuGet do SDK do serviço IoT do Azure] no projeto **ReadFileUploadNotification**.

4. No arquivo **Program.cs** , adicione as seguintes instruções na parte superior do arquivo:
   
        using Microsoft.Azure.Devices;
5. Adicione os seguintes campos à classe **Program** . Substitua os valores de espaço reservado pela cadeia de conexão do Hub IoT da [Introdução ao Hub IoT]:
   
        static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";
6. Adicione o seguinte método à classe **Programa** :
   
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
7. Por fim, adicione as seguintes linhas ao método **Main** :
   
        Console.WriteLine("Receive file upload notifications\n");
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
        ReceiveFileUploadNotificationAsync().Wait();
        Console.ReadLine();

## <a name="run-the-applications"></a>Executar os aplicativos
Agora você está pronto para executar os aplicativos.

1. No Visual Studio, clique com o botão direito para a solução e selecione **Definir projetos de inicialização**. Selecione **Vários projetos de inicialização**, em seguida, selecione a ação **Iniciar** para **ReadFileUploadNotification** e **SimulatedDevice**.
2. Pressione **F5**. Ambos os aplicativos devem ser iniciados. Você deve ver o carregamento concluído em um aplicativo de console e a mensagem de notificação do carregamento sendo recebida pelo outro aplicativo de console. Você pode usar o [Portal do Azure] ou o Gerenciador de Servidores do Visual Studio para verificar a presença do arquivo carregado em sua conta de Armazenamento do Azure.
   
   ![][50]

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu a aproveitar os recursos de carregamento de arquivo do Hub IoT para simplificar os carregamentos de arquivos de dispositivos. Você pode continuar explorando os recursos e cenários do Hub IoT com os seguintes artigos:

* [Criar um Hub IoT de modo programático][lnk-create-hub]
* [Introdução ao SDK de C][lnk-c-sdk]
* [SDKs do Azure IoT][lnk-sdks]

Para explorar melhor as funcionalidades do Hub IoT, consulte:

* [Simulando um dispositivo com o SDK de Gateway IoT][lnk-gateway]

<!-- Images. -->

[50]: ./media/iot-hub-csharp-csharp-file-upload/run-apps1.png
[1]: ./media/iot-hub-csharp-csharp-file-upload/image-properties.png
[2]: ./media/iot-hub-csharp-csharp-file-upload/create-identity-csharp1.png

<!-- Links -->

[Portal do Azure]: https://portal.azure.com/

[Azure Data Factory]: https://azure.microsoft.com/documentation/services/data-factory/
[Hadoop]: https://azure.microsoft.com/documentation/services/hdinsight/

[Enviar mensagens da nuvem para o dispositivo com o Hub IoT]: iot-hub-csharp-csharp-c2d.md
[Como processar mensagens de dispositivo para nuvem]: iot-hub-csharp-csharp-process-d2c.md
[Introdução ao Hub IoT]: iot-hub-csharp-csharp-getstarted.md
[Central de Desenvolvedores do IoT do Azure]: http://www.azure.com/develop/iot

[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Armazenamento do Azure]: ../storage/storage-create-storage-account.md#create-a-storage-account
[lnk-configure-upload]: iot-hub-configure-file-upload.md
[pacote NuGet do SDK do serviço IoT do Azure]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md





<!--HONumber=Dec16_HO1-->


