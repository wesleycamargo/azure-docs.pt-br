<properties
	pageTitle="Introdução ao Hub IoT do Azure para Node.js | Microsoft Azure"
	description="Siga este tutorial para começar a usar o Hub IoT do Azure com Node.js."
	services="iot-hub"
	documentationCenter="nodejs"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="javascript"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="01/19/2016"
     ms.author="dobett"/>

# Introdução ao Hub IoT do Azure para Node.js

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

## Introdução

O Hub IoT do Azure é um serviço totalmente gerenciado que permite comunicações bidirecionais confiáveis e seguras entre milhões de dispositivos IoT e um back-end da solução. Um dos maiores desafios enfrentados por projetos IoT é como conectar dispositivos de forma confiável e segura para a solução de back-end. Para enfrentar esse desafio, o hub IoT:

- Oferece, de forma confiável, mensagens em larga escala do dispositivo para a nuvem e da nuvem para o dispositivo.
- Permite proteger as comunicações usando credenciais de segurança e controle de acesso por dispositivo.
- Inclui bibliotecas de dispositivo para as plataformas e idiomas mais populares.

Este tutorial mostra como:

- Use o portal do Azure para criar um hub IoT.
- Crie uma identidade de dispositivo em seu hub IoT.
- Crie um dispositivo simulado que envia a telemetria para o back-end de nuvem.

No fim deste tutorial, você terá três aplicativos de console do Node.js:

* **CreateDeviceIdentity.js**, que cria uma identidade do dispositivo e uma chave de segurança associada para conectar o dispositivo simulado.
* **ReadDeviceToCloudMessages.js**, que exibe a telemetria enviada pelo dispositivo simulado.
* **SimulatedDevice.js**, que se conecta ao hub IoT com a identidade do dispositivo criada anteriormente e envia uma mensagem de telemetria a cada segundo.

> [AZURE.NOTE] O artigo [SDKs do hub IoT][lnk-hub-sdks] fornece informações sobre vários SDKs que você pode usar para criar aplicativos executados em dispositivos e no back-end da solução.

Para concluir este tutorial, você precisará do seguinte:

+ Node.js versão 0.12.x ou posterior. <br/> [Preparar o ambiente de desenvolvimento][lnk-dev-setup] descreve como instalar o Node.js para este tutorial no Windows ou no Linux.

+ Uma conta ativa do Azure. <br/>Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos Para obter detalhes, consulte [Avaliação gratuita do Azure][lnk-free-trial].

## Crie um hub IoT

Você precisa criar um hub IoT ao qual o dispositivo simulado se conectará. As etapas a seguir mostram como concluir esta tarefa usando o portal do Azure.

1. Entre no [Portal do Azure][lnk-portal].

2. Na barra de navegação, clique em **Novo**, em seguida em **Internet das Coisas** e em **Hub IoT do Azure**.

    ![][1]

3. Na folha **Hub IoT**, escolha a configuração para o hub IoT.

    ![][2]

    * Na caixa **Nome**, insira um nome para identificar seu hub IoT. Se o **Nome** for válido e se estiver disponível, aparecerá uma marca de seleção verde na caixa **Nome**.
    * Selecione um **Tipo de preço e de dimensionamento**. Este tutorial não requer uma camada específica.
    * No **Grupo de recursos**, crie um novo grupo de recursos ou selecione um existente. Para obter mais informações, consulte [Usando os grupos de recursos para gerenciar seus recursos do Azure][lnk-resource-groups].
    * Em **Local**, selecione o local para hospedar o hub IoT.  

4. Quando você tiver escolhido as opções de configuração do hub IoT, clique em **Criar**. O Azure poderá demorar alguns minutos para criar seu hub IoT. Para verificar o status, você pode monitorar o progresso no quadro inicial ou no painel Notificações.

    ![][3]

5. Quando o hub IoT tiver sido criado com êxito, abra a folha do novo hub IoT, anote o **Nome do host** e clique no ícone **Chaves**.

    ![][4]

6. Clique na política **iothubowner**, copie e anote a cadeia de conexão na folha **iothubowner**.

    ![][5]

7. Clique em **Configurações** na folha Hub IoT, então em **Mensagens** na folha **Configurações**. Na folha **Mensagens**, anote o **nome compatível com o Hub de Eventos** e o **ponto de extremidade compatível com o Hub de Eventos**. Você precisará desses valores ao criar o aplicativo **read-d2c-messages**.

    ![][6]

Você criou seu hub IoT e tem o nome de host do Hub IoT, a cadeia de conexão do Hub IoT, o nome compatível com o Hub de Eventos e os valores de ponto de extremidade compatíveis com o Hub de Eventos necessário para concluir o restante deste tutorial.

[AZURE.INCLUDE [iot-hub-get-started-cloud-node](../../includes/iot-hub-get-started-cloud-node.md)]


[AZURE.INCLUDE [iot-hub-get-started-device-node](../../includes/iot-hub-get-started-device-node.md)]

## Executar os aplicativos

Agora você está pronto para executar os aplicativos.

1. Em um prompt de comando na pasta **readdevicetocloudmessages**, execute o seguinte comando para iniciar o monitoramento de seu hub IoT:

    ```
    node ReadDeviceToCloudMessages.js 
    ```

    ![][7]

2. Em um prompt de comando na pasta **simulateddevice**, execute o seguinte comando para iniciar o envio de dados de telemetria para seu hub IoT:

    ```
    node SimulatedDevice.js
    ```

    ![][8]

## Próximas etapas

Neste tutorial, você configurou um novo hub IoT no portal e depois criou uma identidade de dispositivo no registro de identidade do hub. Você usou essa identidade de dispositivo em um dispositivo simulado que envia mensagens de dispositivo para a nuvem para o hub e criou outro aplicativo que exibe as mensagens recebidas pelo hub. Você pode continuar a explorar os recursos do hub IoT e outros cenários de IoT nestes tutoriais:

- [Enviar mensagens da Nuvem para o Dispositivo com o Hub IoT][lnk-c2d-tutorial] mostra como enviar mensagens para dispositivos e como processar os comentários de entrega produzidos pelo Hub IoT.
- [Processar mensagens do Dispositivo para a Nuvem][lnk-process-d2c-tutorial] mostra como processar de forma confiável a telemetria e as mensagens interativas vindas dos dispositivos.
- [Carregando arquivos de dispositivos][lnk-upload-tutorial] descreve um padrão que usa mensagens da nuvem para o dispositivo para facilitar o carregamento de arquivo dos dispositivos.

<!-- Images. -->
[1]: ./media/iot-hub-node-node-getstarted/create-iot-hub1.png
[2]: ./media/iot-hub-node-node-getstarted/create-iot-hub2.png
[3]: ./media/iot-hub-node-node-getstarted/create-iot-hub3.png
[4]: ./media/iot-hub-node-node-getstarted/create-iot-hub4.png
[5]: ./media/iot-hub-node-node-getstarted/create-iot-hub5.png
[6]: ./media/iot-hub-node-node-getstarted/create-iot-hub6.png
[7]: ./media/iot-hub-node-node-getstarted/runapp1.png
[8]: ./media/iot-hub-node-node-getstarted/runapp2.png

<!-- Links -->
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/node/device/doc/devbox_setup.md
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
[lnk-upload-tutorial]: iot-hub-csharp-csharp-file-upload.md

[lnk-hub-sdks]: iot-hub-sdks-summary.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-resource-groups]: resource-group-portal.md
[lnk-portal]: https://portal.azure.com/

<!---HONumber=AcomDC_0128_2016-->