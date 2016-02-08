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
     ms.date="01/05/2016"
     ms.author="dobett"/>

# Tutorial: Como processar mensagens de dispositivo para nuvem do Hub IoT

## Introdução

O Hub IoT do Azure é um serviço totalmente gerenciado que permite comunicações bidirecionais confiáveis e seguras entre milhões de dispositivos IoT e um back-end de aplicativo. Outros tutoriais - [Introdução ao Hub Iot] e [Enviar mensagens de nuvem para o dispositivo com o Hub IoT] - mostram como usar a funcionalidade básica de mensagem do dispositivo para nuvem e nuvem para dispositivo do Hub IoT.

Esse tutorial se baseia no código mostrado no tutorial [Introdução ao Hub IoT] e mostra dois padrões dimensionáveis que você pode usar para processar mensagens de dispositivo para nuvem:

- O armazenamento confiável de mensagens do dispositivo para a nuvem no [Armazenamento de blob do Azure]. Este é um cenário muito comum quando você implementa análise de *caminho frio*, em que você armazena dados em blobs para usar como entrada em processos de análise acionados por ferramentas como o [Azure Data Factory] ou a pilha do [HDInsight (Hadoop)].

- O processamento confiável de mensagens *interativas* do dispositivo para nuvem. As mensagens do dispositivo para a nuvem são interativas quando dispararem imediatamente um conjunto de ações no back-end do aplicativo, diferente das mensagens *ponto de dados*, que são alimentadas em um mecanismo de análise. Por exemplo, um alarme proveniente de um dispositivo que deve disparar a inserção de um tíquete em um sistema de CRM é uma mensagem de dispositivo para nuvem interativa, diferente da telemetria, como exemplos de temperatura, que é uma mensagem de dispositivo de ponto de dados para a nuvem.

Como o Hub IoT expõe um ponto de extremidade compatível com os Hubs de Eventos para receber mensagens de dispositivo para nuvem, este tutorial usa uma instância [EventProcessorHost], que:

* Armazena com segurança as mensagens *do ponto de dados* nos Blobs do Azure.
* Encaminha mensagens *interativas* do dispositivo para nuvem para uma [Fila do barramento de serviço] para processamento imediato.

O Barramento de Serviço é uma ótima maneira de garantir processamento confiável de mensagens interativas, pois fornece pontos de verificação por mensagem e eliminação de duplicação baseados em janela de tempo.

No final deste tutorial, você executará três aplicativos de console do Windows:

* **SimulatedDevice**, uma versão modificada do aplicativo criado no tutorial [Introdução com Hub IoT] que envia mensagens do dispositivo de ponto de dados para nuvem a cada segundo, e mensagens interativas do dispositivo para nuvem a cada 10 segundos.
* **ProcessDeviceToCloudMessages**, que usa a classe [EventProcessorHost] para recuperar mensagens do ponto de extremidade compatível com o Hub de Eventos e, depois, armazenar com confiança mensagens de ponto de dados em um blob do Azure e encaminhar mensagens interativas para uma fila do Barramento de Serviço.
* **ProcessD2CInteractiveMessages**, que desenfileira as mensagens interativas da fila do Barramento de Serviço.

> [AZURE.NOTE] O Hub IoT tem suporte de SDK para várias plataformas de dispositivo e linguagens, incluindo C, Java e JavaScript. Consulte o [Centro de Desenvolvedores do IoT do Azure] para obter instruções passo a passo sobre como substituir o dispositivo simulado neste tutorial por um dispositivo físico e, de modo geral, como conectar dispositivos ao Hub IoT do Azure.

Este tutorial se aplica diretamente a outras formas de consumir mensagens compatíveis com Hubs de Eventos, como projetos do [HDInsight (Hadoop)]. Veja o [Guia do desenvolvedor do Hub IoT do Azure - Dispositivo para nuvem] para saber mais.

Para concluir este tutorial, você precisará do seguinte:

+ Microsoft Visual Studio 2015.

+ Uma conta ativa do Azure. <br/>Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos Para obter detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fpt-BR%2Fdevelop%2Fiot%2Ftutorials%2Fprocess-d2c%2F target="\_blank").

Você também precisa ter um conhecimento básico do [Armazenamento do Azure] e do [Barramento de Serviço do Azure].


