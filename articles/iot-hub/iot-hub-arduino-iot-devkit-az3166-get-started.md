---
title: "IoT DevKit para a nuvem - conexão IoT DevKit AZ3166 ao Azure Hub Azure IoT Hub | Microsoft Docs"
description: Neste tutorial, aprenda a configurar e conectar IoT DevKit AZ3166 ao Azure IoT Hub para enviar dados para a plataforma de nuvem do Azure.
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: 
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2017
ms.author: xshi
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 122fac584ac5b954ef7b33a3121bee2c502ebc63
ms.contentlocale: pt-br
ms.lasthandoff: 08/16/2017

---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub-in-the-cloud"></a>Como conectar o IoT DevKit AZ3166 ao Azure IoT Hub na nuvem

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

O [MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) pode ser usado para desenvolver e criar protótipos de soluções de Internet das Coisas (IoT) usufruindo dos serviços do Microsoft Azure. Ele inclui um quadro de Arduino compatível com periféricos avançados e sensores, um pacote de quadro do código-fonte aberto e um [catálogo de projetos](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) crescente.

## <a name="what-you-do"></a>O que fazer
Conecte o [DevKit](https://microsoft.github.io/azure-iot-developer-kit/) com um Azure IoT Hub que você criou, colete os dados de temperatura e umidade de sensores e envie os dados para o Hub IoT.

Você ainda não tem um DevKit? Obtenha um novo [aqui](https://aka.ms/iot-devkit-purchase).

## <a name="what-you-learn"></a>O que você aprenderá

* Como conectar IoT DevKit com o ponto de acesso sem fio e preparar o ambiente de desenvolvimento.
* Como criar um Hub IoT e registrar um dispositivo para o MXChip IoT DevKit.
* Como coletar dados de sensor executando um aplicativo de exemplo no MXChip IoT DevKit.
* Como enviar os dados do sensor para o hub IoT.

## <a name="what-you-need"></a>O que você precisa

* Uma placa MXChip IoT DevKit com um cabo micro USB. [Experimente agora](https://aka.ms/iot-devkit-purchase)
* Um computador que executa o Windows 10 ou macOS 10.10 +
* Uma assinatura ativa do Azure
  * Ative uma [conta do Microsoft Azure de avaliação por 30 dias](https://azureinfo.microsoft.com/us-freetrial.html)

## <a name="prepare-your-hardware"></a>Prepare seu hardware

Conecte o hardware no seu computador.

### <a name="hardware-you-need"></a>O hardware que você precisa

* Placa DevKit
* Cabo micro USB

![guia de introdução: hardware](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

### <a name="connect-devkit-to-your-computer"></a>Conecte o DevKit ao seu computador

1. Conecte a extremidade do USB ao seu computador
2. Conecte a extremidade do USB micro ao DevKit
3. O LED verde perto do botão ligar/desligar confirmará a conexão

![guia de introdução: conexão](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect.jpg)

## <a name="configure-wifi"></a>Configuração do Wi-Fi

Os projetos de IoT requerem conexão com a Internet. Use as instruções a seguir para configurar o DevKit e conectar-se ao Wi-Fi.

### <a name="enter-ap-mode"></a>Entrar no modo PA

Mantenha o botão B pressionado e solte o botão reiniciar, em seguida, solte o botão B. O seu DevKit entrará no modo PA para configurar o Wi-Fi. A tela exibirá o identificador SSID do DevKit e o endereço IP do portal de configuração:

![guia de introdução: PA Wi-Fi](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ap.jpg)

### <a name="connect-to-devkit-ap"></a>Conexão do PA do DevKit

Agora, use outro dispositivo habilitado para Wi-Fi (PC ou telefone celular) para se conectar ao SSID do DevKit (destacado na captura de tela acima), deixe a senha em branco.

![guia de introdução: SSID](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect-ssid.png)

### <a name="configure-wifi-for-devkit"></a>Configuração do Wi-Fi ao DevKit

Abra o endereço IP exibido na tela do DevKit no seu navegador do PC ou do celular, selecione a rede Wi-Fi com a qual deseja conectar o DevKit e digite a senha. Clique em **Conectar** para concluir:

![Guia de Introdução: portal do Wi-Fi](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-portal.png)

Se a conexão for bem-sucedida, o DevKit reiniciará em alguns segundos. Se for bem-sucedida, você verá o endereço IP e nome do Wi-Fi na tela:

![Guia de Introdução: IP do Wi-Fi](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ip.jpg)

> [!NOTE] 
O endereço IP exibido na foto pode não corresponder o IP real atribuído e exibido na tela do DevKit. Isso é normal já que o Wi-Fi usa o DHCP para atribuir IPs dinamicamente.

Após a configuração do Wi-Fi, suas credenciais serão mantidas no dispositivo para essa conexão, mesmo se desconectado. Por exemplo, se você configurar o DevKit para o Wi-Fi em casa e, em seguida, levar o DevKit para o escritório, será necessário reconfigurar o modo PA (começando na etapa **Acessar modo AP**) para conectar o DevKit ao Wi-Fi do seu escritório. 

## <a name="start-using-devkit"></a>Começar a usar o DevKit

O aplicativo padrão em execução no DevKit verificará a versão mais recente do firmware e exibirá alguns dados de diagnóstico de sensor para você.

### <a name="upgrade-to-the-latest-firmware"></a>Como atualizar para o firmware mais recente

Se houver uma atualização necessária, aparecerá uma solicitação na tela tanto na versão atual quanto na versão mais recente do firmware. Siga o guia [Atualização do firmware](https://microsoft.github.io/azure-iot-developer-kit/docs/upgrading/) para atualizá-lo.

![Guia de Introdução: firmware](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/firmware.jpg)

> [!NOTE] 
Você só precisará fazer isso uma vez. Depois de desenvolver o DevKit e carregar seu aplicativo, o seu aplicativo já virá com a versão mais recente do firmware.

### <a name="test-various-sensors"></a>Teste de vários sensores

Pressione o botão B para testar os sensores e continue pressionando e soltando o botão até completar o ciclo entre os sensores.

![Guia de Introdução: sensores](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/sensors.jpg)

## <a name="prepare-development-environment"></a>Prepare o ambiente de desenvolvimento

Agora é hora de configurar o ambiente de desenvolvimento: ferramentas e pacotes para você compilar aplicativos impressionantes de IoT. Você pode escolher a versão do Windows ou macOS de acordo com seu sistema operacional.


### <a name="windows"></a>Windows

Recomendamos usar o pacote de instalação para preparar o ambiente de desenvolvimento. Se tiver alguma dificuldade, siga as [etapas manuais](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/).

#### <a name="download-latest-package"></a>Baixe o pacote mais recente

O arquivo `.zip` baixado contém todas as ferramentas e pacotes necessários para o desenvolvimento do DevKit.

> [!div class="button"]
[Baixar](https://azureboard.azureedge.net/prod/installpackage/devkit_install_1.0.2.zip)


> [!NOTE] 
> O arquivo `.zip` contém as seguintes ferramentas e pacotes. Se você já tiver alguns componentes instalados, o script irá detectá-los e ignorá-los.
> * Node.js e Yarn: tempo de execução para o script de instalação e as tarefas automatizadas
> * [MSI da CLI 2.0 do Azure](https://docs.microsoft.com//cli/azure/install-azure-cli#windows) -experiência de linha de comando de plataforma cruzada para o gerenciamento de recursos do Azure, o MSI contém Python e pip dependentes.
> * [Visual Studio Code](https://code.visualstudio.com/): editor de códigos simples para o desenvolvimento do DevKit
> * [Extensão do Visual Studio Code para Arduino](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-arduino): permite o desenvolvimento do Arduino no VS Code
> * [Arduino IDE](https://www.arduino.cc/en/Main/Software): a extensão para Arduino depende dessa ferramenta
> * Pacote de placa do DevKit: cadeias de ferramentas, bibliotecas e projetos para o DevKit
> * Utilitário de Link-ST: utilitários e drivers essenciais

#### <a name="run-installation-script"></a>Execute o script de instalação

No Explorador de arquivos do Windows, localize e extraia o `.zip` e, então, localize `install.cmd` e selecione com o botão direito o **"Executar como administrador"** para iniciar.

![Guia de Introdução: execução-admin](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/run-admin.png)

Durante a instalação, você verá o progresso de cada ferramenta ou pacote.

![Introdução: instalação](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install.png)

#### <a name="confirm-to-install-drivers"></a>Confirme para instalar os drivers

O VS Code para extensão do Arduino depende do Arduino IDE. Se esta for a primeira vez que você está instalando o Arduino IDE, será necessário instalar os drivers:

![Guia de Introdução: driver](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/driver.png)

Deve levar cerca de 10 minutos para concluir a instalação, dependendo da velocidade da Internet. Depois que a instalação for concluída, você deverá ver os atalhos do Visual Studio Code e do Arduino IDE na área de trabalho.

> [!NOTE] 
Eventualmente, ao iniciar o VS Code, aparecerá um erro notificando que não foi possível encontrar o Arduino IDE nem o pacote de placa relacionado. Para resolvê-lo, feche o VS Code e inicialize o Arduino IDE uma vez, assim o VS Code localizará o caminho do Arduino IDE corretamente.


### <a name="macos-preview"></a>macOS (visualização)

Siga estas etapas para preparar o ambiente de desenvolvimento no macOS.

#### <a name="install-azure-cli-20"></a>Instalar a CLI 2.0 do Azure

Siga o [guia oficial](https://docs.microsoft.com//cli/azure/install-azure-cli) para instalar a CLI 2.0 do Azure:

Instale a CLI 2.0 do Azure com um comando `curl`:

```bash
curl -L https://aka.ms/InstallAzureCli | bash
```

E reinicie o seu shell de comando para que as alterações entrem em vigor:

```bash
exec -l $SHELL
```

#### <a name="install-arduino-ide"></a>Instalação do Arduino IDE

A extensão de Arduino de Visual Studio Code se baseia no Arduino IDE. Baixe e instale o [Arduino IDE para macOS](https://www.arduino.cc/en/Main/Software).

#### <a name="install-visual-studio-code"></a>Instalar o Visual Studio Code

Baixe e instale o [Visual Studio Code para macOS](https://code.visualstudio.com/). Essa será a ferramenta de desenvolvimento principal para criar aplicativos de DevKit de IoT.

####  <a name="download-latest-package"></a>Baixe o pacote mais recente

1. Instale o Node.js. Você pode usar o gerenciador de pacotes macOS popular [Homebrew](https://brew.sh/) ou um [instalador pré-compilado](https://nodejs.org/en/download/) para instalá-lo.

2. Baixe o arquivo `.zip` que contém scripts de tarefas necessárias para o desenvolvimento de DevKit no VS Code.

   > [!div class="button"]
   [Baixar](https://azureboard.azureedge.net/installpackage/devkit_tasks_1.0.2.zip)

   Localize e extraia o `.zip`. Em seguida, inicie o aplicativo **Terminal** e execute os seguintes comandos para configurar:

   Mova a pasta extraída para sua pasta de usuário macOS:
   ```bash
   mv [.zip extracted folder]/azure-board-cli ~/. ; cd ~/azure-board-cli
   ```
  
   Instale os pacotes `npm`:
   ```
   npm install
   ```

#### <a name="install-vs-code-extension-for-arduino"></a>Instalação da extensão de VS Code para Arduino

O Visual Studio Code permite que você instale extensões do Marketplace diretamente na ferramenta. Clique no ícone de extensões no painel de menu à esquerda e, em seguida, procure `Arduino` para instalar:

![extensões de instalação](media/iot-hub-arduino-devkit-az3166-get-started/installation-extensions-mac.png)

#### <a name="install-devkit-board-package"></a>Instalação do pacote de placa do DevKit

Você precisará adicionar a placa DevKit usando o gerenciador de placas no Visual Studio Code.

1. Use `Cmd+Shift+P` para invocar a paleta de comandos e digite **Arduino**, em seguida, localize e selecione **Arduino: gerenciador de placas**.

2. Clique em **"URLs adicionais"** na parte inferior direita.
   ![instalação adicional de URLs](media/iot-hub-arduino-devkit-az3166-get-started/installation-additional-urls-mac.png)

3. No arquivo `settings.json`, adicione uma linha na parte inferior do painel `USER SETTINGS` e salve.
   ```json
   "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
   ```
   ![instalação de configurações de json](media/iot-hub-arduino-devkit-az3166-get-started/installation-settings-json-mac.png)

4. Agora, no gerenciador de placas, procure "az3166" e instale a versão mais recente.
   ![instalação az3166](media/iot-hub-arduino-devkit-az3166-get-started/installation-az3166-mac.png)

Agora você já instalou todas as ferramentas e os pacotes necessários para macOS.


## <a name="open-project-folder"></a>Abra a pasta do projeto

### <a name="launch-vs-code"></a>Iniciar o VS Code

Verifique se que o DevKit não está conectado. Inicie o VS Code primeiro e conecte o DevKit ao seu computador. O VS Code o encontra automaticamente e abre uma página pop up de introdução:

![mini-solution-vscode](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution-vscode.png)

> [!NOTE] 
Eventualmente, ao iniciar o VS Code, aparecerá um erro notificando que não foi possível encontrar o Arduino IDE nem o pacote de placa relacionado. Para resolvê-lo, feche o VS Code e inicialize o Arduino IDE uma vez, assim o VS Code localizará o caminho do Arduino IDE corretamente.

### <a name="open-arduino-examples-folder"></a>Abra a pasta de exemplos de Arduino

Mude para a guia **"Exemplos de Arduino"**, navegue até `Examples for MXCHIP AZ3166 > AzureIoT` e clique em `GetStarted`.

![Exemplos de minisolução](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution-examples.png)

Se você fechar o painel, para recarregá-lo, use `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) para executar a paleta de comandos e digite **Arduino** para localizar e selecionar **Arduino: exemplos**.

## <a name="provision-azure-services"></a>Provisionamento dos serviços do Azure

Na janela de solução, execute a tarefa `Ctrl+P` (macOS: `Cmd+P`), digitando "task cloud-provision":

No terminal do VS Code, uma linha de comando interativa orientará você durante o provisionamento dos serviços do Azure necessários:

![provisionamento de nuvem de minisolução](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/cloud-provision.png)

## <a name="build-and-upload-arduino-sketch"></a>Compilação e carregamento do esboço do Arduino

### <a name="install-required-library"></a>Instalação da biblioteca necessária

1. Pressione `F1` ou `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) para executar a paleta de comandos e digite **Arduino**, em seguida, localize e selecione **Arduino: gerenciador de biblioteca**.

2. Procure `ArduinoJson` biblioteca e clique em **instalar**

### <a name="build-and-upload-the-device-code"></a>Compilação e carregamento do código de dispositivo

Use `Ctrl+P` (macOS: `Cmd+P`) para executar o carregamento de dispositivo de tarefas. O terminal solicitará que você entre no modo de configuração. Para fazer isso, mantenha o botão A pressionado e, em seguida, solte o botão Reiniciar. A tela exibirá "Configuração". Isso para definir a cadeia de conexão que recupera da etapa "tarefa provisionamento de nuvem".

Em seguida, ele verificará e carregará o esboço do Arduino:

![carregamento de dispositivo de minisolução](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/device-upload.png)

O DevKit reinicializará e iniciará a execução do código.

## <a name="test-the-project"></a>Teste do projeto

No VS Code, clique no ícone de tomada de alimentação na barra de status para abrir o Monitor Serial.

O aplicativo de exemplo é executado com êxito quando você vê os seguintes resultados:

* O Monitor Serial exibe as mesmas informações como o conteúdo na captura de tela abaixo.
* O LED no MXChip IoT DevKit está piscando.

![Saída final no VS Code](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/result-serial-output.png)

## <a name="problems-and-feedback"></a>Comentários e problemas

Acesse [Perguntas frequentes](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) se tiver alguma dúvida ou entrar em contato conosco pelos canais abaixo.

## <a name="next-steps"></a>Próximas etapas

Você conectou um MXChip IoT DevKit ao seu Hub IoT e enviou os dados do sensor de captura ao seu IoT DevKit.

Para continuar a introdução ao Hub IoT e explorar outros cenários de IoT, confira:

- [Gerenciar mensagens do dispositivos de nuvem com o iothub-explorer](https://docs.microsoft.com/azure/iot-hub/iot-hub-explorer-cloud-device-messaging)
- [Salvar mensagens do Hub IoT para o armazenamento de dados do Azure](https://docs.microsoft.com//azure/iot-hub/iot-hub-store-data-in-azure-table-storage)
- [Use o Power BI para visualizar dados do sensor do Hub IoT do Azure em tempo real](https://docs.microsoft.com//azure/iot-hub/iot-hub-live-data-visualization-in-power-bi)
- [Use Aplicativos Web do Azure para visualizar dados do sensor do Hub IoT do Azure em tempo real](https://docs.microsoft.com//azure/iot-hub/iot-hub-live-data-visualization-in-web-apps)
- [Previsão do tempo usando os dados de sensor do seu Hub IoT no Azure Machine Learning](https://docs.microsoft.com/azure/iot-hub/iot-hub-weather-forecast-machine-learning)
- [Gerenciamento de dispositivos com o iothub-explorer](https://docs.microsoft.com/azure/iot-hub/iot-hub-device-management-iothub-explorer)
- [Monitoramento remoto e notificações com Aplicativos Lógicos](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps)
