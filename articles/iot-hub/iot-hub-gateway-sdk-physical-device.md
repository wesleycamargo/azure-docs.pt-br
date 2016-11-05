---
title: Usar um dispositivo real com o SDK do gateway | Microsoft Docs
description: Passo a passo do SDK de gateway do Hub IoT do Azure usando um Texas Instruments SensorTag para enviar dados para o Hub IoT por meio de um gateway em execução em um módulo de computação Intel Edison
services: iot-hub
documentationcenter: ''
author: chipalost
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/29/2016
ms.author: andbuc

---
# <a name="iot-gateway-sdk-(beta)-–-send-device-to-cloud-messages-with-a-real-device-using-linux"></a>SDK de gateway do IoT (beta): enviar mensagens do dispositivo para a nuvem com um dispositivo real usando o Linux
Este passo a passo do exemplo de [Bluetooth de baixa energia][lnk-ble-samplecode] mostra como usar o [Gateway do SDK do Microsoft Azure IoT][lnk-sdk] para encaminhar a telemetria do dispositivo para a nuvem para o Hub IoT de um dispositivo físico e como rotear comandos do Hub IoT para um dispositivo físico.

Este passo a passo aborda:

* **Arquitetura**: informações importantes de arquitetura sobre o exemplo de Bluetooth de baixa energia.
* **Criar e executar**: as etapas necessárias para criar e executar a amostra.

## <a name="architecture"></a>Arquitetura
O passo a passo mostra como criar e executar um gateway do IoT em um módulo de computação Intel Edison que executa o Linux. O gateway é criado usando o SDK de gateway do IoT. O exemplo usa um dispositivo BLE (Bluetooth de baixa energia) Texas Instruments SensorTag para coletar dados de temperatura.

Quando você executa o gateway, ele:

* Conecta-se a um dispositivo SensorTag usando o protocolo de BLE (Bluetooth de baixa energia).
* Conecta-se ao Hub IoT usando o protocolo AMQP.
* Encaminha a telemetria do dispositivo SensorTag ao Hub IoT.
* Roteia comandos do Hub IoT para o dispositivo SensorTag.

O gateway contém os seguintes módulos:

* Um *módulo BLE* que interage com um dispositivo BLE para receber dados de temperatura do dispositivo e envia comandos para o dispositivo.
* Um *Nuvem BLE para o módulo de dispositivo* que traduz as mensagens JSON provenientes da nuvem em instruções BLE para o *módulo BLE*.
* Um *módulo logger* que registra todas as mensagens do gateway.
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
5. O módulo de agente registra todas as mensagens do agente em um arquivo de disco.

O diagrama de bloco a seguir ilustra o pipeline do fluxo de dados de comando do dispositivo:

![](media/iot-hub-gateway-sdk-physical-device/gateway_ble_command_data_flow.png)

1. O módulo de Hub IoT periodicamente sonda o Hub IoT quanto a novas mensagens de comando.
2. Quando o módulo de Hub IoT recebe uma nova mensagem de comando, ele a publica no agente.
3. O módulo de mapeamento de identidade capta a mensagem de comando e usa uma tabela interna para converter a ID do dispositivo Hub IoT para um endereço MAC do dispositivo. Em seguida, ele publica uma nova mensagem que inclui o endereço MAC do dispositivo de destino no mapa de propriedades da mensagem.
4. O módulo Nuvem BLE para dispositivo pega essa mensagem e a converte na instrução BLE adequada para o módulo BLE. Em seguida, ele publica uma nova mensagem.
5. O módulo BLE capta essa mensagem e executa a instrução de E/S se comunicando com o dispositivo BLE.
6. O módulo de agente registra todas as mensagens do agente em um arquivo de disco.

