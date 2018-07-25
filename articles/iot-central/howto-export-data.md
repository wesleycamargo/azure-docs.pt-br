---
title: Exportar dados no Azure IoT Central | Microsoft Docs
description: Como exportar dados do aplicativo do Azure IoT Central
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 07/3/2018
ms.topic: article
ms.prod: azure-iot-central
manager: peterpr
ms.openlocfilehash: 6d35e3cfefcefef0b4ff40364cbdab92d486b769
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/13/2018
ms.locfileid: "39011454"
---
# <a name="export-your-data-in-azure-iot-central"></a>Exportar dados no Azure IoT Central

Use a exportação contínua de dados para exportar periodicamente os dados em sua conta de Armazenamento de Blobs do Azure. Opte por exportar **medições**, **dispositivos** e **modelos de dispositivo** em arquivos no formato [Apache AVRO](https://avro.apache.org/docs/current/index.html). Use os dados exportados para a análise de caminho frio, assim como modelos de treinamento no Azure Machine Learning ou análise de tendências de longo prazo no Power BI.

> [!Note]
> Quando você ativa a Exportação Contínua de Dados, você obtém apenas os dados recebidos em entrada desse momento em diante. Atualmente, não há nenhuma maneira de recuperar dados de quando a Exportação Contínua de Dados estava desligada. Ative a Exportação Contínua de Dados cedo para reter mais dados históricos!

## <a name="prerequisites"></a>Pré-requisitos

- Um aplicativo em versão de avaliação de 30 dias estendida ou um aplicativo pago
- Conta do Azure com a assinatura do Azure
- A mesma conta do Azure é um administrador em seu aplicativo IoT Central
- A mesma conta do Azure tem permissões para criar uma conta de armazenamento ou acessar uma conta de armazenamento existente na mesma assinatura do Azure

## <a name="types-of-data-to-export"></a>Tipos de dados a serem exportados

### <a name="measurements"></a>Medidas

As medidas que os dispositivos enviam são exportadas para sua conta de armazenamento. Dados de medidas são exportados aproximadamente uma vez por minuto, e contêm todas as novas mensagens que foram recebidas pela IoT Central de todos os dispositivos nesse intervalo de tempo. Os arquivos do AVRO exportados estão no mesmo formato que os arquivos de mensagens exportados pelo [roteamento de mensagens do Hub IoT](https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-csharp-csharp-process-d2c) para o Armazenamento de Blobs.

> [!NOTE]
> Os dispositivos que enviaram as medidas são representados por IDs de dispositivo (veja abaixo). Para obter os nomes dos dispositivos, você precisa exportar instantâneos de dispositivos também. Você pode correlacionar cada registro de mensagem usando o connectionDeviceId que corresponde à ID do dispositivo.

Este é um exemplo de um registro no arquivo do AVRO decodificado.

```json
{
    "EnqueuedTimeUtc": "2018-06-11T00:00:08.2250000Z",
    "Properties": {},
    "SystemProperties": {
        "connectionDeviceId": "2383d8ba-c98c-403a-b4d5-8963859643bb",
        "connectionAuthMethod": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
        "connectionDeviceGenerationId": "636614021491644195",
        "enqueuedTime": "2018-06-11T00:00:08.2250000Z"
    },
    "Body": "{\"humidity\":80.59100954598546,\"magnetometerX\":0.29451796907056726,\"magnetometerY\":0.5550332126050068,\"magnetometerZ\":-0.04116681874733441,\"connectivity\":\"connected\",\"opened\":\"triggered\"}"
}
```

### <a name="devices"></a>Dispositivos

Quando você ativar a Exportação Contínua de Dados pela primeira vez, um único instantâneo que contém todos os dispositivos é exportado. Isso inclui:
- IDs de dispositivo
- Nomes de dispositivo
- IDs de modelo de dispositivo
- Valores de propriedades
- Valores de configurações

Aproximadamente uma vez por minuto, é gravado um novo instantâneo que contém:

- Os novos dispositivos que foram adicionados desde o último instantâneo
- Dispositivos que tiveram valores de propriedades e de configurações alterados desde o último instantâneo

> [!NOTE]
> Dispositivos que foram excluídos desde o último instantâneo não são exportados. Não há nenhum indicador nos instantâneos para dispositivos que foram excluídos neste momento.

> [!NOTE]
> O modelo de dispositivo ao qual cada dispositivo pertence é representado por uma ID de modelo de dispositivo. Para obter o nome do modelo de dispositivo, você precisa exportar instantâneos do modelo de dispositivo também.

Cada registro no arquivo AVRO decodificado tem este aspecto:

```json
{
    "id": "2383d8ba-c98c-403a-b4d5-8963859643bb",
    "name": "Refrigerator 2",
    "simulated": true,
    "deviceTemplate": {
        "id": "c318d580-39fc-4aca-b995-843719821049",
        "version": "1.0.0"
    },
    "properties": {
        "cloud": {
            "location": "New York",
            "maintCon": true,
            "tempThresh": 20
        },
        "device": {
            "lastReboot": "2018-02-09T22:22:47.156Z"
        }
    },
    "settings": {
        "device": {
            "fanSpeed": 0
        }
    }
}
```

### <a name="device-templates"></a>Modelos de dispositivo

Quando você ativar a Exportação Contínua de Dados pela primeira vez, um único instantâneo que contém todos os modelos de dispositivo é exportado. Isso inclui: 
- IDs de modelo de dispositivo
- Tipos de dados de medição e valores mínimos/máximos
- Valores padrão e tipos de dados de propriedades
- Valores padrão e tipos de dados de configurações

Aproximadamente uma vez por minuto, é gravado um novo instantâneo que contém:

- Os novos modelos de dispositivo que foram adicionados desde o último instantâneo
- Modelos de dispositivo que tinham medidas, propriedades e definições de configurações que foram alteradas desde o último instantâneo

> [!NOTE]
> Modelos de dispositivo que foram excluídos desde o último instantâneo não são exportados. Não há nenhum indicador nos instantâneos para modelos de dispositivo que foram excluídos neste momento.

Cada registro no arquivo AVRO decodificado tem este aspecto:

```json
{
    "id": "c318d580-39fc-4aca-b995-843719821049",
    "name": "Refrigerated Vending Machine",
    "version": "1.0.0",
    "measurements": {
        "telemetry": {
            "humidity": {
                "dataType": "double",
                "name": "Humidity"
            },
            "magnetometerX": {
                "dataType": "double",
                "name": "Magnetometer X"
            },
            "magnetometerY": {
                "dataType": "double",
                "name": "Magnetometer Y"
            },
            "magnetometerZ": {
                "dataType": "double",
                "name": "Magnetometer Z"
            }
        },
        "states": {
            "connectivity": {
                "dataType": "enum",
                "name": "Connectivity"
            }
        },
        "events": {
            "opened": {
                "name": "Door Opened",
                "category": "informational"
            }
        }
    },
    "settings": {
        "device": {
            "fanSpeed": {
                "dataType": "double",
                "name": "Fan Speed",
                "initialValue": 0
            }
        }
    },
    "properties": {
        "cloud": {
            "location": {
                "dataType": "string",
                "name": "Location",
                "initialValue": "Seattle"
            },
            "maintCon": {
                "dataType": "boolean",
                "name": "Maintenance Contract",
                "initialValue": true
            },
            "tempThresh": {
                "dataType": "double",
                "name": "Temperature Alert Threshold",
                "initialValue": 30
            }
        },
        "device": {
            "lastReboot": {
                "dataType": "dateTime",
                "name": "Last Reboot"
            }
        }
    }
}
```

## <a name="how-to-set-up-data-export"></a>Como configurar a exportação de dados

1. Se você não tiver uma, crie uma conta de Armazenamento do Azure **na assinatura do Azure em que seu aplicativo está**. [Clique aqui](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) para ir diretamente para o portal do Azure para criar uma nova conta de Armazenamento do Azure.

    - Escolha um entre os tipos de conta *Uso geral* ou *Armazenamento de blobs*.
    - Selecione a assinatura em que seu aplicativo IoT Central está. Se não vir a assinatura, talvez você precise entrar em uma conta do Azure diferente ou pedir acesso à assinatura.
    - Você pode escolher um grupo de recursos existente ou criar um novo. Aprenda [como criar uma nova conta de armazenamento](https://aka.ms/blobdocscreatestorageaccount).

2. Crie um contêiner na conta de armazenamento para o qual exportar seus dados da IoT Central. Navegue até a conta de armazenamento -> Procurar Blobs e, em seguida, crie um novo contêiner. ![Criar uma imagem de contêiner](media/howto-export-data/createcontainer.png)

3. Conecte-se ao seu aplicativo da IoT Central usando a mesma conta do Azure.
1. Navegue até Administração -> Exportação Contínua de Dados.
![CDE da IoT Central](media/howto-export-data/continuousdataexport.PNG)
1. Usando os menus suspensos, selecione sua conta de armazenamento e o contêiner. Em seguida, use os botões de alternância para ativar ou desativar os diferentes tipos de dados a serem exportados.
1. Por fim, ative a Exportação Contínua de Dados usando o botão de alternância e pressione "Salvar".
1. Aguarde alguns minutos e você deverá ver os dados aparecerem na conta de armazenamento. Você poderá navegar até sua conta de armazenamento, selecionar Procurar blobs, selecionar seu contêiner, e você verá três pastas. Os caminhos padrão para os arquivos do AVRO que contém os diferentes tipos de dados são:
- Mensagens: **{contêiner}/measurements/{nomedohub}/{YYYY}/{MM}/{dd}/{hh}/{mm}/00.avro**
- Dispositivos: **{contêiner}/devices/{nomedohub}/{YYYY}/{MM}/{dd}/{hh}/{mm}/00.avro**
- Modelos de dispositivo: **{contêiner}/deviceTemplates/{nomedohub}/{YYYY}/{MM}/{dd}/{hh}/{mm}/00.avro**

## <a name="how-to-read-exported-avro-files"></a>Como ler arquivos do AVRO exportados

O AVRO é um formato binário, então os arquivos não podem ser lidos em seu estado bruto. Eles podem ser decodificados para o formato JSON. Os exemplos a seguir mostram como analisar os arquivos do AVRO de medidas, dispositivos e modelos de dispositivo usando os exemplos acima.

## <a name="python"></a>Python

### <a name="install-pandas-and-the-pandaavro-package"></a>Instale o Pandas e o pacote PandaAvro:

```python
pip install pandas
pip install pandavro
```
### <a name="parse-measurements-avro-file"></a>Analisar o arquivo do AVRO de medidas

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the avro file into a pandas DataFrame where each record is
    # a single row
    measurements = pdx.from_avro(filePath)

    # In this example, we create a new DataFrame and load a series for each
    # column we map into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The SystemProperties column contains a dictionary with the device id
    # located under the "connectionDeviceId" key.
    transformed["device_id"] = measurements["SystemProperties"].apply(lambda x: x["connectionDeviceId"])

    # The Body column is a series of utf-8 bytes that is stringified and parsed
    # as json. In this example, we pull the "humidity" property off of each one
    # to get the humidity field.
    transformed["humidity"] = measurements["Body"].apply(lambda x: json.loads(bytes(x).decode('utf-8'))["humidity"])

    # Finally, we print the new DataFrame with our device ids and humidities
    print(transformed)

```
### <a name="parse-devices-avro-file"></a>Analisar o arquivo do AVRO de dispositivos

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the avro file into a pandas DataFrame where each record is
    # a single row
    devices = pdx.from_avro(filePath)

    # In this example, we create a new DataFrame and load a series for each
    # column we map into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The device id is available directly in the "id" column
    transformed["device_id"] = devices["id"]

    # The template id and version are present in a dictionary under the
    # deviceTemplate column
    transformed["template_id"] = devices["deviceTemplate"].apply(lambda x: x["id"])
    transformed["template_version"] = devices["deviceTemplate"].apply(lambda x: x["version"])

    # The fanSpeed setting value is located in a nested dictionary under the
    # settings column
    transformed["fan_speed"] = devices["settings"].apply(lambda x: x["device"]["fanSpeed"])

    # Finally, we print the new DataFrame with our device and template
    # information, along with the value of the fan speed
    print(transformed)

```

### <a name="parse-device-templates-avro-file"></a>Analisar o arquivo do AVRO de modelos de dispositivo

```python
import json
import pandavro as pdx
import pandas as pd

def parse(filePath):
    # Pandavro loads the avro file into a pandas DataFrame where each record is
    # a single row
    templates = pdx.from_avro(filePath)

    # In this example, we create a new DataFrame and load a series for each
    # column we map into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The template and version are available directly in the "id" and "version"
    # columns, respectively
    transformed["template_id"] = templates["id"]
    transformed["template_version"] = templates["version"]

    # The fanSpeed setting value is located in a nested dictionary under the
    # settings column
    transformed["fan_speed"] = templates["settings"].apply(lambda x: x["device"]["fanSpeed"])

    # Finally, we print the new DataFrame with our device and template
    # information, along with the value of the fan speed
    print(transformed)
```

## <a name="c"></a>C#

### <a name="install-microsofthadoopavro"></a>Instalar o Microsoft.Hadoop.Avro

```csharp
Install-Package Microsoft.Hadoop.Avro -Version 1.5.6
```

### <a name="parse-measurements-avro-file"></a>Analisar o arquivo do AVRO de medidas

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;
using Newtonsoft.Json;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro Container, it may contain multiple blocks
            // Loop through each block within the container
            while (reader.MoveNext())
            {
                // Loop through Avro record inside the block and extract the fields
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    var systemProperties = record.GetField<IDictionary<string, object>>("SystemProperties");
                    var deviceId = systemProperties["connectionDeviceId"] as string;
                    Console.WriteLine("Device ID: {0}", deviceId);

                    using (var stream = new MemoryStream(record.GetField<byte[]>("Body")))
                    {
                        using (var streamReader = new StreamReader(stream, Encoding.UTF8))
                        {
                            var body = JsonSerializer.Create().Deserialize(streamReader, typeof(IDictionary<string, dynamic>)) as IDictionary<string, dynamic>;
                            var humidity = body["humidity"];
                            Console.WriteLine("Humidity: {0}", humidity);
                        }
                    }
                }
            }
        }
    }
}
```

### <a name="parse-devices-avro-file"></a>Analisar o arquivo do AVRO de dispositivos

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro Container, it may contains multiple blocks
            // Loop through each block within the container
            while (reader.MoveNext())
            {
                // Loop through Avro record inside the block and extract the
                // fields from it
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    // Get the field value directly. You can also yield return
                    // record and make the function IEnumerable<AvroRecord>
                    var deviceId = record.GetField<string>("id");

                    // The device template information is stored in a sub-record
                    // under the "deviceTemplate" field.
                    var deviceTemplateRecord = record.GetField<AvroRecord>("deviceTemplate");
                    var templateId = deviceTemplateRecord.GetField<string>("id");
                    var templateVersion = deviceTemplateRecord.GetField<string>("version");

                    // The settings and properties are nested two levels deep,
                    // with the first level indicating settings or properties
                    // and the second indicating the kind of setting/property
                    var settingsRecord = record.GetField<AvroRecord>("settings");
                    var deviceSettingsRecord = settingsRecord.GetField<IDictionary<string, dynamic>>("device");
                    var fanSpeed = deviceSettingsRecord["fanSpeed"];
                    
                    Console.WriteLine(
                        "ID: {0}, Template ID: {1}, Template Version: {2}, Fan Speed: {3}",
                        deviceId,
                        templateId,
                        templateVersion,
                        fanSpeed
                    );
                }
            }
        }
    }
}

```
### <a name="parse-device-templates-avro-file"></a>Analisar o arquivo do AVRO de modelos de dispositivo

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro Container, it may contains multiple blocks
            // Loop through each block within the container
            while (reader.MoveNext())
            {
                // Loop through Avro record inside the block and extract the
                // fields from it
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    // Get the field value directly. You can also yield return
                    // record and make the function IEnumerable<AvroRecord>
                    var id = record.GetField<string>("id");
                    var version = record.GetField<string>("version");

                    // The settings and properties are nested two levels deep,
                    // with the first level indicating settings or properties
                    // and the second indicating the kind of setting/property
                    var settingsRecord = record.GetField<AvroRecord>("settings");
                    var deviceSettingsRecord = settingsRecord.GetField<IDictionary<string, dynamic>>("device");
                    var fanSpeed = deviceSettingsRecord["fanSpeed"];
                    
                    Console.WriteLine(
                        "ID: {1}, Version: {2}, Fan Speed: {3}",
                        id,
                        version,
                        fanSpeed
                    );
                }
            }
        }
    }
}
```

## <a name="javascript"></a>JavaScript

### <a name="install-avsc"></a>Instalar o avsc

```javascript
npm install avsc
```

### <a name="parse-measurements-avro-file"></a>Analisar o arquivo do AVRO de medidas

```javascript
const avro = require('avsc');

