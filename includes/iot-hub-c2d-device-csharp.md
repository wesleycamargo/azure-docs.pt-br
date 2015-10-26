## Recebendo mensagens do dispositivo simulado

Nesta seção, você modificará o aplicativo do dispositivo simulado criado na [Introdução ao Hub IoT] para receber mensagens da nuvem para o dispositivo do hub IoT.

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

    O método `ReceiveAsync` retorna de forma assíncrona a mensagem recebida no momento em que ela é recebida pelo dispositivo. Ela retorna *null* após um período de tempo limite especificável (nesse caso, será usado o padrão de 1 minuto). Quando isso acontece, queremos que o código continue aguardando novas mensagens. Essa é a razão para a linha `if (receivedMessage == null) continue`.

    A chamada para `CompleteAsync()` notifica o Hub IoT de que a mensagem foi processada com êxito e de que ela pode ser removida com segurança da fila do dispositivo. Se ocorreu algo que impediu que o aplicativo do dispositivo concluísse o processamento da mensagem, o Hub IoT a entregará novamente; por isso, é importante que a lógica de processamento de mensagem no aplicativo do dispositivo seja *idempotente*, ou seja, o recebimento várias vezes da mesma mensagem deve produzir o mesmo resultado. Um aplicativo também pode temporariamente `Abandon` uma mensagem, o que fará com que o hub IoT retenha a mensagem na fila para consumo futuro; ou `Reject` uma mensagem, o que removerá permanentemente a mensagem da fila. Veja o [Guia do Desenvolvedor do Hub IoT][IoT Hub Developer Guide - C2D] para obter mais informações sobre o ciclo de vida da mensagem da nuvem para o dispositivo.

> [AZURE.NOTE]Ao usar o HTTP/1 em vez do AMQP como transporte, o `ReceiveAsync` será retornado imediatamente. O padrão com suporte para mensagens da nuvem para o dispositivo com o HTTP/1 são dispositivos conectados intermitentemente que verificam mensagens com pouca frequência (ou seja, uma frequência menor que a cada 25 minutos). Emitir mais recebimentos de HTTP/1 resultará na limitação das solicitações pelo Hub IoT. Veja o [Guia do Desenvolvedor do Hub IoT][IoT Hub Developer Guide - C2D] para obter mais informações sobre as diferenças entre o suporte do AMQP e do HTTP/1, bem como a limitação do Hub IoT.

2. Adicione o seguinte método no método **Main**, logo antes da linha `Console.ReadLine()`:

        ReceiveC2dAsync();

> [AZURE.NOTE]Para simplificar, este tutorial não implementa nenhuma política de repetição. No código de produção, é recomendado implementar políticas de repetição (por exemplo, retirada exponencial), como sugerido no artigo do MSDN [Tratamento de falhas transitórias].

<!-- Links -->
[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d

<!-- Images -->

<!---HONumber=Oct15_HO3-->