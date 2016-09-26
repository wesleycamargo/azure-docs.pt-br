<properties
	pageTitle="Carregar arquivos dos dispositivos usando Hub IoT | Microsoft Azure"
	description="Siga este tutorial para aprender a carregar arquivos por meio de dispositivos usando o Hub IoT do Azure com C#."
	services="iot-hub"
	documentationCenter=".net"
	authors="fsautomata"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="06/21/2016"
     ms.author="elioda"/>

# Tutorial: Como carregar arquivos de dispositivos para a nuvem com o Hub IoT

## Introdução

O Hub IoT do Azure é um serviço totalmente gerenciado que permite comunicações bidirecionais confiáveis e seguras entre milhões de dispositivos IoT e um back-end de aplicativo. Os tutoriais anteriores ([Introdução ao Hub IoT] e [Como enviar mensagens da nuvem para o dispositivo com o Hub IoT]) ilustram a funcionalidade básica de troca de mensagens do dispositivo para a nuvem e da nuvem para o dispositivo do Hub IoT, e o tutorial [Como processar mensagens de dispositivo para nuvem] descreve uma maneira de armazenar com segurança mensagens do dispositivo para a nuvem no armazenamento de Blobs do Azure. No entanto, em alguns cenários você não pode mapear facilmente os dados que seus dispositivos enviam em mensagens relativamente menores do dispositivo para a nuvem que o Hub IoT aceita. Exemplos incluem arquivos grandes que contenham imagens, vídeos, exemplos de dados de vibração em alta frequência ou que contenham alguma forma de dados pré-processados. Esses arquivos normalmente são processados em lote na nuvem usando ferramentas como o [Azure Data Factory] ou a pilha do [Hadoop]. Quando o modo de preferência para enviar eventos é o carregamento de arquivo, você ainda pode usar a funcionalidade de segurança e confiabilidade do Hub IoT.

Este tutorial se baseia no código do tutorial [Como enviar mensagens da nuvem para o dispositivo com o Hub IoT] para mostrar como usar os recursos de carregamento de arquivo do Hub IoT. Ele mostra como fornecer com segurança um dispositivo com um URI do blob do Azure para carregamento de um arquivo e como usar as notificações de carregamento de arquivo do Hub IoT para disparar o processamento do arquivo no back-end do aplicativo.

No fim deste tutorial, você executará dois aplicativos de console do Windows:

* **SimulatedDevice**, uma versão modificada do aplicativo criado no tutorial [Como enviar mensagens da nuvem para o dispositivo com o Hub IoT], que carrega um arquivo para o armazenamento usando um URI SAS fornecido pelo seu Hub IoT.
* **ReadFileUploadNotification**, que recebe notificações de carregamento de arquivo de seu Hub IoT.

> [AZURE.NOTE] O Hub IoT é compatível com muitas plataformas de dispositivo e linguagens (incluindo C, Java e Javascript) por meio dos SDKs do dispositivo IoT do Azure. Consulte o [Centro de Desenvolvedores do IoT do Azure] para obter instruções passo a passo sobre como conectar seu dispositivo ao código mostrado neste tutorial e, de modo geral, ao Hub IoT do Azure.

Para concluir este tutorial, você precisará do seguinte:

+ Microsoft Visual Studio 2015,

+ Uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação Gratuita do Azure][lnk-free-trial].)

## Associar uma conta do Armazenamento do Azure ao Hub IoT

Como o dispositivo simulado carrega um arquivo para um blob de armazenamento do Azure, você deve ter uma conta de [armazenamento do Azure] associada ao Hub IoT. Quando você associa uma conta de armazenamento a um hub IoT, o hub pode gerar um URI SAS que pode usar um dispositivo para carregar um arquivo com segurança em um contêiner de blob. O serviço de Hub IoT e os SDKs do dispositivo coordenam o processo que gera o URI de SAS e o torna disponível para um dispositivo a ser usado para carregar um arquivo.

Siga as instruções em [Gerenciar os hubs IoT através do portal do Azure] para associar uma conta de armazenamento do Azure para seu hub IoT.

## Carregar um arquivo por meio de um dispositivo simulado

Nesta seção, você modifica o aplicativo de dispositivo simulado criado em [Enviar mensagens de nuvem para o dispositivo com o Hub IoT] para receber mensagens de nuvem para dispositivos do Hub IoT.

1. No Visual Studio, clique com o botão direito o projeto **SimulatedDevice** clique em **Add**, e, em seguida, clique em **Item existente**. Navegue até um arquivo de imagem e inclua-o no projeto. Este tutorial presume que a imagem é denominada `image.jpg`.

2. Clique na imagem e, em seguida, clique em **Propriedades**. Verifique se **Copiar para o Diretório de Saída** é definido como **Copiar sempre**.

    ![][1]

3. No arquivo **Program.cs**, adicione as seguintes instruções na parte superior do arquivo:

        using System.IO;

4. Adicione o seguinte método à classe **Program**:
         
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

    O método `UploadToBlobAsync` dá permissão ao nome do arquivo e à fonte de fluxo do arquivo a ser carregado e manipula o carregamento para o armazenamento. O aplicativo de console exibe o tempo necessário para carregar o arquivo.