// Read the avro file and parse the device id and humidity from each record
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the device id from the system properties
        const deviceId = record.SystemProperties.connectionDeviceId;

        // Convert the Body from a Buffer to a string and parse it
        const body = JSON.parse(record.Body.toString());

        // Get the humidty property off of the body
        const humidity = body.humidity;

        // Log the device id and humidity we found
        console.log(`Device ID: ${deviceId}`);
        console.log(`Humidity: ${humidity}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream. We
        // collect them up into an array and return them when we get to the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

### <a name="parse-devices-avro-file"></a>Analisar o arquivo do AVRO de dispositivos

```javascript
const avro = require('avsc');

// Read the avro file and parse the device and template identification info as
// well as the fanSpeed setting for each device record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the device id from the id property
        const deviceId = record.id;

        // Fetch the tempalte id and version from the deviceTemplate property
        const deviceTemplateId = record.deviceTemplate.id;
        const deviceTemplateVersion = record.deviceTemplate.version;

        // Get the fanSpeed off the nested device settings property
        const fanSpeed = record.settings.device.fanSpeed;

        // Log the device id and humidity we found
        console.log(`ID: ${deviceId}, Template ID: ${deviceTemplateId}, Template Version: ${deviceTemplateVersion}, Fan Speed: ${fanSpeed}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream. We
        // collect them up into an array and return them when we get to the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

### <a name="parse-device-templates-avro-file"></a>Analisar o arquivo do AVRO de modelos de dispositivo

```javascript
const avro = require('avsc');

// Read the avro file and parse the device and template identification info as
// well as the fanSpeed setting for each device record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the template id and version from the id and verison properties
        const templateId = record.id;
        const templateVersion = record.version;

        // Get the fanSpeed off the nested device settings property
        const fanSpeed = record.settings.device.fanSpeed;

        // Log the device id and humidity we found
        console.log(`Template ID: ${templateId}, Template Version: ${templateVersion}, Fan Speed: ${fanSpeed}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream. We
        // collect them up into an array and return them when we get to the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

## <a name="next-steps"></a>Próximas etapas

Saiba como [gerenciar seus dispositivos](howto-manage-devices.md) no Device Explorer. 
