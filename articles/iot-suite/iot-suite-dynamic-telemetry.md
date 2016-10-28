<properties
	pageTitle="Usar a telemetria dinâmica | Microsoft Azure"
	description="Siga este tutorial para saber como usar a telemetria dinâmica com a solução pré-configurada de monitoramento remoto."
	services=""
    suite="iot-suite"
	documentationCenter=""
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/25/2016"
     ms.author="dobett"/>

# Usar telemetria dinâmica com a solução pré-configurada de monitoramento remoto

## Introdução

A telemetria dinâmica permite que você visualize qualquer telemetria enviada à solução pré-configurada de monitoramento remoto. Os dispositivos simulados que implantam a solução pré-configurada enviam telemetria de temperatura e umidade, que pode ser visualizada no painel. Se você personalizar os dispositivos simulados existentes, criar novos dispositivos simulados ou conectar dispositivos físicos à solução pré-configurada, você poderá enviar outros valores de telemetria, como a temperatura externa, RPM ou velocidade do vento. Em seguida, você pode visualizar essa telemetria adicional no painel.

Este tutorial usa um dispositivo Node.js simples simulado que você pode facilmente modificar a fim de experimentar a telemetria dinâmica.

Para concluir este tutorial, você precisará de:

- Uma assinatura ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure][lnk_free_trial].
- [Node.js][lnk-node] versão 0.12.x ou posterior.

Você pode concluir este tutorial em qualquer sistema operacional, como Windows ou Linux, onde é possível instalar o Node.js.

