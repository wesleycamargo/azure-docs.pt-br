---
title: Enviar mensagens da nuvem para o dispositivo com o Hub IoT| Microsoft Docs
description: Siga este tutorial para aprender a enviar mensagens da nuvem para o dispositivo usando o Hub IoT do Azure com C#.
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/23/2016
ms.author: elioda

---
# Tutorial: Como enviar mensagens de nuvem para o dispositivo com o Hub IoT e .Net
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## Introdução
O Hub IoT do Azure é um serviço totalmente gerenciado que ajuda a permitir comunicações bidirecionais confiáveis e seguras entre milhões de dispositivos IoT e um back-end de aplicativo. O tutorial [Introdução ao Hub IoT] mostra como criar um hub IoT, provisionar uma identidade do dispositivo e codificar um dispositivo simulado que envia mensagens do dispositivo para a nuvem.

Esse tutorial se baseia [Começar com ao Hub IoT]. Ele mostra como:

* Do seu aplicativo de nuvem de back-end, envie mensagens da nuvem para o dispositivo em um único dispositivo por meio do Hub IoT.
* Receber mensagens da nuvem para o dispositivo em um dispositivo.
* Do seu aplicativo de nuvem de back-end, solicitar confirmação de entrega (*comentários*) para as mensagens enviadas a um dispositivo do Hub IoT.

Encontre mais informações sobre as mensagens da nuvem para o dispositivo no [Guia do Desenvolvedor do Hub IoT][IoT Hub Developer Guide - C2D].

No final deste tutorial, você executará dois aplicativos de console do Windows:

* **SimulatedDevice**, uma versão modificada do aplicativo criado na [Introdução ao Hub IoT], que se conecta a seu hub IoT e recebe mensagens da nuvem para o dispositivo.
* **SendCloudToDevice**, que envia uma mensagem da nuvem para o dispositivo ao dispositivo simulado por meio do Hub IoT e recebe sua confirmação de entrega.

> [!NOTE]
> O Hub IoT tem suporte a SDK para várias plataformas de dispositivo e linguagens (incluindo C, Java e Javascript) nos SDKs do dispositivo IoT do Azure. Para obter instruções passo a passo sobre como conectar seu dispositivo ao código deste tutorial e, em geral, ao Hub IoT do Azure, veja o [Centro de Desenvolvedores do IoT do Azure].
> 
> 

Para concluir esse tutorial, você precisará do seguinte:

* Microsoft Visual Studio 2015
* Uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação Gratuita do Azure][lnk-free-trial].)

## Receber mensagens no dispositivo simulado
Nesta seção, você modificará o aplicativo do dispositivo simulado criado na [Introdução ao Hub IoT] para receber mensagens da nuvem para o dispositivo do Hub IoT.

1. No Visual Studio, no projeto **SimulatedDevice**, adicione o método a seguir à classe **Program**.
   
        private static async void ReceiveC2dAsync()
        {
            Console.WriteLine("\nReceiving cloud to device messages from service");
            while (true)
            {
                Message receivedMessage = await deviceClient.ReceiveAsync();
                if (receivedMessage == null) continue;
   
                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received message: {0}", Encoding.ASCII.GetString(receivedMessage.GetBytes()));
                Console.ResetColor();
   
                await deviceClient.CompleteAsync(receivedMessage);
            }
        }
   
    O método `ReceiveAsync` retorna de forma assíncrona a mensagem recebida no momento em que ela é recebida pelo dispositivo. Ela retorna *null* após um período de tempo limite especificável (nesse caso, será usado o padrão de um minuto). Quando isso acontece, o código deve continuar a aguardar novas mensagens. Essa é a razão para a linha `if (receivedMessage == null) continue`.
   
    A chamada para `CompleteAsync()` notifica o Hub IoT de que a mensagem foi processada com êxito. A mensagem pode ser removida da fila do dispositivo com segurança. Se ocorreu algo que impediu que o aplicativo do dispositivo concluísse o processamento da mensagem, o Hub IoT a entrega novamente. Também é importante que a lógica de processamento de mensagem no aplicativo do dispositivo seja *idempotente*, de modo que receber a mesma mensagem várias vezes produz o mesmo resultado. Um aplicativo também pode abandonar temporariamente uma mensagem, o que resulta em um Hub IoT reter a mensagem na fila para consumo futuro. Ou, o aplicativo pode rejeitar uma mensagem, que a remove permanentemente da fila. Para saber mais sobre o ciclo de vida da mensagem da nuvem para o dispositivo, veja o [Guia do desenvolvedor do Hub IoT][IoT Hub Developer Guide - C2D].
   
   > [!NOTE]
   > Ao usar o HTTP/1 em vez do AMQP como transporte, o método `ReceiveAsync` é retornado imediatamente. O padrão com suporte para mensagens da nuvem para o dispositivo com o HTTP/1 são dispositivos conectados intermitentemente que verificam mensagens com pouca frequência (menos do que a cada 25 minutos). Emitir mais recebimentos de HTTP/1 resulta na limitação das solicitações pelo Hub IoT. Para saber mais sobre as diferenças entre o suporte do AMQP e HTTP/1 e a limitação do Hub IoT, veja o [Guia do desenvolvedor do Hub IoT][IoT Hub Developer Guide - C2D].
   > 
   > 
2. Adicione o seguinte método ao método **Main**, logo antes da linha `Console.ReadLine()`:
   
        ReceiveC2dAsync();

> [!NOTE]
> Para simplificar, este tutorial não implementa nenhuma política de repetição. No código de produção, implemente políticas de repetição (como uma retirada exponencial), conforme sugestão no artigo [Tratamento de Falhas Transitórias] do MSDN.
> 
> 

