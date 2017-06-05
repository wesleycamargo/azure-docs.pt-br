> [!div class="op_single_selector"]
> * [Linux](../articles/iot-hub/iot-hub-linux-iot-edge-get-started.md)
> * [Windows](../articles/iot-hub/iot-hub-windows-iot-edge-get-started.md)
> 
> 

Este artigo fornece uma passo a passo detalhado do [código de exemplo do Olá, Mundo][lnk-helloworld-sample] para ilustrar os componentes fundamentais da arquitetura do [IoT Edge do Azure][lnk-iot-edge]. A amostra usa o IoT Edge do Azure para criar um gateway simples que registra uma mensagem “hello world” em um arquivo a cada cinco segundos.

Este passo a passo aborda:

* **Conceitos**: uma visão geral conceitual dos componentes que formam qualquer gateway criado com o IoT Edge.
* **Arquitetura da amostra do Hello World**: descreve como os conceitos se aplicam à amostra do Hello World e como os componentes se complementam.
* **Como criar a amostra**: as etapas necessárias para criar a amostra.
* **Como executar a amostra**: as etapas necessárias para executar a amostra. 
* **Saída típica**: um exemplo da saída esperada ao executar a amostra.
* **Trechos de código**: uma coleção de trechos de código para mostrar como a amostra do Olá, Mundo implementa os principais componentes do gateway do IoT Edge.

## <a name="azure-iot-edge-concepts"></a>Conceitos do IoT Edge do Azure
Antes de examinar o código de exemplo ou criar seu próprio gateway de campo usando o IoT Edge, é necessário entender os principais conceitos nos quais se baseia arquitetura do SDK.

### <a name="iot-edge-modules"></a>Módulos do IoT Edge
Você cria um gateway com o IoT Edge do Azure criando e montando *módulos do IoT Edge*. Os módulos usam *mensagens* para trocar dados entre si. Um módulo recebe uma mensagem, executa alguma ação nela, opcionalmente, a transforma em uma nova mensagem e a publica para ser processada por outros módulos. Alguns módulos poderão gerar apenas novas mensagens e nunca processar as mensagens de entrada. Uma cadeia de módulos cria um pipeline de processamento de dados com cada módulo executando uma transformação nos dados em um ponto do pipeline.

![Uma cadeia de módulos no gateway, compilada com o IoT Edge do Azure][1]

O IoT Edge contém o seguinte:

* Módulos pré-gravados que executam funções comuns de gateway.
* As interfaces que um desenvolvedor pode usar para gravar módulos personalizados.
* A infraestrutura necessária para implantar e executar um conjunto de módulos.

O SDK fornece uma camada de abstração que permite criar gateways para serem executados em uma variedade de sistemas operacionais e plataformas.

![Camada de abstração do IoT Edge do Azure][2]

### <a name="messages"></a>Mensagens
Mesmo que pensar sobre os módulos que transmitem mensagens entre si seja uma maneira conveniente de conceitualizar o funcionamento de um gateway, ela não reflete com precisão o que acontece. Os módulos do IoT Edge usam um agente para se comunicar entre si, publicar mensagens no agente (barramento, pubsub ou outro padrão de mensagens) e permitir que o agente faça o roteamento da mensagem para os módulos conectados a ele.

Um módulo usa a função **Broker_Publish** para publicar uma mensagem para o agente. O agente entrega as mensagens para um módulo, invocando uma função de retorno de chamada. Uma mensagem consiste em um conjunto de propriedades de chave/valor e no conteúdo transmitido como um bloco de memória.

![A função do Agente no IoT Edge do Azure][3]

### <a name="message-routing-and-filtering"></a>Roteamento e filtragem de mensagens
Há duas maneiras de direcionar mensagens para os módulos corretos do IoT Edge. Um conjunto de links pode ser transmitido para o agente para que este saiba a origem e o coletor de cada módulo, ou o módulo pode filtrar as propriedades da mensagem. Um módulo só deve agir em relação a uma mensagem se a mensagem é destinada a ele. Os links e a filtragem de mensagens são o que efetivamente cria um pipeline de mensagem.

## <a name="hello-world-sample-architecture"></a>Arquitetura da amostra do Hello World
A amostra do Hello World ilustra os conceitos descritos na seção anterior. A amostra do Olá, Mundo implementa um gateway do IoT Edge que contém um pipeline composto por dois módulos do IoT Edge:

* O módulo *hello world* cria uma mensagem a cada cinco segundos e a transmite para o módulo do agente.
* O módulo *logger* grava as mensagens recebidas em um arquivo.

![Arquitetura de exemplo de Olá, Mundo criada com o IoT Edge do Azure][4]

Conforme descrito na seção anterior, o módulo do Hello World não transmite mensagens diretamente para o módulo de agente a cada cinco segundos. Em vez disso, ele publica uma mensagem no agente a cada cinco segundos.

O módulo de agente recebe a mensagem do agente e age sobre ele gravando o conteúdo da mensagem em um arquivo.

O módulo do agente consome apenas as mensagens do agente; ele nunca publica novas mensagens no agente.

![Como o agente roteia mensagens entre módulos no IoT Edge do Azure][5]

A figura acima mostra a arquitetura da amostra do Hello World e os caminhos relativos para os arquivos de origem que implementam partes diferentes da amostra no [repositório][lnk-iot-edge]. Explore o código por conta própria ou use os trechos de código abaixo como guia.

<!-- Images -->
[1]: media/iot-hub-iot-edge-getstarted-selector/modules.png
[2]: media/iot-hub-iot-edge-getstarted-selector/modules_2.png
[3]: media/iot-hub-iot-edge-getstarted-selector/messages_1.png
[4]: media/iot-hub-iot-edge-getstarted-selector/high_level_architecture.png
[5]: media/iot-hub-iot-edge-getstarted-selector/detailed_architecture.png

<!-- Links -->
[lnk-helloworld-sample]: https://github.com/Azure/iot-edge/tree/master/samples/hello_world
[lnk-iot-edge]: https://github.com/Azure/iot-edge