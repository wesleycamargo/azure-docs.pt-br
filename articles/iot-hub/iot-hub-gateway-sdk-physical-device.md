---
title: "Usar um dispositivo físico com o SDK do Gateway IoT do Azure | Microsoft Docs"
description: "Como usar um dispositivo Texas Instruments SensorTag para enviar dados para um Hub IoT por meio de um gateway que executa um dispositivo Raspberry Pi 3. O gateway é criado usando o SDK do Gateway IoT do Azure."
services: iot-hub
documentationcenter: 
author: chipalost
manager: timlt
editor: 
ms.assetid: 212dacbf-e5e9-48b2-9c8a-1c14d9e7b913
ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2016
ms.author: andbuc
translationtype: Human Translation
ms.sourcegitcommit: e1cf5ed3f2434a9e98027afd0225207ad5d2f1b1
ms.openlocfilehash: 199f07ce705036c3d8f9d56115b5df9c8c52dc45


---
# <a name="use-the-azure-iot-gateway-sdk-to-send-device-to-cloud-messages-with-a-physical-device-linux"></a>Use o SDK do Gateway IoT do Azure para enviar mensagens do dispositivo para a nuvem com um dispositivo físico (Linux)
Este passo a passo do [exemplo de Bluetooth de baixa energia][lnk-ble-samplecode] mostra como usar o [SDK do Gateway do Azure IoT][lnk-sdk] para encaminhar a telemetria do dispositivo para a nuvem para o Hub IoT de um dispositivo físico e como rotear comandos do Hub IoT para um dispositivo físico.

Este passo a passo aborda:

* **Arquitetura**: informações importantes de arquitetura sobre o exemplo de Bluetooth de baixa energia.
* **Criar e executar**: as etapas necessárias para criar e executar a amostra.

## <a name="architecture"></a>Arquitetura
O passo a passo mostra como criar e executar um gateway de IoT em um Raspberry Pi 3 que executa o Raspbian Linux. O gateway é criado usando o SDK de gateway do IoT. O exemplo usa um dispositivo BLE (Bluetooth de baixa energia) Texas Instruments SensorTag para coletar dados de temperatura.

Quando você executa o gateway, ele:

* Conecta-se a um dispositivo SensorTag usando o protocolo de BLE (Bluetooth de baixa energia).
* Conecta-se ao Hub IoT usando o protocolo HTTP.
* Encaminha a telemetria do dispositivo SensorTag ao Hub IoT.
* Roteia comandos do Hub IoT para o dispositivo SensorTag.

O gateway contém os seguintes módulos:

* Um *módulo BLE* que interage com um dispositivo BLE para receber dados de temperatura do dispositivo e envia comandos para o dispositivo.
* Um *Nuvem BLE para o módulo de dispositivo* que traduz as mensagens JSON provenientes da nuvem em instruções BLE para o *módulo BLE*.
* Um *módulo de agente* que registra todas as mensagens do gateway em um arquivo local.
* Um *módulo de mapeamento de identidade* que faz a conversão entre endereços MAC dos dispositivos BLE e identidades de dispositivos do Hub IoT do Azure.
* Um *módulo do Hub IoT* que carrega dados de telemetria para um Hub IoT e recebe comandos de dispositivo de um Hub IoT.
* Um *módulo de impressora BLE* que interpreta a telemetria do dispositivo BLE e imprime dados formatados para o console para habilitar a solução de problemas e depuração.

### <a name="how-data-flows-through-the-gateway"></a>Como os dados fluem pelo gateway
O diagrama de bloco a seguir ilustra o pipeline do fluxo de dados de upload de telemetria:

![](media/iot-hub-gateway-sdk-physical-device/gateway_ble_upload_data_flow.png)

As etapas que um item de telemetria realiza ao viajar de um dispositivo BLE para o Hub IoT são:

1. O dispositivo BLE gera uma amostra de temperatura e a envia por Bluetooth para o módulo BLE no gateway.
2. O módulo BLE recebe a amostra e a publica para o agente com o endereço MAC do dispositivo.
3. O módulo de mapeamento de identidade capta essa mensagem e usa uma tabela interna para converter o endereço MAC do dispositivo em uma identidade de dispositivo do Hub IoT (uma ID de dispositivo e uma chave do dispositivo). Em seguida, ele publica uma nova mensagem que contém os dados de exemplo de temperatura, o endereço MAC, a ID e a chave do dispositivo.
4. O módulo de Hub IoT recebe essa nova mensagem (gerada pelo módulo de mapeamento de identidade) e a publica no Hub IoT.
5. O módulo de agente registra todas as mensagens do agente em um arquivo local.

