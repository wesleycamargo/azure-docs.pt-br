---
title: ESP8266 para a nuvem - Conectar o Feather HUZZAH ESP8266 ao Hub IoT do Azure | Microsoft Docs
description: "Um guia para conectar um dispositivo Arduino, o Adafruit Feather HUZZAH ESP8266, ao Hub IoT do Azure, que é um serviço de nuvem da Microsoft que ajuda a gerenciar seus ativos IoT."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: 
ms.assetid: c505aacf-89a8-40ed-a853-493b75bec524
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/15/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: 034725a50d203d28a9fc4b43a5389eac0a232cbe
ms.lasthandoff: 03/17/2017


---
# <a name="connect-adafruit-feather-huzzah-esp8266-to-azure-iot-hub-in-the-cloud"></a>Conectar-se o Adafruit Feather HUZZAH ESP8266 ao Hub IoT do Azure na nuvem

![conexão entre o Hub IoT, o Feather HUZZAH ESP8266 e o DHT22](media/iot-hub-arduino-huzzah-esp8266-get-started/1_connection-hdt22-feather-huzzah-iot-hub.png)

## <a name="what-you-will-do"></a>O que você fará

Conecte o Adafruit Feather HUZZAH ESP8266 a um hub IoT que será criado. Execute um aplicativo de exemplo no ESP8266 para coletar dados de temperatura e umidade de um sensor DHT22. Por fim, envie os dados do sensor para o hub IoT.

