<properties
	pageTitle="Processar mensagens de dispositivo para nuvem de Hub IoT | Microsoft Azure"
	description="Siga este tutorial para aprender os padrões úteis para processar as mensagens dispositivo para nuvem Hub IoT."
	services="iot-hub"
	documentationCenter=".net"
	authors="fsautomata"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="csharp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/29/2015"
     ms.author="elioda"/>

# Tutorial: Como processar mensagens de dispositivo para nuvem do Hub IoT

## Introdução

O Hub IoT do Azure é um serviço totalmente gerenciado que permite comunicações bidirecionais confiáveis e seguras entre milhões de dispositivos IoT e um back-end de aplicativo. Tutoriais anteriores ([Introdução ao Hub Iot] e [Enviar mensagens de nuvem para o dispositivo com o Hub IoT]) ilustram a funcionalidade da mensagem do dispositivo para nuvem básico e nuvem para dispositivo para o Hub IoT, e como acessá-los dos dispositivos e componentes de nuvem.

Este tutorial se baseia no código apresentado na [Introdução ao Hub IoT] para apresentar dois padrões para processar mensagens do dispositivo para nuvem.

O primeiro padrão é o armazenamento confiável de mensagens do dispositivo para a nuvem em [Blobs do Azure]. Esse cenário é muito comum ao implementar a análise de *caminho frio*, em que os dados armazenados em blobs são usados como entrada para análise orientada por ferramentas como [Azure Data Factory] ou pilha de [Hadoop].

O segundo padrão é o processamento confiável de mensagens *interativas* do dispositivo para nuvem. As mensagens do dispositivo para a nuvem são chamadas de *interativas* quando forem disparadas imediatamente para um conjunto de ações no back-end do aplicativo, ao contrário de uma mensagem de *ponto de dados* que é alimentada em um mecanismo de análise. Por exemplo, um alarme proveniente de um dispositivo tem de disparar a inserção de uma permissão em um sistema é uma mensagem dispositivo para nuvem *interativa*, em oposição à mensagem de telemetria contendo os exemplos de temperatura, que é a mensagem do *ponto de dados*.

Assim que o Hub IoT expõe um ponto de extremidade compatível com os Hubs de Eventos para receber mensagens do dispositivo para nuvem, este tutorial usa [EventProcessorHost] para hospedar uma classe de processador do evento, que:

* Armazenar com segurança as mensagens *do ponto de dados* no Blobs do Azure, e
* Encaminhar mensagens *interativas* do dispositivo para nuvem para uma [Fila do barramento de serviço] para processamento imediato.

[O barramento de serviço][Service Bus Queue] é uma ótima maneira de garantir processamento confiável de mensagens interativas, pois fornece pontos de verificação por mensagem e eliminação de duplicação baseados em janela de tempo.

No final deste tutorial, você executará três aplicativos de console do Windows:

* **SimulatedDevice**, uma versão modificada do aplicativo criado na [Introdução com Hub IoT], que envia mensagens do dispositivo para nuvem do *ponto inicial* a cada segundo, e mensagens *interativas* do dispositivo para nuvem a cada 10 segundos.
* **ProcessDeviceToCloudMessages**, que usa o [EventProcessorHost] para armazenar com segurança as mensagens do *ponto de dados* em um blob do Azure e encaminha mensagens *interativas* para uma fila do barramento de serviço, e
* **ProcessD2cInteractiveMessages**, que desenfileira as mensagens da fila.

> [AZURE.NOTE]O Hub IoT tem suporte SDK para várias plataformas de dispositivo e idiomas (incluindo C, Java e Javascript) no entanto, SDKs de dispositivo IoT do Azure. Consulte o [Centro de Desenvolvedores do IoT do Azure] para obter instruções passo a passo sobre como conectar seu dispositivo ao código deste tutorial e, em geral, do Hub IoT do Azure.

> [AZURE.NOTE]O conteúdo deste tutorial se aplica diretamente a outras formas para consumir mensagens de Hubs de Eventos compatível, por exemplo, projetos de [Hadoop] como Storm. Consulte [Orientação Hub IoT - compatibilidade de Hubs de eventos] para obter mais informações.

Para concluir este tutorial, você precisará do seguinte:

+ Microsoft Visual Studio 2015,

+ Uma conta ativa do Azure. <br/>Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos Para obter detalhes, consulte [Avaliação Gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fpt-BR%2Fdevelop%2Fiot%2Ftutorials%2Fprocess-d2c%2F target="\_blank").

Também se presume um conhecimento do [armazenamento do Azure] e [Barramento de Serviço do Azure].


