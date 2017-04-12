---
title: "ESP8266 para a nuvem – conectar o Sparkfun ESP8266 Thing Dev ao Hub IoT do Azure | Microsoft Docs"
description: "Um guia para conectar um dispositivo Arduino, o Sparkfun ESP8266 Thing Dev, ao Hub IoT do Azure, que é um serviço de nuvem da Microsoft que ajuda a gerenciar seus ativos IoT."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: 
ms.assetid: 587fe292-9602-45b4-95ee-f39bba10e716
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/15/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: be140e86047cae304bfb5c32c5cdd9135413df82
ms.lasthandoff: 04/12/2017


---
# <a name="connect-sparkfun-esp8266-thing-dev-to-azure-iot-hub-in-the-cloud"></a>Conectar a Sparkfun ESP8266 Thing Dev ao Hub IoT do Azure na nuvem

![conexão entre DHT22, a Thing Dev e o Hub IoT](media/iot-hub-sparkfun-thing-dev-get-started/1_connection-hdt22-thing-dev-iot-hub.png)

## <a name="what-you-will-do"></a>O que você fará

Conecte a Sparkfun ESP8266 Thing Dev a um Hub IoT que você criará. Execute um aplicativo de exemplo no ESP8266 para coletar dados de temperatura e umidade de um sensor DHT22. Por fim, envie os dados do sensor para o hub IoT.

