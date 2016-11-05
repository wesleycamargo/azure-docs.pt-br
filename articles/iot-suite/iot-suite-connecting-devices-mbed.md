---
title: Conectar um dispositivo usando C no mbed | Microsoft Docs
description: Descreve como conectar um dispositivo à solução pré-configurada de monitoramento remoto do Azure IoT Suite usando um aplicativo escrito em C em um dispositivo mbed.
services: ''
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: ''

ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/14/2016
ms.author: dobett

---
# Conectar seu dispositivo à solução pré-configurada de monitoramento remoto (mbed)
[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## Criar e executar a solução de exemplo do C
As instruções a seguir descrevem as etapas para conectar um dispositivo [Freescale FRDM-K64F habilitado para mbed][lnk-mbed-home] para a solução de monitoramento remoto.

### Conectar o dispositivo mbed ao computador desktop e à rede
1. Conecte o dispositivo mbed à sua rede usando um cabo Ethernet. Essa etapa é necessária porque o aplicativo de exemplo requer acesso à internet.
2. Consulte [Introdução ao mbed][lnk-mbed-getstarted] para conectar o dispositivo mbed ao seu computador.
3. Se o seu computador executar o Windows, consulte [Configuração de PC][lnk-mbed-pcconnect] para configurar o acesso de porta serial ao dispositivo mbed.

### Crie um projeto mbed e importe o código de exemplo
1. No navegador da Web, vá até o [site de desenvolvedor](https://developer.mbed.org/) mbed.org. Caso ainda não tenha se inscrito, você verá uma opção para criar uma nova conta (é gratuita). Caso contrário, faça logon com suas credenciais de conta. Em seguida, clique em **Compilador** no canto superior direito da página. Essa ação exibe a interface do *Espaço de trabalho*.
2. Verifique se a plataforma de hardware que você está usando é exibida no canto superior direito da janela ou clique no ícone no canto superior direito para selecionar a plataforma de hardware.
3. Clique em **Importar** no menu principal. Clique em **Clique aqui** para importar do link de URL ao lado do logotipo de globo mbed.
   
    ![][6]
4. Na janela pop-up, insira o link para o código de exemplo https://developer.mbed.org/users/AzureIoTClient/code/remote_monitoring/ e clique em **Importar**.
   
    ![][7]
5. Você pode ver na janela do compilador mbed que a importação desse projeto também importa várias bibliotecas. Algumas são fornecidas e mantidas pela equipe do Azure IoT ([azureiot\_common](https://developer.mbed.org/users/AzureIoTClient/code/azureiot_common/), [iothub\_client](https://developer.mbed.org/users/AzureIoTClient/code/iothub_client/), [iothub\_amqp\_transport](https://developer.mbed.org/users/AzureIoTClient/code/iothub_amqp_transport/), [azure\_uamqp](https://developer.mbed.org/users/AzureIoTClient/code/azure_uamqp/)), enquanto outras são bibliotecas de terceiros disponíveis no catálogo de bibliotecas mbed.
   
    ![][8]
6. Abra o arquivo remote\_monitoring\\remote\_monitoring.c e localize o seguinte código no arquivo:
   
    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "[IoTHub Suffix, i.e. azure-devices.net]";
    ```
7. Substitua [Device Id] e [Device Key] pelos dados do dispositivo para permitir que o programa de exemplo se conecte ao seu hub IoT. Use o Nome de host do Hub IoT para substituir os espaços reservados [Nome do Hub IoT] e [Sufixo do Hub IoT, ou seja, azure-devices.net]. Por exemplo, se seu nome de host do Hub IoT for **contoso.azure-devices.net**, **contoso** será o **hubName** e tudo depois dele será o **hubSuffix**:
   
    ```
    static const char* deviceId = "mydevice";
    static const char* deviceKey = "mykey";
    static const char* hubName = "contoso";
    static const char* hubSuffix = "azure-devices.net";
    ```
   
    ![][9]

### Explorar o código
Se você estiver interessado em como funciona o programa, esta seção descreve algumas das partes principais do código de exemplo. Se você quiser executar o código, pule para [Compilar e executar o programa](#buildandrun).

#### Definindo o modelo
Este exemplo usa a biblioteca do [serializador][lnk-serializer] para definir um modelo que especifica as mensagens que o dispositivo pode enviar ao Hub IoT e receber do Hub IoT. Neste exemplo, o namespace **Contoso** define um modelo **Therrmostat** que especifica os dados de telemetria **Temperature**, **ExternalTemperature** e **Humidity** com metadados, como a id do dispositivo, as propriedades do dispositivo e os comandos aos quais o dispositivo responde:

```
BEGIN_NAMESPACE(Contoso);

DECLARE_STRUCT(SystemProperties,
    ascii_char_ptr, DeviceID,
    _Bool, Enabled
);

DECLARE_STRUCT(DeviceProperties,
ascii_char_ptr, DeviceID,
_Bool, HubEnabledState
);

DECLARE_MODEL(Thermostat,

    /* Event data (temperature, external temperature and humidity) */
    WITH_DATA(int, Temperature),
    WITH_DATA(int, ExternalTemperature),
    WITH_DATA(int, Humidity),
    WITH_DATA(ascii_char_ptr, DeviceId),

    /* Device Info - This is command metadata + some extra fields */
    WITH_DATA(ascii_char_ptr, ObjectType),
    WITH_DATA(_Bool, IsSimulatedDevice),
    WITH_DATA(ascii_char_ptr, Version),
    WITH_DATA(DeviceProperties, DeviceProperties),
    WITH_DATA(ascii_char_ptr_no_quotes, Commands),

    /* Commands implemented by the device */
    WITH_ACTION(SetTemperature, int, temperature),
    WITH_ACTION(SetHumidity, int, humidity)
);

END_NAMESPACE(Contoso);
```

À definição do modelo estão relacionadas as definições para os comandos **SetTemperature** e **SetHumidity** às quais o dispositivo responde:

```
EXECUTE_COMMAND_RESULT SetTemperature(Thermostat* thermostat, int temperature)
{
    (void)printf("Received temperature %d\r\n", temperature);
    thermostat->Temperature = temperature;
    return EXECUTE_COMMAND_SUCCESS;
}

EXECUTE_COMMAND_RESULT SetHumidity(Thermostat* thermostat, int humidity)
{
    (void)printf("Received humidity %d\r\n", humidity);
    thermostat->Humidity = humidity;
    return EXECUTE_COMMAND_SUCCESS;
}
```

#### Conectando o modelo à biblioteca
As funções **sendMessage** e **IoTHubMessage** são o código clichê para enviar telemetria do dispositivo e conectar as mensagens do Hub IoT aos manipuladores de comando.

#### A função remote\_monitoring\_run
A função **main** do programa invoca a função **remote\_monitoring\_run** quando o aplicativo começa a executar o comportamento do dispositivo como um cliente do dispositivo Hub IoT. Essa função **remote\_monitoring\_run** é fundamentalmente formada por pares aninhados de funções:

* **platform\_init** e **platform\_deinit** executam operações de inicialização e desligamento específicas da plataforma.
* **serializer\_init** e **serializer\_deinit** inicializam e desligam a biblioteca do serializador.
* **IoTHubClient\_Create** e **IoTHubClient\_Destroy** criam um identificador de cliente, **iotHubClientHandle**, usando as credenciais do dispositivo para se conectar ao Hub IoT.

Na principal seção da função **remote\_monitoring\_run**, o programa executa as seguintes operações usando o identificador **iotHubClientHandle**:

* Cria uma instância do modelo de termostato da Contoso e configura os retornos de chamada de mensagem para os dois comandos.
* Envia informações sobre o dispositivo, incluindo os comandos para os quais ele tem suporte, para o Hub IoT usando a biblioteca do serializador. Quando o hub recebe essa mensagem, ele altera o status do dispositivo no painel de **Pendente** para **Em Execução**.
* Inicia um loop **while** que envia os valores de umidade, temperatura externa e temperatura ao Hub IoT a cada segundo.

Para referência, veja um exemplo de mensagem de **DeviceInfo** enviada ao Hub IoT na inicialização:

```
{
  "ObjectType":"DeviceInfo",
  "Version":"1.0",
  "IsSimulatedDevice":false,
  "DeviceProperties":
  {
    "DeviceID":"mydevice01", "HubEnabledState":true
  }, 
  "Commands":
  [
    {"Name":"SetHumidity", "Parameters":[{"Name":"humidity","Type":"double"}]},
    { "Name":"SetTemperature", "Parameters":[{"Name":"temperature","Type":"double"}]}
  ]
}
```

Para referência, veja um exemplo de mensagem de **Telemetria** enviada ao Hub IoT:

```
{"DeviceId":"mydevice01", "Temperature":50, "Humidity":50, "ExternalTemperature":55}
```

Para referência, veja um exemplo de **Comando** recebido do Hub IoT:

```
{
  "Name":"SetHumidity",
  "MessageId":"2f3d3c75-3b77-4832-80ed-a5bb3e233391",
  "CreatedTime":"2016-03-11T15:09:44.2231295Z",
  "Parameters":{"humidity":23}
}
```

<a id="buildandrun"/>

### Compile e execute o programa
1. Clique em **Compilar** para criar o programa. Você pode ignorar os avisos com segurança, mas se a compilação gerar erros, corrija-os antes de continuar.
2. Se o build for bem-sucedido, o site do compilador mbed gerará um arquivo .bin com o nome do seu projeto e o baixa para o computador local. Copie o arquivo .bin para o dispositivo. Salvar o arquivo .bin no dispositivo faz com que o dispositivo reinicie e execute o programa contido no arquivo .bin. Você pode reiniciar manualmente o programa a qualquer momento pressionando o botão de reinicialização no dispositivo mbed.
3. Conecte-se ao dispositivo usando um aplicativo de cliente SSH, como o PuTTY. Você pode determinar a porta serial que o dispositivo usa verificando o Gerenciador de Dispositivos do Windows.
   
    ![][11]
4. Em PuTTY, clique no tipo de conexão **Serial**. Normalmente, o dispositivo se conecta a 9600 bauds, portanto, insira 9600 na caixa **Velocidade**. Em seguida, clique em **Abrir**.
5. O programa inicia a execução. Talvez seja necessário redefinir a placa (pressione CTRL+Break ou pressione o botão de redefinição da placa) se o programa não iniciar automaticamente quando você se conectar.
   
    ![][10]

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[6]: ./media/iot-suite-connecting-devices-mbed/mbed1.png
[7]: ./media/iot-suite-connecting-devices-mbed/mbed2a.png
[8]: ./media/iot-suite-connecting-devices-mbed/mbed3a.png
[9]: ./media/iot-suite-connecting-devices-mbed/suite6.png
[10]: ./media/iot-suite-connecting-devices-mbed/putty.png
[11]: ./media/iot-suite-connecting-devices-mbed/mbed6.png

[lnk-mbed-home]: https://developer.mbed.org/platforms/FRDM-K64F/
[lnk-mbed-getstarted]: https://developer.mbed.org/platforms/FRDM-K64F/#getting-started-with-mbed
[lnk-mbed-pcconnect]: https://developer.mbed.org/platforms/FRDM-K64F/#pc-configuration
[lnk-serializer]: https://azure.microsoft.com/documentation/articles/iot-hub-device-sdk-c-intro/#serializer

<!---HONumber=AcomDC_0720_2016-->