<properties
	pageTitle="Introdução ao Hub IoT| Microsoft Azure"
	description="Siga este tutorial para começar a usar o Hub IoT do Azure com C#."
	services="iot-hub"
	documentationCenter=".net"
	authors="fsautomata"
	manager="kevinmil"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="csharp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="tbd"
     ms.date="09/29/2015"
     ms.author="elioda"/>

# Introdução ao Hub IoT

## Introdução

O Hub IoT do Azure é um serviço totalmente gerenciado que permite comunicações bidirecionais confiáveis e seguras entre milhões de dispositivos IoT e um back-end de aplicativo. Um dos maiores desafios enfrentados por projetos IoT é sobre conectar dispositivos de forma confiável e segura ao back-end de aplicativo. Para simplificar este cenário, o Hub IoT do Azure oferece um sistema de mensagens confiável em grande escala da nuvem para dispositivo e do dispositivo para a nuvem, permite comunicações seguras usando credenciais de segurança e controle de acesso por dispositivo, além de incluir bibliotecas de dispositivos para os idiomas e as plataformas mais populares.

Este tutorial mostra como usar o portal do Azure para criar um Hub IoT. Também mostra como criar uma identidade de dispositivo em seu hub IoT, criar um dispositivo simulado que envia mensagens de dispositivo para nuvem e recebe essas mensagens de seu back-end de nuvem.

Ao final deste tutorial, terá criado três aplicativos de console do Windows:

* **CreateDeviceIdentity**, que cria uma identidade do dispositivo e chave de segurança associada para conectar o dispositivo simulado,
* **ReadDeviceToCloudMessages**, que lê mensagens de dispositivo para nuvem e exibe seu conteúdo, e
* **SimulatedDevice**, que se conecta ao hub IoT com a identidade do dispositivo criada anteriormente e envia uma mensagem de dispositivo na nuvem a cada segundo.

> [AZURE.NOTE]O Hub IoT tem suporte SDK para várias plataformas de dispositivo e idiomas (incluindo C, Java e Javascript) no entanto, SDKs de dispositivo IoT do Azure. Consulte o [Centro de Desenvolvedores do IoT do Azure] para obter instruções passo a passo sobre como conectar seu dispositivo ao código deste tutorial e, em geral, do Hub IoT do Azure. SDKs de serviço de IoT do Azure para Jave e Node estarão disponíveis em breve.

Para concluir este tutorial, você precisará do seguinte:

+ Microsoft Visual Studio 2015,

+ Uma conta ativa do Azure. <br/>Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos Para obter detalhes, consulte [Avaliação Gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fpt-BR%2Fdevelop%2Fiot%2Ftutorials%2Fgetstarted%2F target="\_blank").

## Crie um hub IoT

1. Faça logon no [Portal de Visualização do Azure].

2. Na barra de navegação, clique em **Novo**, em seguida, clique em **Internet das coisas**, e, em seguida, clique em **Hub IoT**.

   	![][1]

3. Na folha **Novo Hub IoT**, especifique a configuração desejada para o Hub IoT.

   	![][2]

    * Na caixa **Nome** digite um nome para identificar seu hub IoT. Quando o **Nome** for validado, uma marca de seleção verde será exibida na caixa **Nome**.
    * Alterar a **Camada de preços e escala** conforme desejado. Este tutorial não requer uma camada específica.
    * Na caixa **Grupo de recursos**, crie um novo grupo de recursos ou selecione um grupo de recursos existente. Para obter mais informações, consulte [Usando os grupos de recursos para gerenciar seus recursos do Azure](resource-group-portal.md).
    * Use **Local** para especificar a localização geográfica na qual hospedar o seu hub IoT.  


4. Uma vez que as novas opções de hub IoT forem configuradas, clique em **Criar**. Pode levar alguns minutos para que o hub IoT seja criado. Para verificar o status, você pode monitorar o progresso na Startboard. Ou então, você pode monitorar o progresso da seção de notificações.

    ![][3]


