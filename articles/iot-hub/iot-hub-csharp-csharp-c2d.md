---
title: Mensagens da nuvem para o dispositivo com o Hub IoT do Azure (.NET)| Microsoft Docs
description: Como enviar mensagens da nuvem para o dispositivo para um dispositivo de um Hub IoT do Azure usando os SDKs do IoT do Azure para .NET. Você modifica um aplicativo de dispositivo para receber mensagens da nuvem para o dispositivo e modificar um aplicativo de back-end para enviá-las.
author: fsautomata
manager: ''
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/24/2017
ms.author: elioda
ms.openlocfilehash: 2dca04cadd4c5b904c752ac8a01af8bcb7c6ae33
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47220101"
---
# <a name="send-messages-from-the-cloud-to-your-device-with-iot-hub-net"></a>Enviar mensagens de nuvem para seu dispositivo com o Hub IoT (.NET)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Introdução

O Hub IoT do Azure é um serviço totalmente gerenciado que ajuda a permitir comunicações bidirecionais confiáveis e seguras entre milhões de dispositivos e um back-end de solução. [Enviar telemetria de um dispositivo para um Hub IoT...](quickstart-send-telemetry-dotnet.md) mostra como criar um Hub IoT, como provisionar uma identidade do dispositivo nele e como codificar um aplicativo de dispositivo que envia mensagens de dispositivo para nuvem.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Este tutorial se baseia no início rápido [Enviar telemetria de um dispositivo para um Hub IoT...](quickstart-send-telemetry-dotnet.md). Ele mostra como executar as etapas a seguir:

* Da sua solução de back-end, envie mensagens da nuvem para o dispositivo em um único dispositivo por meio do Hub IoT.

* Receber mensagens da nuvem para o dispositivo em um dispositivo.

* Da sua solução de back-end, solicite confirmação de entrega (*comentários*) para as mensagens enviadas a um dispositivo do Hub IoT.

É possível encontrar mais informações sobre mensagens de nuvem para dispositivo em [Mensagens de dispositivo para nuvem e nuvem para dispositivo com o Hub IoT](iot-hub-devguide-messaging.md).

No final deste tutorial, você executará dois aplicativos do console .NET:

* **SimulatedDevice**, uma versão modificada do aplicativo criado em [Enviar telemetria de um dispositivo para um Hub IoT](quickstart-send-telemetry-dotnet.md), que se conecta ao Hub IoT e recebe mensagens de nuvem para dispositivo.

* **SendCloudToDevice**, que envia uma mensagem da nuvem para o dispositivo ao aplicativo do dispositivo por meio do Hub IoT e recebe sua confirmação de entrega.

> [!NOTE]
> O Hub IoT tem suporte a SDK para várias plataformas de dispositivo e linguagens (incluindo C, Java e Javascript) nos [SDKs do dispositivo IoT do Azure](iot-hub-devguide-sdks.md). Para obter instruções passo a passo sobre como conectar seu dispositivo ao código deste tutorial e, em geral, ao Hub IoT do Azure, veja o [Guia do desenvolvedor do Hub IoT](iot-hub-devguide.md).
> 

Para concluir este tutorial, você precisará do seguinte:

* Visual Studio 2017

