---
title: Como usar funções definidas pelo usuário em Gêmeos Digitais do Azure | Microsoft Docs
description: Diretrizes sobre como criar funções definidas pelo usuário, correspondentes e atribuições de função com Gêmeos Digitais do Azure.
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: alinast
ms.openlocfilehash: 6a757dca48dc3ff41adfe6f8802fad40e7a4ca81
ms.sourcegitcommit: 542964c196a08b83dd18efe2e0cbfb21a34558aa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51636825"
---
# <a name="how-to-use-user-defined-functions-in-azure-digital-twins"></a>Como usar funções definidas pelo usuário em Gêmeos Digitais do Azure

[UDF](./concepts-user-defined-functions.md) (Funções Definidas pelo Usuário) permitem que o usuário execute uma lógica personalizada em relação às mensagens de telemetria recebidas e aos metadados de grafo espacial. Em seguida, o usuário poderá enviar eventos para pontos de extremidade predefinidos. Este guia descreve um exemplo de como agir em eventos de temperatura para detectar e alertar sobre qualquer leitura que exceda uma determinada temperatura.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

## <a name="client-library-reference"></a>Referência da biblioteca de clientes

As funções que estão disponíveis como métodos auxiliares no tempo de execução das funções definidas pelo usuário são listadas na seção de [referência do cliente](#Client-Reference).

## <a name="create-a-matcher"></a>Criar um correspondente

Correspondentes são objetos de grafo que determinam quais funções definidas pelo usuário são executadas para uma determinada mensagem de telemetria.

- Comparações de condição de correspondente válidas:

  - `Equals`
  - `NotEquals`
  - `Contains`

- Destinos de condição de correspondente válidos:

  - `Sensor`
  - `SensorDevice`
  - `SensorSpace`

O correspondente de exemplo a seguir é avaliado como verdadeiro em qualquer evento de telemetria do sensor `"Temperature"` como valor de tipo de dados. Você pode criar vários correspondentes em uma função definida pelo usuário:

```plaintext
POST YOUR_MANAGEMENT_API_URL/matchers
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
  "SpaceId": "YOUR_SPACE_IDENTIFIER"
}
```

| Valor | Substitua por |
| --- | --- |
| YOUR_SPACE_IDENTIFIER | Em qual região do servidor de sua instância está hospedada |

## <a name="create-a-user-defined-function-udf"></a>Criar uma UDF (função definida pelo usuário)

Depois que os correspondentes forem criados, carregue o trecho de código de função com a seguinte chamada **POST**:

> [!IMPORTANT]
> - Nos Cabeçalhos, defina o seguinte `Content-Type: multipart/form-data; boundary="userDefinedBoundary"`.
> - Corpo com diversas partes:
>   - A primeira parte é sobre os metadados necessários para a UDF.
>   - A segunda parte é a lógica de computação JavaScript.
> - Na seção **USER_DEFINED_BOUNDARY** substitua os valores **SpaceId** e **Machers**.

```plaintext
POST YOUR_MANAGEMENT_API_URL/userdefinedfunctions with Content-Type: multipart/form-data; boundary="USER_DEFINED_BOUNDARY"
```

| Valor de parâmetro | Substitua por |
| --- | --- |
| *USER_DEFINED_BOUNDARY* | Um nome de limite de conteúdo com diversas partes |

### <a name="body"></a>Corpo

```plaintext
--USER_DEFINED_BOUNDARY
Content-Type: application/json; charset=utf-8
Content-Disposition: form-data; name="metadata"

{
  "SpaceId": "YOUR_SPACE_IDENTIFIER",
  "Name": "User Defined Function",
  "Description": "The contents of this udf will be executed when matched against incoming telemetry.",
  "Matchers": ["YOUR_MATCHER_IDENTIFIER"]
}
--USER_DEFINED_BOUNDARY
Content-Disposition: form-data; name="contents"; filename="userDefinedFunction.js"
Content-Type: text/javascript

function process(telemetry, executionContext) {
  // Code goes here.
}

--USER_DEFINED_BOUNDARY--
```

| Valor | Substitua por |
| --- | --- |
| YOUR_SPACE_IDENTIFIER | O identificador de espaço  |
| YOUR_MATCHER_IDENTIFIER | A ID do correspondente que você quer usar |

### <a name="example-functions"></a>Funções de exemplo

Defina a leitura da telemetria do sensor diretamente para o sensor com o tipo de dados **Temperatura**, que é `sensor.DataType`:

```JavaScript
function process(telemetry, executionContext) {

  // Get sensor metadata
  var sensor = getSensorMetadata(telemetry.SensorId);

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Set the sensor reading as the current value for the sensor.
  setSensorValue(telemetry.SensorId, sensor.DataType, parseReading.SensorValue);
}
```

O parâmetro de **telemetria** expõe os atributos **SensorId** e **Message**, correspondendo a uma mensagem enviada por um sensor. O parâmetro **executionContext** expõe os seguintes atributos:

```csharp
var executionContext = new UdfExecutionContext
{
    EnqueuedTime = request.HubEnqueuedTime,
    ProcessorReceivedTime = request.ProcessorReceivedTime,
    UserDefinedFunctionId = request.UserDefinedFunctionId,
    CorrelationId = correlationId.ToString(),
};
```

No próximo exemplo, registramos uma mensagem se a leitura da telemetria do sensor ultrapassar um limite predefinido. Se as configurações de diagnóstico estiverem habilitadas na instância dos Gêmeos Digitais do Azures, os logs das funções definidas pelo usuário também serão encaminhados:

```JavaScript
function process(telemetry, executionContext) {

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Example sensor telemetry reading range is greater than 0.5
  if(parseFloat(parseReading.SensorValue) > 0.5) {
    log(`Alert: Sensor with ID: ${telemetry.SensorId} detected an anomaly!`);
  }
}
```

O código a seguir disparará uma notificação se o nível de temperatura ultrapassar a constante predefinida:

```JavaScript
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

Para um código de exemplo de UDF mais complexo, [verifique os espaços disponíveis com uma UDF atualizada](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availability.js).

## <a name="create-a-role-assignment"></a>Criar uma atribuição de função

É necessário criar uma atribuição de função para que a função definida pelo usuário seja executada. Se não fizermos isso, não haverá permissões adequadas para interagir com a API de Gerenciamento para executar ações em objetos de grafos. As ações que a função definida pelo usuário executa não estão isentas do controle de acesso baseado em função nas APIs do Gerenciamento de Gêmeos Digitais do Azure. Elas podem ter escopo limitado, especificando determinadas funções ou determinados caminhos de controle de acesso. Para obter mais informações, consulte a documentação de [controle de acesso baseado em função](./security-role-based-access-control.md).

1. Consulte as funções e obtenha a ID da função que você quer atribuir à UDF. Passe-a para **RoleId**:

    ```plaintext
    GET YOUR_MANAGEMENT_API_URL/system/roles
    ```

1. **ObjectId** será o ID do UDF que foi criado anteriormente.
1. Encontre o valor do **Caminho** consultando seus espaços com `fullpath`.
1. Copie o valor `spacePaths` retornado. Isso será utilizado no código a seguir:

    ```plaintext
    GET YOUR_MANAGEMENT_API_URL/spaces?name=YOUR_SPACE_NAME&includes=fullpath
    ```

    | Valor de parâmetro | Substitua por |
    | --- | --- |
    | *YOUR_SPACE_NAME* | O nome do espaço que você deseja usar |

1. Cole o valor `spacePaths` retornado no **Caminho** para criar uma atribuição de função de UDF:

    ```plaintext
    POST YOUR_MANAGEMENT_API_URL/roleassignments
    {
      "RoleId": "YOUR_DESIRED_ROLE_IDENTIFIER",
      "ObjectId": "YOUR_USER_DEFINED_FUNCTION_ID",
      "ObjectIdType": "YOUR_USER_DEFINED_FUNCTION_TYPE_ID",
      "Path": "YOUR_ACCESS_CONTROL_PATH"
    }
    ```

    | O valor | Substitua por |
    | --- | --- |
    | YOUR_DESIRED_ROLE_IDENTIFIER | O identificador para a função desejada |
    | YOUR_USER_DEFINED_FUNCTION_ID | A ID para o UDF que você deseja usar |
    | YOUR_USER_DEFINED_FUNCTION_TYPE_ID | A ID especificando o tipo de UDF |
    | YOUR_ACCESS_CONTROL_PATH | O caminho de controle de acesso |

## <a name="send-telemetry-to-be-processed"></a>Enviar telemetria para ser processada

A telemetria gerada pelo sensor descrito no gráfico dispara a execução da função definida pelo usuário que foi carregada. O processador de dados pega a telemetria. Em seguida, um plano de execução será criado para a chamada da função definida pelo usuário.

1. Recupere os correspondentes para o sensor do qual a leitura foi gerada.
1. Dependendo do quais correspondentes foram avaliados com sucesso, recupere as funções definidas pelo usuário associadas.
1. Execute cada função definida pelo usuário.

## <a name="client-reference"></a>Referência do cliente

### <a name="getspacemetadataid--space"></a>getSpaceMetadata(id) ⇒ `space`

Dado um identificador de espaço, essa função recupera o espaço do grafo.

**Tipo**: função global

| Parâmetro  | Tipo                | DESCRIÇÃO  |
| ---------- | ------------------- | ------------ |
| *ID*  | `guid` | Identificador de espaço |

### <a name="getsensormetadataid--sensor"></a>getSensorMetadata(id) ⇒ `sensor`

Dado um identificador de sensor, essa função recupera o sensor do grafo.

**Tipo**: função global

| Parâmetro  | Tipo                | DESCRIÇÃO  |
| ---------- | ------------------- | ------------ |
| *ID*  | `guid` | Identificador de sensor |

### <a name="getdevicemetadataid--device"></a>getDeviceMetadata(id) ⇒ `device`

Dado um identificador de dispositivo, essa função recupera o dispositivo do grafo.

**Tipo**: função global

| Parâmetro  | Tipo                | DESCRIÇÃO  |
| ------ | ------------------- | ------------ |
| *ID* | `guid` | Identificador de dispositivo |

### <a name="getsensorvaluesensorid-datatype--value"></a>getSensorValue(sensorId, dataType) ⇒ `value`

Dado um identificador de sensor e o tipo de dados, essa função recupera o valor atual para esse sensor.

**Tipo**: função global

| Parâmetro  | Tipo                | DESCRIÇÃO  |
| ------ | ------------------- | ------------ |
| *sensorId*  | `guid` | Identificador de sensor |
| *dataType*  | `string` | Tipo de dados de sensor |

### <a name="getspacevaluespaceid-valuename--value"></a>getSpaceValue(spaceId, valueName) ⇒ `value`

Dado um identificador de espaço e o nome do valor, essa função recupera o valor atual para essa propriedade de espaço.

**Tipo**: função global

| Parâmetro  | Tipo                | DESCRIÇÃO  |
| ------ | ------------------- | ------------ |
| *spaceId*  | `guid` | Identificador de espaço |
| *valueName* | `string` | Nome da propriedade de espaço |

### <a name="getsensorhistoryvaluessensorid-datatype--value"></a>getSensorHistoryValues(sensorId, dataType) ⇒ `value[]`

Dado um identificador de sensor e o tipo de dados, essa função recupera os valores históricos desse sensor.

**Tipo**: função global

| Parâmetro  | Tipo                | DESCRIÇÃO  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | Identificador de sensor |
| *dataType* | `string` | Tipo de dados de sensor |

### <a name="getspacehistoryvaluesspaceid-datatype--value"></a>getSpaceHistoryValues(spaceId, dataType) ⇒ `value[]`

Dado um identificador de espaço e o nome do valor, essa função recupera os valores históricos dessa propriedade no espaço.

**Tipo**: função global

| Parâmetro  | Tipo                | DESCRIÇÃO  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identificador de espaço |
| *valueName* | `string` | Nome da propriedade de espaço |

### <a name="getspacechildspacesspaceid--space"></a>getSpaceChildSpaces(spaceId) ⇒ `space[]`

Dado um identificador de espaço, essa função recupera os espaços filhos para esse espaço pai.

**Tipo**: função global

| Parâmetro  | Tipo                | DESCRIÇÃO  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identificador de espaço |

### <a name="getspacechildsensorsspaceid--sensor"></a>getSpaceChildSensors(spaceId) ⇒ `sensor[]`

Dado um identificador de espaço, essa função recupera os sensores filhos para esse espaço pai.

**Tipo**: função global

| Parâmetro  | Tipo                | DESCRIÇÃO  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identificador de espaço |

### <a name="getspacechilddevicesspaceid--device"></a>getSpaceChildDevices(spaceId) ⇒ `device[]`

Dado um identificador de espaço, essa função recupera os dispositivos filhos para esse espaço pai.

**Tipo**: função global

| Parâmetro  | Tipo                | DESCRIÇÃO  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identificador de espaço |

### <a name="getdevicechildsensorsdeviceid--sensor"></a>getDeviceChildSensors(deviceId) ⇒ `sensor[]`

Dado um identificador de dispositivo, essa função recupera os sensores filhos desse dispositivo pai.

**Tipo**: função global

| Parâmetro  | Tipo                | DESCRIÇÃO  |
| ------ | ------------------- | ------------ |
| *deviceId* | `guid` | Identificador de dispositivo |

### <a name="getspaceparentspacechildspaceid--space"></a>getSpaceParentSpace(childSpaceId) ⇒ `space`

Dado um identificador de espaço, essa função recupera o espaço pai.

**Tipo**: função global

| Parâmetro  | Tipo                | DESCRIÇÃO  |
| ------ | ------------------- | ------------ |
| *childSpaceId* | `guid` | Identificador de espaço |

### <a name="getsensorparentspacechildsensorid--space"></a>getSensorParentSpace(childSensorId) ⇒ `space`

Dado um identificador de sensor, essa função recupera o espaço pai.

**Tipo**: função global

| Parâmetro  | Tipo                | DESCRIÇÃO  |
| ------ | ------------------- | ------------ |
| *childSensorId* | `guid` | Identificador de sensor |

### <a name="getdeviceparentspacechilddeviceid--space"></a>getDeviceParentSpace(childDeviceId) ⇒ `space`

Dado um identificador de dispositivo, essa função recupera o espaço pai.

**Tipo**: função global

| Parâmetro  | Tipo                | DESCRIÇÃO  |
| ------ | ------------------- | ------------ |
| *childDeviceId* | `guid` | Identificador de dispositivo |

### <a name="getsensorparentdevicechildsensorid--space"></a>getSensorParentDevice(childSensorId) ⇒ `space`

Dado um identificador de sensor, essa função recupera o dispositivo pai.

**Tipo**: função global

| Parâmetro  | Tipo                | DESCRIÇÃO  |
| ------ | ------------------- | ------------ |
| *childSensorId* | `guid` | Identificador de sensor |

### <a name="getspaceextendedpropertyspaceid-propertyname--extendedproperty"></a>getSpaceExtendedProperty(spaceId, propertyName) ⇒ `extendedProperty`

Dado um identificador de espaço, essa função recupera a propriedade e o valor do espaço.

**Tipo**: função global

| Parâmetro  | Tipo                | DESCRIÇÃO  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identificador de espaço |
| *propertyName* | `string` | Nome da propriedade de espaço |

### <a name="getsensorextendedpropertysensorid-propertyname--extendedproperty"></a>getSensorExtendedProperty(sensorId, propertyName) ⇒ `extendedProperty`

Dado um identificador de sensor, esta função recupera a propriedade e o valor do sensor.

**Tipo**: função global

| Parâmetro  | Tipo                | DESCRIÇÃO  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | Identificador de sensor |
| *propertyName* | `string` | Nome da propriedade do sensor |

### <a name="getdeviceextendedpropertydeviceid-propertyname--extendedproperty"></a>getDeviceExtendedProperty(deviceId, propertyName) ⇒ `extendedProperty`

Dado um identificador de dispositivo, essa função recupera a propriedade e o valor do dispositivo.

**Tipo**: função global

| Parâmetro  | Tipo                | DESCRIÇÃO  |
| ------ | ------------------- | ------------ |
| *deviceId* | `guid` | Identificador de dispositivo |
| *propertyName* | `string` | Nome da propriedade do dispositivo |

### <a name="setsensorvaluesensorid-datatype-value"></a>setSensorValue(sensorId, dataType, value)

Essa função define um valor no objeto sensor com o tipo de dados dado.

**Tipo**: função global

| Parâmetro  | Tipo                | DESCRIÇÃO  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | Identificador de sensor |
| *dataType*  | `string` | Tipo de dados de sensor |
| *valor*  | `string` | Valor |

### <a name="setspacevaluespaceid-datatype-value"></a>setSpaceValue(spaceId, dataType, value)

Essa função define um valor no objeto de espaço com o tipo de dados fornecido.

**Tipo**: função global

| Parâmetro  | Tipo                | DESCRIÇÃO  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identificador de espaço |
| *dataType* | `string` | Tipo de dados |
| *valor* | `string` | Valor |

### <a name="logmessage"></a>log(message)

Essa função registra a seguinte mensagem dentro da função definida pelo usuário.

**Tipo**: função global

| Parâmetro  | Tipo                | DESCRIÇÃO  |
| ------ | ------------------- | ------------ |
| *message* | `string` | Mensagem a ser registrada em log |

### <a name="sendnotificationtopologyobjectid-topologyobjecttype-payload"></a>sendNotification(topologyObjectId, topologyObjectType, payload)

Essa função envia uma notificação personalizada a ser despachada.

**Tipo**: função global

| Parâmetro  | Tipo                | DESCRIÇÃO  |
| ------ | ------------------- | ------------ |
| *topologyObjectId*  | `guid` | Identificador de objeto do grafo. Exemplos são espaço, sensor e ID do dispositivo.|
| *topologyObjectType*  | `string` | Exemplos são sensor e dispositivo.|
| *payload*  | `string` | A carga útil JSON a ser enviada com a notificação. |

## <a name="return-types"></a>Tipos de retorno

Os modelos a seguir descrevem os objetos de retorno da referência do cliente anterior.

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

### <a name="space-methods"></a>Métodos de espaço

#### <a name="parent--space"></a>Parent() ⇒ `space`

Essa função retorna o espaço pai do espaço atual.

#### <a name="childsensors--sensor"></a>ChildSensors() ⇒ `sensor[]`

Essa função retorna os sensores filhos do espaço atual.

#### <a name="childdevices--device"></a>ChildDevices() ⇒ `device[]`

Essa função retorna os dispositivos filhos do espaço atual.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

Essa função retorna a propriedade estendida e o valor para o espaço atual.

| Parâmetro  | Tipo                | DESCRIÇÃO  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | Nome da propriedade estendida |

#### <a name="valuevaluename--value"></a>Value(valueName) ⇒ `value`

Essa função retorna o valor do espaço atual.

| Parâmetro  | Tipo                | DESCRIÇÃO  |
| ------ | ------------------- | ------------ |
| *valueName* | `string` | Nome do valor |

#### <a name="historyvaluename--value"></a>History(valueName) ⇒ `value[]`

Essa função retorna os valores históricos do espaço atual.

| Parâmetro  | Tipo                | DESCRIÇÃO  |
| ------ | ------------------- | ------------ |
| *valueName* | `string` | Nome do valor |

#### <a name="notifypayload"></a>Notify(payload)

Essa função envia uma notificação com a carga especificada.

| Parâmetro  | Tipo                | DESCRIÇÃO  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | payload JSON a ser incluído na notificação |

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

### <a name="device-methods"></a>Métodos do dispositivo

#### <a name="parent--space"></a>Parent() ⇒ `space`

Essa função retorna o espaço pai do dispositivo atual.

#### <a name="childsensors--sensor"></a>ChildSensors() ⇒ `sensor[]`

Essa função retorna os sensores filhos do dispositivo atual.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

Essa função retorna a propriedade estendida e o valor para o dispositivo atual.

| Parâmetro  | Tipo                | DESCRIÇÃO  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | Nome da propriedade estendida |

#### <a name="notifypayload"></a>Notify(payload)

Essa função envia uma notificação com a carga especificada.

| Parâmetro  | Tipo                | DESCRIÇÃO  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | payload JSON a ser incluído na notificação |

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

### <a name="sensor-methods"></a>Métodos do sensor

#### <a name="space--space"></a>Space() ⇒ `space`

Essa função retorna o espaço pai do sensor atual.

#### <a name="device--device"></a>Device() ⇒ `device`

Essa função retorna o dispositivo pai do sensor atual.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

Essa função retorna a propriedade estendida e o valor para o sensor atual.

| Parâmetro  | Tipo                | DESCRIÇÃO  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | Nome da propriedade estendida |

#### <a name="value--value"></a>Value() ⇒ `value`

Essa função retorna o valor do sensor atual.

#### <a name="history--value"></a>History() ⇒ `value[]`

Esta função retorna os valores históricos do sensor atual.

#### <a name="notifypayload"></a>Notify(payload)

Essa função envia uma notificação com a carga especificada.

| Parâmetro  | Tipo                | DESCRIÇÃO  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | payload JSON a ser incluído na notificação |

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

- Saiba com [criar pontos de extremidade de Gêmeos Digitais do Azure ](how-to-egress-endpoints.md) para envio de eventos.

- Para obter mais detalhes sobre os pontos de extremidade de Gêmeos Digitais do Azure, saiba [mais sobre pontos de extremidade](concepts-events-routing.md).
