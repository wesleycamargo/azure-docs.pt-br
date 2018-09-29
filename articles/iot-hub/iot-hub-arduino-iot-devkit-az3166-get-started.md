---
title: Kit de Desenvolvimento da IoT para a nuvem -- Conectar o Kit de Desenvolvimento da IoT AZ3166 ao Hub IoT do Azure | Microsoft Docs
description: Neste tutorial, aprenda a configurar e conectar o Kit de Desenvolvimento da IoT AZ3166 ao Hub IoT do Azure para enviar dados para a plataforma de nuvem do Azure.
author: rangv
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 08/27/2018
ms.author: rangv
ms.openlocfilehash: 55901d6f3bcbf5511b6921939fdcba03972efed3
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47182834"
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub"></a>Como conectar o IoT DevKit AZ3166 ao Hub IoT do Azure

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

Você pode usar o [Kit de Desenvolvimento da IoT MXChip](https://microsoft.github.io/azure-iot-developer-kit/) para desenvolver e criar protótipos de soluções de Internet das Coisas (IoT) que usufruem dos serviços do Microsoft Azure. Ele inclui uma placa compatível com Arduino com periféricos avançados e sensores, um pacote de placa de software livre e um [catálogo de projetos](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) crescente.

## <a name="what-you-do"></a>O que fazer

Conecte o [Kit de Desenvolvimento](https://microsoft.github.io/azure-iot-developer-kit/) a um Hub IoT do Azure que você criou, colete os dados de temperatura e umidade de sensores e envie os dados para o Hub IoT.

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

### <a name="connect-to-devkit-ap"></a>Conexão do PA do DevKit

Agora, use outro dispositivo habilitado para Wi-Fi (computador ou telefone celular) para se conectar ao SSID do Kit de Desenvolvimento (destacado na imagem anterior). Deixe a senha em branco.

![Informações de rede e o botão Conectar](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect-ssid.png)

### <a name="configure-wi-fi-for-the-devkit"></a>Configurar o Wi-Fi para o Kit de Desenvolvimento

Abra o endereço IP exibido na tela do DevKit no seu navegador do PC ou do celular, selecione a rede Wi-Fi com a qual deseja que o DevKit se conecte e digite a senha. Selecione **Conectar**.

![Caixa de senha e botão Conectar](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-portal.png)

Quando a conexão for bem-sucedida, o DevKit reiniciará em alguns segundos. Você verá então o endereço IP e nome do Wi-Fi na tela:

![Nome do Wi-Fi e endereço IP](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ip.jpg)

> [!NOTE] 
> O endereço IP exibido na foto pode não corresponder ao endereço IP real atribuído e exibido na tela do Kit de Desenvolvimento. Isso é normal, já que o Wi-Fi usa o DHCP para atribuir IPs dinamicamente.

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

### <a name="install-azure-iot-workbench"></a>Instalar o Azure IoT Workbench

A extensão [Azure IoT Workbench](https://aka.ms/iot-workbench) é recomendada para o Visual Studio Code para desenvolvimento no kit de desenvolvimento.

O Azure IoT Workbench fornece uma experiência integrada para o desenvolvimento de soluções de IoT. Ele ajuda no desenvolvimento para dispositivos e para a nuvem usando o Azure IoT e outros serviços. Assista a este vídeo do Channel9 para obter uma visão geral do que ele faz.

Siga estas etapas para preparar o ambiente de desenvolvimento do kit de desenvolvimento:

1. Baixe e instale o [IDE do Arduino](https://www.arduino.cc/en/Main/Software). Ele fornece a cadeia de ferramentas necessária para compilar e carregar o código do Arduino.
    * **Windows**: use a versão do Windows Installer.
    * **macOS**: arraste e solte o **Arduino.app** extraído na pasta `/Applications`.
    * **Ubuntu**: descompacte-o na pasta, como `$HOME/Downloads/arduino-1.8.5`

2. Instale o [Visual Studio Code](https://code.visualstudio.com/), um editor de código-fonte multiplataforma com ferramentas de desenvolvimento poderosas, como preenchimento de código do IntelliSense e depuração.

3. Procure o **Azure IoT Workbench** no marketplace de extensões e instale-o.
    ![Instalar o Azure IoT Workbench](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-workbench.png) Juntamente com o Azure IoT Workbench, outras extensões dependentes serão instaladas.

4. Abra **Arquivo > Preferências > Configurações** e adicione as seguintes linhas para configurar o Arduino.
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

    ```json
    "arduino.path": "/home/{username}/Downloads/arduino-1.8.5",
    "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
    ```

5. Clique em `F1` para abrir a paleta de comandos, digite e selecione **Arduino: Gerenciador de Placas**. Pesquise **AZ3166** e instale a versão mais recente.
    ![Instalar o SDK do Kit de Desenvolvimento](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-sdk.png)

### <a name="install-st-link-drivers"></a>Instalar drivers do ST-Link

[ST-Link/V2](http://www.st.com/en/development-tools/st-link-v2.html) é a interface USB que o kit de desenvolvimento de IoT usa para comunicar-se com seu computador de desenvolvimento. Siga as etapas específicas do sistema operacional para permitir o acesso do computador ao dispositivo.

* **Windows**: baixe e instale o driver USB do [site STMicroelectronics](http://www.st.com/en/development-tools/stsw-link009.html).
* **macOS**: não é necessário nenhum driver para o macOS.
* **Ubuntu**: execute o seguinte no terminal e faça logoff e logon para que a alteração do grupo entre em vigor:
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

1. Verifique se o kit de desenvolvimento de IoT **não está conectado** ao computador. Inicie o VS Code primeiro e, em seguida, conecte o kit de desenvolvimento ao computador.

1. Na barra de status inferior direita, verifique se **MXCHIP AZ3166** é mostrado como a placa selecionada e se a porta serial com **STMicroelectronics** está sendo usada.
    ![Selecionar a placa e o COM](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-board.png)

1. Clique em `F1` para abrir a paleta de comandos, digite e selecione **IoT Workbench: exemplos**. Em seguida, selecione **Kit de Desenvolvimento de IoT** como placa.

1. Na página de Exemplos do IoT Workbench, localize **Introdução** e clique em **Abrir Exemplo**. Em seguida, selecione o caminho padrão para baixar o código de exemplo.
    ![Abrir exemplo](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/open-sample.png)

1. Se você não tiver a extensão Arduino no código VS instalada, clique em **Instalar** no painel de notificação.
    ![Instalar a extensão Arduino](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-arduino-ext.png)

1. Na nova janela de projeto aberta, clique em `F1` para abrir a paleta de comandos, digite e selecione **IoT Workbench: nuvem** e, em seguida, selecione **Provisionamento do Azure**. Siga o guia passo a passo para concluir o provisionamento do Hub IoT do Azure e criar o dispositivo.
    ![Provisionamento na nuvem](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/cloud-provision.png)

1. Clique em `F1` para abrir a paleta de comandos, digite e selecione **IoT Workbench: dispositivo** e, em seguida, selecione **Definir Configurações do Dispositivo > Selecionar Cadeia de Conexão de Dispositivo do Hub IoT**.

1. No kit de desenvolvimento, mantenha pressionado o **botão A**, pressione e solte o botão **redefinir** e, em seguida, solte o **botão A**. O kit de desenvolvimento entra no modo de configuração e salva a cadeia de conexão.
    ![Cadeia de conexão](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connection-string.png)

1. Clique em `F1` novamente, digite e selecione **IoT Workbench: dispositivo** e, em seguida, selecione **Upload de Dispositivo**.
    ![Upload do Arduino](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/arduino-upload.png)

O Kit de Desenvolvimento reinicia e começa a execução do código.

> [!NOTE]
> Sempre que há erros ou interrupções, é possível recuperar executando o comando novamente.

## <a name="test-the-project"></a>Teste do projeto

### <a name="view-the-telemetry-sent-to-azure-iot-hub"></a>Exibir a telemetria enviada ao Hub IoT do Azure

Clique no ícone de tomada na barra de status para abrir o Monitor de Serial: ![Monitor de serial](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/serial-monitor.png)

O aplicativo de exemplo é executado com êxito quando você vê os seguintes resultados:

* O Monitor de Serial exibe a mensagem enviada ao Hub IoT.
* O LED no Kit de Desenvolvimento de IoT MXChip está piscando.

![Saída do monitor de serial](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/result-serial-output.png)

### <a name="view-the-telemetry-received-by-azure-iot-hub"></a>Exibir a telemetria recebida pelo Hub IoT do Azure

Você pode usar o [Kit de ferramentas de IoT do Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) para monitorar mensagens D2C (do dispositivo para a nuvem) no Hub IoT.

1. No Visual Studio Code, procure **Kit de ferramentas de IoT do Azure** no marketplace de extensões e instale-o.

1. Entre no [portal do Azure](https://portal.azure.com/) e encontre o Hub IoT que você criou.
    ![portal do Azure](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-hub-portal.png)

1. No painel **Políticas de acesso compartilhado**, clique na **política iothubowner** e anote a cadeia de conexão do Hub IoT.
    ![Cadeia de conexão do Hub IoT do Azure](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-portal-conn-string.png)

1. No Visual Studio Code, expanda **DISPOSITIVOS DO HUB IOT DO AZURE** no canto inferior esquerdo e clique em **Definir Cadeia de Conexão do Hub IoT**.
    ![Defina a cadeia de conexão do Hub IoT do Azure](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-toolkit-conn-string.png)

1. Clique em **IoT: iniciar o monitoramento de mensagens D2C** no menu de contexto.

1. No painel **SAÍDA**, veja as mensagens D2C de entrada do Hub IoT.
    ![Mensagem D2C](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-toolkit-console.png)

## <a name="problems-and-feedback"></a>Comentários e problemas

Se houver problemas, procure uma solução nas [Perguntas frequentes sobre o kit de desenvolvimento de IoT](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) ou fale conosco por meio do [Gitter](https://gitter.im/Microsoft/azure-iot-developer-kit). Você também pode nos fornecer comentários fazendo-os nesta página.

## <a name="next-steps"></a>Próximas etapas

Você conectou um Kit de Desenvolvimento de IoT MXChip ao seu Hub IoT e enviou os dados do sensor de captura ao seu Kit de Desenvolvimento de IoT.

[!INCLUDE [iot-hub-get-started-az3166-next-steps](../../includes/iot-hub-get-started-az3166-next-steps.md)]
