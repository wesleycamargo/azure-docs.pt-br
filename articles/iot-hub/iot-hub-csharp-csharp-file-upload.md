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
     ms.date="09/29/2015"
     ms.author="elioda"/>

# Tutorial: Como carregar arquivos de dispositivos para a nuvem com o Hub IoT

## Introdução

O Hub IoT do Azure é um serviço totalmente gerenciado que permite comunicações bidirecionais confiáveis e seguras entre milhões de dispositivos IoT e um back-end de aplicativo. Tutoriais anteriores ([Introdução ao Hub Iot] e [Enviar mensagens de nuvem para o dispositivo com o Hub IoT]) ilustram a funcionalidade da mensagem do dispositivo para nuvem básico e nuvem para dispositivo para o Hub IoT, e como acessá-los dos dispositivos e componentes de nuvem. [As mensagens do processo de dispositivo para nuvem] descreveu uma forma de armazenamento confiável das mensagens do dispositivo para nuvem no armazenamento de blob do Azure. Há casos, no entanto, em que os dados dos dispositivos não mapeiam facilmente para mensagens dispositivo para nuvem relativamente pequenas. Alguns exemplos são arquivos grandes que contêm imagens, vídeos, exemplo de dados de vibração com bastante frequência ou que contém algum tipo de dados pré-processados. Esses arquivos normalmente são processados em um modo de lotes usando ferramentas como o [Azure Data Factory] ou a pilha de [Hadoop]. Ao carregar um arquivo de um dispositivo é preferencial enviar eventos, ainda é possível usar a funcionalidade de confiabilidade e segurança de Hub IoT.

Este tutorial se baseia no código apresentado em [Enviar mensagens da nuvem para o dispositivo com o Hub IoT] para mostrar como usar mensagens da nuvem para o dispositivo para fornecer com segurança um URI de blob do Azure ao dispositivo a ser usado para carregar o arquivo. Além disso, ele mostra como usar os reconhecimentos de entrega do Hub IoT para disparar o processamento do arquivo do seu back-end de aplicativo. As vantagens dessa abordagem é a reutilização de identidade do dispositivo Hub IoT e a confirmação de entrega de mensagens da nuvem para o dispositivo para informar o back-end do aplicativo que o arquivo foi carregado com êxito.

> [AZURE.NOTE]A mesma abordagem usada aqui pode ser usada para dispositivos baixarem arquivos com segurança da nuvem.

Encontre mais informações sobre as mensagens da nuvem para o dispositivo e segurança IoT no [Guia do Desenvolvedor do Hub IoT].

No final deste tutorial, você executará dois aplicativos de console do Windows:

* **SimulatedDevice**, uma versão modificada do aplicativo criado em [Enviar mensagens da nuvem para dispositivo], que se conecta a seu hub IoT e recebe mensagens da nuvem para o dispositivo contendo os URIs do blob do Azure. Para cada mensagem de nuvem para dispositivo recebida, ele dispara um upload de arquivo para o URI do blob especificado.
* **SendCloudToDevice**, que compila um Azure URI do blob (conforme explicado em [Criar e usar uma SAS com o serviço Blob](../storage/storage-dotnet-shared-access-signature-part-2.md), envia em uma mensagem de nuvem para o dispositivo para dispositivo simulado por meio do Hub IoT e, em seguida, recebe seu reconhecimento de entrega.

> [AZURE.NOTE]O Hub IoT tem suporte SDK para várias plataformas de dispositivo e idiomas (incluindo C, Java e Javascript) no entanto, SDKs de dispositivo IoT do Azure. Consulte o [Centro de Desenvolvedores do IoT do Azure] para obter instruções passo a passo sobre como conectar seu dispositivo ao código deste tutorial e, em geral, do Hub IoT do Azure. SDKs de serviço de IoT do Azure para Jave e Node estarão disponíveis em breve.

Para concluir este tutorial, você precisará do seguinte:

+ Microsoft Visual Studio 2015,

+ Uma conta ativa do Azure. <br/>Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos Para obter detalhes, consulte [Avaliação Gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fpt-BR%2Fdevelop%2Fiot%2Ftutorials%2Ffile-upload%2F target="\_blank").


[AZURE.INCLUDE [iot-hub-file-upload-cloud-csharp](../../includes/iot-hub-file-upload-cloud-csharp.md)]


[AZURE.INCLUDE [iot-hub-file-upload-device-csharp](../../includes/iot-hub-file-upload-device-csharp.md)]

## Executar os aplicativos

Agora você está pronto para executar os aplicativos.

1.  No Visual Studio, clique com o botão direito do mouse na solução e selecione **Definir projetos de inicialização...**. Selecione **Vários projetos de inicialização** e, em seguida, selecione a ação **Iniciar** para **SimulatedDevice**, e aplicativos **SendCloudToDevice**.

2.  Pressione **F5** e você verá todos os aplicativos de início. Selecione a janela **SendCloudToDevice** e pressione uma tecla. Você verá uma mensagem de saída do dispositivo simulado quando o arquivo for carregado e o aplicativo **SendCloudToDevice** mostrará o recebimento de comentários. Você pode usar o [portal do Azure] ou o Gerenciador de Servidores do Visual Studio para verificar a presença do arquivo em sua conta de armazenamento.

  ![][50]


## Próximas etapas

Neste tutorial, você aprendeu como utilizar mensagens de dispositivo de nuvem para simplificar os carregamentos de arquivos de dispositivos. Você pode continuar a explorar os recursos de Hub IoT e os cenários com os seguintes tutoriais:

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

[portal do Azure]: https://portal.azure.com/

[Azure Data Factory]: https://azure.microsoft.com/pt-BR/documentation/services/data-factory/
[Hadoop]: https://azure.microsoft.com/pt-BR/documentation/services/hdinsight/

[Get started with IoT Hub]: iot-hub-csharp-csharp-getstarted.md
[Enviar mensagens da nuvem para dispositivo]: iot-hub-csharp-csharp-c2d.md
[Enviar mensagens da nuvem para o dispositivo com o Hub IoT]: iot-hub-csharp-csharp-c2d.md
[Enviar mensagens de nuvem para o dispositivo com o Hub IoT]: iot-hub-csharp-csharp-c2d.md
[As mensagens do processo de dispositivo para nuvem]: iot-hub-csharp-csharp-process-d2c.md
[Mensagens do processo de dispositivo para nuvem]: iot-hub-csharp-csharp-process-d2c.md
[Uploading files from devices]: iot-hub-csharp-csharp-file-upload.md

[Visão geral do Hub IoT]: iot-hub-what-is-iot-hub.md
[Orientação sobre o Hub IoT]: iot-hub-guidance.md
[Guia do Desenvolvedor do Hub IoT]: iot-hub-devguide.md
[IoT Hub Supported Devices]: iot-hub-supported-devices.md
[Introdução ao Hub Iot]: iot-hub-csharp-csharp-getstarted.md
[Supported devices]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/tested_configurations.md
[Centro de Desenvolvedores do IoT do Azure]: http://www.azure.com/develop/iot

<!---HONumber=AcomDC_1203_2015-->