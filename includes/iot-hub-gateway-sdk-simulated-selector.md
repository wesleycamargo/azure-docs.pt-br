> [!div class="op_single_selector"]
> * [Linux](../articles/iot-hub/iot-hub-linux-gateway-sdk-simulated-device.md)
> * [Windows](../articles/iot-hub/iot-hub-windows-gateway-sdk-simulated-device.md)
> 
> 

Este passo a passo da [amostra Carregamento para a Nuvem de Dispositivo Simulado] apresenta como usar o [SDK do Gateway IoT do Microsoft Azure][lnk-sdk] para enviar a telemetria do dispositivo para a nuvem ao Hub IoT por meio dos dispositivos simulados.

Este passo a passo aborda:

1. **Arquitetura**: informações importantes de arquitetura sobre a amostra Upload para a Nuvem de Dispositivo Simulado.
2. **Criar e executar**: as etapas necessárias para criar e executar a amostra.

## <a name="architecture"></a>Arquitetura
A amostra Upload para a Nuvem de Dispositivo Simulado mostra como usar o SDK para criar um gateway que envia a telemetria dos dispositivos simulados para um hub IoT. Os dispositivos simulados não podem se conectar diretamente ao Hub IoT, porque:

* Os dispositivos não usam um protocolo de comunicação compreendido pelo Hub IoT.
* Os dispositivos não são inteligentes o bastante para se lembrarem da identidade atribuída a eles pelo Hub IoT.

O gateway resolve esses problemas para os dispositivos simulados das seguintes maneiras:

* O gateway compreende o protocolo usado pelos dispositivos simulados, recebe dos dispositivos a telemetria do dispositivo para a nuvem e encaminha essas mensagens ao Hub IoT usando um protocolo compreendido pelo hub.
* O gateway armazena as identidades do Hub IoT em nome dos dispositivos simulados e atua como um proxy quando os dispositivos simulados enviam mensagens ao Hub IoT.

O diagrama a seguir mostra os principais componentes da amostra, incluindo os módulos de gateway:

![][1]

> [!NOTE]
> Os módulos não transmitem mensagens diretamente entre si. Os módulos publicam mensagens em um agente interno, que entrega as mensagens para outros módulos usando um mecanismo de assinatura, conforme mostrado no diagrama abaixo. Para saber mais, veja [Introdução ao SDK do gateway][lnk-gw-getstarted].
> 
> 

### <a name="protocol-ingestion-module"></a>Módulo de ingestão de protocolo
Esse módulo é o ponto de partida para obtenção de dados de dispositivos, por meio do gateway, e inserção na nuvem. Na amostra, o módulo executa quatro tarefas:

1. Ele cria dados de temperatura simulados.
   
   Observação: se você estiver usando dispositivos reais, o módulo lerá os dados desses dispositivos físicos.
2. Ele coloca os dados de temperatura simulados no conteúdo de uma mensagem.
3. Ele adiciona uma propriedade com um endereço MAC fictício à mensagem que contém os dados de temperatura simulados.
4. Ele disponibiliza a mensagem para o próximo módulo na cadeia.

> [!NOTE]
> O módulo chamado **Ingestão de protocolo X** no diagrama acima é chamado de **Dispositivo simulado** no código-fonte.
> 
> 

### <a name="mac-&lt;-&gt;-iot-hub-id-module"></a>Módulo de identificação do MAC &lt;-&gt; Hub IoT
Este módulo examina mensagens que incluem uma propriedade que contém o endereço MAC, adicionado pelo módulo de ingestão de protocolo, do dispositivo simulado. Se o módulo detectar essa propriedade, ele adicionará outra propriedade com uma chave de dispositivo Hub IoT à mensagem e disponibilizará a mensagem para o próximo módulo na cadeia. É assim como a amostra associa as identidades de dispositivo Hub IoT com os dispositivos simulados. O desenvolvedor define o mapeamento entre os endereços MAC e as identidades do Hub IoT manualmente como parte da configuração do módulo. 

> [!NOTE]
> Esta amostra usa um endereço MAC como um identificador de dispositivo exclusivo e o correlaciona com uma identidade de dispositivo Hub IoT. No entanto, é possível escrever seu próprio módulo que usa um identificador exclusivo diferente. Por exemplo, você poderá ter dispositivos com números de série exclusivos ou dados de telemetria com um nome de dispositivo exclusivo inserido que pode ser usado para determinar a identidade do dispositivo Hub IoT.
> 
> 

### <a name="iot-hub-communication-module"></a>Módulo de comunicação do Hub IoT
Este módulo usa mensagens com uma identidade de dispositivo Hub IoT atribuída pelo módulo anterior e envia o conteúdo da mensagem ao Hub IoT usando o HTTPS. HTTPS é um dos três protocolos compreendidos pelo Hub IoT.

Em vez de abrir uma conexão com o Hub IoT de cada dispositivo simulado, este módulo abre uma única conexão HTTP do gateway para o hub IoT e multiplexa as conexões de todos os dispositivos simulados por essa conexão. Isso permite que um único gateway conecte um número muito maior de dispositivos, simulados ou não, do que seria possível se ele abrisse uma conexão exclusiva para cada dispositivo.

![][2]

<!-- Images -->
[1]: media/iot-hub-gateway-sdk-simulated-selector/image1.png
[2]: media/iot-hub-gateway-sdk-simulated-selector/image2.png

<!-- Links -->
[Exemplo de carregamento de nuvem do dispositivo simulado]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/sample_simulated_device_cloud_upload.md
[lnk-sdk]: https://github.com/Azure/azure-iot-gateway-sdk
[lnk-gw-getstarted]: ../articles/iot-hub/iot-hub-linux-gateway-sdk-get-started.md

<!--HONumber=Oct16_HO2-->


