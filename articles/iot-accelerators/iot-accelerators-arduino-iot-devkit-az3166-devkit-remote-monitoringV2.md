---
title: 'Kit de Desenvolvimento de IoT para a nuvem: conectar o IoT DevKit AZ3166 a um acelerador de solução de Monitoramento Remoto de IoT| Microsoft Docs'
description: Neste tutorial, saiba como enviar status de sensores no IoT DevKit AZ3166 ao acelerador de solução de IoT de Monitoramento Remoto para monitoramento e visualização.
author: isabelcabezasm
manager: ''
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: c
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: isacabe
ms.openlocfilehash: e900b952ab9bb2054b9e4174670894027cdd2618
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38969445"
---
# <a name="connect-mxchip-iot-devkit-az3166-to-the-iot-remote-monitoring-solution-accelerator"></a>Conectar o MXChip IoT DevKit AZ3166 ao acelerador de solução de Monitoramento Remoto de IoT


[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Neste tutorial, você aprenderá como executar um aplicativo de exemplo em seu DevKit para enviar dados de sensor para seu acelerador de solução.

O [MXChip IoT DevKit](https://aka.ms/iot-devkit) é uma placa compatível com Arduino all-in-one periféricos e sensores avançados. É possível desenvolvê-lo usando a [extensão do Visual Studio Code para Arduino](https://aka.ms/arduino). E vem com um [catálogo de projetos](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) crescente para guiá-lo no protótipo de soluções de IoT (Internet das Coisas) que aproveitam os serviços do Microsoft Azure.

## <a name="what-you-need"></a>O que você precisa

Siga o [Guia de Introdução](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) para:

* Ter seu DevKit conectado ao Wi-Fi
* Preparar o ambiente de desenvolvimento


## <a name="open-the-remotemonitoring-sample"></a>Abra a amostra RemoteMonitoring

1. Desconecte o DevKit de seu computador, se ele estiver conectado.

2. Iniciar o VS Code.

3. Conecte o DevKit ao computador. O Código VS detecta automaticamente o DevKit e abre as seguintes páginas:
  * Página de introdução do DevKit.
  * Exemplos de Arduino: Exemplos práticos de como começar com DevKit.

4. Expanda a seção **EXEMPLOS DO ARDUINO** no lado esquerdo, vá até **Exemplos para MXCHIP AZ3166 > AzureIoT** e selecione **RemoteMonitoringv2**. Isso abrirá uma nova janela do VS Code com a pasta de projeto nela.

  ![Abra o projeto de Monitoramento Remoto](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-arduino-examples.png)


  > [!NOTE]
  > Se você fechar o painel, você poderá reabri-lo. Use `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) para abrir a paleta de comandos, digite **Arduino** e, em seguida, localize e selecione **Arduino: Exemplos**.

## <a name="add-a-new-physical-device"></a>Adicionar um novo dispositivo físico

No portal, vá para a seção **Dispositivos** e, clique no botão **+Novo dispositivo**. 

![Adicionar um novo dispositivo](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-add-device.png)

O *formulário de novo dispositivo* deve ser preenchido.
1. Clique em **Físico** na seção *Tipo de dispositivo*.
2. Defina sua própria ID de dispositivo (por exemplo *MXChip* ou *AZ3166*).
3. Escolha **Auto gerar chaves** na seção *Chave de autenticação*.
4. Clique no botão *Aplicar*.

![Adicionar um formulário de novo dispositivo](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-add-new-device-form.png)

Aguarde até que o portal termine o provisionamento do novo dispositivo.

![Provisionar um novo dispositivo ](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-add-device-provisioning.png)


Em seguida, a configuração do novo dispositivo será exibida.
Copie a **Cadeia de conexão** gerada.

![Cadeia de conexão do dispositivo](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-new-device-connstring.png)


Essa cadeia de conexão será usada na próxima seção.





## <a name="build-and-upload-the-device-code"></a>Compilação e carregamento do código de dispositivo

Volte para o Visual Studio Code: 

1. Use `Ctrl+P` (macOS: `Cmd + P`) e o digite a **conexão dos dispositivos de configuração da tarefa**.

  ![Escolha a sua assinatura do Azure e seu Hub IoT](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-task-config-device-conexion.png)

2. O terminal solicitará se você deseja usar a cadeia de caracteres de conexão do dispositivo IoT que você gostaria de usar. Selecione *Criar novos*e cole-o agora.

  ![Cole a cadeia de conexão](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-task-config-device-conexion-choose-iot-hub-press-button-A.png)

3. O terminal, às vezes, solicita que você entre no modo de configuração. Para fazer isso, mantenha pressionado o botão A, e em seguida, envie por push e solte o botão de redefinição e, em seguida, solte o botão A. A tela exibe a ID de DevKit e 'Configuração'.

  ![Tela do DevKit do dispositivo](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-devkit-screen.png)

  > [!NOTE]
  > A cadeia de conexão deve ser salva em sua área de transferência se você seguiu a última seção deste tutorial. Caso contrário, você deve ir para o portal do Azure e procurar o Hub IoT de seu grupo de recursos de Monitoramento Remoto. Lá, você pode ver os dispositivos conectados do Hub IoT e copiar a cadeia de conexão do dispositivo.

  ![Procure a cadeia de conexão](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-connection-string-of-a-device.png)


Agora, você pode ver o novo dispositivo físico na seção do VS Code "dispositivos de Hub IoT do Azure":

![Observe o novo dispositivo de Hub IoT](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-new-iot-hub-device.png)

## <a name="test-the-project"></a>Teste do projeto

Quando o aplicativo de exemplo for executado, o DevKit enviará dados de sensor por Wi-Fi para seus aceleradores de solução de IoT. Para ver o resultado, siga estas etapas:

1. Vá até o acelerador de solução de IoT e clique em **PAINEL**.

2. No console do acelerador de solução de IoT, você verá o status do sensor de DevKit. 

![Dados de sensor em aceleradores de solução de IoT](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-dashboard.png)

Se você clicar no nome do sensor (AZ3166) uma guia é exibida no lado direito do painel, onde você pode ver o gráfico de sensores de MX Chip em tempo real.


## <a name="send-a-c2d-message"></a>Enviar uma mensagem C2D
O Monitoramento Remoto v2 permite que você chame o método remoto no dispositivo.
O código de exemplo do MX Chip publica três métodos que você pode ver na seção de Método quando o sensor está selecionado.

![Métodos MX Chip](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-methods.png)

Você pode alterar a cor de um dos leds de MX Chip usando o método "LedColor". Para fazer isso, marque a caixa de seleção do dispositivo e clique no botão de agenda. 

![Métodos MX Chip](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-schedule.png)

Escolha o método chamado ChangeColor na lista suspensa onde todos os métodos apareceram, escreva um nome e aplique.

![Lista suspensa MX Chip](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/iot-suite-change-color.png)

Em alguns segundos, o MX Chip físico deve alterar a cor do led de RGB (abaixo do botão A)

![LED do MX Chip](./media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringV2/azure-iot-suite-devkit-led.png)


## <a name="change-device-id"></a>Alterar ID do dispositivo

Você pode alterar a ID do dispositivo no Hub IoT seguindo [neste guia](https://microsoft.github.io/azure-iot-developer-kit/docs/customize-device-id/).


## <a name="problems-and-feedback"></a>Comentários e problemas

Se você encontrar problemas, consulte [Perguntas frequentes](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) ou entre em contato conosco pelos canais abaixo:

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a conectar a um dispositivo de DevKit a seus aceleradores de solução de IoT e a visualizar os dados do sensor, estas são as próximas etapas sugeridas:

* [Visão geral dos aceleradores de solução de IoT](https://docs.microsoft.com/azure/iot-suite/)
* [Conectar um dispositivo MXChip IoT DevKit ao aplicativo Microsoft IoT Central](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit)
