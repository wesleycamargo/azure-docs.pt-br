<properties
	pageTitle="Enviar mensagens da nuvem para o dispositivo com o Hub IoT| Microsoft Azure"
	description="Siga este tutorial para aprender a enviar mensagens da nuvem para o dispositivo usando o Hub IoT do Azure com C#."
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

# Tutorial: Como enviar mensagens da nuvem para o dispositivo com o Hub IoT

## Introdução

O Hub IoT do Azure é um serviço totalmente gerenciado que permite comunicações bidirecionais confiáveis e seguras entre milhões de dispositivos IoT e um back-end de aplicativo. O tutorial [Introdução ao Hub IoT] mostra como criar um hub IoT, provisionar uma identidade do dispositivo e codificar um dispositivo simulado que envia mensagens do dispositivo para a nuvem.

Este tutorial se baseia na [Introdução ao Hub IoT] e mostra como enviar mensagens da nuvem para o dispositivo a um único dispositivo, como solicitar o reconhecimento de entrega (*comentários*) do Hub IoT e recebê-lo de seu back-end da nuvem do aplicativo.

Encontre mais informações sobre as mensagens da nuvem para o dispositivo no [Guia do Desenvolvedor do Hub IoT][IoT Hub Developer Guide - C2D].

No final deste tutorial, você executará dois aplicativos de console do Windows:

* **SimulatedDevice**, uma versão modificada do aplicativo criado na [Introdução ao Hub IoT], que se conecta a seu hub IoT e recebe mensagens da nuvem para o dispositivo.
* **SendCloudToDevice**, que envia uma mensagem da nuvem para o dispositivo para dispositivo simulado por meio do Hub IoT e, em seguida, recebe seu reconhecimento de entrega.

> [AZURE.NOTE] O Hub IoT tem suporte SDK para várias plataformas de dispositivo e idiomas (incluindo C, Java e Javascript) no entanto, SDKs de dispositivo IoT do Azure. Consulte o [Centro de Desenvolvedores do IoT do Azure] para obter instruções passo a passo sobre como conectar seu dispositivo ao código deste tutorial e, em geral, do Hub IoT do Azure. SDKs de serviço de IoT do Azure para Jave e Node estarão disponíveis em breve.

Para concluir este tutorial, você precisará do seguinte:

+ Microsoft Visual Studio 2015,

+ Uma conta ativa do Azure. <br/>Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos Para obter detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fpt-BR%2Fdevelop%2Fiot%2Ftutorials%2Fc2d%2F target="\_blank").

[AZURE.INCLUDE [iot-hub-c2d-device-csharp](../../includes/iot-hub-c2d-device-csharp.md)]


[AZURE.INCLUDE [iot-hub-c2d-cloud-csharp](../../includes/iot-hub-c2d-cloud-csharp.md)]

## Próximas etapas

Neste tutorial você aprendeu a enviar e receber mensagens da nuvem para o dispositivo. Você pode continuar a explorar os recursos de hub IoT e cenário com os seguintes tutoriais:

- [Mensagens do processo de dispositivo para nuvem], mostra como processar de forma confiável a telemetria e mensagens interativas provenientes dos dispositivos.
- [Carregando arquivos de dispositivos], descreve um padrão que faz uso das mensagens da nuvem para o dispositivo para facilitar o carregamento de arquivo dos dispositivos.

Informações adicionais sobre o Hub IoT:

* [Visão geral do Hub IoT]
* [Guia do desenvolvedor do Hub IoT]
* [Orientação sobre o Hub IoT]
* [Plataformas e idiomas do dispositivo com suporte][Supported devices]
* [Centro de Desenvolvedores do IoT do Azure]

<!-- Images. -->

<!-- Links -->

[Get started with IoT Hub]: iot-hub-csharp-csharp-getstarted.md

[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d

[Azure portal]: https://portal.azure.com/

[Send Cloud-to-Device messages with IoT Hub]: iot-hub-csharp-csharp-c2d.md
[Mensagens do processo de dispositivo para nuvem]: iot-hub-csharp-csharp-process-d2c.md
[Carregando arquivos de dispositivos]: iot-hub-csharp-csharp-file-upload.md

[Visão geral do Hub IoT]: iot-hub-what-is-iot-hub.md
[Orientação sobre o Hub IoT]: iot-hub-guidance.md
[Guia do desenvolvedor do Hub IoT]: iot-hub-devguide.md
[IoT Hub Supported Devices]: iot-hub-supported-devices.md
[Introdução ao Hub IoT]: iot-hub-csharp-csharp-getstarted.md
[Supported devices]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/tested_configurations.md
[Centro de Desenvolvedores do IoT do Azure]: http://www.azure.com/develop/iot

<!---HONumber=AcomDC_0128_2016-->