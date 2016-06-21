<properties
	pageTitle="Processar mensagens de dispositivo para nuvem de Hub IoT | Microsoft Azure"
	description="Siga este tutorial para aprender os padrões úteis para processar as mensagens dispositivo para nuvem Hub IoT."
	services="iot-hub"
	documentationCenter=".net"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="csharp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="04/29/2016"
     ms.author="dobett"/>

# Tutorial: Como processar mensagens de dispositivo para nuvem do Hub IoT

## Introdução

O Hub IoT do Azure é um serviço totalmente gerenciado que permite comunicações bidirecionais confiáveis e seguras entre milhões de dispositivos IoT e um back-end de aplicativo. Outros tutoriais ([Introdução ao Hub IoT] e [Como enviar mensagens da nuvem para o dispositivo com o Hub IoT]) mostram como usar a funcionalidade básica de mensagem do dispositivo para nuvem e da nuvem para dispositivo do Hub IoT.

Esse tutorial se baseia no código mostrado no tutorial [Introdução ao Hub IoT] e mostra dois padrões escalonáveis que você pode usar para processar mensagens de dispositivo para nuvem:

- O armazenamento confiável de mensagens do dispositivo para a nuvem no [Armazenamento de Blobs do Azure]. Um cenário muito comum é a análise de *caminho frio*, no qual você armazena dados de telemetria em blobs para usar como entrada em processo analíticos. Esses processos podem ser orientados por ferramentas como [Azure Data Factory] ou a pilha [HDInsight (Hadoop)].

- O processamento confiável de mensagens *interativas* do dispositivo para nuvem. As mensagens do dispositivo para nuvem são interativas quando disparadas imediatamente para um conjunto de ações no back-end do aplicativo. Por exemplo, um dispositivo pode enviar uma mensagem de alarme que dispara e insere um tíquete em um sistema CRM. Por outro lado, as mensagens de *ponto de dados* simplesmente são alimentadas no mecanismo de análise. Por exemplo, a telemetria de temperatura de um dispositivo que deve ser armazenado para análise posterior é uma mensagem de ponto de dados.

Como o Hub IoT expõe um ponto de extremidade compatível com os [Hubs de Eventos][lnk-event-hubs] para receber mensagens de dispositivo para nuvem, este tutorial usa uma instância [EventProcessorHost]. Esta instância:

* Armazena com segurança as mensagens do *ponto de dados* no armazenamento de blobs do Azure.
* Encaminha mensagens *interativas* do dispositivo para nuvem a uma [Fila do Barramento de Serviço] para processamento imediato.

O Barramento de Serviço ajuda a garantir processamento confiável de mensagens interativas, pois fornece pontos de verificação por mensagem e eliminação de duplicação baseada em janela de tempo.

> [AZURE.NOTE] Uma instância **EventProcessorHost** é apenas uma maneira de processar mensagens interativas. Outras opções incluem [Service Fabric Azure][lnk-service-fabric] e [Stream Analytics do Azure][lnk-stream-analytics].

Ao final deste tutorial, você executará três aplicativos de console do Windows:

* **SimulatedDevice**, uma versão modificada do aplicativo criado no tutorial [Introdução ao Hub IoT], que envia mensagens de ponto de dados do dispositivo para nuvem a cada segundo e mensagens interativas do dispositivo para nuvem a cada 10 segundos. Este aplicativo usa o protocolo AMQPS para se comunicar com o Hub IoT.
* **ProcessDeviceToCloudMessages** usa a classe [EventProcessorHost] para recuperar mensagens do ponto de extremidade compatível com os Hubs de Eventos. Desse modo, ele armazena confiavelmente mensagens de pontos de dados no armazenamento de blobs do Azure e encaminha mensagens interativas a uma fila do Barramento de Serviço.
* **ProcessD2CInteractiveMessages** remove da fila as mensagens interativas da fila do Barramento de Serviço.

> [AZURE.NOTE] O Hub IoT tem suporte de SDK para várias plataformas de dispositivo e linguagens, incluindo C, Java e JavaScript. Para obter instruções passo a passo sobre como substituir o dispositivo simulado neste tutorial por um dispositivo físico e, de modo geral, como conectar dispositivos a um Hub IoT, confira o [Centro de desenvolvedores do Azure IoT].

Esse tutorial se aplica diretamente a outras formas de consumir mensagens compatíveis com Hubs de Eventos, como projetos do [HDInsight (Hadoop)]. Para obter mais informações,confira [Guia do desenvolvedor do Hub IoT do Azure — dispositivo para nuvem].

Para concluir esse tutorial, você precisará do seguinte:

