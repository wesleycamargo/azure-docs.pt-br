<properties
	pageTitle="Introdução ao Hub IoT do Azure | Microsoft Azure"
	description="Siga este tutorial para começar a usar o Hub IoT do Azure com Java."
	services="iot-hub"
	documentationCenter="java"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="java"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="12/21/2015"
     ms.author="dobett"/>

# Tutorial: Introdução ao Hub IoT do Azure

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

Ao fim deste tutorial, você terá três aplicativos de console do Java:

* **create-device-identity**, que cria uma identidade do dispositivo e uma chave de segurança associada para conectar o dispositivo simulado.
* **read-d2c-messages**, que exibe a telemetria enviada pelo dispositivo simulado.
* **simulated-device**, que se conecta ao hub IoT com a identidade do dispositivo criada anteriormente e envia uma mensagem de telemetria a cada segundo.

> [AZURE.NOTE]O artigo [SDKs do hub IoT][lnk-hub-sdks] fornece informações sobre vários SDKs que você pode usar para criar aplicativos executados em dispositivos e no back-end da solução.

Para concluir este tutorial, você precisará do seguinte:

+ Java SE 8. <br/> [Preparar o ambiente de desenvolvimento][lnk-dev-setup] descreve como instalar o Java para este tutorial no Windows ou no Linux.

+ Maven 3. <br/> [Preparar o ambiente de desenvolvimento][lnk-dev-setup] descreve como instalar o Maven para este tutorial no Windows ou no Linux.

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

Você criou seu hub IoT e tem o nome de host do Hub IoT, a cadeia de conexão do Hub IoT, o nome compatível com o Hub de Eventos e o ponto de extremidade compatível com o Hub de Eventos necessário para concluir o restante deste tutorial.

[AZURE.INCLUDE [iot-hub-get-started-cloud-java](../../includes/iot-hub-get-started-cloud-java.md)]


[AZURE.INCLUDE [iot-hub-get-started-device-java](../../includes/iot-hub-get-started-device-java.md)]

## Executar os aplicativos

Agora você está pronto para executar os aplicativos.

1. Em um prompt de comando na pasta read-d2c, execute o seguinte comando para iniciar o monitoramento de seu hub IoT:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```

    ![][7]

2. Em um prompt de comando na pasta simulated-device, execute o seguinte comando para iniciar o envio de dados de telemetria para seu hub IoT:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```

    ![][8]

## Próximas etapas

Neste tutorial, você configurou um novo hub IoT no portal e depois criou uma identidade de dispositivo no registro de identidade do hub. Você usou essa identidade de dispositivo em um dispositivo simulado que envia mensagens de dispositivo para a nuvem para o hub e criou outro aplicativo que exibe as mensagens recebidas pelo hub. Você pode continuar a explorar os recursos do hub IoT e outros cenários de IoT nestes tutoriais:

- [Enviar mensagens da Nuvem para o Dispositivo com o Hub IoT][lnk-c2d-tutorial] mostra como enviar mensagens para dispositivos e como processar os comentários de entrega produzidos pelo Hub IoT.
- [Processar mensagens do Dispositivo para a Nuvem][lnk-process-d2c-tutorial] mostra como processar de forma confiável a telemetria e as mensagens interativas vindas dos dispositivos.
- [Carregando arquivos de dispositivos][lnk-upload-tutorial] descreve um padrão que usa mensagens da nuvem para o dispositivo para facilitar o carregamento de arquivo dos dispositivos.

Veja mais informações sobre o hub IoT nos seguintes artigos:

* [Visão geral do Hub IoT][lnk-hub-overview]
* [Guia do desenvolvedor do Hub IoT][lnk-hub-dev-guide]
* [Projetar sua solução do hub IoT][lnk-hub-guidance]
* [Plataformas e idiomas do dispositivo com suporte][lnk-supported-devices]
* [Centro de Desenvolvedores do IoT do Azure][lnk-dev-center]

<!-- Images. -->
[1]: ./media/iot-hub-java-java-getstarted/create-iot-hub1.png
[2]: ./media/iot-hub-java-java-getstarted/create-iot-hub2.png
[3]: ./media/iot-hub-java-java-getstarted/create-iot-hub3.png
[4]: ./media/iot-hub-java-java-getstarted/create-iot-hub4.png
[5]: ./media/iot-hub-java-java-getstarted/create-iot-hub5.png
[6]: ./media/iot-hub-java-java-getstarted/create-iot-hub6.png
[7]: ./media/iot-hub-java-java-getstarted/runapp1.png
[8]: ./media/iot-hub-java-java-getstarted/runapp2.png

<!-- Links -->
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/java/device/doc/devbox_setup.md
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
[lnk-upload-tutorial]: iot-hub-csharp-csharp-file-upload.md

[lnk-hub-overview]: iot-hub-what-is-iot-hub.md
[lnk-hub-guidance]: iot-hub-guidance.md
[lnk-hub-dev-guide]: iot-hub-devguide.md
[lnk-supported-devices]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/tested_configurations.md
[lnk-dev-center]: http://www.azure.com/develop/iot
[lnk-hub-sdks]: iot-hub-sdks-summary.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-resource-groups]: resource-group-portal.md
[lnk-portal]: https://portal.azure.com/

<!---HONumber=AcomDC_0114_2016-->