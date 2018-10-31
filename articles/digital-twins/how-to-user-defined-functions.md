---
title: Como usar funções definidas pelo usuário em Gêmeos Digitais do Azure | Microsoft Docs
description: Orientação sobre como criar funções definidas pelo usuário, correspondentes e atribuições de função com Gêmeos Digitais do Azure.
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: alinast
ms.openlocfilehash: 7fbaff5ed1b60a4434ba2eb0c78c6aa1f3fd6645
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323676"
---
# <a name="how-to-use-user-defined-functions-in-azure-digital-twins"></a>Como usar funções definidas pelo usuário em Gêmeos Digitais do Azure

[Funções definidas pelo usuário](./concepts-user-defined-functions.md) permitem ao usuário executar lógica personalizada em mensagens de telemetria de entrada e os metadados de grafo espacial, permitindo que o usuário envie eventos para pontos de extremidade predefinidos. Neste guia, vamos examinar um exemplo de agir em eventos de temperatura para detectar e alertar sobre qualquer leitura que exceda uma determinada temperatura.

Nos exemplos a seguir, `https://yourManagementApiUrl` refere-se ao URI de APIs de Gêmeos Digitais:

```plaintext
https://yourInstanceName.yourLocation.azuresmartspaces.net/management
```

| Nome do Atributo Personalizado | Substitua por |
| --- | --- |
| `yourInstanceName` | O nome da sua instância de Gêmeos Digitais do Azure |
| `yourLocation` | Em qual região do servidor de sua instância está hospedada |

## <a name="client-library-reference"></a>Referência da biblioteca de clientes