5. Depois que o hub IoT foi criado com êxito, abra a folha do novo hub IoT, anote o URI e selecione o ícone da **chave** ícone na parte superior.

   	![][4]

6. Selecione a política de acesso compartilhado chamada **iothubowner**, em seguida, copie e anote a cadeia de conexão na folha à direita.

   	![][5]

O hub IoT foi criado e você tiver o URI e a cadeia de conexão que você precisa para concluir este tutorial.

[AZURE.INCLUDE [iot-hub-get-started-cloud-csharp](../../includes/iot-hub-get-started-cloud-csharp.md)]


[AZURE.INCLUDE [iot-hub-get-started-device-csharp](../../includes/iot-hub-get-started-device-csharp.md)]

## Executar os aplicativos

Agora você está pronto para executar os aplicativos.

1.	No Visual Studio, clique com o botão direito do mouse na solução e selecione **Definir projetos de inicialização...**. Selecione **Vários projetos de inicialização** e, em seguida, selecione a ação **Iniciar** para os aplicativos **ProcessDeviceToCloudMessages** e **SimulatedDevice**.

   	![][41]

2.	Pressione **F5**, e você deverá ver o início do aplicativo e as mensagens sendo enviadas pelo aplicativo simulado e recebidas pelo aplicativo de processador.

   	![][42]

## Próximas etapas

Neste tutorial, você configurou um novo hub IoT, criou uma identidade do dispositivo no Registro de identidade do hub e usou essa identidade para programar um dispositivo simulado que envia mensagens de dispositivo para a nuvem. Você pode continuar a explorar os recursos de hub IoT e cenário com os seguintes tutoriais:

- [Enviar mensagens da nuvem para o dispositivo com o Hub IoT], mostrar como enviar mensagens para dispositivos e processar os comentários de entrega produzido pelo Hub IoT.
- [Mensagens do processo de dispositivo para nuvem], mostra como processar de forma confiável a telemetria e mensagens interativas provenientes dos dispositivos.
- [Carregando arquivos de dispositivos], descreve um padrão que faz uso das mensagens da nuvem para o dispositivo para facilitar o carregamento de arquivo dos dispositivos.

Informações adicionais sobre o Hub IoT:

* [Visão geral do Hub IoT]
* [Guia do desenvolvedor do Hub IoT]
* [Orientação sobre o Hub IoT]
* [Plataformas e idiomas do dispositivo com suporte][Supported devices]
* [Centro de Desenvolvedores do IoT do Azure]

<!-- Images. -->
[1]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub1.png
[2]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub2.png
[3]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub3.png
[4]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub4.png
[5]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub5.png

[41]: ./media/iot-hub-csharp-csharp-getstarted/run-apps1.png
[42]: ./media/iot-hub-csharp-csharp-getstarted/run-apps2.png

<!-- Links -->
[Portal de Visualização do Azure]: https://portal.azure.com/

[Enviar mensagens da nuvem para o dispositivo com o Hub IoT]: iot-hub-csharp-csharp-c2d.md
[Mensagens do processo de dispositivo para nuvem]: iot-hub-csharp-csharp-process-d2c.md
[Carregando arquivos de dispositivos]: iot-hub-csharp-csharp-file-upload.md

[Visão geral do Hub IoT]: iot-hub-what-is-iot-hub.md
[Orientação sobre o Hub IoT]: iot-hub-guidance.md
[Guia do desenvolvedor do Hub IoT]: iot-hub-devguide.md
[IoT Hub Supported Devices]: iot-hub-supported-devices.md
[Get started with IoT Hub]: iot-hub-csharp-csharp-getstarted.md
[Supported devices]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/tested_configurations.md
[Centro de Desenvolvedores do IoT do Azure]: http://www.azure.com/iotdev



 

<!---HONumber=Oct15_HO1-->