> [!NOTE]
> Se você estiver usando outras placas ESP8266, você ainda pode seguir estas etapas para conectar-se ao seu hub IoT. Dependendo da placa ESP8266 que você esteja usando, talvez seja necessário reconfigurar o `LED_PIN`. Por exemplo, se você estiver usando a ESP8266 da Al Thinker, poderá alterá-la de `0` para `2`. Ainda não tem um kit?: Clique [aqui](http://azure.com/iotstarterkits)

## <a name="what-you-will-learn"></a>O que você aprenderá

* Como criar um Hub IoT e registrar um dispositivo para a Thing Dev.
* Como conectar o Thing Dev ao sensor e seu computador.
* Como coletar dados de sensor executando um aplicativo de exemplo no Thing Dev.
* Como enviar os dados do sensor para o hub IoT.

## <a name="what-you-will-need"></a>O que será necessário

![partes necessárias para o tutorial](media/iot-hub-sparkfun-thing-dev-get-started/2_parts-needed-for-the-tutorial.png)

Para concluir esta operação, você precisará das seguintes partes do seu Kit de início do Thing Dev:

* A placa de desenvolvimento Sparkfun ESP8266 Thing Dev.
* Um cabo Micro USB para USB Tipo A.

Você também precisa do seguinte para seu ambiente de desenvolvimento:

* Mac ou PC que esteja executando o Windows ou Ubuntu.
* Rede sem fio à qual a Sparkfun ESP8266 Thing Dev pode se conectar.
* Uma conexão com a Internet para baixar a ferramenta de configuração.
* [IDE Arduino](https://www.arduino.cc/en/main/software) versão 1.6.8 (ou mais recente), versões anteriores não funcionará com a biblioteca de AzureIoT.

Os itens a seguir são opcionais, caso você não tenha um sensor. Você também tem a opção de usar dados de sensor simulada.

* Um sensor de temperatura e umidade Adafruit DHT22.
* Uma placa universal.
* Cabos de jumper de M/M.

## <a name="create-an-iot-hub-and-register-a-device-for-sparkfun-esp8266-thing-dev"></a>Criar um Hub IoT e registrar um dispositivo para a Sparkfun ESP8266 Thing Dev

### <a name="create-your-azure-iot-hub-in-the-azure-portal"></a>Criar seu hub IoT do Azure no portal do Azure

1. Entre no [Portal do Azure](https://portal.azure.com/).
1. Clique em **Novo** > **Internet das Coisas** > **Hub IoT**.

   ![Criar hub iot](media/iot-hub-sparkfun-thing-dev-get-started/3_iot-hub-creation.png)

1. No **hub IoT** painel, insira as informações necessárias para o hub IoT:

   ![informações básicas de criação do hub iot](media/iot-hub-sparkfun-thing-dev-get-started/4_iot-hub-provide-basic-info.png)

   * **Nome**: O nome para o hub IoT. Se o nome for válido, uma marca de seleção verde é exibida.
   * **Camada de preços e dimensionamento**: selecione a camada livre de F1, será suficiente para esta demonstração. Veja [camada de preços e de escala](https://azure.microsoft.com/pricing/details/iot-hub/).
   * **Grupo de recursos**: criar um grupo de recursos para hospedar o hub IoT ou usar um existente. Veja [Uso dos grupos de recursos para gerenciar seus recursos do Azure](../azure-resource-manager/resource-group-portal.md).
   * **Local**: selecione o local mais próximo a você onde o hub IoT é criado.
   * **Fixe o painel**: marque esta opção para facilitar o acesso ao seu hub IoT do painel.
1. Clique em **Criar**. Pode levar alguns minutos para que o hub IoT seja criado. Você pode ver o progresso no **notificações** painel.

   ![monitorar o andamento da criação do hub iot no painel de notificação](media/iot-hub-sparkfun-thing-dev-get-started/5_iot-hub-monitor-creation-progress-notification-pane.png)

1. Depois de criar seu hub IoT, clique no painel. Anote o **Nome do host** a ser usado posteriormente e clique em **Políticas de acesso compartilhado**.

   ![obter nome de host do seu hub IoT](media/iot-hub-sparkfun-thing-dev-get-started/6_iot-hub-get-hostname.png)

1. No **políticas de acesso compartilhado** painel, clique o **iothubowner** política e, em seguida, copiar e anote a **cadeia de conexão** do seu hub IoT que é usado posteriormente. Para saber mais, veja [Controlar o acesso ao Hub IoT](iot-hub-devguide-security.md).

   ![obter cadeia de conexão de hub iot](media/iot-hub-sparkfun-thing-dev-get-started/7_iot-hub-get-connection-string.png)

Você criou seu Hub IoT. A cadeia de conexão e de nome de host que anotado será usada posteriormente.

### <a name="register-a-device-for-sparkfun-esp8266-thing-dev-in-your-iot-hub"></a>Registrar um dispositivo para a Sparkfun ESP8266 Thing Dev em seu Hub IoT

Cada Hub IoT tem um registro de identidade que armazena informações sobre os dispositivos com permissão para se conectar ao Hub IoT. Antes de um dispositivo poder se conectar a um Hub IoT, deve existir uma entrada para esse dispositivo no registro de identidade do Hub IoT.

Nesta seção, você usará um Gerenciador de iothub ferramenta CLI para registrar um dispositivo para a ESP8266 Thing Dev no Registro de identidade do Hub IoT.

> [!NOTE]
> iothub explorer requer o Node. js 4. x ou superior para funcionar corretamente.

Para registrar um dispositivo para a ESP8266 Thing Dev, siga estas etapas:

1. [Baixe](https://nodejs.org/en/download/) e instalar a versão mais recente do LTS do Node.js, NPM incluído.
1. Instale o iothub explorer usando o NPM.

   * Windows 7 ou posterior Inicie um prompt de comando como administrador. Instale o explorer iothub executando o seguinte comando:

     ```bash
     npm install -g iothub-explorer
     ```
   * Ubuntu 16.04 ou posterior para abrir um terminal usando o atalho de teclado Ctrl + Alt + T e execute o seguinte comando:

     ```bash
     sudo npm install -g iothub-explorer
     ```
   * macOS 10.1 ou posteriores abra um terminal e, em seguida, execute o seguinte comando:

     ```bash
     npm install -g iothub-explorer
     ```
1. Faça logon no hub IoT executando o comando a seguir:

   ```bash
   iothub-explorer login [your iot hub connection string]
   ```
1. Registrar um novo dispositivo, que `deviceID` é `new-device`e obter sua cadeia de conexão executando o comando a seguir.

   ```bash
   iothub-explorer create new-device --connection-string
   ```

Anote a cadeia de conexão do dispositivo registrado, ele será usado posteriormente.

## <a name="connect-esp8266-thing-dev-with-the-sensor-and-your-computer"></a>Conecte a ESP8266 Thing Dev ao sensor e seu computador

### <a name="connect-a-dht22-temperature-and-humidity-sensor-to-esp8266-thing-dev"></a>Conecte um sensor de temperatura e umidade DHT22 a ESP8266 Thing Dev

Use os cabos da placa universal e jumper para fazer a conexão da seguinte maneira. Se você não tiver um sensor, ignore esta seção porque você pode usar dados de sensor simulado em vez disso.

![referência de conexões](media/iot-hub-sparkfun-thing-dev-get-started/15_connections_on_breadboard.png)

Pinos de sensor, usaremos a ligação a seguir:

| Início (Sensor)           | End (quadro)           | Cor de cabo   |
| -----------------------  | ---------------------- | ------------: |
| VDD (Pin 27F)            | 3V (Pin 8A)           | Cabo vermelho     |
| DATA (Pin 28F)           | GPIO 2 (Pin 9A)       | Cabo branco    |
| GND (Pin 30F)            | GND (Pin 7J)          | Cabo preto   |


- Para obter mais informações, consulte: [Instalação do sensor DHT22](http://cdn.sparkfun.com/datasheets/Sensors/Weather/RHT03.pdf) e [Especificação da Sparkfun ESP8266 Thing Dev](https://www.sparkfun.com/products/13711)

Agora sua Sparkfun ESP8266 Thing Dev deve estar conectada a um sensor ativo.

![conectar o dht22 à ESP8266 Thing Dev](media/iot-hub-sparkfun-thing-dev-get-started/8_connect-dht22-thing-dev.png)

### <a name="connect-sparkfun-esp8266-thing-dev-to-your-computer"></a>Conectar a Sparkfun ESP8266 Thing Dev ao computador

Use o cabo Micro USB para USB tipo A para conectar a Sparkfun ESP8266 Thing Dev a seu computador conforme descrito a seguir.

![conectar o feather huzzah ao seu computador](media/iot-hub-sparkfun-thing-dev-get-started/9_connect-thing-dev-computer.png)

### <a name="add-serial-port-permissions--ubuntu-only"></a>Adicionar permissões de porta serial - somente Ubuntu

Se você usar o Ubuntu, certifique-se de que um usuário normal tenha as permissões para operar na porta USB da Sparkfun ESP8266 Thing Dev. Para adicionar permissões de porta serial para um usuário normal, siga estas etapas:

1. Execute os seguintes comandos em um terminal:

   ```bash
   ls -l /dev/ttyUSB*
   ls -l /dev/ttyACM*
   ```

   Você recebe uma das seguintes saídas:

   * crw-rw---- 1 root uucp xxxxxxxx
   * crw-rw---- 1 root dialout xxxxxxxx

   Na saída, observe `uucp` ou `dialout` que é o nome do proprietário do grupo da porta USB.

1. Adicione o usuário ao grupo, executando o seguinte comando:

   ```bash
   sudo usermod -a -G <group-owner-name> <username>
   ```

   `<group-owner-name>` é o nome de proprietário de grupo que você obteve na etapa anterior. `<username>` é o nome de usuário do Ubuntu.

1. Faça logoff do Ubuntu e faça logon novamente para que a alteração entre em vigor.

## <a name="collect-sensor-data-and-send-it-to-your-iot-hub"></a>Coletar dados de sensor e enviá-los para o hub IoT

Nesta seção, implante e execute um aplicativo de exemplo em Sparkfun ESP8266 Thing Dev. O aplicativo de exemplo pisca o LED da Sparkfun ESP8266 Thing Dev e envia os dados de temperatura e umidade coletados do sensor DHT22 para o Hub IoT.

### <a name="get-the-sample-application-from-github"></a>Obter o aplicativo de exemplo do GitHub

O aplicativo de exemplo está hospedado no GitHub. Clone o repositório de exemplo que contém o aplicativo de exemplo do GitHub. Para clonar o repositório de exemplo, siga estas etapas:

1. Abra um prompt de comando ou uma janela de terminal.
1. Ir para uma pasta onde você deseja que o aplicativo de exemplo a ser armazenado.
1. Execute o comando a seguir:

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-SparkFun-ThingDev-client-app.git
   ```

Instale o pacote para a Sparkfun ESP8266 Thing Dev no IDE Arduino:

1. Abra a pasta onde o aplicativo de exemplo está armazenado.
1. Abra o arquivo app.ino na pasta de aplicativo em Arduino IDE.

   ![abrir o aplicativo de exemplo no ide arduino](media/iot-hub-sparkfun-thing-dev-get-started/10_arduino-ide-open-sample-app.png)

1. No IDE Arduino, clique em **arquivo** > **preferências**.
1. No **preferências** caixa de diálogo, clique no ícone ao lado de **URLs adicionais do Gerenciador de placas** caixa de texto.
1. Na janela pop-up, insira a URL a seguir e clique em **OK**.

   `http://arduino.esp8266.com/stable/package_esp8266com_index.json`

   ![aponte para uma url do pacote no ide arduino](media/iot-hub-sparkfun-thing-dev-get-started/11_arduino-ide-package-url.png)

1. No **preferência** caixa de diálogo, clique em **OK**.
1. Clique em **Ferramentas** > **Placa** > **Gerenciador de Placas** e procure esp8266.
   ESP8266 com uma versão 2.2.0 ou posterior deve ser instalado.

   ![o pacote de esp8266 está instalado](media/iot-hub-sparkfun-thing-dev-get-started/12_arduino-ide-esp8266-installed.png)

1. Clique em **ferramentas** > **placa** > **Adafruit HUZZAH ESP8266**.

### <a name="install-necessary-libraries"></a>Instalar as bibliotecas necessárias

1. No IDE Arduino, clique em **esboço** > **biblioteca incluem** > **gerenciar bibliotecas**.
1. Procure a seguinte biblioteca nomes individualmente. Para cada biblioteca que você localizar, clique em **instalar**.
   * `AzureIoTHub`
   * `AzureIoTUtility`
   * `AzureIoTProtocol_MQTT`
   * `ArduinoJson`
   * `DHT sensor library`
   * `Adafruit Unified Sensor`

### <a name="dont-have-a-real-dht22-sensor"></a>Você não tem um sensor DHT22 real?

O aplicativo de exemplo pode simular a temperatura e umidade dados caso você não tenha um sensor DHT22 real. Para habilitar o aplicativo de exemplo usar dados simulados, siga estas etapas:

1. Abra o `config.h` arquivo o `app` pasta.
1. Localize a seguinte linha de código e altere o valor de `false` para `true`:
   ```c
   define SIMULATED_DATA true
   ```
   ![Configurar o aplicativo de exemplo para usar dados simulados](media/iot-hub-sparkfun-thing-dev-get-started/13_arduino-ide-configure-app-use-simulated-data.png)
   
1. Salvar com `Control-s`.

### <a name="deploy-the-sample-application-to-sparkfun-esp8266-thing-dev"></a>Implantar o aplicativo de exemplo para a Sparkfun ESP8266 Thing Dev

1. No IDE Arduino, clique em **Ferramenta** > **Porta** e clique na porta serial para a Sparkfun ESP8266 Thing Dev.
1. Clique em **Esboço** > **Upload** para criar e implantar o aplicativo de exemplo para a Sparkfun ESP8266 Thing Dev.

### <a name="enter-your-credentials"></a>Inserir suas credenciais

Depois que o carregamento for concluído com êxito, siga as etapas para inserir suas credenciais:

1. No IDE Arduino, clique em **ferramentas** > **Serial Monitor**.
1. Na janela monitor serial, observe as duas listas suspensas no canto inferior direito.
1. Selecione **nenhuma final de linha** para obter a lista suspensa à esquerda.
1. Selecione **115200 baud** para obter a lista suspensa à direita.
1. Na caixa de entrada localizada na parte superior da janela do monitor serial, insira as seguintes informações se você for solicitado a fornecê-las e clique em **Enviar**.
   * SSID Wi-Fi
   * Senha de Wi-Fi
   * Cadeia de conexão de dispositivo

> [!Note]
> As informações de credencial são armazenadas no EEPROM da Sparkfun ESP8266 Thing Dev. Se você clicar no botão Redefinir no painel da Sparkfun ESP8266 Thing Dev, o aplicativo de exemplo pergunta se você deseja apagar as informações. Digite `Y` para apagar as informações e será solicitado que você forneça as informações novamente.

### <a name="verify-the-sample-application-is-running-successfully"></a>Verificar se o aplicativo de exemplo está sendo executado com êxito

Se você vir a seguinte saída da janela serial monitor e o LED piscando na Sparkfun ESP8266 Thing Dev, o aplicativo de exemplo está sendo executado com êxito.

![saída final no ide arduino](media/iot-hub-sparkfun-thing-dev-get-started/14_arduino-ide-final-output.png)

## <a name="next-steps"></a>Próximas etapas

Você conectou uma Sparkfun ESP8266 Thing Dev ao Hub IoT e enviou os dados capturados pelo sensor ao Hub IoT com êxito. 

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