## <a name="prepare-your-hardware"></a>Prepare seu hardware
Este tutorial presume que você esteja usando um dispositivo [Texas Instruments SensorTag](http://www.ti.com/ww/en/wireless_connectivity/sensortag2015/index.html) conectado a uma placa Intel Edison.

### <a name="set-up-the-edison-board"></a>Configure a placa Edison
Antes de começar, você deve se certificar de que pode conectar seu dispositivo Edison à sua rede sem fio. Para configurar seu dispositivo Edison, você precisa se conectar a um computador host. A Intel fornece guias de introdução para os seguintes sistemas operacionais:

* [Get Started with the Intel Edison Development Board on Windows 64-bit (Introdução à placa de desenvolvimento Intel Edison no Windows 64 bits)][lnk-setup-win64].
* [Get Started with the Intel Edison Development Board on Windows 32-bit (Introdução à placa de desenvolvimento Intel Edison no Windows 32 bits)][lnk-setup-win32].
* G[et Started with the Intel Edison Development Board on Mac OS X (Introdução à placa de desenvolvimento Intel Edison no Mac OS X)][lnk-setup-osx].
* [Getting Started with the Intel® Edison Board on Linux (Introdução à placa Intel® Edison no Linux)][lnk-setup-linux].

Para configurar seu dispositivo Edison e se familiarizar com ele, você deve concluir todas as etapas nesses artigos de “Introdução” exceto a última etapa "Choose IDE" (Escolher a IDE), que não é necessária para o tutorial atual. No final do processo de configuração da placa Edison, você:

* Atualizou a placa Edison com o firmware mais recente.
* Estabeleceu uma conexão serial do host para a placa Edison.
* Execute o script **configure_edison** para definir uma senha e habilitar o Wi-Fi em sua placa Edison.

### <a name="enable-connectivity-to-the-sensortag-device-from-your-edison-board"></a>Habilite a conectividade para o dispositivo SensorTag da sua placa Edison
Antes de executar o exemplo, você precisa verificar se sua placa Edison pode se conectar ao dispositivo SensorTag.

Primeiro, você precisa verificar se a placa Edison pode se conectar ao dispositivo SensorTag.

1. Desbloqueie o Bluetooth na placa Edison e verifique se o número de versão é **5.37**.
   
    ```
    rfkill unblock bluetooth
    bluetoothctl --version
    ```
2. Execute o comando **bluetoothctl** . Agora você está em um shell Bluetooth interativo. 
3. Digite o comando **power on** para ligar o controlador bluetooth. Você deve ver uma saída semelhante a:
   
    ```
    [NEW] Controller 98:4F:EE:04:1F:DF edison [default]
    ```
4. Ainda no shell interativo bluetooth, digite o comando **scan on** para verificar se há dispositivos bluetooth. Você deve ver uma saída semelhante a:
   
    ```
    Discovery started
    [CHG] Controller 98:4F:EE:04:1F:DF Discovering: yes
    ```
5. Torne o dispositivo SensorTag detectável pressionando o botão pequeno (o LED verde deve piscar). A placa Edison deve detectar o dispositivo SensorTag:
   
    ```
    [NEW] Device A0:E6:F8:B5:F6:00 CC2650 SensorTag
    [CHG] Device A0:E6:F8:B5:F6:00 TxPower: 0
    [CHG] Device A0:E6:F8:B5:F6:00 RSSI: -43
    ```
   
    Neste exemplo, você pode ver que é o endereço MAC do dispositivo SensorTag é **A0:E6:F8:B5:F6:00**.
6. Desative a verificação inserindo o comando **scan off** .
   
    ```
    [CHG] Controller 98:4F:EE:04:1F:DF Discovering: no
    Discovery stopped
    ```
7. Conecte-se ao dispositivo SensorTag usando seu endereço MAC inserindo **connect<MAC address>**. Observe que a saída de exemplo abaixo está abreviada:
   
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
   
    Observação: você pode listar as características do GATT do dispositivo novamente usando o comando **list-attributes** .
8. Agora você pode se desconectar do dispositivo usando o comando **disconnect** e sair do shell Bluetooth usando o comando **quit**:
   
    ```
    Attempting to disconnect from A0:E6:F8:B5:F6:00
    Successful disconnected
    [CHG] Device A0:E6:F8:B5:F6:00 Connected: no
    ```

Agora você está pronto para executar o exemplo de gateway de BLE no dispositivo Edison.

## <a name="run-the-ble-gateway-sample"></a>Execute o exemplo de gateway de BLE
Para executar o exemplo de BLE em sua placa Edison, você precisa concluir três tarefas:

* Configurar dois dispositivos de exemplo em seu Hub IoT.
* Compilar o SDK de gateway em seu dispositivo Edison.
* Configurar e execute o exemplo de BLE em seu dispositivo Edison.

No momento da redação desse artigo, o SDK do Gateway dá suporte apenas para gateways que usam módulos de BLE no Linux.

### <a name="configure-two-sample-devices-in-your-iot-hub"></a>Configurar dois dispositivos de exemplo em seu Hub IoT
* [Criar um Hub IoT][lnk-create-hub] em sua assinatura do Azure. Você precisará do nome do hub para concluir este passo a passo. Se você ainda não tiver uma assinatura do Azure, é possível obter uma [conta gratuita][lnk-free-trial].
* Adicione um dispositivo chamado **SensorTag_01** ao hub IoT e anote sua ID e chave de dispositivo. Você pode usar as ferramentas do [iothub-explorer ou o Gerenciador de Dispositivos][lnk-explorer-tools] para adicionar esse dispositivo ao Hub IoT que criou na etapa anterior e para recuperar sua chave. Você mapeará este dispositivo para o dispositivo SensorTag quando configurar o gateway.

### <a name="build-the-gateway-sdk-on-your-edison-device"></a>Compilar o SDK de gateway em seu dispositivo Edison
A versão do **git** no Edison não permite submódulos. Para baixar o código-fonte completo para o SDK de gateway para a placa Edison, você tem duas opções:

* Opção 1: clonar o repositório do [SDK de Gateway do Microsoft Azure IoT][lnk-sdk] em seu Edison e clonar manualmente o repositório para cada submódulo.
* Opção 2: clonar o repositório do [SDK de Gateway do Microsoft Azure IoT][lnk-sdk] em um dispositivo de área de trabalho em que o **git** permita submódulos e copiar o repositório completo com submódulos para seu Edson.

Se você escolher a opção 2, use os seguintes comandos **git** para clonar o SDK de Gateway e todos os seus submódulos:

```
git clone --recursive https://github.com/Azure/azure-iot-gateway-sdk.git 
git submodule update --init --recursive
```

Em seguida, você deve compactar todo o repositório local em um único arquivo antes de copiá-lo para o Edison. Você pode usar um utilitário como o **pscp**, que está incluído no **Putty**, para copiar o arquivo para o Edison. Por exemplo:

```
pscp .\gatewaysdk.zip root@192.168.0.45:/home/root
```

Quando você tem uma cópia completa do repositório do SDK de gateway em seu Edison, você pode compilá-lo usando o comando a seguir na pasta que contém o SDK:

```
./tools/build.sh
```

### <a name="configure-and-run-the-ble-sample-on-your-edison-device"></a>Configurar e execute o exemplo de BLE em seu dispositivo Edison
Para inicializar e executar o exemplo, você precisa configurar cada módulo que participa no gateway. Essa configuração é fornecida em um arquivo JSON e você precisa configurar todos os cinco módulos participantes. Há um arquivo JSON de exemplo fornecido no repositório chamado **gateway_sample.json**, que pode ser usado como ponto de partida para criar seu próprio arquivo de configuração. Esse arquivo está na pasta **samples/ble_gateway_hl/src** na cópia local do repositório do SDK de Gateway.

As seções a seguir descrevem como editar esse arquivo de configuração para o exemplo de BLE e pressupõem que o repositório do SDK de gateway está na pasta **/home/root/azure-iot-gateway-sdk/** de seu dispositivo Edison. Se o repositório estiver em outro lugar, você deverá ajustar os caminhos adequadamente:

#### <a name="logger-configuration"></a>Configuração do agente
Pressupondo que o repositório de gateway esteja localizado na pasta **/home/root/azure-iot-gateway-sdk/**, configure o módulo de agente da seguinte maneira:

```json
{
    "module name": "logger",
    "module path": "/home/root/azure-iot-gateway-sdk/build/modules/logger/liblogger_hl.so",
    "args":
    {
        "filename":"/home/root/gw_logger.log"
    }
}
```

#### <a name="ble-module-configuration"></a>Configuração do módulo BLE
A configuração de exemplo do dispositivo BLE pressupõe um dispositivo Texas Instruments SensorTag. Qualquer dispositivo BLE padrão que pode operar como um GATT periférico deve funcionar, mas você precisará atualizar as IDs de característica GATT e os dados (para instruções de gravação). Adicione o endereço MAC do dispositivo SensorTag: 

```json
{
  "module name": "SensorTag",
  "module path": "/home/root/azure-iot-gateway-sdk/build/modules/ble/libble_hl.so",
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
  "module name": "IoTHub",
  "module path": "/home/root/azure-iot-gateway-sdk/build/modules/iothub/libiothub_hl.so",
  "args": {
    "IoTHubName": "<<Azure IoT Hub Name>>",
    "IoTHubSuffix": "<<Azure IoT Hub Suffix>>",
    "Transport": "HTTP"
  }
}
```

#### <a name="identity-mapping-module-configuration"></a>Configuração do módulo de mapeamento de identidade
Adicione o endereço MAC do dispositivo SensorTag e a ID e a chave do dispositivo **SensorTag_01** adicionado ao seu Hub IoT:

```json
{
  "module name": "mapping",
  "module path": "/home/root/azure-iot-gateway-sdk/build/modules/identitymap/libidentity_map_hl.so",
  "args": [
    {
      "macAddress": "<<AA:BB:CC:DD:EE:FF>>",
      "deviceId": "<<Azure IoT Hub Device ID>>",
      "deviceKey": "<<Azure IoT Hub Device Key>>"
    }
  ]
}
```

#### <a name="ble-printer-module-configuration"></a>Configuração do módulo de impressora BLE
```json
{
    "module name": "BLE Printer",
    "module path": "/home/root/azure-iot-gateway-sdk/build/samples/ble_gateway_hl/ble_printer/libble_printer.so",
    "args": null
}
```

#### <a name="routing-configuration"></a>Configuração de roteamento
A configuração a seguir garante o seguinte:

* O módulo **Logger** recebe e registra todas as mensagens.
* O módulo **SensorTag** envia mensagens para os módulos **mapeamento** e **Impressora BLE**.
* O módulo **mapeamento** envia mensagens ao módulo **IoTHub** a ser enviado ao seu Hub IoT.
* O módulo **IoTHub** envia as mensagens de volta para o módulo **mapeamento**.
* O módulo **mapeamento** envia as mensagens de volta para o módulo **SensorTag**.

```json
"links" : [
    {"source" : "*", "sink" : "Logger" },
    {"source" : "SensorTag", "sink" : "mapping" },
    {"source" : "SensorTag", "sink" : "BLE Printer" },
    {"source" : "mapping", "sink" : "IoTHub" },
    {"source" : "IoTHub", "sink" : "mapping" },
    {"source" : "mapping", "sink" : "SensorTag" }
  ]
```

Para executar o exemplo, você deve executar o binário **ble_gateway_hl** passando o caminho para o arquivo de configuração JSON. Se você usou o arquivo **gateway_sample.json**, o comando para executar tem uma aparência semelhante a esta:

```
./build/samples/ble_gateway_hl/ble_gateway_hl ./samples/ble_gateway_hl/src/gateway_sample.json
```

Talvez seja necessário pressionar o botão pequeno no SensorTag para torná-lo detectável antes de executar o exemplo.

Ao executar o exemplo, você pode usar a ferramenta do [iothub-explorer ou o Gerenciador de Dispositivos][lnk-explorer-tools] para monitorar as mensagens que o gateway encaminha para o dispositivo SensorTag.

## <a name="send-cloud-to-device-messages"></a>Envie mensagens da nuvem para o dispositivo
O módulo BLE também dá suporte ao envio de instruções do Hub IoT do Azure para o dispositivo. Você pode usar o [Gerenciador de Dispositivos do Hub IoT do Azure](https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md) ou o [Gerenciador de Hub IoT](https://github.com/Azure/azure-iot-sdks/tree/master/tools/iothub-explorer) para enviar as mensagens JSON que o módulo de gateway BLE passa para o dispositivo BLE. Por exemplo, se você estiver usando o dispositivo Texas Instruments SensorTag, poderá enviar as mensagens JSON a seguir para o dispositivo do Hub IoT.

* Redefinir todos os LEDs e a campainha (desativá-los)
  
    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "AA=="
    }
    ```
* Configurar a E/S como 'remota'
  
    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA66-0451-4000-B000-000000000000",
      "data": "AQ=="
    }
    ```
* Ativar o LED vermelho
  
    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "AQ=="
    }
    ```
* Ativar o LED verde
  
    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "Ag=="
    }
    ```
* Ativar a campainha
  
    ```json
    {
      "type": "write_once",
      "characteristic_uuid": "F000AA65-0451-4000-B000-000000000000",
      "data": "BA=="
    }
    ```

O comportamento padrão para um dispositivo usando o protocolo HTTP para se conectar ao Hub IoT é verificar a cada 25 minutos se há um novo comando. Portanto, se você enviar vários comandos separados, precisará esperar 25 minutos para que o dispositivo receba cada comando.

> [!NOTE]
> O gateway também verifica se há novos comandos sempre que é iniciado de forma que você pode forçá-lo a processar um comando parando e iniciando o gateway.
> 
> 

## <a name="next-steps"></a>Próximas etapas
Se você quiser obter uma compreensão mais avançada do SDK do Gateway e experimentar alguns exemplos de código, visite os seguintes recursos e tutoriais para desenvolvedores:

* [SDK de Gateway do Azure IoT][lnk-sdk]

Para explorar melhor as funcionalidades do Hub IoT, consulte:

* [Guia de desenvolvedor][lnk-devguide]

<!-- Links -->
[lnk-ble-samplecode]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/samples/ble_gateway_hl
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md
[lnk-setup-win64]: https://software.intel.com/get-started-edison-windows
[lnk-setup-win32]: https://software.intel.com/get-started-edison-windows-32
[lnk-setup-osx]: https://software.intel.com/get-started-edison-osx
[lnk-setup-linux]: https://software.intel.com/get-started-edison-linux
[lnk-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/


[lnk-devguide]: iot-hub-devguide.md
[lnk-create-hub]: iot-hub-create-through-portal.md 


<!--HONumber=Oct16_HO2-->