* Uma conta ativa do Azure. (Se você não tiver uma conta, poderá criar uma [conta gratuita](http://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.)

## <a name="receive-messages-in-the-device-app"></a>Receber mensagens no aplicativo do dispositivo

Nesta seção, você modificará o aplicativo do dispositivo criado em [Enviar telemetria de um dispositivo para um Hub IoT...](quickstart-send-telemetry-dotnet.md) para receber mensagens de nuvem para dispositivo do Hub IoT.

1. No Visual Studio, no projeto **SimulatedDevice**, adicione o método a seguir à classe **Program**.

   ```csharp   
    private static async void ReceiveC2dAsync()
    {
        Console.WriteLine("\nReceiving cloud to device messages from service");
        while (true)
        {
            Message receivedMessage = await deviceClient.ReceiveAsync();
            if (receivedMessage == null) continue;

            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received message: {0}", 
            Encoding.ASCII.GetString(receivedMessage.GetBytes()));
            Console.ResetColor();

            await deviceClient.CompleteAsync(receivedMessage);
        }
    }
   ```

   O método `ReceiveAsync` retorna de forma assíncrona a mensagem recebida no momento em que ela é recebida pelo dispositivo. Ela retorna *null* após um período de tempo limite especificável (nesse caso, será usado o padrão de um minuto). Quando o aplicativo recebe *null*, ele deve continuar aguardando novas mensagens. Esse requisito é o motivo da linha `if (receivedMessage == null) continue`.
   
    A chamada para `CompleteAsync()` notifica o Hub IoT de que a mensagem foi processada com êxito. A mensagem pode ser removida da fila do dispositivo com segurança. Se ocorreu algo que impediu que o aplicativo do dispositivo concluísse o processamento da mensagem, o Hub IoT a entrega novamente. Também é importante que a lógica de processamento de mensagem no aplicativo do dispositivo seja *idempotente*, de modo que receber a mesma mensagem várias vezes produza o mesmo resultado. 
    
    Um aplicativo também pode abandonar temporariamente uma mensagem, o que resulta em um Hub IoT reter a mensagem na fila para consumo futuro. Ou o aplicativo pode rejeitar uma mensagem, o que a remove permanentemente da fila. Para obter mais informações sobre o ciclo de vida da mensagem de nuvem para dispositivo, consulte [Mensagens de dispositivo para nuvem e nuvem para dispositivo com o Hub IoT](iot-hub-devguide-messaging.md).
   
   > [!NOTE]
   > Ao usar HTTPS em vez de MQTT ou AMQP como transporte, o método `ReceiveAsync` é retornado imediatamente. O padrão com suporte para mensagens da nuvem para o dispositivo com o HTTPS são dispositivos conectados intermitentemente que verificam mensagens com pouca frequência (menos do que a cada 25 minutos). Emitir mais recebimentos de HTTPS resulta na limitação das solicitações pelo Hub IoT. Para obter mais informações sobre as diferenças entre o suporte de MQTT, AMQP e HTTPS e a limitação do Hub IoT, consulte [Mensagens de dispositivo para nuvem e nuvem para dispositivo com o Hub IoT](iot-hub-devguide-messaging.md).
   > 
   > 
2. Adicione o seguinte método ao método **Main**, logo antes da linha `Console.ReadLine()`:
   
   ``` csharp   
   ReceiveC2dAsync();
   ```

## <a name="send-a-cloud-to-device-message"></a>Envie uma mensagem da nuvem para o dispositivo
Nesta seção, você escreve um aplicativo de console .NET que envia mensagens da nuvem para o dispositivo ao aplicativo do dispositivo.

1. Na solução do Visual Studio atual, crie um projeto de Aplicativo da Área de Trabalho do Visual C# usando o modelo de projeto do **Aplicativo do Console**. Nomeie o projeto **SendCloudToDevice**.
   
   ![Novo projeto no Visual Studio](./media/iot-hub-csharp-csharp-c2d/create-identity-csharp1.png)

2. No Gerenciador de Soluções, clique com o botão direito do mouse na solução e, então, clique em **Gerenciar Pacotes NuGet para Solução...**. 
   
    Essa ação abre a janela **Gerenciar Pacotes NuGet**.

3. Pesquise por **Microsoft.Azure.Devices**, clique em **Instalar** e aceite os termos de uso. 
   
    Isso baixa, instala e adiciona uma referência ao [pacote NuGet do SDK do serviço IoT do Azure](https://www.nuget.org/packages/Microsoft.Azure.Devices/).

4. Adicione a seguinte instrução `using` na parte superior do arquivo **Program.cs** :

   ``` csharp   
   using Microsoft.Azure.Devices;
   ```

5. Adicione os seguintes campos à classe **Program** . Substitua o valor do espaço reservado pela cadeia de conexão do Hub IoT em [Enviar telemetria de um dispositivo para um Hub IoT...](quickstart-send-telemetry-dotnet.md):

   ``` csharp
   static ServiceClient serviceClient;
   static string connectionString = "{iot hub connection string}";
   ```

6. Adicione o seguinte método à classe **Programa** :
   
   ``` csharp
   private async static Task SendCloudToDeviceMessageAsync()
   {
        var commandMessage = new 
         Message(Encoding.ASCII.GetBytes("Cloud to device message."));
        await serviceClient.SendAsync("myFirstDevice", commandMessage);
   }
   ```

   Esse método envia uma nova mensagem da nuvem para o dispositivo ao dispositivo com a ID `myFirstDevice`. Altere esse parâmetro somente se você tiver modificado o valor usado para ele em [Enviar telemetria de um dispositivo a um Hub IoT](quickstart-send-telemetry-dotnet.md).

7. Por fim, adicione as seguintes linhas ao método **Principal** :

   ``` csharp
   Console.WriteLine("Send Cloud-to-Device message\n");
   serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
   
   Console.WriteLine("Press any key to send a C2D message.");
   Console.ReadLine();
   SendCloudToDeviceMessageAsync().Wait();
   Console.ReadLine();
   ```

8. No Visual Studio, clique com o botão direito do mouse na solução e selecione **Definir Projetos de inicialização...**. Selecione **Vários projetos de inicialização** e, em seguida, selecione a ação **Iniciar** para **ProcessDeviceToCloudMessages**, **SimulatedDevice** e **SendCloudToDevice**.

9. Pressione **F5**. Todos os três aplicativos devem ser iniciados. Selecione as janelas **SendCloudToDevice** e pressione **Enter**. Você deve ver a mensagem que está sendo recebida pelo aplicativo do dispositivo.
   
   ![Aplicativo recebendo mensagens](./media/iot-hub-csharp-csharp-c2d/sendc2d1.png)

## <a name="receive-delivery-feedback"></a>Receber comentários de entrega

É possível solicitar confirmações de entrega (ou expiração) de Hub IoT para cada mensagem da nuvem para o dispositivo. Essa opção permite que o back-end da solução informe a lógica de repetição ou de compensação facilmente. Para obter mais informações sobre comentários da nuvem para o dispositivo, consulte [Mensagens de dispositivo para nuvem e nuvem para dispositivo com o Hub IoT](iot-hub-devguide-messaging.md).

Nesta seção, você modifica o aplicativo **SendCloudToDevice** para solicitar comentários e os recebe do Hub IoT.

1. No Visual Studio, no projeto **SendCloudToDevice**, adicione o método a seguir à classe **Program**.

   ```csharp
   private async static void ReceiveFeedbackAsync()
   {
        var feedbackReceiver = serviceClient.GetFeedbackReceiver();
   
        Console.WriteLine("\nReceiving c2d feedback from service");
        while (true)
        {
            var feedbackBatch = await feedbackReceiver.ReceiveAsync();
            if (feedbackBatch == null) continue;
   
            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received feedback: {0}", 
              string.Join(", ", feedbackBatch.Records.Select(f => f.StatusCode)));
            Console.ResetColor();
   
            await feedbackReceiver.CompleteAsync(feedbackBatch);
        }
    }
    ```

    É importante lembrar que o padrão de recebimento é o mesmo usado para receber mensagens da nuvem para o dispositivo do aplicativo do dispositivo.

2. Adicione o seguinte método ao método **Main**, logo após a linha `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)`:
   
   ``` csharp
   ReceiveFeedbackAsync();
   ```

3. Para solicitar comentários sobre a entrega da mensagem da nuvem para o dispositivo, você deve especificar uma propriedade no método **SendCloudToDeviceMessageAsync** . Adicione a seguinte linha, logo após a linha `var commandMessage = new Message(...);` :
   
   ``` csharp
   commandMessage.Ack = DeliveryAcknowledgement.Full;
   ```

4. Execute os aplicativos pressionando **F5**. Você deve ver todos os três aplicativos serem iniciados. Selecione as janelas **SendCloudToDevice** e pressione **Enter**. Você deve ver a mensagem sendo recebida pelo aplicativo do dispositivo e, depois de alguns segundos, a mensagem de comentários sendo recebida pelo aplicativo **SendCloudToDevice**.
   
   ![Aplicativo recebendo mensagens](./media/iot-hub-csharp-csharp-c2d/sendc2d2.png)

> [!NOTE]
> Para simplificar, este tutorial não implementa nenhuma política de repetição. No código de produção, implemente políticas de repetição (como uma retirada exponencial), conforme sugerido no artigo [Tratamento de falhas transitórias](/azure/architecture/best-practices/transient-faults).
> 

## <a name="next-steps"></a>Próximas etapas

Neste guia, você aprendeu a enviar e receber mensagens de nuvem para dispositivo. 

Para ver exemplos de soluções completas que usam o Hub IoT, consulte [Acelerador de solução de Monitoramento Remoto do Azure IoT](https://docs.microsoft.com/azure/iot-suite/).

Para saber mais sobre como desenvolver soluções com o Hub IoT, consulte o [Guia do desenvolvedor do Hub IoT](iot-hub-devguide.md).