## Enviar mensagens interativas por meio do dispositivo simulado

Nesta seção, você modificará o aplicativo do dispositivo simulado para enviar mensagens interativas do dispositivo para a nuvem ao hub IoT.

1. No Visual Studio, no projeto **SimulatedDevice**, adicione o método a seguir à classe **Program**.
   
        private static async void SendDeviceToCloudInteractiveMessagesAsync()
        {
            while (true)
            {
                var interactiveMessageString = "Alert message!";
                var interactiveMessage = new Message(Encoding.ASCII.GetBytes(interactiveMessageString));
                interactiveMessage.Properties["messageType"] = "interactive";
                interactiveMessage.MessageId = Guid.NewGuid().ToString();

                await deviceClient.SendEventAsync(interactiveMessage);
                Console.WriteLine("{0} > Sending interactive message: {1}", DateTime.Now, interactiveMessageString);

                Thread.Sleep(10000);
            }
        }

    Esse método é muito semelhante ao método `SendDeviceToCloudMessagesAsync()` criado na [Introdução ao Hub IoT]. A única diferença é que a propriedade do sistema `MessageId` e uma propriedade de usuário chamada `messageType` agora estão definidas. A propriedade `MessageId` está definida para uma ID exclusiva globalmente (GUID), que será usada para eliminar a duplicação dos recebimentos de mensagem. A propriedade `messageType` é usada para distinguir as mensagens interativas das de ponto de dados. Essas informações são transmitidas nas propriedades da mensagem, em vez de no corpo da mensagem, para que o processador de evento no back-end não precise desserializar a mensagem inteira apenas para executar o roteamento.

> [AZURE.NOTE]É importante que o `MessageId`, usado para eliminar a duplicação das mensagens interativas, seja criado no dispositivo, já que a comunicação de rede intermitente (ou outras falhas) poderia resultar em várias retransmissões da mesma mensagem do dispositivo. Uma ID de mensagem semântica (por exemplo, um hash dos campos relevantes de dados da mensagem) também pode ser usada, em vez de um GUID.

2. Adicione o seguinte método no método **Main**, logo antes da linha `Console.ReadLine()`:

        SendDeviceToCloudInteractiveMessagesAsync();

> [AZURE.NOTE]Para simplificar, este tutorial não implementa nenhuma política de repetição. No código de produção, é recomendado implementar políticas de repetição (por exemplo, retirada exponencial), como sugerido no artigo do MSDN [Tratamento de falhas transitórias].

<!-- Links -->
[Introdução ao Hub IoT]: iot-hub-csharp-csharp-getstarted.md
[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d

<!-- Images -->
[10]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp1.png
[12]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp3.png

[20]: ./media/iot-hub-getstarted-cloud-csharp/create-storage1.png
[21]: ./media/iot-hub-getstarted-cloud-csharp/create-storage2.png
[22]: ./media/iot-hub-getstarted-cloud-csharp/create-storage3.png

<!---HONumber=Nov15_HO3-->