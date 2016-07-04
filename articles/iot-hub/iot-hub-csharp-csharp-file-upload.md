<properties
	pageTitle="Carregar arquivos dos dispositivos usando Hub IoT | Microsoft Azure"
	description="Siga este tutorial para aprender a carregar arquivos por meio de dispositivos usando o Hub IoT do Azure com C#."
	services="iot-hub"
	documentationCenter=".net"
	authors="fsautomata"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="06/21/2016"
     ms.author="elioda"/>

# Tutorial: Como carregar arquivos de dispositivos para a nuvem com o Hub IoT

## Introdução

O Hub IoT do Azure é um serviço totalmente gerenciado que permite comunicações bidirecionais confiáveis e seguras entre milhões de dispositivos IoT e um back-end de aplicativo. Os tutoriais anteriores ([Introdução ao Hub IoT] e [Como enviar mensagens da nuvem para o dispositivo com o Hub IoT]) ilustram a funcionalidade básica de troca de mensagens do dispositivo para a nuvem e da nuvem para o dispositivo do Hub IoT, e o tutorial [Como processar mensagens de dispositivo para nuvem] descreve uma maneira de armazenar com segurança mensagens do dispositivo para a nuvem no armazenamento de Blobs do Azure. No entanto, em alguns cenários você não pode mapear facilmente os dados que seus dispositivos enviam em mensagens relativamente menores do dispositivo para a nuvem que o Hub IoT aceita. Exemplos incluem arquivos grandes que contenham imagens, vídeos, exemplos de dados de vibração em alta frequência ou que contenham alguma forma de dados pré-processados. Esses arquivos normalmente são processados em lote na nuvem usando ferramentas como o [Azure Data Factory] ou a pilha do [Hadoop]. Quando o modo de preferência para enviar eventos é o carregamento de arquivo, você ainda pode usar a funcionalidade de segurança e confiabilidade do Hub IoT.

Este tutorial se baseia no código do tutorial [Como enviar mensagens da nuvem para o dispositivo com o Hub IoT] para mostrar como usar os recursos de carregamento de arquivo do Hub IoT. Ele mostra como fornecer com segurança um dispositivo com um URI do blob do Azure para carregamento de um arquivo e como usar as notificações de carregamento de arquivo do Hub IoT para disparar o processamento do arquivo no back-end do aplicativo.

No fim deste tutorial, você executará dois aplicativos de console do Windows:

* **SimulatedDevice**, uma versão modificada do aplicativo criado no tutorial [Como enviar mensagens da nuvem para o dispositivo com o Hub IoT], que carrega um arquivo para o armazenamento usando um URI SAS fornecido pelo seu Hub IoT.
* **ReadFileUploadNotification**, que recebe notificações de carregamento de arquivo de seu Hub IoT.

> [AZURE.NOTE] O Hub IoT é compatível com muitas plataformas de dispositivo e linguagens (incluindo C, Java e Javascript) por meio dos SDKs do dispositivo IoT do Azure. Consulte o [Centro de Desenvolvedores do IoT do Azure] para obter instruções passo a passo sobre como conectar seu dispositivo ao código mostrado neste tutorial e, de modo geral, ao Hub IoT do Azure.

Para concluir este tutorial, você precisará do seguinte:

+ Microsoft Visual Studio 2015,

+ Uma conta ativa do Azure. <br/>Se não tiver uma conta, você poderá criar uma conta gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fpt-BR%2Fdevelop%2Fiot%2Ftutorials%2Ffile-upload%2F target="\_blank").

[AZURE.INCLUDE [iot-hub-file-upload-device-csharp](../../includes/iot-hub-file-upload-device-csharp.md)]


[AZURE.INCLUDE [iot-hub-file-upload-cloud-csharp](../../includes/iot-hub-file-upload-cloud-csharp.md)]

## Executar os aplicativos

Agora você está pronto para executar os aplicativos.

1. No Visual Studio, clique com o botão direito do mouse na solução e escolha **Definir projetos de inicialização**. Escolha **Vários projetos de inicialização** e a ação **Iniciar** para **ReadFileUploadNotification** e **SimulatedDevice**.

2. Pressione **F5**. Ambos os aplicativos devem ser iniciados. Você deve ver o carregamento concluído em um aplicativo de console e a mensagem de notificação do carregamento sendo recebida pelo outro aplicativo de console. Você pode usar o [Portal do Azure] ou o Gerenciador de Servidores do Visual Studio para verificar a presença do arquivo carregado em sua conta de armazenamento.

  ![][50]


## Próximas etapas

Neste tutorial, você aprendeu a aproveitar os recursos de carregamento de arquivo do Hub IoT para simplificar os carregamentos de arquivos de dispositivos. Você pode continuar a explorar os recursos de Hub IoT e os cenários com os seguintes tutoriais:

- [Mensagens do processo de dispositivo para nuvem], mostra como processar de forma confiável a telemetria e mensagens interativas provenientes dos dispositivos.

Informações adicionais sobre o Hub IoT:

* [Visão geral do Hub IoT]
* [Guia do desenvolvedor do Hub IoT]
* [Orientação sobre o Hub IoT]
* [Plataformas e idiomas do dispositivo com suporte][Supported devices]
* [Centro de Desenvolvedores do IoT do Azure]

<!-- Images. -->

[50]: ./media/iot-hub-csharp-csharp-file-upload/run-apps1.png

<!-- Links -->

[Send Cloud-to-Device messages with IoT Hub]: iot-hub-csharp-csharp-c2d.md

[Portal do Azure]: https://portal.azure.com/

[Azure Data Factory]: https://azure.microsoft.com/documentation/services/data-factory/
[Hadoop]: https://azure.microsoft.com/documentation/services/hdinsight/

[Get started with IoT Hub]: iot-hub-csharp-csharp-getstarted.md
[Como enviar mensagens da nuvem para o dispositivo com o Hub IoT]: iot-hub-csharp-csharp-c2d.md
[Como processar mensagens de dispositivo para nuvem]: iot-hub-csharp-csharp-process-d2c.md
[Mensagens do processo de dispositivo para nuvem]: iot-hub-csharp-csharp-process-d2c.md
[Uploading files from devices]: iot-hub-csharp-csharp-file-upload.md

[Visão geral do Hub IoT]: iot-hub-what-is-iot-hub.md
[Orientação sobre o Hub IoT]: iot-hub-guidance.md
[Guia do desenvolvedor do Hub IoT]: iot-hub-devguide.md
[IoT Hub Supported Devices]: iot-hub-supported-devices.md
[Introdução ao Hub IoT]: iot-hub-csharp-csharp-getstarted.md
[Supported devices]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/tested_configurations.md
[Centro de Desenvolvedores do IoT do Azure]: http://www.azure.com/develop/iot

<!---HONumber=AcomDC_0622_2016-->