5. Adicione o seguinte método ao método **Main**, logo antes da linha `Console.ReadLine()`:

        SendToBlobAsync();

> [AZURE.NOTE] Para simplificar, este tutorial não implementa nenhuma política de repetição. No código de produção, implemente políticas de repetição (como uma retirada exponencial), conforme sugestão no artigo [Transient Fault Handling] \(Tratamento de Falhas Transitórias) do MSDN.

## Receber uma notificação de upload de arquivo

Nesta seção, você escreverá um aplicativo de console do Windows que recebe mensagens de notificação de upload de arquivo do Hub IoT.

1. Na solução atual do Visual Studio, crie um novo projeto do Visual C# do Windows usando o modelo de projeto **Aplicativo de Console**. Nomeie o projeto **ReadFileUploadNotification**.

    ![Novo projeto no Visual Studio][2]

2. No Solution Explorer, clique com o botão direito no projeto **ReadFileUploadNotification** e, em seguida, clique em **Manage NuGet Packages**.

    Isto mostra a janela Gerenciar Pacotes NuGet.

2. Pesquise por `Microsoft.Azure.Devices`, clique em **Instalar** e aceite os termos de uso.

	Isso baixa, instala e adiciona uma referência para o [IoT do Azure - pacote NuGet do SDK de serviço] no projeto **ReadFileUploadNotification**.

3. No arquivo **Program.cs**, adicione as seguintes instruções na parte superior do arquivo:

        using Microsoft.Azure.Devices;

4. Adicione os seguintes campos à classe **Program**. Substitua o valor de espaço reservado pela cadeia de conexão do Hub IoT a partir da [Introdução ao Hub IoT]\:

		static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";
        
5. Adicione o seguinte método à classe **Program**:
   
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

6. Por fim, adicione as seguintes linhas ao método **Main**:

        Console.WriteLine("Receive file upload notifications\n");
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
        ReceiveFileUploadNotificationAsync().Wait();
        Console.ReadLine();

## Executar os aplicativos

Agora você está pronto para executar os aplicativos.

1. No Visual Studio, clique com o botão direito para a solução e selecione **Definir projetos de inicialização**. Selecione **Vários projetos de inicialização**, em seguida, selecione a ação **Iniciar** para **ReadFileUploadNotification** e **SimulatedDevice**.

2. Pressione **F5**. Ambos os aplicativos devem ser iniciados. Você deve ver o carregamento concluído em um aplicativo de console e a mensagem de notificação do carregamento sendo recebida pelo outro aplicativo de console. Você pode usar o [Portal do Azure] ou o Gerenciador de Servidores do Visual Studio para verificar a presença do arquivo carregado em sua conta de armazenamento.

  ![][50]


## Próximas etapas

Neste tutorial, você aprendeu a aproveitar os recursos de carregamento de arquivo do Hub IoT para simplificar os carregamentos de arquivos de dispositivos. Você pode continuar a explorar os recursos de Hub IoT e os cenários com os seguintes artigos:

- [Criar um hub IoT programaticamente][lnk-create-hub]
- [Introdução ao SDK de C][lnk-c-sdk]
- [SDKs do Hub IoT][lnk-sdks]

Para explorar melhor as funcionalidades do Hub IoT, consulte:

- [Projetar sua solução][lnk-design]
- [Explorar o gerenciamento de dispositivo usando a interface do usuário de exemplo][lnk-dmui]
- [Simular um dispositivo com o SDK do Gateway][lnk-gateway]
- [Usar o Portal do Azure para gerenciar o Hub IoT][lnk-portal]

<!-- Images. -->

[50]: ./media/iot-hub-csharp-csharp-file-upload/run-apps1.png
[1]: ./media/iot-hub-csharp-csharp-file-upload/image-properties.png
[2]: ./media/iot-hub-csharp-csharp-file-upload/create-identity-csharp1.png

<!-- Links -->

[Portal do Azure]: https://portal.azure.com/

[Azure Data Factory]: https://azure.microsoft.com/documentation/services/data-factory/
[Hadoop]: https://azure.microsoft.com/documentation/services/hdinsight/

[Como enviar mensagens da nuvem para o dispositivo com o Hub IoT]: iot-hub-csharp-csharp-c2d.md
[Enviar mensagens de nuvem para o dispositivo com o Hub IoT]: iot-hub-csharp-csharp-c2d.md
[Como processar mensagens de dispositivo para nuvem]: iot-hub-csharp-csharp-process-d2c.md
[Introdução ao Hub IoT]: iot-hub-csharp-csharp-getstarted.md
[Centro de Desenvolvedores do IoT do Azure]: http://www.azure.com/develop/iot

[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[armazenamento do Azure]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Gerenciar os hubs IoT através do portal do Azure]: iot-hub-manage-through-portal.md#file-upload
[IoT do Azure - pacote NuGet do SDK de serviço]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-create-hub]: iot-hub-rm-template-powershell.md
[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-sdks-summary.md

[lnk-design]: iot-hub-guidance.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0914_2016-->