> [!NOTE]
> Se você estiver usando outras placas ESP8266, você ainda pode seguir estas etapas para conectar-se ao seu hub IoT. Dependendo da placa ESP8266 que você esteja usando, talvez seja necessário reconfigurar o `LED_PIN`. Por exemplo, se você estiver usando a ESP8266 da Al Thinker, poderá alterá-la de `0` para `2`. Ainda não tem um kit?: Clique [aqui](http://azure.com/iotstarterkits)

## <a name="what-you-will-learn"></a>O que você aprenderá

* Como criar um hub IoT e registrar um dispositivo para o Feather HUZZAH ESP8266.
* Como conectar o Feather HUZZAH ESP8266 ao sensor e seu computador.
* Como coletar dados de sensor executando um aplicativo de exemplo em Feather HUZZAH ESP8266.
* Como enviar os dados do sensor para o hub IoT.

## <a name="what-you-will-need"></a>O que será necessário

![partes necessárias para o tutorial](media/iot-hub-arduino-huzzah-esp8266-get-started/2_parts-needed-for-the-tutorial.png)

Para concluir esta operação, você precisará das seguintes partes do seu Kit de Início do Feather HUZZAH ESP8266:

* A placa Feather HUZZAH ESP8266.
* Um cabo Micro USB para USB Tipo A.

Você também precisa do seguinte para seu ambiente de desenvolvimento:

* Mac ou PC que esteja executando o Windows ou Ubuntu.
* Rede sem fio à qual Feather HUZZAH ESP8266 deve se conectar.
* Uma conexão com a Internet para baixar a ferramenta de configuração.
* [IDE Arduino](https://www.arduino.cc/en/main/software) versão 1.6.8 (ou mais recente), versões anteriores não funcionará com a biblioteca de AzureIoT).

Os itens a seguir são opcionais, caso você não tenha um sensor. Você também tem a opção de usar dados de sensor simulada.

* Um sensor de temperatura e umidade Adafruit DHT22.
* Uma placa universal.
* Cabos de jumper de M/M.

## <a name="create-an-iot-hub-and-register-a-device-for-feather-huzzah-esp8266"></a>Criar um hub IoT e registrar um dispositivo para difusão HUZZAH ESP8266

### <a name="create-your-azure-iot-hub-in-the-azure-portal"></a>Criar seu hub IoT do Azure no portal do Azure

1. Entre no [Portal do Azure](https://portal.azure.com/).
1. Clique em **Novo** > **Internet das Coisas** > **Hub IoT**.

   ![Criar hub iot](media/iot-hub-arduino-huzzah-esp8266-get-started/3_iot-hub-creation.png)

1. No **hub IoT** painel, insira as informações necessárias para o hub IoT:

   ![informações básicas de criação do hub iot](media/iot-hub-arduino-huzzah-esp8266-get-started/4_iot-hub-provide-basic-info.png)

   * **Nome**: O nome para o hub IoT. Se o nome for válido, uma marca de seleção verde é exibida.
   * **Camada de preços e dimensionamento**: selecione a camada livre de F1, será suficiente para esta demonstração. Veja [camada de preços e de escala](https://azure.microsoft.com/pricing/details/iot-hub/).
   * **Grupo de recursos**: criar um grupo de recursos para hospedar o hub IoT ou usar um existente. Veja [Uso dos grupos de recursos para gerenciar seus recursos do Azure](../azure-resource-manager/resource-group-portal.md).
   * **Local**: selecione o local mais próximo a você onde o hub IoT é criado.
   * **Fixe o painel**: marque esta opção para facilitar o acesso ao seu hub IoT do painel.
1. Clique em **Criar**. Pode levar alguns minutos para que o hub IoT seja criado. Você pode ver o progresso no **notificações** painel.

   ![monitorar o andamento da criação do hub iot no painel de notificação](media/iot-hub-arduino-huzzah-esp8266-get-started/5_iot-hub-monitor-creation-progress-notification-pane.png)

1. Depois de criar seu hub IoT, clique no painel. Anote o **Nome do host** a ser usado posteriormente e clique em **Políticas de acesso compartilhado**.

   ![obter nome de host do seu hub IoT](media/iot-hub-arduino-huzzah-esp8266-get-started/6_iot-hub-get-hostname.png)

1. No **políticas de acesso compartilhado** painel, clique o **iothubowner** política e, em seguida, copiar e anote a **cadeia de conexão** do seu hub IoT que é usado posteriormente. Para saber mais, veja [Controlar o acesso ao Hub IoT](iot-hub-devguide-security.md).

   ![obter cadeia de conexão de hub iot](media/iot-hub-arduino-huzzah-esp8266-get-started/7_iot-hub-get-connection-string.png)

Você criou seu Hub IoT. A cadeia de conexão e de nome de host que anotado será usada posteriormente.

### <a name="register-a-device-for-feather-huzzah-esp8266-in-your-iot-hub"></a>Registrar um dispositivo para o Feather HUZZAH ESP8266 em seu hub IoT

Cada Hub IoT tem um registro de identidade que armazena informações sobre os dispositivos com permissão para se conectar ao Hub IoT. Antes de um dispositivo poder se conectar a um Hub IoT, deve existir uma entrada para esse dispositivo no registro de identidade do Hub IoT.

Nesta seção, você usará um Gerenciador de iothub ferramenta CLI para registrar um dispositivo para o Feather HUZZAH ESP8266 no registro de identidade do hub IoT.

> [!NOTE]
> iothub explorer requer o Node. js 4. x ou superior para funcionar corretamente.

Para registrar um dispositivo para Feather HUZZAH ESP8266, siga estas etapas:

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

## <a name="connect-feather-huzzah-esp8266-with-the-sensor-and-your-computer"></a>Difusão HUZZAH ESP8266 de conexão com o sensor e seu computador

### <a name="connect-a-dht22-temperature-and-humidity-sensor-to-feather-huzzah-esp8266"></a>Conecte um sensor de temperatura e umidade DHT22 a Feather HUZZAH ESP8266

Use os cabos da placa universal e jumper para fazer a conexão da seguinte maneira. Se você não tiver um sensor, ignore esta seção porque você pode usar dados de sensor simulado em vez disso.

![referência de conexões](media/iot-hub-arduino-huzzah-esp8266-get-started/15_connections_on_breadboard.png)

Pinos de sensor, usaremos a ligação a seguir:

| Início (Sensor)           | End (quadro)           | Cor de cabo   |
| -----------------------  | ---------------------- | ------------: |
| VDD (Pin 31F)            | 3V (Fixar 58H)           | Cabo vermelho     |
| DADOS de (Pin 32F)           | GPIO 2 (Pin 46A)       | Cabo azul    |
| GND (Pin 34F)            | GND (PIn 56I)          | Cabo preto   |


- Para saber mais, veja: [Instalação do sensor Adafruit DHT22](https://learn.adafruit.com/dht/connecting-to-a-dhtxx-sensor) e [Pinos do Adafruit Feather HUZZAH Esp8266](https://learn.adafruit.com/adafruit-feather-huzzah-esp8266/using-arduino-ide?view=all#pinouts)

Agora seu ESP8266 de Huzzah Adafruit difusão deve estar conectado com um sensor de trabalho.

![conectar dht22 com feather huzzah](media/iot-hub-arduino-huzzah-esp8266-get-started/8_connect-dht22-feather-huzzah.png)

### <a name="connect-feather-huzzah-esp8266-to-your-computer"></a>Conectar o Feather HUZZAH ESP8266 ao seu computador

Use o USB Micro cabo USB de um tipo para conectar Feather HUZZAH ESP8266 em seu computador da seguinte maneira.

![conectar o feather huzzah ao seu computador](media/iot-hub-arduino-huzzah-esp8266-get-started/9_connect-feather-huzzah-computer.png)

### <a name="add-serial-port-permissions--ubuntu-only"></a>Adicionar permissões de porta serial - somente Ubuntu

Se você usar o Ubuntu, certifique-se de que um usuário normal tenha as permissões para operar na porta USB do Feather HUZZAH ESP826. Para adicionar permissões de porta serial para um usuário normal, siga estas etapas:

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

Nesta seção, implante e execute um aplicativo de exemplo em Feather HUZZAH ESP8266. O aplicativo de exemplo pisca o LED de Feather HUZZAH ESP8266 e envia os dados de temperatura e umidade coletados do sensor DHT22 para o hub IoT.

### <a name="get-the-sample-application-from-github"></a>Obter o aplicativo de exemplo do Github

O aplicativo de exemplo está hospedado no Github. Clone o repositório de exemplo que contém o aplicativo de exemplo do GitHub. Para clonar o repositório de exemplo, siga estas etapas:

1. Abra um prompt de comando ou uma janela de terminal.
1. Ir para uma pasta onde você deseja que o aplicativo de exemplo a ser armazenado.
1. Execute o comando a seguir:

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-feather-huzzah-client-app.git
   ```

Instale o pacote para o Feather HUZZAH ESP8266 no IDE Arduino:

1. Abra a pasta onde o aplicativo de exemplo está armazenado.
1. Abra o arquivo app.ino na pasta de aplicativo em Arduino IDE.

   ![abrir o aplicativo de exemplo no ide arduino](media/iot-hub-arduino-huzzah-esp8266-get-started/10_arduino-ide-open-sample-app.png)

1. No IDE Arduino, clique em **arquivo** > **preferências**.
1. No **preferências** caixa de diálogo, clique no ícone ao lado de **URLs adicionais do Gerenciador de placas** caixa de texto.
1. Na janela pop-up, insira a URL a seguir e clique em **OK**.

   `http://arduino.esp8266.com/stable/package_esp8266com_index.json`

   ![aponte para uma url do pacote no ide arduino](media/iot-hub-arduino-huzzah-esp8266-get-started/11_arduino-ide-package-url.png)

1. No **preferência** caixa de diálogo, clique em **OK**.
1. Clique em **Ferramentas** > **Placa** > **Gerenciador de Placas** e procure esp8266.
   ESP8266 com uma versão 2.2.0 ou posterior deve ser instalado.

   ![o pacote de esp8266 está instalado](media/iot-hub-arduino-huzzah-esp8266-get-started/12_arduino-ide-esp8266-installed.png)

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
   ![Configurar o aplicativo de exemplo para usar dados simulados](media/iot-hub-arduino-huzzah-esp8266-get-started/13_arduino-ide-configure-app-use-simulated-data.png)
   
1. Salvar com `Control-s`.

### <a name="deploy-the-sample-application-to-feather-huzzah-esp8266"></a>Implantar o aplicativo de exemplo para Feather HUZZAH ESP8266

1. No IDE Arduino, clique em **Ferramenta** > **Porta** e clique na porta serial para Feather HUZZAH ESP8266.
1. Clique em **esboço** > **carregar** para criar e implantar o aplicativo de exemplo para Feather HUZZAH ESP8266.

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
> As informações de credencial são armazenadas no EEPROM do Feather HUZZAH ESP8266. Se você clicar no botão Redefinir no painel de Feather HUZZAH ESP8266, o aplicativo de exemplo pergunta se você deseja apagar as informações. Digite `Y` para apagar as informações e será solicitado que você forneça as informações novamente.

### <a name="verify-the-sample-application-is-running-successfully"></a>Verificar se o aplicativo de exemplo está sendo executado com êxito

Se você vir a seguinte saída da janela serial monitor e o LED piscando no Feather HUZZAH ESP8266, o aplicativo de exemplo está sendo executado com êxito.

![saída final no ide arduino](media/iot-hub-arduino-huzzah-esp8266-get-started/14_arduino-ide-final-output.png)

## <a name="summary"></a>Resumo

Você conectou um ESP8266 HUZZAH de difusão para o hub IoT e enviados os dados do sensor capturado para o hub IoT com êxito.