## Envie uma mensagem da nuvem para o dispositivo
Nesta seção, você escreverá um aplicativo do console do Windows que envia mensagens da nuvem para o dispositivo ao aplicativo do dispositivo simulado.

1. Na solução do Visual Studio atual, crie um novo projeto de Aplicativo da Área de Trabalho do Visual C# usando o modelo de projeto do **Aplicativo do Console**. Nomeie o projeto **SendCloudToDevice**.
   
    ![Novo projeto no Visual Studio][20]
2. No Gerenciador de Soluções, clique com o botão direito do mouse na solução e, então, clique em **Gerenciar Pacotes NuGet para Solução...**.
   
    Isso abre a janela **Gerenciar Pacotes NuGet**.
3. Pesquise por `Microsoft Azure Devices`, clique em **Instalar** e aceite os termos de uso.
   
    Isso baixa, instala e adiciona uma referência ao [pacote NuGet do SDK de Serviço - IoT do Azure].
4. Adicione a seguinte instrução `using` na parte superior do arquivo **Program.cs**:
   
        using Microsoft.Azure.Devices;
5. Adicione os seguintes campos à classe **Program**. Substitua o valor de espaço reservado pela cadeia de conexão do Hub IoT na [Introdução ao Hub IoT]\:
   
        static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";
6. Adicione o seguinte método à classe **Programa**:
   
        private async static Task SendCloudToDeviceMessageAsync()
        {
            var commandMessage = new Message(Encoding.ASCII.GetBytes("Cloud to device message."));
            await serviceClient.SendAsync("myFirstDevice", commandMessage);
        }
   
    Esse método envia uma nova mensagem da nuvem para o dispositivo ao dispositivo com a ID `myFirstDevice`. Altere este parâmetro da maneira correta, caso o tenha modificado com base no usado em [Introdução ao Hub IoT].
7. Por fim, adicione as seguintes linhas ao método **Principal**:
   
        Console.WriteLine("Send Cloud-to-Device message\n");
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
   
        Console.WriteLine("Press any key to send a C2D message.");
        Console.ReadLine();
        SendCloudToDeviceMessageAsync().Wait();
        Console.ReadLine();
8. No Visual Studio, clique com o botão direito do mouse na solução e selecione **Definir Projetos de inicialização...**. Selecione **Vários projetos de inicialização** e, em seguida, selecione a ação **Iniciar** para **ProcessDeviceToCloudMessages**, **SimulatedDevice** e **SendCloudToDevice**.
9. Pressione **F5**. Todos os três aplicativos devem ser iniciados. Selecione as janelas **SendCloudToDevice** e pressione **Enter**. Você deve ver a mensagem que está sendo recebida pelo aplicativo simulado.
   
   ![Aplicativo recebendo mensagens][21]

## Receber comentários de entrega
É possível solicitar confirmações de entrega (ou expiração) de Hub IoT para cada mensagem da nuvem para o dispositivo. Isso permite que o back-end de nuvem informe a lógica de repetição ou de compensação facilmente. Para saber mais sobre os comentários da nuvem para o dispositivo, veja o [Guia do Desenvolvedor do Hub IoT][IoT Hub Developer Guide - C2D].

Nesta seção, você modificará o aplicativo **SendCloudToDevice** para solicitar comentários e recebê-lo do Hub IoT.

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
2. Adicione o seguinte método ao método **Main**, logo após a linha `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)`:
   
        ReceiveFeedbackAsync();
3. Para solicitar comentários sobre a entrega da mensagem da nuvem para o dispositivo, você deve especificar uma propriedade no método **SendCloudToDeviceMessageAsync**. Adicione a seguinte linha, logo após a linha `var commandMessage = new Message(...);`:
   
        commandMessage.Ack = DeliveryAcknowledgement.Full;
4. Execute os aplicativos pressionando **F5**. Você deve ver todos os três aplicativos serem iniciados. Selecione as janelas **SendCloudToDevice** e pressione **Enter**. Você verá a mensagem recebida pelo aplicativo simulado e, depois de alguns segundos, a mensagem de comentários recebida pelo aplicativo **SendCloudToDevice**.
   
   ![Aplicativo recebendo mensagens][22]

> [!NOTE]
> Para simplificar, este tutorial não implementa nenhuma política de repetição. No código de produção, implemente políticas de repetição (como uma retirada exponencial), conforme sugestão no artigo [Tratamento de Falhas Transitórias] do MSDN.
> 
> 

## Próximas etapas
Neste tutorial você aprendeu a enviar e receber mensagens da nuvem para o dispositivo.

Para ver exemplos de soluções completas que usam o Hub IoT, consulte [Azure IoT Suite].

Para saber mais sobre como desenvolver soluções com o Hub IoT, consulte o [Guia do desenvolvedor do Hub IoT].

<!-- Images -->
[20]: ./media/iot-hub-csharp-csharp-c2d/create-identity-csharp1.png
[21]: ./media/iot-hub-csharp-csharp-c2d/sendc2d1.png
[22]: ./media/iot-hub-csharp-csharp-c2d/sendc2d2.png

<!-- Links -->

[pacote NuGet do SDK de Serviço - IoT do Azure]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[Tratamento de Falhas Transitórias]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d

[Guia do desenvolvedor do Hub IoT]: iot-hub-devguide.md
[Começar com ao Hub IoT]: iot-hub-csharp-csharp-getstarted.md
[Introdução ao Hub IoT]: iot-hub-csharp-csharp-getstarted.md
[Centro de Desenvolvedores do IoT do Azure]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[Azure IoT Suite]: https://azure.microsoft.com/documentation/suites/iot-suite/

<!---HONumber=AcomDC_0727_2016-->