[AZURE.INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

## Configurar o dispositivo Node.js simulado

1. No painel de monitoramento remoto, clique em **+ Adicionar um dispositivo** e, em seguida, adicione um dispositivo personalizado. Anote o nome de host do Hub IoT, id do dispositivo e chave do dispositivo. Você precisará deles mais tarde neste tutorial ao preparar o aplicativo cliente de dispositivo remote\_monitoring.js.

2. Certifique-se de que o Node.js versão 0.12.x ou posterior esteja instalado no computador de desenvolvimento. Execute `node --version` em um prompt de comando ou em um shell para verificar a versão. Para obter informações sobre como usar um gerenciador de pacotes para instalar o Node.js no Linux, confira [Instalação do Node.js por meio do gerenciador de pacotes][node-linux].

3. Após a instalação do Node.js, clone a versão mais recente do repositório [azure-iot-sdks][lnk-github-repo] em seu computador de desenvolvimento. Sempre use a ramificação **mestre** para a versão mais recente das bibliotecas e exemplos.

4. Em sua cópia local do repositório [azure-iot-sdks][lnk-github-repo], copie os dois arquivos a seguir da pasta node/device/samples em uma pasta vazia em seu computador de desenvolvimento:

  - packages.json
  - remote\_monitoring.js

5. Abra o arquivo remote\_monitoring.js e procure a seguinte definição de variável:

    ```
    var connectionString = "[IoT Hub device connection string]";
    ```

6. Substitua a **[cadeia de conexão de dispositivo do Hub IoT]** pela cadeia de conexão do seu dispositivo. Use os valores de seu nome de host do Hub IoT, ID do dispositivo e chave de dispositivo que você anotou na etapa 1. Uma cadeia de conexão do dispositivo tem o seguinte formato:

    ```
    HostName={your IoT Hub hostname};DeviceId={your device id};SharedAccessKey={your device key}
    ```

    Se o seu nome de host do Hub IoT for **contoso** e a sua ID de dispositivo for **mydevice**, sua cadeia de conexão terá esta aparência:

    ```
    var connectionString = "HostName=contoso.azure-devices.net;DeviceId=mydevice;SharedAccessKey=2s ... =="
    ```

7. Salve o arquivo. Execute os seguintes comandos em um shell ou prompt de comando na pasta que contém os arquivos para instalar os pacotes necessários e, em seguida, execute o aplicativo de exemplo:

    ```
    npm install
    node remote_monitoring.js
    ```

## Observe a telemetria dinâmica em ação

O painel mostra a telemetria de temperatura e umidade dos dispositivos existentes simulados:

![O painel padrão][image1]

Se você selecionar dispositivo simulado Node.js executado na seção anterior, verá a telemetria de temperatura, umidade e temperatura externa:

![Adicionar temperatura externa ao painel][image2]

A solução de monitoramento remoto detecta automaticamente o tipo de telemetria adicional de temperatura externa e o adiciona ao gráfico no painel.

## Adicionar um tipo de telemetria

A próxima etapa é substituir a telemetria gerada pelo dispositivo simulado Node.js por um novo conjunto de valores:

1. Interrompa o dispositivo simulado Node.js digitando **Ctrl + C** no prompt de comando ou no shell.

2. No arquivo remote\_monitoring.js, você pode ver os valores de dados base para a telemetria existente de temperatura, umidade e temperatura externa. Adicione um valor de dados base para **rpm** da seguinte maneira:

    ```
    // Sensors data
    var temperature = 50;
    var humidity = 50;
    var externalTemperature = 55;
    var rpm = 200;
    ```

3. O dispositivo simulado Node.js usa a função **generateRandomIncrement** no arquivo remote\_monitoring.js para adicionar um incremento aleatório aos valores de dados base. Torne o valor de **rpm** aleatório adicionando uma linha de código após as aleatorizações existentes da seguinte maneira:

    ```
    temperature += generateRandomIncrement();
    externalTemperature += generateRandomIncrement();
    humidity += generateRandomIncrement();
    rpm += generateRandomIncrement();
    ```

4. Adicione o novo valor de rpm à carga JSON que o dispositivo envia ao Hub IoT:

    ```
    var data = JSON.stringify({
      'DeviceID': deviceId,
      'Temperature': temperature,
      'Humidity': humidity,
      'ExternalTemperature': externalTemperature,
      'RPM': rpm
    });
    ```

5. Execute o dispositivo Node.js simulado usando o seguinte comando:

    ```
    node remote_monitoring.js
    ```

6. Observe o novo tipo de telemetria RPM exibido no gráfico no painel:

![Adicionar RPM ao painel][image3]

> [AZURE.NOTE] Talvez seja necessário desabilitar e, em seguida, habilitar o dispositivo Node.js na página **Dispositivos** no painel, para ver a alteração imediatamente.

## Personalizar a exibição do painel

A mensagem **Informações do Dispositivo** pode incluir metadados sobre a telemetria que o dispositivo pode enviar para o Hub IoT. Esses metadados podem especificar os tipos de telemetria enviados pelo dispositivo. Modifique o valor de **deviceMetaData** no arquivo remote\_monitoring.js a fim de incluir uma definição de **Telemetria** logo após a definição de **Comandos**. O seguinte trecho de código mostra a definição **Comandos** (não se esqueça de adicionar um `,` após a definição de **Comandos**):

```
'Commands': [{
  'Name': 'SetTemperature',
  'Parameters': [{
    'Name': 'Temperature',
    'Type': 'double'
  }]
},
{
  'Name': 'SetHumidity',
  'Parameters': [{
    'Name': 'Humidity',
    'Type': 'double'
  }]
}],
'Telemetry': [{
  'Name': 'Temperature',
  'Type': 'double'
},
{
  'Name': 'Humidity',
  'Type': 'double'
},
{
  'Name': 'ExternalTemperature',
  'Type': 'double'
}]
```

> [AZURE.NOTE] A solução de monitoramento remoto usa uma correspondência que diferencia maiúsculas e minúsculas a fim de comparar a definição de metadados com os dados no fluxo de telemetria.

A adição de uma definição de **Telemetria**, como mostra o trecho de código acima, não altera o comportamento do painel. No entanto, os metadados também podem incluir um atributo **DisplayName** para personalizar a exibição no painel. Atualize a definição de metadados de **Telemetria** conforme mostra o trecho a seguir:

```
'Telemetry': [
{
  'Name': 'Temperature',
  'Type': 'double',
  'DisplayName': 'Temperature (C*)'
},
{
  'Name': 'Humidity',
  'Type': 'double',
  'DisplayName': 'Humidity (relative)'
},
{
  'Name': 'ExternalTemperature',
  'Type': 'double',
  'DisplayName': 'Outdoor Temperature (C*)'
}
]
```

A captura de tela a seguir mostra como essa alteração modifica a legenda do gráfico no painel:

![Personalizar a legenda do gráfico][image4]

> [AZURE.NOTE] Talvez seja necessário desabilitar e, em seguida, habilitar o dispositivo Node.js na página **Dispositivos** no painel, para ver a alteração imediatamente.

## Filtrar os tipos de telemetria

Por padrão, o gráfico no painel mostra cada série de dados no fluxo de telemetria. Você pode usar os metadados **Informações do Dispositivo** para suprimir a exibição de tipos de telemetria específicos no gráfico.

Para fazer com que o gráfico mostre somente a telemetria de Temperatura e Umidade, omita **ExternalTemperature** dos metadados **Informações do Dispositivo** de **Telemetria** da seguinte maneira:

```
'Telemetry': [
{
  'Name': 'Temperature',
  'Type': 'double',
  'DisplayName': 'Temperature (C*)'
},
{
  'Name': 'Humidity',
  'Type': 'double',
  'DisplayName': 'Humidity (relative)'
},
//{
//  'Name': 'ExternalTemperature',
//  'Type': 'double',
//  'DisplayName': 'Outdoor Temperature (C*)'
//}
]
```

A **Temperatura Externa** não aparece mais no gráfico:

![Filtrar a telemetria no painel][image5]

Essa alteração afeta somente a exibição do gráfico. Os valores de **ExternalTemperature** ainda são armazenados e disponibilizados para o processamento de qualquer back-end.

> [AZURE.NOTE] Talvez seja necessário desabilitar e, em seguida, habilitar o dispositivo Node.js na página **Dispositivos** no painel, para ver a alteração imediatamente.

## Tratar erros

Para que um fluxo de dados seja exibido no gráfico, seu **Tipo** nos metadados **Informações do Dispositivo** deve corresponder ao tipo de dados dos valores de telemetria. Por exemplo, se os metadados especificarem que o **Tipo** de dados de umidade é **int** e um **double** for encontrado no fluxo de telemetria, a telemetria de umidade não será exibida no gráfico. No entanto, os valores de **umidade** ainda são armazenados e disponibilizados para o processamento de qualquer back-end.

## Próximas etapas

Agora que você já viu como usar a telemetria dinâmica, saiba mais sobre como as soluções pré-configuradas usam das informações do dispositivo: [Metadados de informações de dispositivo na solução pré-configurada de monitoramento remoto][lnk-devinfo].

[lnk-devinfo]: iot-suite-remote-monitoring-device-info.md

[image1]: media/iot-suite-dynamic-telemetry/image1.png
[image2]: media/iot-suite-dynamic-telemetry/image2.png
[image3]: media/iot-suite-dynamic-telemetry/image3.png
[image4]: media/iot-suite-dynamic-telemetry/image4.png
[image5]: media/iot-suite-dynamic-telemetry/image5.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-node]: http://nodejs.org
[node-linux]: https://github.com/nodejs/node-v0.x-archive/wiki/Installing-Node.js-via-package-manager
[lnk-github-repo]: https://github.com/Azure/azure-iot-sdks

<!---HONumber=AcomDC_0831_2016-->