[AZURE.INCLUDE [iot-hub-process-d2c-device-csharp](../../includes/iot-hub-process-d2c-device-csharp.md)]


[AZURE.INCLUDE [iot-hub-process-d2c-cloud-csharp](../../includes/iot-hub-process-d2c-cloud-csharp.md)]

## Executar os aplicativos

Agora você está pronto para executar os aplicativos.

1.	No Visual Studio, clique com o botão direito do mouse na solução e selecione **Definir projetos de inicialização...**. Selecione **Vários projetos de inicialização** e, em seguida, selecione a ação **Iniciar** para os aplicativos **ProcessDeviceToCloudMessages**, **SimulatedDevice** e **ProcessD2cInteractiveMessages**.

2.	Pressione **F5**, e você verá todos os aplicativos de início e cada mensagem interativa enviada pelo dispositivo simulado deve ser processada pelo processador de mensagens interativas.

  ![][50]

> [AZURE.NOTE]Para ver o arquivo de blob que está sendo atualizado, talvez você precise reduzir o `MAX_BLOCK_SIZE` constante `StoreEventProcessor` para um valor menor (ou seja, `1024`). Isso ocorre porque isso levará algum tempo para alcançar o limite de tamanho de bloco com os dados enviados pelo dispositivo simulado. Com essa edição, você verá um blob que está sendo criado e atualizado em seu contêiner de armazenamento.

## Próximas etapas

Neste tutorial, você aprendeu como processar de maneira confiável o *ponto de dados* e mensagens *interativas* mensagens de dispositivo para a nuvem usando [EventProcessorHost]. A lógica de processamento de mensagem de análoga pode ser implementada com:

- [Carregando arquivos de dispositivos], descreve um padrão que faz uso das mensagens da nuvem para o dispositivo para facilitar o carregamento de arquivo dos dispositivos.

Informações adicionais sobre o Hub IoT:

* [Visão geral do Hub IoT]
* [Guia do desenvolvedor do Hub IoT]
* [Orientação sobre o Hub IoT]
* [Plataformas e idiomas do dispositivo com suporte][Supported devices]
* [Centro de Desenvolvedores do IoT do Azure]

<!-- Images. -->
[50]: ./media/iot-hub-csharp-csharp-process-d2c/run1.png


<!-- Links -->

[Blobs do Azure]: https://azure.microsoft.com/pt-BR/documentation/articles/storage-dotnet-how-to-use-blobs/
[Azure Data Factory]: https://azure.microsoft.com/pt-BR/documentation/services/data-factory/
[Hadoop]: https://azure.microsoft.com/pt-BR/documentation/services/hdinsight/
[Service Bus Queue]: https://azure.microsoft.com/pt-BR/documentation/articles/service-bus-dotnet-how-to-use-queues/
[Fila do barramento de serviço]: https://azure.microsoft.com/pt-BR/documentation/articles/service-bus-dotnet-how-to-use-queues/
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx

[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Tratamento de falhas transitórias]: https://msdn.microsoft.com/pt-BR/library/hh680901(v=pandp.50).aspx

[Orientação Hub IoT - compatibilidade de Hubs de eventos]: iot-hub-guidance.md#eventhubcompatible

[armazenamento do Azure]: https://azure.microsoft.com/pt-BR/documentation/services/storage/
[Barramento de Serviço do Azure]: https://azure.microsoft.com/pt-BR/documentation/services/service-bus/

[Azure portal]: https://portal.azure.com/

[Enviar mensagens de nuvem para o dispositivo com o Hub IoT]: iot-hub-csharp-csharp-c2d.md
[Process Device-to-Cloud messages]: iot-hub-csharp-csharp-process-d2c.md
[Processar mensagens do dispositivo para a nuvem]: iot-hub-csharp-csharp-process-d2c.md
[Carregando arquivos de dispositivos]: iot-hub-csharp-csharp-file-upload.md

[Visão geral do Hub IoT]: iot-hub-what-is-iot-hub.md
[Orientação sobre o Hub IoT]: iot-hub-guidance.md
[Guia do desenvolvedor do Hub IoT]: iot-hub-devguide.md
[IoT Hub Supported Devices]: iot-hub-supported-devices.md
[Introdução ao Hub Iot]: iot-hub-csharp-csharp-getstarted.md
[Introdução com Hub IoT]: iot-hub-csharp-csharp-getstarted.md
[Supported devices]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/tested_configurations.md
[Centro de Desenvolvedores do IoT do Azure]: http://www.azure.com/develop/iot

<!---HONumber=AcomDC_1210_2015-->