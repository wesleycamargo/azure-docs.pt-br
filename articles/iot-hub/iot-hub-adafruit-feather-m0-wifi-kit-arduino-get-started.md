---
title: "M0 para a nuvem – conectar o Feather M0 WiFi ao Hub IoT do Azure | Microsoft Docs"
description: "Explica como conectar um dispositivo Arduino, chamado Adafruit Feather M0 WiFi, ao Hub IoT do Azure, que é um serviço de nuvem da Microsoft que ajuda a gerenciar seus ativos IoT."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: 
ms.assetid: 51befcdb-332b-416f-a6a1-8aabdb67f283
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: e2d44f821635ce9d91b67ecdc0653e2ba9c99b01
ms.lasthandoff: 04/13/2017


---

# <a name="connect-adafruit-feather-m0-wifi-to-azure-iot-hub-in-the-cloud"></a>Conectar o Adafruit Feather M0 WiFi ao Hub IoT do Azure na nuvem
[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

![Conexão entre o BME280, o Feather M0 WiFi e o Hub IoT](media/iot-hub-adafruit-feather-m0-wifi-get-started/1_connection-m0-feather-m0-iot-hub.png)

Neste tutorial, você começa aprendendo as noções básicas de como trabalhar com sua placa Arduino. Em seguida, aprenderá a conectar seus dispositivos diretamente à nuvem usando o [Hub IoT do Azure](iot-hub-what-is-iot-hub.md).

## <a name="what-you-do"></a>O que fazer

Conecte o Adafruit Feather M0 WiFi a um Hub IoT criado por você. Em seguida, você executa um aplicativo de exemplo no M0 WiFi para coletar dados de temperatura e umidade de um BME280. Por fim, você envia os dados do sensor para o Hub IoT.



## <a name="what-you-learn"></a>O que você aprenderá

* Como criar um Hub IoT e registrar um dispositivo para o Feather M0 WiFi
* Como conectar o Feather M0 WiFi ao sensor e ao seu computador
* Como coletar dados de sensor executando um aplicativo de exemplo no Feather M0 WiFi
* Como enviar os dados do sensor para o Hub IoT

## <a name="what-you-need"></a>O que você precisa

![Partes necessárias para o tutorial](media/iot-hub-adafruit-feather-m0-wifi-get-started/2_parts-needed-for-the-tutorial.png)

Para concluir esta operação, você precisará das seguintes partes do seu Kit de Início do Feather M0 WiFi:

* A placa Feather M0 WiFi
* Um cabo Micro USB para USB Tipo A

Você também precisa destes itens para seu ambiente de desenvolvimento:

* Mac ou PC que esteja executando o Windows ou Ubuntu.
* Rede sem fio à qual o Feather M0 WiFi deve se conectar.
* Uma conexão com a Internet para baixar a ferramenta de configuração.
* [IDE do Arduino](https://www.arduino.cc/en/main/software) versão 1.6.8 ou posterior. As versões anteriores não funcionam com a biblioteca do AzureIoT.


Os itens a seguir são opcionais, caso você não tenha um sensor. Você também tem a opção de usar dados de sensor simulada.

* Um sensor de temperatura e umidade BME280
* Uma placa universal
* Cabos de jumper macho/macho

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="connect-feather-m0-wifi-with-the-sensor-and-your-computer"></a>Conectar o Feather M0 WiFi ao sensor e ao seu computador
Nesta seção, você conecta os sensores à sua placa. Em seguida, você conecta o dispositivo ao seu computador para uso posterior.
### <a name="connect-a-dht22-temperature-and-humidity-sensor-to-feather-m0-wifi"></a>Conectar um sensor de temperatura e umidade DHT22 ao Feather M0 WiFi

Use os cabos da placa universal e jumper para fazer a conexão da seguinte maneira. Se você não tiver um sensor, ignore esta seção porque você pode usar dados de sensor simulado em vez disso.

![Referência de conexões](media/iot-hub-adafruit-feather-m0-wifi-get-started/3_connections_on_breadboard.png)


Use a seguinte fiação para os pinos do sensor:


| Início (Sensor)           | End (quadro)            | Cor de cabo   |
| -----------------------  | ---------------------- | ------------: |
| VDD (pino 27A)            | 3V (pino 3A)            | Cabo vermelho     |
| GND (pino 29A)            | GND (pino 6A)           | Cabo preto   |
| SCK (pino 30A)            | SCK (pino 12A)          | Cabo amarelo  |
| SDO (pino 31A)            | MI (pino 14A)           | Cabo branco   |
| SDI (pino 32A)            | M0 (pino 13A)           | Cabo azul    |
| CS (pino 33A)             | GPIO 5 (pino 15J)       | Cabo laranja  |

Para obter mais informações, consulte [Saídas do sensor de umidade, pressão barométrica e temperatura Adafruit BME280](https://learn.adafruit.com/adafruit-bme280-humidity-barometric-pressure-temperature-sensor-breakout/wiring-and-test?view=all) e [Pinagem do Adafruit Feather M0 WiFi](https://learn.adafruit.com/adafruit-feather-m0-wifi-atwinc1500/pinouts).



Agora seu Feather M0 WiFi deve estar conectado a um sensor ativo.

![Conectar o DHT22 com o Feather Huzzah](media/iot-hub-adafruit-feather-m0-wifi-get-started/4_connect-bme280-feather-m0-wifi.png)

### <a name="connect-feather-m0-wifi-to-your-computer"></a>Conectar o Feather M0 WiFi ao seu computador

Conforme mostrado a seguir, use o cabo Micro USB para USB tipo A para conectar o Feather M0 WiFi ao seu computador.

![Conectar o Feather Huzzah ao seu computador](media/iot-hub-adafruit-feather-m0-wifi-get-started/5_connect-feather-m0-wifi-computer.png)

### <a name="add-serial-port-permissions-ubuntu-only"></a>Adicionar permissões de porta serial (somente Ubuntu)

Se você usa o Ubuntu, verifique se tem as permissões para operar na porta USB do Feather M0 WiFi. Para adicionar permissões de porta serial, siga estas etapas:


1. Execute os seguintes comandos em um terminal:

   ```bash
   ls -l /dev/ttyUSB*
   ls -l /dev/ttyACM*
   ```

   Você recebe uma das seguintes saídas:

   * crw-rw---- 1 root uucp xxxxxxxx
   * crw-rw---- 1 root dialout xxxxxxxx

   Na saída, observe que `uucp` ou `dialout` é o nome do proprietário do grupo da porta USB.

1. Adicione o usuário ao grupo, executando o seguinte comando:

   ```bash
   sudo usermod -a -G <group-owner-name> <username>
   ```

   `<group-owner-name>` é o nome de proprietário de grupo que você obteve na etapa anterior. `<username>` é o nome de usuário do Ubuntu.

1. Saia do Ubuntu e entre novamente para que a alteração apareça.

## <a name="collect-sensor-data-and-send-it-to-your-iot-hub"></a>Coletar dados de sensor e enviá-los para o hub IoT

Nesta seção, você implanta e executa um aplicativo de exemplo no Feather M0 WiFi. O aplicativo de exemplo pisca o LED no Feather M0 WiFi e envia os dados de temperatura e umidade coletados do sensor BME280 para o Hub IoT.

### <a name="get-the-sample-application-from-github-and-prepare-arduino-ide"></a>Obter o aplicativo de exemplo no GitHub e preparar o IDE do Arduino

O aplicativo de exemplo está hospedado no GitHub. Clone o repositório de exemplo que contém o aplicativo de exemplo do GitHub. Para clonar o repositório de exemplo, siga estas etapas:

1. Abra um prompt de comando ou uma janela de terminal.
1. Ir para uma pasta onde você deseja que o aplicativo de exemplo a ser armazenado.
1. Execute o comando a seguir:

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-Feather-M0-WiFi-client-app.git
   ```

Instale o pacote para o Feather M0 WiFi no IDE do Arduino:

1. Abra a pasta onde o aplicativo de exemplo está armazenado.
1. Abra o arquivo app.ino na pasta do aplicativo no IDE do Arduino.

   ![Abrir o aplicativo de exemplo no IDE do Arduino](media/iot-hub-adafruit-feather-m0-wifi-get-started/6_arduino-ide-open-sample-app.png)

1. Clique em **Ferramentas** > **Placa** > **Gerenciador de Placas** e, em seguida, instale o `Arduino SAMD Boards` versão `1.6.2` ou posterior 

   O Gerenciador de Placas indica que o `Arduino SAMD Boards` com uma versão `1.6.2` ou posterior está instalado.

   ![O pacote esp8266 está instalado](media/iot-hub-adafruit-feather-m0-wifi-get-started/7_arduino-ide-package-url.png)

1. Clique em **Ferramentas** > **Placa** > **Adafruit M0 WiFi**.

1. Instale os drivers (somente Windows) ao plugar o Feather. É possível que você tenha que instalar um driver. Clique [aqui](https://github.com/adafruit/Adafruit_Windows_Drivers/releases/download/1.1/adafruit_drivers.exe) para baixar o Instalador de Driver.
   Siga as etapas para instalar os drivers que deseja.

### <a name="install-necessary-libraries"></a>Instalar as bibliotecas necessárias

1. No IDE Arduino, clique em **esboço** > **biblioteca incluem** > **gerenciar bibliotecas**.
1. Procure a seguinte biblioteca nomes individualmente. Para cada biblioteca que você localizar, clique em **Instalar**.
   * `Adafruit_WINC1500`
   * `RTCZero`
   * `NTPClient`
   * `AzureIoTHub`
   * `AzureIoTUtility`
   * `AzureIoTProtocol_HTTP`
   * `ArduinoJson`
   * `Adafruit BME280 Library`
   * `Adafruit Unified Sensor`

### <a name="dont-have-a-real-bme280-sensor"></a>Você não tem um sensor BME280?

O aplicativo de exemplo pode simular os dados de temperatura e a umidade, caso você não tenha um sensor BME280. Para configurar o aplicativo de exemplo para usar dados simulados, siga estas etapas:

1. Abra o `config.h` arquivo o `app` pasta.
1. Localize a seguinte linha de código e altere o valor de `false` para `true`:
   ```c
   define SIMULATED_DATA true
   ```
   ![Configurar o aplicativo de exemplo para usar dados simulados](media/iot-hub-adafruit-feather-m0-wifi-get-started/8_arduino-ide-configure-app-use-simulated-data.png)

1. Salve o arquivo com `Control-s`.

### <a name="deploy-the-sample-application-to-feather-m0-wifi"></a>Implantar o aplicativo de exemplo para o Feather M0 WiFi

1. No IDE do Arduino, clique em **Ferramenta** > **Porta** e clique na porta serial do Feather M0 WiFi.
1. Clique em **Esboço** > **Carregar** para criar e implantar o aplicativo de exemplo do Feather M0 WiFi.

### <a name="enter-your-credentials"></a>Inserir suas credenciais

Depois que o upload for concluído com êxito, siga estas etapas para inserir suas credenciais:

1. No IDE Arduino, clique em **ferramentas** > **Serial Monitor**.
1. Na janela monitor serial, observe as duas listas suspensas no canto inferior direito.
1. Selecione **nenhuma final de linha** para obter a lista suspensa à esquerda.
1. Selecione **115200 baud** para obter a lista suspensa à direita.
1. Na caixa de entrada localizada na parte superior da janela do monitor serial, insira as seguintes informações se você for solicitado a fornecê-las e clique em **Enviar**.
   * SSID Wi-Fi
   * Senha de Wi-Fi
   * Cadeia de conexão de dispositivo

> [!Note]
> As informações de credencial são armazenadas na EEPROM do Feather M0 WiFi. Se você clicar no botão de redefinir na placa do Feather M0 WiFi, o aplicativo de exemplo perguntará se você deseja apagar as informações. Digite `Y` para apagar as informações. Você será solicitado a fornecer as informações uma segunda vez.

### <a name="verify-the-sample-application-is-running-successfully"></a>Verificar se o aplicativo de exemplo está sendo executado com êxito

Caso você veja a seguinte saída na janela monitor serial e o LED piscando no Feather M0 WiFi, o aplicativo de exemplo está sendo executado com êxito.

![Saída final no IDE do Arduino](media/iot-hub-adafruit-feather-m0-wifi-get-started/9_arduino-ide-final-output.png)

## <a name="next-steps"></a>Próximas etapas

Você conectou um Feather M0 WiFi ao Hub IoT e enviou os dados capturados pelo sensor para o Hub IoT com êxito. 

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]


