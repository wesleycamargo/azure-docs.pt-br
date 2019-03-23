---
title: Kit de Desenvolvimento da IoT para a nuvem -- Conectar o Kit de Desenvolvimento da IoT AZ3166 ao Hub IoT do Azure | Microsoft Docs
description: Neste tutorial, aprenda a configurar e conectar o Kit de Desenvolvimento da IoT AZ3166 ao Hub IoT do Azure para enviar dados para a plataforma de nuvem do Azure.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 03/21/2019
ms.author: wesmc
ms.openlocfilehash: 941455e39a32405097563b043046866aeb5c7964
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351925"
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub"></a>Como conectar o IoT DevKit AZ3166 ao Hub IoT do Azure

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Você pode usar o [Kit de Desenvolvimento da IoT MXChip](https://microsoft.github.io/azure-iot-developer-kit/) para desenvolver e criar protótipos de soluções de Internet das Coisas (IoT) que usufruem dos serviços do Microsoft Azure. Ele inclui uma placa compatível com Arduino com periféricos avançados e sensores, um pacote de placa de software livre e um [catálogo de projetos](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) crescente.

## <a name="what-you-do"></a>O que fazer

Conecte o Kit de Desenvolvimento a um Hub IoT do Azure que você criou. Em seguida, colete os dados de temperatura e umidade de sensores e envie os dados para o Hub IoT.

Você ainda não tem um DevKit? Experimente o [simulador DevKit](https://azure-samples.github.io/iot-devkit-web-simulator/) ou [compre um kit de desenvolvimento](https://aka.ms/iot-devkit-purchase).

## <a name="what-you-learn"></a>O que você aprenderá

* Como conectar Kit de Desenvolvimento da IoT com o ponto de acesso sem fio e preparar o ambiente de desenvolvimento.
* Como criar um Hub IoT e registrar um dispositivo para o Kit de Desenvolvimento da IoT MXChip.
* Como coletar dados de sensor executando um aplicativo de exemplo no Kit de Desenvolvimento da IoT MXChip.
* Como enviar os dados do sensor para o hub IoT.

## <a name="what-you-need"></a>O que você precisa

* Uma placa MXChip IoT DevKit com um cabo micro USB. [Obtenha agora](https://aka.ms/iot-devkit-purchase).
* Um computador que executa o Windows 10 ou macOS 10.10+.
* Uma assinatura ativa do Azure. [Ative uma conta de avaliação do Microsoft Azure por 30 dias](https://azureinfo.microsoft.com/us-freetrial.html).
  
## <a name="prepare-your-hardware"></a>Prepare seu hardware

Conecte o hardware a seguir no seu computador:

* Placa DevKit
* Cabo micro USB

![Requisitos de hardware](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

Conecte o Kit de Desenvolvimento ao computador, siga essas etapas:

1. Conecte a extremidade USB ao computador.

2. Conecte a extremidade micro USB ao Kit de Desenvolvimento.

3. O LED verde perto do botão liga/desliga confirma a conexão.

   ![Conexões de hardware](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect.jpg)

## <a name="configure-wi-fi"></a>Configurar o Wi-Fi

Os projetos de IoT requerem conectividade com a Internet. Use as instruções a seguir para configurar o Kit de Desenvolvimento e conectar-se ao Wi-Fi.

### <a name="enter-ap-mode"></a>Entrar no modo AP

Mantenha o botão B pressionado e solte o botão reiniciar; em seguida, solte o botão B. O seu Kit de Desenvolvimento entrará no modo AP para configurar o Wi-Fi. A tela exibirá o identificador SSID do Kit de Desenvolvimento e o endereço IP do portal de configuração.

![Botão de reinicialização, botão B e SSID](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ap.jpg)

![Definir o modo AP](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/set-ap-mode.gif)

### <a name="connect-to-devkit-ap"></a>Conexão do PA do DevKit

Agora, use outro dispositivo habilitado para Wi-Fi (computador ou telefone celular) para se conectar ao SSID do Kit de Desenvolvimento (destacado na imagem anterior). Deixe a senha em branco.

![Informações de rede e o botão Conectar](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect-ssid.png)

### <a name="configure-wi-fi-for-the-devkit"></a>Configurar o Wi-Fi para o Kit de Desenvolvimento

Abra o endereço IP exibido na tela do DevKit no seu navegador do PC ou do celular, selecione a rede Wi-Fi com a qual deseja que o DevKit se conecte e digite a senha. Selecione **Conectar**.

![Caixa de senha e botão Conectar](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-portal.png)

Quando a conexão for bem-sucedida, o DevKit reiniciará em alguns segundos. Você verá então o endereço IP e nome do Wi-Fi na tela:

![Nome do Wi-Fi e endereço IP](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ip.jpg)

> [!NOTE]
> Você precisará de uma rede de 2,4 GHz para trabalhar de DevKit de IoT. O módulo de Wi-Fi em IoT DevKit não é compatível com a rede de 5GHz. Confira as [Perguntas frequentes](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#wi-fi-configuration) para obter mais detalhes.

Após a configuração do Wi-Fi, suas credenciais persistirão no dispositivo para essa conexão, mesmo que o dispositivo esteja desconectado. Por exemplo, se você configurar o Kit de Desenvolvimento para o Wi-Fi em casa e, em seguida, levar o Kit de Desenvolvimento para o escritório, será necessário reconfigurar o modo AP (começando na etapa na seção "Entrar no modo AP") para conectar o Kit de Desenvolvimento ao Wi-Fi do seu escritório.

## <a name="start-using-the-devkit"></a>Começar a usar o Kit de Desenvolvimento

O aplicativo padrão em execução no DevKit verifica a versão mais recente do firmware e exibe alguns dados de diagnóstico de sensor para você.

### <a name="upgrade-to-the-latest-firmware"></a>Como atualizar para o firmware mais recente

> [!NOTE]
> Desde a v1.1, o Kit de desenvolvimento permite o ST-SAFE no carregador de inicialização. Você precisa atualizar o firmware se você estiver executando uma versão anterior à versão 1.1.

Se você precisa de uma atualização de firmware, a tela exibirá a versão de firmware atual e a mais recente. Siga o guia [Atualizar o firmware](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/) para atualizá-lo.

![Exibição das versões mais recente e atual do firmware](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/firmware.jpg)

> [!NOTE]
> Essa é uma tarefa que só precisa ser executada uma vez. Depois de começar a desenvolver no Kit de Desenvolvimento e carregar seu aplicativo, ele já virá com a versão mais recente do firmware.

### <a name="test-various-sensors"></a>Teste de vários sensores

Pressione o botão B para testar os sensores. Continue pressionando e soltando o botão B para completar o ciclo por cada sensor.

![Botão B e exibição de sensor](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/sensors.jpg)

## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento

### <a name="install-azure-iot-tools"></a>Instalar as Ferramentas do Azure IoT

O pacote de extensão [Ferramentas do Azure IoT](https://aka.ms/azure-iot-tools) é recomendado para o Visual Studio Code para desenvolvimento no kit de desenvolvimento. Ele contém o [Workbench dos Dispositivos de IoT do Azure](https://aka.ms/iot-workbench) para desenvolver e depurar em vários dispositivos do Kit de desenvolvimento de IoT e o [Kit de ferramentas do Hub IoT do Azure](https://aka.ms/iot-toolkit) para gerenciar e interagir com o Hub IoT do Azure.

Você pode observar estes vídeos do [Channel 9](https://channel9.msdn.com/) para ter uma visão geral sobre o que eles fazem:
* [Introdução à nova extensão de IoT do Workbench para o VS Code](https://channel9.msdn.com/Shows/Internet-of-Things-Show/IoT-Workbench-extension-for-VS-Code)
* [Quais são as novidades na extensão do Kit de ferramentas do IoT para o VS Code](https://channel9.msdn.com/Shows/Internet-of-Things-Show/Whats-new-in-the-IoT-Toolkit-extension-for-VS-Code)

Siga estas etapas para preparar o ambiente de desenvolvimento do kit de desenvolvimento:

1. Instalar o [Arduino IDE](https://www.arduino.cc/en/Main/Software). Ele fornece a cadeia de ferramentas necessária para compilar e carregar o código do Arduino.
    * **Windows**: use a versão do Windows Installer. Não instale da Store do aplicativo.
    * **macOS**: arraste e solte o **Arduino.app** extraído na pasta `/Applications`.
    * **Ubuntu**: descompacte-o na pasta, como `$HOME/Downloads/arduino-1.8.8`

2. Instale o [Visual Studio Code](https://code.visualstudio.com/), um editor de código-fonte multiplataforma com ferramentas de desenvolvimento poderosas, como preenchimento de código do IntelliSense e depuração.

3. Inicie o VS Code, procure o **Arduino** no marketplace de extensões e instale-o. Essa extensão fornece experiências aprimoradas para desenvolvimento na plataforma do Arduino.
    ![Instalar Arduino](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-arduino.png)

4. Procure as **Ferramentas do Azure IoT** no marketplace de extensões e instale-as.
    ![Instalar as Ferramentas do Azure IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-azure-iot-tools.png)

    > [!div class="nextstepaction"]
    > [Instalar o pacote de extensão de ferramentas do Azure IoT](vscode:extension/vsciot-vscode.azure-iot-tools)

5. Configurar o VS Code com configurações do Arduino.

    No Visual Studio Code, clique em **Arquivo > Preferências > Configurações**. Em seguida, clique em **...** e **abra settings.json**.
    ![Instalar as Ferramentas do Azure IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/user-settings-arduino.png)
    
    Adicione as seguintes linhas para configurar o Arduino, dependendo de sua plataforma: 

    * **Windows**:
      
        ```json
        "arduino.path": "C:\\Program Files (x86)\\Arduino",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

    * **macOS**:

        ```json
        "arduino.path": "/Applications",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

    * **Ubuntu**:
    
        Substitua o espaço reservado **{username}** abaixo por seu nome de usuário.

        ```json
        "arduino.path": "/home/{username}/Downloads/arduino-1.8.8",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

6. Clique em `F1` para abrir a paleta de comandos, digite e selecione **Arduino: Board Manager**. Pesquise **AZ3166** e instale a versão mais recente.
    ![Instalar o SDK do Kit de Desenvolvimento](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-az3166-sdk.png)

### <a name="install-st-link-drivers"></a>Instalar drivers do ST-Link

[ST-Link/V2](https://www.st.com/en/development-tools/st-link-v2.html) é a interface USB que o kit de desenvolvimento de IoT usa para comunicar-se com seu computador de desenvolvimento. Você precisará instalá-lo no Windows para exibir o código de dispositivo compilados para o Kit de desenvolvimento. Siga as etapas específicas do sistema operacional para permitir o acesso do computador ao dispositivo.

* **Windows**: Baixe e instale o driver USB do [site STMicroelectronics](https://www.st.com/en/development-tools/stsw-link009.html) para [link direto](https://aka.ms/stlink-v2-windows).
* **macOS**: não é necessário nenhum driver para o macOS.
* **Ubuntu**: Execute os comandos no terminal e sair e entrar para que a alteração de grupo entrem em vigor:
    ```bash
    # Copy the default rules. This grants permission to the group 'plugdev'
    sudo cp ~/.arduino15/packages/AZ3166/tools/openocd/0.10.0/linux/contrib/60-openocd.rules /etc/udev/rules.d/
    sudo udevadm control --reload-rules

    # Add yourself to the group 'plugdev'
    # Logout and log back in for the group to take effect
    sudo usermod -a -G plugdev $(whoami)
    ```

Agora a preparação e a configuração do ambiente de desenvolvimento estão concluídas. Vamos criar o exemplo "Olá, Mundo" para IoT: enviando dados de telemetria de temperatura para o Hub IoT do Azure.

## <a name="build-your-first-project"></a>Criar seu primeiro projeto

### <a name="open-sample-code-from-sample-gallery"></a>Abrir o código de exemplo da Galeria de exemplo

1. Verifique se o kit de desenvolvimento de IoT **não está conectado** ao computador. Inicie o VS Code primeiro e, em seguida, conecte o kit de desenvolvimento ao computador.

1. Clique em `F1` para abrir a paleta de comandos, digite e selecione **Workbench dos Dispositivos de IoT do Azure: Abrir Exemplos...**. Em seguida, selecione **Kit de Desenvolvimento de IoT** como placa.

1. Na página de Exemplos do IoT Workbench, localize **Introdução** e clique em **Abrir Exemplo**. Em seguida, selecione o caminho padrão para baixar o código de exemplo.
    ![Abrir exemplo](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/open-sample.png)

### <a name="provision-azure-iot-hub-and-device"></a>Provisionamento do Azure IoT Hub e dispositivo

1. Na nova janela de projeto aberta, clique em `F1` para abrir a paleta de comandos, digite e selecione **Workbench dos Dispositivos de IoT do Azure: Provisionar Serviços do Azure...**. Siga o guia passo a passo para concluir o provisionamento do Hub IoT do Azure e criar o dispositivo no Hub IoT.
    ![Comando de provisão](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision.png)

    > [!NOTE]
    > Se você não tiver entrado no Azure. Siga a notificação de pop-up para entrar.

1. Selecione a assinatura que deseja usar.
    ![Selecione sub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-subscription.png)

1. Em seguida, selecione ou crie uma nova [grupo de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#terminology).
    ![Selecione o grupo de recursos](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-resource-group.png)

1. No grupo de recursos que você especificou, siga o guia para selecionar ou criar um novo IoT Hub do Azure.
    ![Selecione as etapas do IoT Hub](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provision.png)

    ![Selecione o Hub IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-hub.png)

    ![Hub IoT selecionado](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-selected.png)

1. Na janela de saída, você verá o IoT Hub do Azure provisionado ![provisionado de Hub IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provisioned.png)

1. Selecione ou crie um novo dispositivo no Hub do IoT do Azure você provisionou.
    ![Selecione as etapas de dispositivo IoT](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-device-provision.png)

    ![Selecione o dispositivo de IoT provisionado](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-device.png)

1. Agora você tem o IoT Hub do Azure provisionados e o dispositivo criado nele. Também a cadeia de caracteres de conexão do dispositivo será salvo no VS Code para configurar o IoT DevKit mais tarde.
    ![Provisionamento concluído](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision-done.png)

### <a name="configure-and-compile-device-code"></a>Configurar e compilar o código de dispositivo

1. Na barra de status inferior direita, verifique se **MXCHIP AZ3166** é mostrado como a placa selecionada e se a porta serial com **STMicroelectronics** está sendo usada.
    ![Selecionar a placa e o COM](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-com.png)

1. Clique em `F1` para abrir a paleta de comandos, digite e selecione **Workbench dos Dispositivos de IoT do Azure: Definir as Configurações do Dispositivo...**  e, em seguida, selecione **Definir Configurações do Dispositivo> Configurar Cadeia de Conexão do Dispositivo > Selecionar Cadeia de Conexão do Dispositivo do Hub IoT**.

1. No kit de desenvolvimento, mantenha pressionado o **botão A**, pressione e solte o botão **redefinir** e, em seguida, solte o **botão A**. O kit de desenvolvimento entra no modo de configuração e salva a cadeia de conexão.
    ![Cadeia de conexão](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connection-string.png)

1. Clique em `F1` novamente, digite e selecione **Workbench dos Dispositivos de IoT do Azure: Carregar o código de dispositivo**. Ele começa a compilar e carregar o código para o Kit de desenvolvimento.
    ![Upload do Arduino](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/arduino-upload.png)

O Kit de Desenvolvimento reinicia e começa a execução do código.

> [!NOTE]
> Sempre que há erros ou interrupções, é possível recuperar executando o comando novamente.

## <a name="test-the-project"></a>Teste do projeto

### <a name="view-the-telemetry-sent-to-azure-iot-hub"></a>Exibir a telemetria enviada ao Hub IoT do Azure

Clique no ícone de tomada na barra de status para abrir o Monitor Serial: ![Monitor serial](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/serial-monitor.png)

O aplicativo de exemplo é executado com êxito quando você vê os seguintes resultados:

* O Monitor de Serial exibe a mensagem enviada ao Hub IoT.
* O LED no Kit de Desenvolvimento de IoT MXChip está piscando.

![Saída do monitor de serial](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/result-serial-output.png)

### <a name="view-the-telemetry-received-by-azure-iot-hub"></a>Exibir a telemetria recebida pelo Hub IoT do Azure

Você pode usar as [Ferramentas do Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) para monitorar mensagens D2C (do dispositivo para a nuvem) no Hub IoT.

1. Entre no [portal do Azure](https://portal.azure.com/) e encontre o Hub IoT que você criou.
    ![Portal do Azure](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-hub-portal.png)

1. No painel **Políticas de acesso compartilhado**, clique na **política iothubowner** e anote a cadeia de conexão do Hub IoT.
    ![Cadeia de conexão do Hub IoT do Azure](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-portal-conn-string.png)

1. No VS Code, clique em `F1`, digite e selecione **Hub IoT do Azure: Definir cadeia de conexão do Hub IoT**. Copie a cadeia de conexão.
    ![Defina a cadeia de conexão do Hub IoT do Azure](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/set-iothub-connection-string.png)

1. Expanda o painel **DISPOSITIVOS DO HUB IOT DO AZURE** à direita, clique com botão direito no nome do dispositivo que você criou e selecione **Começar a monitorar a mensagem D2C**.
    ![Monitorar a mensagem D2C](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/monitor-d2c.png)

1. No painel **SAÍDA**, veja as mensagens D2C de entrada do Hub IoT.
    ![Mensagem D2C](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/d2c-output.png)

## <a name="review-the-code"></a>Examine o código

O `GetStarted.ino` é o principal arquivo de esboço de Arduino.

![Mensagem D2C](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/code.png)

Para ver como a telemetria do dispositivo será enviada ao IoT Hub do Azure, abra o `utility.cpp` arquivo na mesma pasta. Modo de exibição [referência da API](https://microsoft.github.io/azure-iot-developer-kit/docs/apis/arduino-language-reference/) para aprender a usar periféricos e sensores em IoT DevKit.

O `DevKitMQTTClient` usado é um wrapper do **iothub_client** da [SDKs de IoT do Microsoft Azure e bibliotecas para C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client) para interagir com o IoT Hub do Azure.

## <a name="problems-and-feedback"></a>Comentários e problemas

Se houver problemas, procure uma solução nas [Perguntas frequentes sobre o kit de desenvolvimento de IoT](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) ou fale conosco por meio do [Gitter](https://gitter.im/Microsoft/azure-iot-developer-kit). Você também pode nos fornecer comentários fazendo-os nesta página.

## <a name="next-steps"></a>Próximas etapas

Você conectou um Kit de Desenvolvimento de IoT MXChip ao seu Hub IoT e enviou os dados do sensor de captura ao seu Kit de Desenvolvimento de IoT.

[!INCLUDE [iot-hub-get-started-az3166-next-steps](../../includes/iot-hub-get-started-az3166-next-steps.md)]