+ Microsoft Visual Studio 2015.

+ Uma conta ativa do Azure. <br/>Se você não tiver uma assinatura do Azure, poderá criar uma [conta gratuita](https://azure.microsoft.com/free/) em apenas alguns minutos.

Você também precisa ter um conhecimento básico do [Armazenamento do Azure] e do [Barramento de Serviço do Azure].


[AZURE.INCLUDE [iot-hub-process-d2c-device-csharp](../../includes/iot-hub-process-d2c-device-csharp.md)]


[AZURE.INCLUDE [iot-hub-process-d2c-cloud-csharp](../../includes/iot-hub-process-d2c-cloud-csharp.md)]

## Executar os aplicativos

Agora você está pronto para executar os aplicativos.

1.	No Visual Studio, no Gerenciador de Soluções, clique com o botão direito do mouse na solução e selecione **Definir Projetos de Inicialização**. Selecione **Vários projetos de inicialização** e, em seguida, **Iniciar** como a ação dos projetos **ProcessDeviceToCloudMessages**, **SimulatedDevice** e **ProcessD2CInteractiveMessages**.

2.	Pressione **F5** para iniciar os três aplicativos de console. O aplicativo **ProcessD2CInteractiveMessages** deve processar cada mensagem interativa enviada do aplicativo **SimulatedDevice**.

  ![Três aplicativos de console][50]

> [AZURE.NOTE] Para ver as atualizações em seu arquivo de blob, talvez seja necessário reduzir a constante **MAX\_BLOCK\_SIZE** na classe **StoreEventProcessor** para um valor menor, como **1024**. Isso ocorre porque leva algum tempo para alcançar o limite de tamanho de bloco com os dados enviados pelo dispositivo simulado. Com um tamanho de bloco menor, você não precisa esperar muito tempo para ver o blob ser criado e atualizado. No entanto, usar um tamanho maior de bloco torna o aplicativo mais dimensionável.

## Próximas etapas

Neste tutorial, você aprendeu a processar com segurança as mensagens de ponto de dados e interativas do dispositivo para nuvem usando a classe [EventProcessorHost].

O tutorial [Como carregar arquivos de dispositivos] se baseia neste tutorial usando a lógica de processamento de mensagem análoga. Ele descreve um padrão que usa mensagens da nuvem para dispositivo para facilitar o carregamento de arquivo dos dispositivos.

Informações adicionais sobre o Hub IoT:

* [Visão geral do Hub IoT]
* [Guia do desenvolvedor do Hub IoT]
* [Orientação sobre o Hub IoT]
* [Plataformas e idiomas do dispositivo com suporte][Supported devices]
* [Centro de Desenvolvedores do IoT do Azure]

<!-- Images. -->
[50]: ./media/iot-hub-csharp-csharp-process-d2c/run1.png


<!-- Links -->

[Armazenamento de Blobs do Azure]: ../storage/storage-dotnet-how-to-use-blobs.md
[Azure Data Factory]: https://azure.microsoft.com/documentation/services/data-factory/
[HDInsight (Hadoop)]: https://azure.microsoft.com/documentation/services/hdinsight/
[Service Bus Queue]: ../service-bus/service-bus-dotnet-get-started-with-queues.md
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx



[Guia do desenvolvedor do Hub IoT do Azure — dispositivo para nuvem]: iot-hub-devguide.md#d2c

[Armazenamento do Azure]: https://azure.microsoft.com/documentation/services/storage/
[Barramento de Serviço do Azure]: https://azure.microsoft.com/documentation/services/service-bus/



[Como enviar mensagens da nuvem para o dispositivo com o Hub IoT]: iot-hub-csharp-csharp-c2d.md
[Como carregar arquivos de dispositivos]: iot-hub-csharp-csharp-file-upload.md

[Visão geral do Hub IoT]: iot-hub-what-is-iot-hub.md
[Orientação sobre o Hub IoT]: iot-hub-guidance.md
[Guia do desenvolvedor do Hub IoT]: iot-hub-devguide.md
[Introdução ao Hub IoT]: iot-hub-csharp-csharp-getstarted.md
[Supported devices]: iot-hub-tested-configurations.md
[Centro de Desenvolvedores do IoT do Azure]: https://azure.microsoft.com/develop/iot
[Centro de desenvolvedores do Azure IoT]: https://azure.microsoft.com/develop/iot
[lnk-service-fabric]: https://azure.microsoft.com/documentation/services/service-fabric/
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-hubs]: https://azure.microsoft.com/documentation/services/event-hubs/

<!---HONumber=AcomDC_0608_2016-->