O diagrama de bloco a seguir ilustra o pipeline do fluxo de dados de comando do dispositivo:

![](media/iot-hub-gateway-sdk-physical-device/gateway_ble_command_data_flow.png)

1. O módulo de Hub IoT periodicamente sonda o Hub IoT quanto a novas mensagens de comando.
2. Quando o módulo de Hub IoT recebe uma nova mensagem de comando, ele a publica no agente.
3. O módulo de mapeamento de identidade capta a mensagem de comando e usa uma tabela interna para converter a ID do dispositivo Hub IoT para um endereço MAC do dispositivo. Em seguida, ele publica uma nova mensagem que inclui o endereço MAC do dispositivo de destino no mapa de propriedades da mensagem.
4. O módulo Nuvem para dispositivo BLE pega essa mensagem e a converte na instrução BLE adequada para o módulo BLE. Em seguida, ele publica uma nova mensagem.
5. O módulo BLE capta essa mensagem e executa a instrução de E/S se comunicando com o dispositivo BLE.
6. O módulo de agente registra todas as mensagens do agente em um arquivo de disco.

## <a name="prepare-your-hardware"></a>Prepare seu hardware
Este tutorial presume que você esteja usando um dispositivo [Texas Instruments SensorTag](http://www.ti.com/ww/en/wireless_connectivity/sensortag2015/index.html) conectado a um Raspberry Pi 3 que executa o Raspbian.

### <a name="install-raspbian"></a>Instalar Raspbian
Você pode usar uma das opções a seguir para instalar o Raspbian em seu dispositivo Raspberry Pi 3. 

* Use [NOOBS][lnk-noobs], uma interface gráfica do usuário, para instalar a versão mais recente do Raspbian. 
* [Baixe][lnk-raspbian] e grave a imagem mais recente do sistema operacional Raspbian em cartão SD. 

### <a name="install-bluez-537"></a>Instalar o BlueZ 5.37
Os módulos BLE se comunicar com o hardware de Bluetooth usando a pilha de BlueZ. Você precisa ter a versão 5.37 do BlueZ para que os módulos funcionem corretamente. Essas instruções fazem com que a versão correta do BlueZ esteja instalada.

1. Pare o daemon do bluetooth atual:
   
    ```
    sudo systemctl stop bluetooth
    ```
2. Instale as dependências de BlueZ. 
   
    ```
    sudo apt-get update
    sudo apt-get install bluetooth bluez-tools build-essential autoconf glib2.0 libglib2.0-dev libdbus-1-dev libudev-dev libical-dev libreadline-dev
    ```
3. Baixe o código-fonte do BlueZ do bluez.org. 
   
    ```
    wget http://www.kernel.org/pub/linux/bluetooth/bluez-5.37.tar.xz
    ```
4. Descompacte o código-fonte.
   
    ```
    tar -xvf bluez-5.37.tar.xz
    ```
5. Altere os diretórios para a pasta recém-criada.
   
    ```
    cd bluez-5.37
    ```
6. Configure o código BlueZ a ser criado.
   
    ```
    ./configure --disable-udev --disable-systemd --enable-experimental
    ```
7. Crie o BlueZ.
   
    ```
    make
    ```
8. Instale BlueZ depois que ele terminar de criar.
   
    ```
    sudo make install
    ```
9. Altere a configuração de serviço systemd para o bluetooth a fim de que ele aponte para o novo daemon de bluetooth no arquivo `/lib/systemd/system/bluetooth.service`. Substitua a linha 'ExecStart' pelo seguinte texto: 
    
    ```
    ExecStart=/usr/local/libexec/bluetooth/bluetoothd -E
    ```

### <a name="enable-connectivity-to-the-sensortag-device-from-your-raspberry-pi-3-device"></a>Habilitar conectividade para o dispositivo SensorTag de seu dispositivo Raspberry Pi 3
Antes de executar o exemplo, você precisa verificar se seu Raspberry Pi 3 pode se conectar ao dispositivo SensorTag.


1. Verifique se o utilitário `rfkill` está instalado.
   
    ```
    sudo apt-get install rfkill
    ```
2. Desbloqueie o Bluetooth no Raspberry Pi 3 e verifique se o número de versão é **5.37**.
   
    ```
    sudo rfkill unblock bluetooth
    bluetoothctl --version
    ```
3. Inicie o serviço de bluetooth e execute o comando **bluetoothctl** para inserir um shell de bluetooth interativo. 
   
    ```
    sudo systemctl start bluetooth
    bluetoothctl
    ```
4. Digite o comando **power on** para ligar o controlador bluetooth. Você deve ver uma saída semelhante a:
   
    ```
    [NEW] Controller 98:4F:EE:04:1F:DF C3 raspberrypi [default]
    ```
5. Ainda no shell interativo bluetooth, digite o comando **scan on** para verificar se há dispositivos bluetooth. Você deve ver uma saída semelhante a:
   
    ```
    Discovery started
    [CHG] Controller 98:4F:EE:04:1F:DF Discovering: yes
    ```
6. Torne o dispositivo SensorTag detectável pressionando o botão pequeno (o LED verde deve piscar). O Raspberry Pi 3 deve detectar o dispositivo SensorTag:
   
    ```
    [NEW] Device A0:E6:F8:B5:F6:00 CC2650 SensorTag
    [CHG] Device A0:E6:F8:B5:F6:00 TxPower: 0
    [CHG] Device A0:E6:F8:B5:F6:00 RSSI: -43
    ```
   
    Neste exemplo, você pode ver que é o endereço MAC do dispositivo SensorTag é **A0:E6:F8:B5:F6:00**.
7. Desative a verificação inserindo o comando **scan off** .
   
    ```
    [CHG] Controller 98:4F:EE:04:1F:DF Discovering: no
    Discovery stopped
    ```
8. Conecte-se ao dispositivo SensorTag usando seu endereço MAC inserindo **connect \<endereço MAC>**. Observe que a saída de exemplo abaixo está abreviada:
   
    ```
    Attempting to connect to A0:E6:F8:B5:F6:00
    [CHG] Device A0:E6:F8:B5:F6:00 Connected: yes
    Connection successful
    [CHG] Device A0:E6:F8:B5:F6:00 UUIDs: 00001800-0000-1000-8000-00805f9b34fb
    ...
    [NEW] Primary Service
            /org/bluez/hci0/dev_A0_E6_F8_B5_F6_00/service000c
            Device Information
    ...
    [CHG] Device A0:E6:F8:B5:F6:00 GattServices: /org/bluez/hci0/dev_A0_E6_F8_B5_F6_00/service000c
    ...
    [CHG] Device A0:E6:F8:B5:F6:00 Name: SensorTag 2.0
    [CHG] Device A0:E6:F8:B5:F6:00 Alias: SensorTag 2.0
    [CHG] Device A0:E6:F8:B5:F6:00 Modalias: bluetooth:v000Dp0000d0110
    ```
   
    > Observe que você pode listar as características do GATT do dispositivo novamente usando o comando **list-attributes** .
9. Agora você pode se desconectar do dispositivo usando o comando **disconnect** e sair do shell Bluetooth usando o comando **quit**:
   
    ```
    Attempting to disconnect from A0:E6:F8:B5:F6:00
    Successful disconnected
    [CHG] Device A0:E6:F8:B5:F6:00 Connected: no
    ```

Agora você está pronto para executar o exemplo de gateway de BLE no Raspberry Pi 3.

## <a name="run-the-ble-gateway-sample"></a>Execute o exemplo de gateway de BLE
Para executar o exemplo de BLE, você precisará concluir três tarefas:

* Configurar dois dispositivos de exemplo em seu Hub IoT.
* Crie o SDK do gateway de IoT em seu dispositivo Raspberry Pi 3.
* Configure e execute o exemplo de BLE no dispositivo Raspberry Pi 3.

No momento da redação desse artigo, o SDK do Gateway do IoT dá suporte apenas para gateways que usam módulos de BLE no Linux.

### <a name="configure-two-sample-devices-in-your-iot-hub"></a>Configurar dois dispositivos de exemplo em seu Hub IoT
* [Crie um Hub IoT][lnk-create-hub] em sua assinatura do Azure. Você precisará do nome do hub para concluir este passo a passo. Se não tiver uma conta, você poderá criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.
* Adicione um dispositivo chamado **SensorTag_01** ao hub IoT e anote sua ID e chave de dispositivo. Você pode usar as ferramentas do [iothub-explorer ou o Gerenciador de Dispositivos][lnk-explorer-tools] para adicionar esse dispositivo ao Hub IoT que criou na etapa anterior e para recuperar sua chave. Você mapeará este dispositivo para o dispositivo SensorTag quando configurar o gateway.

### <a name="build-the-azure-iot-gateway-sdk-on-your-raspberry-pi-3"></a>Criar o gateway de IoT do SDK do Azure em seu Raspberry Pi 3

Instale as dependências do SDK do gateway de IoT do Azure.

``` 
sudo apt-get install cmake uuid-dev curl libcurl4-openssl-dev libssl-dev
```
Use os seguintes comandos para clonar o SDK do Gateway do IoT e todos os seus submódulos em seu diretório inicial:

```
cd ~
git clone --recursive https://github.com/Azure/azure-iot-gateway-sdk.git 
cd azure-iot-gateway-sdk
git submodule update --init --recursive
```

Quando você tem uma cópia completa do repositório do SDK de Gateway do IoT em seu Raspberry Pi 3, pode compilá-lo usando o comando abaixo na pasta que contém o SDK

```
./tools/build.sh
```

### <a name="configure-and-run-the-ble-sample-on-your-raspberry-pi-3"></a>Configurar e execute o exemplo de BLE em seu Raspberry Pi 3
Para inicializar e executar o exemplo, você precisa configurar cada módulo que participa no gateway. Essa configuração é fornecida em um arquivo JSON e você precisa configurar todos os cinco módulos participantes. Há um arquivo JSON de exemplo fornecido no repositório chamado **gateway_sample.json**, que pode ser usado como ponto de partida para criar seu próprio arquivo de configuração. Esse arquivo está na pasta **samples/ble_gateway/src** na cópia local do repositório do SDK do Gateway do IoT.

As seções a seguir descrevem como editar esse arquivo de configuração para o exemplo de BLE e pressupõem que o repositório do SDK do Gateway do IoT está na pasta **/home/pi/azure-iot-gateway-sdk/** de seu Raspberry Pi 3. Se o repositório estiver em outro lugar, você deverá ajustar os caminhos adequadamente:

#### <a name="logger-configuration"></a>Configuração do agente
Pressupondo que o repositório de gateway esteja localizado na pasta **/home/pi/azure-iot-gateway-sdk/**, configure o módulo de agente da seguinte maneira:

```json
{
  "name": "Logger",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path" : "build/modules/logger/liblogger.so"
    }
  },
  "args":
  {
    "filename": "<</path/to/log-file.log>>"
  }
}
```

#### <a name="ble-module-configuration"></a>Configuração do módulo BLE
A configuração de exemplo do dispositivo BLE pressupõe um dispositivo Texas Instruments SensorTag. Qualquer dispositivo BLE padrão que pode operar como um GATT periférico deve funcionar, mas você precisará atualizar as IDs de característica GATT e os dados (para instruções de gravação). Adicione o endereço MAC do dispositivo SensorTag: 

```json
{
  "name": "SensorTag",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path": "build/modules/ble/libble.so"
    }
  },
  "args": {
    "controller_index": 0,
    "device_mac_address": "<<AA:BB:CC:DD:EE:FF>>",
    "instructions": [
      {
        "type": "read_once",
        "characteristic_uuid": "00002A24-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A25-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A26-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A27-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A28-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "read_once",
        "characteristic_uuid": "00002A29-0000-1000-8000-00805F9B34FB"
      },
      {
        "type": "write_at_init",
        "characteristic_uuid": "F000AA02-0451-4000-B000-000000000000",
        "data": "AQ=="
      },
      {
        "type": "read_periodic",
        "characteristic_uuid": "F000AA01-0451-4000-B000-000000000000",
        "interval_in_ms": 1000
      },
      {
        "type": "write_at_exit",
        "characteristic_uuid": "F000AA02-0451-4000-B000-000000000000",
        "data": "AA=="
      }
    ]
  }
}
```

#### <a name="iot-hub-module"></a>módulo do Hub IoT
Adicione o nome do Hub IoT. O valor do sufixo é geralmente **azure-devices.net**:

```json
{
  "name": "IoTHub",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path": "build/modules/iothub/libiothub.so"
    }
  },
  "args": {
    "IoTHubName": "<<Azure IoT Hub Name>>",
    "IoTHubSuffix": "<<Azure IoT Hub Suffix>>",
    "Transport" : "amqp"
  }
}
```

#### <a name="identity-mapping-module-configuration"></a>Configuração do módulo de mapeamento de identidade
Adicione o endereço MAC do dispositivo SensorTag e a ID e a chave do dispositivo **SensorTag_01** adicionado ao seu Hub IoT:

```json
{
  "name": "mapping",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path": "build/modules/identitymap/libidentity_map.so"
    }
  },
  "args": [
    {
      "macAddress": "AA:BB:CC:DD:EE:FF",
      "deviceId": "<<Azure IoT Hub Device ID>>",
      "deviceKey": "<<Azure IoT Hub Device Key>>"
    }
  ]
}
```

#### <a name="ble-printer-module-configuration"></a>Configuração do módulo de impressora BLE
```json
{
  "name": "BLE Printer",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path": "build/samples/ble_gateway/ble_printer/libble_printer.so"
    }
  },
  "args": null
}
```

#### <a name="blec2d-module-configuration"></a>Configuração do módulo BLEC2D
```json
{
  "name": "BLEC2D",
  "loader": {
    "name" : "native",
    "entrypoint" : {
      "module.path": "build/modules/ble/libble_c2d.so"
    }
  },
  "args": null
}
```

#### <a name="routing-configuration"></a>Configuração de roteamento
A configuração a seguir garante o seguinte:

* O módulo **Agente** recebe e registra todas as mensagens.
* O módulo **SensorTag** envia mensagens para os módulos **mapeamento** e **Impressora BLE**.
* O módulo **mapeamento** envia mensagens ao módulo **IoTHub** a ser enviado ao seu Hub IoT.
* O módulo **IoTHub** envia as mensagens de volta para o módulo **mapeamento**.
* O módulo **mapeamento** envia mensagens para o módulo **BLEC2D**.
* O módulo **BLEC2D** envia as mensagens de volta para o módulo **SensorTag**.

```json
"links" : [
    {"source" : "*", "sink" : "Logger" },
    {"source" : "SensorTag", "sink" : "mapping" },
    {"source" : "SensorTag", "sink" : "BLE Printer" },
    {"source" : "mapping", "sink" : "IoTHub" },
    {"source" : "IoTHub", "sink" : "mapping" },
    {"source" : "mapping", "sink" : "BLEC2D" },
    {"source" : "BLEC2D", "sink" : "SensorTag"}
 ]
```

Para executar o exemplo, passe o caminho até o arquivo de configuração JSON para o binário **ble_gateway**. Se você usou o arquivo **gateway_sample.json**, o comando pode ser encontrado abaixo. Execute este comando no diretório azure-iot-gateway-sdk

```
./build/samples/ble_gateway/ble_gateway ./samples/ble_gateway/src/gateway_sample.json
```

Talvez seja necessário pressionar o botão pequeno no dispositivo SensorTag para torná-lo detectável antes de executar o exemplo.

Ao executar o exemplo, você pode usar a ferramenta do [iothub-explorer ou o Gerenciador de Dispositivos][lnk-explorer-tools] para monitorar as mensagens que o gateway encaminha para o dispositivo SensorTag.

## <a name="send-cloud-to-device-messages"></a>Envie mensagens da nuvem para o dispositivo
O módulo BLE também dá suporte ao envio de instruções do Hub IoT do Azure para o dispositivo. Você pode usar a ferramenta [Gerenciador de Dispositivos](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer) ou o [iothub-explorer](https://github.com/Azure/iothub-explorer) para enviar as mensagens JSON que o módulo de gateway BLE passa para o dispositivo BLE.
Se você estiver usando o dispositivo Texas Instruments SensorTag, poderá ativar o LED vermelho, o LED verde ou campainha enviando comandos do Hub IoT. Para fazer isso, envie as duas mensagens JSON a seguir na ordem. Em seguida, você pode enviar um dos comandos para ligar as luzes ou a campainha.

1. Redefinir todos os LEDs e a campainha (desativá-los):

    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "AA=="
    }
    ```
    
2. Configurar a E/S como 'remota':
  
    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA66-0451-4000-B000-000000000000",
      "data": "AQ=="
    }
    ```
    
Em seguida, você pode enviar um dos seguintes comandos para ligar as luzes ou a campainha.

* Ativar o LED vermelho:
  
    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "AQ=="
    }
    ```

* Ativar o LED verde:
  
    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "Ag=="
    }
    ```

* Ativar a campainha:
  
    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "BA=="
    }
    ```

## <a name="next-steps"></a>Próximas etapas
Se você desejar obter uma compreensão mais avançada do SDK do Gateway do IoT e experimentar alguns exemplos de código, visite os seguintes recursos e tutoriais para desenvolvedores:

* [SDK do Gateway do IoT do Azure][lnk-sdk]

Para explorar melhor as funcionalidades do Hub IoT, consulte:

* [Guia do desenvolvedor do Hub IoT][lnk-devguide]

<!-- Links -->
[lnk-ble-samplecode]: https://github.com/Azure/azure-iot-gateway-sdk/tree/master/samples/ble_gateway
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md
[lnk-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/
[lnk-noobs]: https://www.raspberrypi.org/documentation/installation/noobs.md
[lnk-raspbian]: https://www.raspberrypi.org/downloads/raspbian/


[lnk-devguide]: iot-hub-devguide.md
[lnk-create-hub]: iot-hub-create-through-portal.md 



<!--HONumber=Feb17_HO3-->


