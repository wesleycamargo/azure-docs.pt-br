## Recebendo mensagens no dispositivo simulado

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

    O método `ReceiveAsync` retorna de forma assíncrona a mensagem recebida no momento em que ela é recebida pelo dispositivo. Ela retorna *null* após um período de tempo limite especificável (nesse caso, será usado o padrão de um minuto). Quando isso acontece, o código deve continuar a aguardar novas mensagens. Essa é a razão para a linha `if (receivedMessage == null) continue`.

    A chamada para `CompleteAsync()` notifica o Hub IoT que a mensagem foi processada com êxito. A mensagem pode ser removida da fila do dispositivo com segurança. Se ocorreu algo que impediu que o aplicativo do dispositivo concluísse o processamento da mensagem, o Hub IoT a entrega novamente. Em seguida, é importante que a lógica de processamento de mensagem no aplicativo do dispositivo seja *idempotente*, de modo que receber a mesma mensagem várias vezes produz o mesmo resultado. Um aplicativo também pode abandonar temporariamente uma mensagem, o que resulta em um Hub IoT reter a mensagem na fila para consumo futuro. Ou, o aplicativo pode rejeitar uma mensagem, que a remove permanentemente da fila. Para obter mais informações sobre o ciclo de vida da mensagem da nuvem para o dispositivo, consulte [Guia do desenvolvedor do Hub IoT][IoT Hub Developer Guide - C2D].

> [AZURE.NOTE] Ao usar o HTTP/1 em vez do AMQP como transporte, o método `ReceiveAsync` é retornado imediatamente. O padrão com suporte para mensagens da nuvem para o dispositivo com o HTTP/1 são dispositivos conectados intermitentemente que verificam mensagens com pouca frequência (menos do que a cada 25 minutos). Emitir mais recebimentos de HTTP/1 resulta na limitação das solicitações pelo Hub IoT. Para obter mais informações sobre as diferenças entre o suporte do AMQP e HTTP/1 e a limitação do Hub IoT, consulte [Guia do desenvolvedor do Hub IoT][IoT Hub Developer Guide - C2D].

2. Adicione o método a seguir ao método **Main**, logo antes da linha `Console.ReadLine()`:

        ReceiveC2dAsync();

> [AZURE.NOTE] Para simplificar, este tutorial não implementa nenhuma política de repetição. No código de produção, implemente políticas de repetição (como uma retirada exponencial), como sugerido no artigo [Transient Fault Handling] \(Tratamento de falhas transitórias) do MSDN.

<!-- Links -->
[IoT Hub Developer Guide - C2D]: ../articles/iot-hub/iot-hub-devguide.md#c2d

<!-- Images -->

<!---HONumber=AcomDC_0608_2016-->