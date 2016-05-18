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

O Hub IoT do Azure é um serviço totalmente gerenciado que permite comunicações bidirecionais confiáveis e seguras entre milhões de dispositivos IoT e um back-end de aplicativo. Outros tutoriais - [Introdução ao Hub Iot] e [Enviar mensagens de nuvem para o dispositivo com o Hub IoT] - mostram como usar a funcionalidade básica de mensagem do dispositivo para nuvem e nuvem para dispositivo do Hub IoT.

Esse tutorial se baseia no código mostrado no tutorial [Introdução ao Hub IoT] e mostra dois padrões dimensionáveis que você pode usar para processar mensagens de dispositivo para nuvem:

- O armazenamento confiável de mensagens do dispositivo para a nuvem no [Armazenamento de Blobs do Azure]. Um cenário muito comum é a análise de *caminho frio*, em que você armazena dados telemétricos em blobs para usar como entrada em processos de análise acionados por ferramentas como o [Azure Data Factory] ou a pilha do [HDInsight (Hadoop)].

- O processamento confiável de mensagens *interativas* do dispositivo para nuvem. As mensagens do dispositivo para a nuvem são interativas quando disparadas imediatamente para um conjunto de ações no back-end do aplicativo, diferente das mensagens de *ponto de dados*, que são alimentadas em um mecanismo de análise. Por exemplo, um alarme proveniente de um dispositivo que deve ser disparado com a inserção de um tíquete em um sistema de CRM é uma mensagem interativa, enquanto a telemetria de temperatura de um dispositivo que deve ser armazenada para análise posterior é uma mensagem de ponto de dados.

Como o Hub IoT expõe um ponto de extremidade compatível com os [Hubs de Eventos][lnk-event-hubs] para receber mensagens de dispositivo para nuvem, este tutorial usa uma instância [EventProcessorHost], que:

* Armazena com segurança as mensagens do *ponto de dados* no armazenamento de blobs do Azure.
* Encaminha mensagens *interativas* do dispositivo para nuvem para uma [Fila do barramento de serviço] para processamento imediato.

O Barramento de Serviço é uma ótima maneira de garantir processamento confiável de mensagens interativas, pois fornece pontos de verificação por mensagem e eliminação de duplicação baseados em janela de tempo.

> [AZURE.NOTE] Uma instância **EventProcessorHost** é apenas uma maneira de processar mensagens interativas. Entre as outras opções estão [Service Fabric do Azure][lnk-service-fabric] e [Stream Analytics do Azure][lnk-stream-analytics].

Ao final deste tutorial, você executará três aplicativos de console do Windows:

* **SimulatedDevice**, uma versão modificada do aplicativo criado no tutorial [Introdução ao Hub IoT] que envia mensagens de ponto de dados do dispositivo para a nuvem a cada segundo e mensagens interativas do dispositivo para a nuvem a cada 10 segundos. Este aplicativo usa o protocolo AMQPS para se comunicar com o Hub IoT.
* **ProcessDeviceToCloudMessages** usa a classe [EventProcessorHost] para recuperar mensagens do ponto de extremidade compatível com o Hub de Eventos e, em seguida, armazena com segurança as mensagens de ponto de dados em um armazenamento de blobs do Azure e encaminha mensagens interativas para uma fila do Barramento de Serviço.
* **ProcessD2CInteractiveMessages** remove da fila as mensagens interativas da fila do Barramento de Serviço.

> [AZURE.NOTE] O Hub IoT tem suporte de SDK para várias plataformas de dispositivo e linguagens, incluindo C, Java e JavaScript. Consulte o [Centro de Desenvolvedores do IoT do Azure] para obter instruções passo a passo sobre como substituir o dispositivo simulado neste tutorial por um dispositivo físico e, de modo geral, como conectar dispositivos a um Hub IoT.