[AZURE.INCLUDE [iot-hub-process-d2c-device-csharp](../../includes/iot-hub-process-d2c-device-csharp.md)]


[AZURE.INCLUDE [iot-hub-process-d2c-cloud-csharp](../../includes/iot-hub-process-d2c-cloud-csharp.md)]

## Executar os aplicativos

Agora você está pronto para executar os aplicativos.

1.	No Visual Studio, no Gerenciador de Soluções, clique com o botão direito do mouse na solução e selecione **Definir projetos de Inicialização**. Selecione **Vários projetos de inicialização** e, em seguida, selecione **Iniciar** como a ação dos projetos **ProcessDeviceToCloudMessages**, **SimulatedDevice** e **ProcessD2CInteractiveMessages**.

2.	Pressione **F5** para iniciar os três aplicativos do console. O aplicativo **ProcessD2CInteractiveMessages** deve processar cada mensagem interativa enviada do aplicativo **SimulatedDevice**.

  ![][50]

> [AZURE.NOTE] Para ver as atualizações em seu arquivo de blob, talvez seja necessário reduzir a constante **MAX\_BLOCK\_SIZE** na classe **StoreEventProcessor** para como um valor menor, como **1024**. Isso ocorre porque leva algum tempo para alcançar o limite de tamanho de bloco com os dados enviados pelo dispositivo simulado. Com um tamanho de bloco menor, você não precisa esperar muito tempo para ver o blob ser criado e atualizado. No entanto, usar um tamanho maior de bloco torna o aplicativo mais dimensionável.

## Próximas etapas

Neste tutorial, você aprendeu como processar de maneira confiável o ponto de dados e mensagens interativas de dispositivo para a nuvem usando a classe [EventProcessorHost].

O tutorial [Carregando arquivos de dispositivos] se baseia neste tutorial usando uma lógica de processamento de mensagem análoga e descreve um padrão que faz uso de mensagens de nuvem para o dispositivo para facilitar os carregamentos de arquivos de dispositivos

Informações adicionais sobre o Hub IoT:

* [Visão geral do Hub IoT]
* [Guia do desenvolvedor do Hub IoT]
* [Orientação sobre o Hub IoT]
* [Plataformas e idiomas do dispositivo com suporte][Supported devices]
* [Centro de Desenvolvedores do IoT do Azure]

<!-- Images. -->
[50]: ./media/iot-hub-csharp-csharp-process-d2c/run1.png


<!-- Links -->

[Armazenamento de blob do Azure]: https://azure.microsoft.com/pt-BR/documentation/articles/storage-dotnet-how-to-use-blobs/
[Azure Data Factory]: https://azure.microsoft.com/pt-BR/documentation/services/data-factory/
[HDInsight (Hadoop)]: https://azure.microsoft.com/pt-BR/documentation/services/hdinsight/
[Fila do barramento de serviço]: https://azure.microsoft.com/pt-BR/documentation/articles/service-bus-dotnet-how-to-use-queues/
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx



[Guia do desenvolvedor do Hub IoT do Azure - Dispositivo para nuvem]: https://azure.microsoft.com/pt-BR/documentation/articles/iot-hub-devguide/#d2c

[Armazenamento do Azure]: https://azure.microsoft.com/pt-BR/documentation/services/storage/
[Barramento de Serviço do Azure]: https://azure.microsoft.com/pt-BR/documentation/services/service-bus/



[Enviar mensagens de nuvem para o dispositivo com o Hub IoT]: iot-hub-csharp-csharp-c2d.md
[Carregando arquivos de dispositivos]: iot-hub-csharp-csharp-file-upload.md

[Visão geral do Hub IoT]: iot-hub-what-is-iot-hub.md
[Orientação sobre o Hub IoT]: iot-hub-guidance.md
[Guia do desenvolvedor do Hub IoT]: iot-hub-devguide.md
[Introdução ao Hub Iot]: iot-hub-csharp-csharp-getstarted.md
[Introdução com Hub IoT]: iot-hub-csharp-csharp-getstarted.md
[Supported devices]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/tested_configurations.md
[Centro de Desenvolvedores do IoT do Azure]: https://azure.microsoft.com/develop/iot

<!---HONumber=AcomDC_0128_2016-->