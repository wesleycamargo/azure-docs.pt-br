---
title: 'Kit de Desenvolvimento de IoT para a nuvem: conectar o IoT DevKit AZ3166 a um acelerador de solução de IoT de Monitoramento Remoto | Microsoft Docs'
description: Neste tutorial, saiba como enviar status de sensores no IoT DevKit AZ3166 ao acelerador de solução de IoT de Monitoramento Remoto para monitoramento e visualização.
author: isabelcabezasm
manager: ''
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: c
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: isacabe
ms.openlocfilehash: 32742b2a680370f443051e2d86f90d94e8632850
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/12/2018
ms.locfileid: "44720575"
---
# <a name="connect-mxchip-iot-devkit-az3166-to-the-iot-remote-monitoring-solution-accelerator"></a>Conectar o MXChip IoT DevKit AZ3166 ao acelerador de solução de Monitoramento Remoto de IoT

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Você aprenderá como executar um aplicativo de exemplo em seu DevKit para enviar dados de sensor para seu acelerador de solução.

O [MXChip IoT DevKit](https://aka.ms/iot-devkit) é uma placa compatível com Arduino all-in-one periféricos e sensores avançados. É possível desenvolvê-lo usando a [Azure Machine Learning Workbench](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench) no Visual Studio Code. E vem com um [catálogo de projetos](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) crescente para guiá-lo no protótipo de soluções de IoT (Internet das Coisas) que aproveitam os serviços do Microsoft Azure.

## <a name="what-you-need"></a>O que você precisa

Vá até [Guia de Introdução](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) e **concluir as seções a seguir apenas**:

* Prepare seu hardware
* Configurar o Wi-Fi
* Começar a usar o Kit de Desenvolvimento
* Preparar o ambiente de desenvolvimento

## <a name="open-the-remote-monitoring-sample-in-vs-code"></a>Abra o exemplo de monitoramento remoto no VS Code

1. Verifique se o kit de desenvolvimento de IoT **não está conectado** ao computador. Inicie o VS Code primeiro e, em seguida, conecte o kit de desenvolvimento ao computador.

2. Clique em `F1` para abrir a paleta de comandos, digite e selecione **IoT Workbench: exemplos**. Em seguida, selecione **Kit de Desenvolvimento de IoT** como placa.

3. Encontre **Monitoramento Remoto** e clique em **Exemplo Aberto**. Isso abrirá uma nova janela do VS Code com a pasta de projeto nela.
  ![Azure Machine Learning Workbench IOT, selecione o exemplo de monitoramento remoto](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-example.png)

## <a name="configure-iot-hub-device-connection-string"></a>Configurar a cadeia de conexão do dispositivo do Hub IoT

1. Alternar o Kit de Desenvolvimento da IoT no **modo de configuração**. Para fazer isso:
   * Mantenha pressionado o botão **A**.
   * Pressione e solte a o botão**Reiniciar**.

2. A tela exibe a ID do Kit de Desenvolvimento e “Configuração”.
   
  ![Modo de configuração do Kit de desenvolvimento da IoT](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/devkit-configuration-mode.png)

3. Clique em `F1` para abrir a paleta de comandos, digite e selecione **Azure Machine Learning Workbench da IoT > Dispositivo > Configurações do Dispositico de Config**.

4. Cole a cadeia de conexão que você acabou de copiar o clique em `Enter` para configurá-lo.

## <a name="build-and-upload-the-device-code"></a>Compilação e carregamento do código de dispositivo

1. Clique em `F1` para abrir a paleta de comandos, digite e selecione **Azure Machine Learning Workbench da IoT: Dispositivo > Carregar dispositivo**.
  ![Azure Machine Learning Workbench da IOT: Dispositivo - > carregar](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-device-upload.png)

1. O Visual Studio Code então inicia a compilação e carregamento do código para o seu kit de desenvolvimento.
  ![Azure Machine Learning Workbench: Dispositivo - > Carregado](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-device-uploaded.png)

O Kit de Desenvolvimento reinicia e começa a execução do código.

## <a name="test-the-project"></a>Teste do projeto

### <a name="view-the-telemetry-sent-to-remote-monitoring-solution"></a>Exibir a telemetria enviada à solução de Monitoramento Remoto

Quando o aplicativo de exemplo for executado, o Kit de Desenvolvimento envia dados de sensor por Wi-Fi para o acelerador de solução de Monitoramento Remoto do Azure IoT. Para ver o resultado, siga estas etapas:

1. Vá para o painel de solução e, em seguida, clique em **Dispositivos**.

2. Clique no nome do dispositivo, na guia direita, você pode ver o status do sensor no Kit de desenvolvimento em tempo real.
  ![Dados de sensor no Azure IoT Suite](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-dashboard.png)

### <a name="send-a-c2d-message"></a>Enviar uma mensagem C2D

O Monitoramento Remoto permite que você chame o método remoto no dispositivo. O código sxample publica três métodos que você pode ver na seção **Método** seção quando o sensor estiver selecionado.

![Métodos do Kit de Desenvolvimento da IoT](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-methods.png)

Vamos tente alterar a cor de um dos LEDs do Kit de desenvolvimento usando o método "LedColor".

1. Selecione o nome do dispositivo da lista de dispositivos e clique e, **Trabalhos**.

  ![Crie um trabalho](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-job.png)

2. Configure os trabalhos conforme mostrado a seguir e clique em **Aplicar**.
  * Selecione trabalho: **Método Run**
  * Nome do método: **LedColor**
  * Nome do trabalho: **ChangeLedColor**
  
  ![Configurações do trabalho](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-suite-change-color.png)

Em alguns segundos, o Kit de desenvolvimento deve alterar a cor do LED de RGB (abaixo do botão A).

![LED vermelho do Kit de Desenvolvimento da IoT](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-devkit-led.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se você planeja passar para outros tutoriais, deixe o acelerador de solução de Monitoramento Remoto implantado.

Caso não precise mais do acelerador de solução, exclua-o da página Soluções provisionadas, selecionando-o e clicando em Excluir Solução:

![Excluir solução](media/quickstart-remote-monitoring-deploy/deletesolution.png)

## <a name="problems-and-feedback"></a>Comentários e problemas

Se você encontrar problemas, consulte [as perguntas frequentes do kit de desenvolvedores da IoT](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) ou entre em contato conosco usando os seguintes canais:

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como conectar a um dispositivo DevKit para o acelerador de solução de Monitoramento Remoto do Azure IoT e visualizar os dados de sensor, aqui estão as próximas etapas sugeridas:

* [Visão geral dos aceleradores de solução do Azure IoT](https://docs.microsoft.com/azure/iot-suite/)
* [Personalizar a interface do usuário](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md)
* [Conectar o kit de desenvolvimento de IoT ao aplicativo Azure IoT Central](../iot-central/howto-connect-devkit.md)