Este tutorial se aplica diretamente a outras formas de consumir mensagens compatíveis com Hubs de Eventos, como projetos do [HDInsight (Hadoop)]. Confira o [Guia do desenvolvedor do Hub IoT do Azure - Dispositivo para nuvem] para saber mais.

Para concluir este tutorial, você precisará do seguinte:

+ Microsoft Visual Studio 2015.

+ Uma conta ativa do Azure. <br/>Se você não tiver uma assinatura do Azure, poderá criar uma [conta gratuita](https://azure.microsoft.com/free/) em apenas alguns minutos.

Você também precisa ter um conhecimento básico do [Armazenamento do Azure] e do [Barramento de Serviço do Azure].


[AZURE.INCLUDE [iot-hub-process-d2c-device-csharp](../../includes/iot-hub-process-d2c-device-csharp.md)]


[AZURE.INCLUDE [iot-hub-process-d2c-cloud-csharp](../../includes/iot-hub-process-d2c-cloud-csharp.md)]

## Executar os aplicativos

Agora você está pronto para executar os aplicativos.

1.	No Visual Studio, no Gerenciador de Soluções, clique com o botão direito do mouse na solução e selecione **Definir Projetos de Inicialização**. Selecione **Vários projetos de inicialização** e, em seguida, **Iniciar** como a ação dos projetos **ProcessDeviceToCloudMessages**, **SimulatedDevice** e **ProcessD2CInteractiveMessages**.

2.	Pressione **F5** para iniciar os três aplicativos de console. O aplicativo **ProcessD2CInteractiveMessages** deve processar cada mensagem interativa enviada do aplicativo **SimulatedDevice**.

  ![][50]

> [AZURE.NOTE] Para ver as atualizações em seu arquivo de blob, talvez seja necessário reduzir a constante **MAX\_BLOCK\_SIZE** na classe **StoreEventProcessor** para um valor menor, como **1024**. Isso ocorre porque leva algum tempo para alcançar o limite de tamanho de bloco com os dados enviados pelo dispositivo simulado. Com um tamanho de bloco menor, você não precisa esperar muito tempo para ver o blob ser criado e atualizado. No entanto, usar um tamanho maior de bloco torna o aplicativo mais dimensionável.

## Próximas etapas

Neste tutorial, você aprendeu a processar com segurança as mensagens de ponto de dados e interativas do dispositivo para a nuvem usando a classe [EventProcessorHost].

O tutorial [Carregando arquivos de dispositivos] se baseia neste tutorial usando uma lógica de processamento de mensagem análoga e descreve um padrão que faz uso de mensagens da nuvem para o dispositivo, a fim de facilitar os uploads de arquivo de dispositivos

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
[Fila do barramento de serviço]: ../service-bus/service-bus-dotnet-how-to-use-queues/
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx



[Guia do desenvolvedor do Hub IoT do Azure - Dispositivo para nuvem]: iot-hub-devguide.md#d2c

[Armazenamento do Azure]: https://azure.microsoft.com/documentation/services/storage/
[Barramento de Serviço do Azure]: https://azure.microsoft.com/documentation/services/service-bus/



[Enviar mensagens de nuvem para o dispositivo com o Hub IoT]: iot-hub-csharp-csharp-c2d.md
[Carregando arquivos de dispositivos]: iot-hub-csharp-csharp-file-upload.md

[Visão geral do Hub IoT]: iot-hub-what-is-iot-hub.md
[Orientação sobre o Hub IoT]: iot-hub-guidance.md
[Guia do desenvolvedor do Hub IoT]: iot-hub-devguide.md
[Introdução ao Hub Iot]: iot-hub-csharp-csharp-getstarted.md
[Supported devices]: iot-hub-tested-configurations.md
[Centro de Desenvolvedores do IoT do Azure]: https://azure.microsoft.com/develop/iot
[lnk-service-fabric]: https://azure.microsoft.com/documentation/services/service-fabric/
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-hubs]: https://azure.microsoft.com/documentation/services/event-hubs/

<!---HONumber=AcomDC_0504_2016-->