As funções disponíveis como métodos auxiliares no tempo de execução de funções definidas pelo usuário são enumeradas na seguinte [Referência de Cliente](#Client-Reference).

## <a name="create-a-matcher"></a>Criar um correspondente

Correspondentes são objetos de grafo, que determinam quais funções definidas pelo usuário serão executadas para uma mensagem de telemetria determinada.

Comparações de condição de correspondente válidas:

- `Equals`
- `NotEquals`
- `Contains`

Destinos de condição de correspondente válidos:

- `Sensor`
- `SensorDevice`
- `SensorSpace`

O correspondente de exemplo a seguir será avaliado como verdadeiro em qualquer evento de telemetria do sensor com `Temperature` como valor de tipo de dados. Você pode criar vários correspondentes em uma função definida pelo usuário.

```text
POST https://yourManagementApiUrl/api/v1.0/matchers
{
  "Name": "Temperature Matcher",
  "Conditions": [
    {
      "target": "Sensor",
      "path": "$.dataType",
      "value": "\"Temperature\"",
      "comparison": "Equals"
    }
  ],
  "SpaceId": "yourSpaceIdentifier"
}
```

| Nome do Atributo Personalizado | Substitua por |
| --- | --- |
| `yourManagementApiUrl` | O caminho da URL completo para a API de Gerenciamento  |
| `yourSpaceIdentifier` | Em qual região do servidor de sua instância está hospedada |

## <a name="create-a-user-defined-function-udf"></a>Criar uma UDF (função definida pelo usuário)

Após os correspondentes terem sido criados, carregue o snippet de código de função com a seguinte chamada POST:

> [!IMPORTANT]
> - Nos Cabeçalhos, defina o seguinte `Content-Type: multipart/form-data; boundary="userDefinedBoundary"`.
> - O Corpo tem várias partes:
>   - A primeira parte é sobre os metadados necessários para o UDF.
>   - A segunda parte é a lógica de computação em JavaScript.
> - Substitua em `userDefinedBoundary` seção `SpaceId` e GUIDs `Machers`.

```plaintext
POST https://yourManagementApiUrl/api/v1.0/userdefinedfunctions with Content-Type: multipart/form-data; boundary="userDefinedBoundary"
```

| Nome do Atributo Personalizado | Substitua por |
| --- | --- |
| `yourManagementApiUrl` | O caminho da URL completo para a API de Gerenciamento  |

Corpo:

```plaintext
--userDefinedBoundary
Content-Type: application/json; charset=utf-8
Content-Disposition: form-data; name="metadata"

{
  "SpaceId": "yourSpaceIdentifier",
  "Name": "User Defined Function",
  "Description": "The contents of this udf will be executed when matched against incoming telemetry.",
  "Matchers": ["yourMatcherIdentifier"]
}
--userDefinedBoundary
Content-Disposition: form-data; name="contents"; filename="userDefinedFunction.js"
Content-Type: text/javascript

function process(telemetry, executionContext) {
  // Code goes here.
}

--userDefinedBoundary--
```

| Nome do Atributo Personalizado | Substitua por |
| --- | --- |
| `yourSpaceIdentifier` | O identificador de espaço  |
| `yourMatcherIdentifier` | A ID do correspondente que você deseja usar |

### <a name="example-functions"></a>Funções de exemplo

Defina a telemetria de sensor com o tipo de dados de leitura diretamente para o sensor `Temperature`, que é sensor.DataType:

```javascript
function process(telemetry, executionContext) {

  // Get sensor metadata
  var sensor = getSensorMetadata(telemetry.SensorId);

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Set the sensor reading as the current value for the sensor.
  setSensorValue(telemetry.SensorId, sensor.DataType, parseReading.SensorValue);
}
```

Registre uma mensagem em log se a leitura de telemetria do sensor ultrapassar um limite predefinido. Se as configurações de diagnóstico estiverem habilitadas na instância de Gêmeos Digitais, funções definidas pelo usuário serão encaminhadas:

```javascript
function process(telemetry, executionContext) {

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Example sensor telemetry reading range is greater than 0.5
  if(parseFloat(parseReading.SensorValue) > 0.5) {
    log(`Alert: Sensor with ID: ${telemetry.SensorId} detected an anomaly!`);
  }
}
```

O código a seguir disparará uma notificação se o nível de temperatura subir acima a constante predefinida.

```javascript
function process(telemetry, executionContext) {

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Define threshold
  var threshold = 70;

  // Trigger notification 
  if(parseInt(parseReading) > threshold) {
    var alert = {
      message: 'Temperature reading has surpassed threshold',
      sensorId: telemetry.SensorId,
      reading: parseReading
    };

    sendNotification(telemetry.SensorId, "Sensor", JSON.stringify(alert));
  }
}
```

Para obter um exemplo de código UDF mais complexo, veja [Verificar espaços disponíveis com UDF de ar fresco](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availability.js)

## <a name="create-a-role-assignment"></a>Criar uma atribuição de função

É necessário criar uma atribuição de função para a função definida pelo usuário sob a qual executar. Se não fizermos isso, ela não terá as permissões corretas para interagir com a API de Gerenciamento para executar ações nos objetos de grafo. As ações que função definida pelo usuário executa não são isentas do controle de acesso baseado em função nas APIs de Gerenciamento de Gêmeos Digitais. Elas podem ter escopo limitado especificando determinadas funções ou determinados caminhos de controle de acesso. Para saber mais, confira a documentação de [Controle de Acesso Baseado em Função](./security-role-based-access-control.md).

- Consulte funções e obtenha a ID da função que você deseja atribuir ao UDF; passe-a para RoleId abaixo.

```plaintext
GET https://yourManagementApiUrl/api/v1.0/system/roles
```

| Nome do Atributo Personalizado | Substitua por |
| --- | --- |
| `yourManagementApiUrl` | O caminho da URL completo para a API de Gerenciamento  |

- ObjectId será a ID do UDF criado anteriormente
- Localize `Path` consultando os Espaços com o caminho completo e copie o valor `spacePaths`. Cole-o no Caminho abaixo ao criar a atribuição de função UDF

```plaintext
GET https://yourManagementApiUrl/api/v1.0/spaces?name=yourSpaceName&includes=fullpath
```

| Nome do Atributo Personalizado | Substitua por |
| --- | --- |
| `yourManagementApiUrl` | O caminho da URL completo para a API de Gerenciamento  |
| `yourSpaceName` | O nome do espaço que você deseja usar |

```plaintext
POST https://yourManagementApiUrl/api/v1.0/roleassignments
{
  "RoleId": "yourDesiredRoleIdentifier",
  "ObjectId": "yourUserDefinedFunctionId",
  "ObjectIdType": "UserDefinedFunctionId",
  "Path": "yourAccessControlPath"
}
```

| Nome do Atributo Personalizado | Substitua por |
| --- | --- |
| `yourManagementApiUrl` | O caminho da URL completo para a API de Gerenciamento  |
| `yourDesiredRoleIdentifier` | O identificador para a função desejada |
| `yourUserDefinedFunctionId` | A ID para o UDF que você deseja usar |
| `yourAccessControlPath` | O caminho de controle de acesso |

## <a name="send-telemetry-to-be-processed"></a>Enviar telemetria para ser processada

A telemetria gerada pelo sensor descrito no grafo deve disparar a execução da função definida pelo usuário que foi carregada. Depois que a telemetria for captada pelo processador de dados, um plano de execução será criado para a invocação da função definida pelo usuário.

1. Recupere os correspondentes para o sensor do qual a leitura foi gerada.
1. Dependendo do quais correspondentes foram avaliados com sucesso, recupere as funções definidas pelo usuário associadas.
1. Execute cada função definida pelo usuário.

## <a name="client-reference"></a>Referência do cliente

### <a name="getspacemetadataid--space"></a>getSpaceMetadata(id) ⇒ `space`

Dado um identificador de espaço, recupera o espaço do grafo.

**Tipo**: função global

| Param  | Tipo                | DESCRIÇÃO  |
| ------ | ------------------- | ------------ |
| ID  | `guid` | identificador de espaço |

### <a name="getsensormetadataid--sensor"></a>getSensorMetadata(id) ⇒ `sensor`

Dado um identificador de sensor, recupera o sensor do grafo.

**Tipo**: função global

| Param  | Tipo                | DESCRIÇÃO  |
| ------ | ------------------- | ------------ |
| ID  | `guid` | identificador de sensor |

### <a name="getdevicemetadataid--device"></a>getDeviceMetadata(id) ⇒ `device`

Dado um identificador de dispositivo, recupera o dispositivo do grafo.

**Tipo**: função global

| Param  | Tipo                | DESCRIÇÃO  |
| ------ | ------------------- | ------------ |
| ID  | `guid` | identificador de dispositivo |

### <a name="getsensorvaluesensorid-datatype--value"></a>getSensorValue(sensorId, dataType) ⇒ `value`

Dado um identificador de sensor e seu tipo de dados, recupere o valor atual para aquele sensor.

**Tipo**: função global

| Param  | Tipo                | DESCRIÇÃO  |
| ------ | ------------------- | ------------ |
| sensorId  | `guid` | identificador de sensor |
| dataType  | `string` | tipo de dados de sensor |

### <a name="getspacevaluespaceid-valuename--value"></a>getSpaceValue(spaceId, valueName) ⇒ `value`

Dado um identificador de espaço e o nome do valor, recupere o valor atual para aquela propriedade de espaço.

**Tipo**: função global

| Param  | Tipo                | DESCRIÇÃO  |
| ------ | ------------------- | ------------ |
| spaceId  | `guid` | identificador de espaço |
| valueName  | `string` | nome da propriedade de espaço |

### <a name="getsensorhistoryvaluessensorid-datatype--value"></a>getSensorHistoryValues(sensorId, dataType) ⇒ `value[]`

Dado um identificador de sensor e seu tipo de dados, recupere os valores históricos para aquele sensor.

**Tipo**: função global

| Param  | Tipo                | DESCRIÇÃO  |
| ------ | ------------------- | ------------ |
| sensorId  | `guid` | identificador de sensor |
| dataType  | `string` | tipo de dados de sensor |

### <a name="getspacehistoryvaluesspaceid-datatype--value"></a>getSpaceHistoryValues(spaceId, dataType) ⇒ `value[]`

Dado um identificador de espaço e o nome do valor, recupere os valores históricos para aquela propriedade no espaço.

**Tipo**: função global

| Param  | Tipo                | DESCRIÇÃO  |
| ------ | ------------------- | ------------ |
| spaceId  | `guid` | identificador de espaço |
| valueName  | `string` | nome da propriedade de espaço |

### <a name="getspacechildspacesspaceid--space"></a>getSpaceChildSpaces(spaceId) ⇒ `space[]`

Dado um identificador de espaço, recupere os espaços filho para aquele espaço pai.

**Tipo**: função global

| Param  | Tipo                | DESCRIÇÃO  |
| ------ | ------------------- | ------------ |
| spaceId  | `guid` | identificador de espaço |

### <a name="getspacechildsensorsspaceid--sensor"></a>getSpaceChildSensors(spaceId) ⇒ `sensor[]`

Dado um identificador de espaço, recupere os sensores filho para aquele espaço pai.

**Tipo**: função global

| Param  | Tipo                | DESCRIÇÃO  |
| ------ | ------------------- | ------------ |
| spaceId  | `guid` | identificador de espaço |

### <a name="getspacechilddevicesspaceid--device"></a>getSpaceChildDevices(spaceId) ⇒ `device[]`

Dado um identificador de espaço, recupere os dispositivos filho para aquele espaço pai.

**Tipo**: função global

| Param  | Tipo                | DESCRIÇÃO  |
| ------ | ------------------- | ------------ |
| spaceId  | `guid` | identificador de espaço |

### <a name="getdevicechildsensorsdeviceid--sensor"></a>getDeviceChildSensors(deviceId) ⇒ `sensor[]`

Dado um identificador de dispositivo, recupere os sensores filho para aquele dispositivo pai.

**Tipo**: função global

| Param  | Tipo                | DESCRIÇÃO  |
| ------ | ------------------- | ------------ |
| deviceId  | `guid` | identificador de dispositivo |

### <a name="getspaceparentspacechildspaceid--space"></a>getSpaceParentSpace(childSpaceId) ⇒ `space`

Dado um identificador de espaço, recupere seu espaço pai.

**Tipo**: função global

| Param  | Tipo                | DESCRIÇÃO  |
| ------ | ------------------- | ------------ |
| childSpaceId  | `guid` | identificador de espaço |

### <a name="getsensorparentspacechildsensorid--space"></a>getSensorParentSpace(childSensorId) ⇒ `space`

Dado um identificador de sensor, recupere seu espaço pai.

**Tipo**: função global

| Param  | Tipo                | DESCRIÇÃO  |
| ------ | ------------------- | ------------ |
| childSensorId  | `guid` | identificador de sensor |

### <a name="getdeviceparentspacechilddeviceid--space"></a>getDeviceParentSpace(childDeviceId) ⇒ `space`

Dado um identificador de dispositivo, recupere seu espaço pai.

**Tipo**: função global

| Param  | Tipo                | DESCRIÇÃO  |
| ------ | ------------------- | ------------ |
| childDeviceId  | `guid` | identificador de dispositivo |

### <a name="getsensorparentdevicechildsensorid--space"></a>getSensorParentDevice(childSensorId) ⇒ `space`

Dado um identificador de sensor, recupere seu dispositivo pai.

**Tipo**: função global

| Param  | Tipo                | DESCRIÇÃO  |
| ------ | ------------------- | ------------ |
| childSensorId  | `guid` | identificador de sensor |

### <a name="getspaceextendedpropertyspaceid-propertyname--extendedproperty"></a>getSpaceExtendedProperty(spaceId, propertyName) ⇒ `extendedProperty`

Dado um identificador de espaço, recupere a propriedade e seu valor do espaço.

**Tipo**: função global

| Param  | Tipo                | DESCRIÇÃO  |
| ------ | ------------------- | ------------ |
| spaceId  | `guid` | identificador de espaço |
| propertyName  | `string` | nome da propriedade de espaço |

### <a name="getsensorextendedpropertysensorid-propertyname--extendedproperty"></a>getSensorExtendedProperty(sensorId, propertyName) ⇒ `extendedProperty`

Dado um identificador de sensor, recupere a propriedade e seu valor do sensor.

**Tipo**: função global

| Param  | Tipo                | DESCRIÇÃO  |
| ------ | ------------------- | ------------ |
| sensorId  | `guid` | identificador de sensor |
| propertyName  | `string` | nome da propriedade do sensor |

### <a name="getdeviceextendedpropertydeviceid-propertyname--extendedproperty"></a>getDeviceExtendedProperty(deviceId, propertyName) ⇒ `extendedProperty`

Dado um identificador de dispositivo, recupere a propriedade e seu valor do dispositivo.

**Tipo**: função global

| Param  | Tipo                | DESCRIÇÃO  |
| ------ | ------------------- | ------------ |
| deviceId  | `guid` | identificador de dispositivo |
| propertyName  | `string` | nome da propriedade do dispositivo |

### <a name="setsensorvaluesensorid-datatype-value"></a>setSensorValue(sensorId, dataType, value)

Define um valor no objeto do sensor com o tipo de dados fornecido.

**Tipo**: função global

| Param  | Tipo                | DESCRIÇÃO  |
| ------ | ------------------- | ------------ |
| sensorId  | `guid` | identificador de sensor |
| dataType  | `string` | tipo de dados de sensor |
| value  | `string` | value |

### <a name="setspacevaluespaceid-datatype-value"></a>setSpaceValue(spaceId, dataType, value)

Define um valor no objeto do espaço com o tipo de dados fornecido.

**Tipo**: função global

| Param  | Tipo                | DESCRIÇÃO  |
| ------ | ------------------- | ------------ |
| spaceId  | `guid` | identificador de espaço |
| dataType  | `string` | tipo de dados |
| value  | `string` | value |

### <a name="logmessage"></a>log(message)

Registra em log a seguinte mensagem dentro da função definida pelo usuário.

**Tipo**: função global

| Param  | Tipo                | DESCRIÇÃO  |
| ------ | ------------------- | ------------ |
| message  | `string` | mensagem a ser registrada em log |

### <a name="sendnotificationtopologyobjectid-topologyobjecttype-payload"></a>sendNotification(topologyObjectId, topologyObjectType, payload)

Envia uma notificação personalizada a ser expedida.

**Tipo**: função global

| Param  | Tipo                | DESCRIÇÃO  |
| ------ | ------------------- | ------------ |
| topologyObjectId  | `Guid` | identificador de objeto do grafo (por exemplo, espaço/sensor/ID do dispositivo)|
| topologyObjectType  | `string` | (ex. espaço/sensor/dispositivo)|
| payload  | `string` | o payload JSON a ser enviado com a notificação |

## <a name="return-types"></a>Tipos de retorno

Estes são os modelos que descrevem os objetos de retorno da referência do cliente acima.

### <a name="space"></a>Espaço

```JSON
{
  "Id": "00000000-0000-0000-0000-000000000000",
  "Name": "Space",
  "FriendlyName": "Conference Room",
  "TypeId": 0,
  "ParentSpaceId": "00000000-0000-0000-0000-000000000001",
  "SubtypeId": 0
}
```

### <a name="space-methods"></a>Métodos de Espaço

#### <a name="parent--space"></a>Parent() ⇒ `space`

Retorna o espaço do pai do espaço atual.

#### <a name="childsensors--sensor"></a>ChildSensors() ⇒ `sensor[]`

Retorna os sensores filho do espaço atual.

#### <a name="childdevices--device"></a>ChildDevices() ⇒ `device[]`

Retorna os dispositivos filho do espaço atual.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

Retorna a propriedade estendida e seu valor para o espaço atual.

| Param  | Tipo                | DESCRIÇÃO  |
| ------ | ------------------- | ------------ |
| propertyName | `string` | nome da propriedade estendida |

#### <a name="valuevaluename--value"></a>Value(valueName) ⇒ `value`

Retorna o valor do espaço atual.

| Param  | Tipo                | DESCRIÇÃO  |
| ------ | ------------------- | ------------ |
| valueName | `string` | nome do valor |

#### <a name="historyvaluename--value"></a>History(valueName) ⇒ `value[]`

Retorna os valores históricos do espaço atual.

| Param  | Tipo                | DESCRIÇÃO  |
| ------ | ------------------- | ------------ |
| valueName | `string` | nome do valor |

#### <a name="notifypayload"></a>Notify(payload)

Envia uma notificação com o payload especificado.

| Param  | Tipo                | DESCRIÇÃO  |
| ------ | ------------------- | ------------ |
| payload | `string` | payload JSON a ser incluído na notificação |

### <a name="device"></a>Dispositivo

```JSON
{
  "Id": "00000000-0000-0000-0000-000000000002",
  "Name": "Device",
  "FriendlyName": "Temperature Sensing Device",
  "Description": "This device contains a sensor that captures temperature readings.",
  "Type": "None",
  "Subtype": "None",
  "TypeId": 0,
  "SubtypeId": 0,
  "HardwareId": "ABC123",
  "GatewayId": "ABC",
  "SpaceId": "00000000-0000-0000-0000-000000000000"
}
```

### <a name="device-methods"></a>Métodos do Dispositivo

#### <a name="parent--space"></a>Parent() ⇒ `space`

Retorna o espaço do pai do dispositivo atual.

#### <a name="childsensors--sensor"></a>ChildSensors() ⇒ `sensor[]`

Retorna os sensores filho do dispositivo atual.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

Retorna a propriedade estendida e seu valor para o dispositivo atual.

| Param  | Tipo                | DESCRIÇÃO  |
| ------ | ------------------- | ------------ |
| propertyName | `string` | nome da propriedade estendida |

#### <a name="notifypayload"></a>Notify(payload)

Envia uma notificação com o payload especificado.

| Param  | Tipo                | DESCRIÇÃO  |
| ------ | ------------------- | ------------ |
| payload | `string` | payload JSON a ser incluído na notificação |

### <a name="sensor"></a>Sensor

```JSON
{
  "Id": "00000000-0000-0000-0000-000000000003",
  "Port": "30",
  "PollRate": 3600,
  "DataType": "Temperature",
  "DataSubtype": "None",
  "Type": "Classic",
  "PortType": "None",
  "DataUnitType": "FahrenheitTemperature",
  "SpaceId": "00000000-0000-0000-0000-000000000000",
  "DeviceId": "00000000-0000-0000-0000-000000000001",
  "PortTypeId": 0,
  "DataUnitTypeId": 0,
  "DataTypeId": 0,
  "DataSubtypeId": 0,
  "TypeId": 0  
}
```

### <a name="sensor-methods"></a>Métodos do Sensor

#### <a name="space--space"></a>Space() ⇒ `space`

Retorna o espaço do pai do sensor atual.

#### <a name="device--device"></a>Device() ⇒ `device`

Retorna o dispositivo pai do sensor atual.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

Retorna a propriedade estendida e seu valor para o sensor atual.

| Param  | Tipo                | DESCRIÇÃO  |
| ------ | ------------------- | ------------ |
| propertyName | `string` | nome da propriedade estendida |

#### <a name="value--value"></a>Value() ⇒ `value`

Retorna o valor do sensor atual.

#### <a name="history--value"></a>History() ⇒ `value[]`

Retorna os valores históricos do sensor atual.

#### <a name="notifypayload"></a>Notify(payload)

Envia uma notificação com o payload especificado.

| Param  | Tipo                | DESCRIÇÃO  |
| ------ | ------------------- | ------------ |
| payload | `string` | payload JSON a ser incluído na notificação |

### <a name="value"></a>Valor

```JSON
{
  "DataType": "Temperature",
  "Value": "70",
  "CreatedTime": ""
}
```

### <a name="extended-property"></a>Propriedade estendida

```JSON
{
  "Name": "OccupancyStatus",
  "Value": "Occupied"
}
```

## <a name="next-steps"></a>Próximas etapas

Para saber como criar pontos de extremidade de Gêmeos Digitais para os quais enviar eventos, leia [Criar pontos de extremidade de Gêmeos Digitais](how-to-egress-endpoints.md).

Para obter mais detalhes sobre pontos de extremidade de Gêmeos Digitais, leia [Saiba mais sobre pontos de extremidade](concepts-events-routing.md).
