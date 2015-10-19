# Microsoft Azure e a Internet das Coisas (IoT)

Uma típica solução de IoT exige uma comunicação bidirecional segura entre dispositivos, possivelmente contabilizando milhões, e um back-end de aplicativo. Alguns exemplos da funcionalidade de back-end do aplicativo podem incluir o processamento de eventos de dispositivo para a nuvem para aprofundar seu conhecimento usando a análise preditiva e automatizada.

A Microsoft fornece um conjunto de bibliotecas, que dão suporte a várias linguagens e plataformas de hardware, para que você possa desenvolver aplicativos de cliente para execução em um dispositivo de IoT. Para implementar seu aplicativo de back-end de IoT, é possível combinar vários serviços do Azure ou usar uma das soluções pré-configuradas disponíveis no [Azure IoT Suite][]. Para entender melhor como o Azure permite essa infraestrutura de IoT, é útil considerar uma típica arquitetura da solução de IoT.

## Arquitetura da solução de IoT

O diagrama abaixo mostra uma arquitetura comum da solução IoT. Observe que ela não inclui os nomes de todos os serviços específicos do Azure, mas descreve os principais elementos em uma arquitetura da solução de IoT genérica. As seções a seguir fornecem mais informações sobre os elementos nesta solução.

![Arquitetura da solução de IoT][img-solution-architecture]

### Conectividade do dispositivo

Em um cenário típico de IoT, os dispositivos enviam dados de telemetria do dispositivo para a nuvem, como leituras de temperatura para um ponto de extremidade na nuvem para o armazenamento e processamento. Os dispositivos também podem receber e responder aos comandos de nuvem para o dispositivo lendo mensagens de um ponto de extremidade de nuvem. Por exemplo, um dispositivo pode recuperar um comando que o instrui a alterar a frequência na qual ele realiza a amostra dos dados.

Um dispositivo ou fonte de dados em uma solução IoT pode variar de um simples sensor conectado à rede a um poderoso dispositivo de computação autônomo. Um dispositivo pode ter capacidade de processamento, memória, largura de banda de comunicação e suporte ao protocolo de comunicação limitados.

Um dispositivo pode se comunicar diretamente com um ponto de extremidade de um gateway de nuvem usando um protocolo de comunicação, como AMQP ou HTTP, ou por algum intermediário, como um gateway de campo que forneça um serviço como a conversão de protocolo.

### Processamento de dados e análise

Na nuvem, um processador de eventos de transmissão recebe mensagens do dispositivo para a nuvem em escala de seus dispositivos e determina como essas mensagens serão processadas e armazenadas. Uma solução para dispositivos conectados permite que você envie dados da nuvem para o dispositivo na forma de comandos para dispositivos específicos. O registro de dispositivos na solução de IoT permite provisionar dispositivos e controlar quais dispositivos têm permissão para se conectar à sua infraestrutura. O back-end permite acompanhar o estado de seus dispositivos e monitorar suas atividades.

As soluções de IoT podem incluir loops automáticos de comentários. Por exemplo, um módulo de aprendizado de máquina pode identificar, por meio dos dados de telemetria do dispositivo para a nuvem, que a temperatura de um dispositivo específico está acima do níveis operacionais normais e enviar um comando para o dispositivo, permitindo que ele execute uma ação corretiva.

### Apresentação

Muitas soluções de IoT permitem aos usuários exibir e analisar os dados coletados de seus dispositivos. Essas exibições podem assumir a forma de painéis ou de relatórios de BI.

[img-solution-architecture]: media/iot-azure-and-iot/iot-reference-architecture.png

[lnk-machinelearning]: http://azure.microsoft.com/services/machine-learning/
[Azure IoT Suite]: http://azure.microsoft.com/solutions/iot

<!---HONumber=Oct15_HO2-->