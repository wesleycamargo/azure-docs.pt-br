<properties
    pageTitle="API do coletor de dados HTTP do Log Analytics | Microsoft Azure"
    description="Você pode usar a API do coletor de dados HTTP do Log Analytics para adicionar dados JSON de POST ao repositório do Log Analytics de qualquer cliente que possa chamar a API REST. Este artigo descreve como usar a API e tem exemplos de como publicar dados usando diferentes linguagens de programação."
    services="log-analytics"
    documentationCenter=""
    authors="bwren"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/11/2016"
    ms.author="bwren"/>



# <a name="log-analytics-http-data-collector-api"></a>API do coletor de dados HTTP do Log Analytics

Quando você usa a API do coletor de dados HTTP do Azure Log Analytics, pode adicionar dados do JSON (JavaScript Object Notation) de POST ao repositório do Log Analytics de qualquer cliente que possa chamar a API REST. Usando esse método, você pode enviar dados de aplicativos de terceiros ou de scripts, como de um runbook na Automação do Azure.  

## <a name="create-a-request"></a>Criar uma solicitação

As próximas duas tabelas listam os atributos que são necessários para cada solicitação para a API do coletor de dados HTTP do Log Analytics. Descrevemos cada atributo em mais detalhes posteriormente neste artigo.

### <a name="request-uri"></a>URI da solicitação

| Atributo | Propriedade |
|:--|:--|
| Método | POST |
| URI | https://<WorkspaceID>.ods.opinsights.azure.com/api/logs?api-version=2016-04-01 |
| Tipo de conteúdo | aplicativo/json |

### <a name="request-uri-parameters"></a>Solicitar parâmetros de URI (Uniform Resource Identifier)
| Parâmetro | Descrição |
|:--|:--|
| CustomerID  | O identificador exclusivo para o espaço de trabalho do Microsoft Operations Management Suite. |
| Recurso    | O nome do recurso de API: /api/logs. |
| Versão da API | A versão da API a ser usada com esta solicitação. Atualmente, ela é 2016-04-01. |

### <a name="request-headers"></a>Cabeçalhos da solicitação
| Cabeçalho | Descrição |
|:--|:--|
| Autorização | A assinatura de autorização. Posteriormente neste artigo, você pode ler sobre como criar um cabeçalho HMAC-SHA256. |
| Log-Type | Especifique o tipo de registro dos dados que estão sendo enviados. Atualmente, o tipo de log dá suporte apenas a caracteres alfa. Ele não dá suporte a caracteres alfanuméricos ou caracteres especiais. |
| x-ms-date | A data em que a solicitação foi processada, no formato RFC 1123. |
| time-generated-field | O nome de um campo nos dados que contém o carimbo de data/hora do item de dados. Se você especificar um campo, seu conteúdo será usado para **TimeGenerated**. Se esse campo não for especificado, o padrão para **TimeGenerated** será a hora em que a mensagem é incluída. O conteúdo do campo de mensagem deve seguir o formato ISO 8601 AAAA-MM-DDThh:mm:ssZ. |


## <a name="authorization"></a>Autorização

Todas as solicitações para a API do coletor de dados HTTP do Log Analytics devem incluir um cabeçalho de autorização. Para autenticar uma solicitação, você deve assinar a solicitação com a chave primária ou secundária para o espaço de trabalho que está fazendo a solicitação. Em seguida, passe essa assinatura como parte da solicitação.   

Aqui está o formato do cabeçalho de autorização:

```
Authorization: SharedKey <WorkspaceID>:<Signature>
```

*WorkspaceID* é o identificador exclusivo para o espaço de trabalho do Operations Management Suite. *Signature* é um [HMAC (Message Authentication Code baseado em Hash)](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx) que é construído por meio da solicitação e, em seguida, calculado usando o [algoritmo SHA256](https://msdn.microsoft.com/library/system.security.cryptography.sha256.aspx). Em seguida, você o codifica usando a codificação Base64.

Use este formato para codificar a cadeia de caracteres de assinatura **SharedKey**:

```
StringToSign = VERB + "\n" +
               Content-Length + "\n" +
               Content-Type + "\n" +
               x-ms-date + "\n" +
               "/api/logs";
```

Aqui está um exemplo de uma cadeia de caracteres de assinatura:

```
POST\n1024\napplication/json\nx-ms-date:Mon, 04 Apr 2016 08:00:00 GMT\n/api/logs
```

Quando você tiver a cadeia de caracteres de assinatura, codifique-a usando o algoritmo HMAC-SHA256 na sequência de caracteres codificada em UTF-8 e codifique o resultado em Base64. Use este formato:

```
Signature=Base64(HMAC-SHA256(UTF8(StringToSign)))
```

Os exemplos nas seções a seguir têm o código de exemplo para ajudá-lo a criar um cabeçalho de autorização.

## <a name="request-body"></a>Corpo da solicitação

O corpo da mensagem deve ser em JSON. Ele deve incluir um ou mais registros com os pares de nome e valor de propriedade neste formato:

```
{
"property1": "value1",
" property 2": "value2"
" property 3": "value3",
" property 4": "value4"
}
```

Você pode criar lotes de vários registros em uma única solicitação usando o formato a seguir. Todos os registros devem ser do mesmo tipo de registro.

```
{
"property1": "value1",
" property 2": "value2"
" property 3": "value3",
" property 4": "value4"
},
{
"property1": "value1",
" property 2": "value2"
" property 3": "value3",
" property 4": "value4"
}
```

## <a name="record-type-and-properties"></a>Propriedades e o tipo de registro

Você define um tipo de registro personalizado ao enviar dados por meio da API do coletor de dados HTTP do Log Analytics. No momento, você não pode gravar dados em tipos de registro existentes que foram criados por outras soluções e tipos de dados. O Log Analytics lê os dados de entrada e, em seguida, cria propriedades que correspondem aos tipos de dados dos valores que você inseriu.

Cada solicitação para a API do Log Analytics deve incluir um cabeçalho **Log-Type** com o nome do tipo de registro. O sufixo **_CL** é acrescentado automaticamente ao nome inserido para distingui-lo de outros tipos de log como um log personalizado. Por exemplo, se você inserir o nome **MyNewRecordType**, o Log Analytics criará um registro com o tipo **MyNewRecordType_CL**. Isso ajuda a garantir que não haja nenhum conflito entre os nomes de tipo criados pelo usuário e aqueles fornecidos nas soluções atuais ou futuras da Microsoft.

Para identificar o tipo de dados de uma propriedade, o Log Analytics adiciona um sufixo ao nome da propriedade. Se uma propriedade contiver um valor nulo, ela não será incluída no registro. Esta tabela lista o tipo de dados de propriedade e o sufixo correspondente:

| Tipo de dados de propriedade | Suffix |
|:--|:--|
| Cadeia de caracteres    | _s |
| Booliano   | _b |
| Duplo    | _d |
| Data/hora | _t |
| GUID      | _g |


O tipo de dados que o Log Analytics usa para cada propriedade depende de se o tipo de registro do novo registro já existe.

- Se o tipo de registro não existir, o Log Analytics criará um novo. O Log Analytics usa a inferência de tipos JSON para determinar o tipo de dados de cada propriedade para o novo registro.
- Se o tipo de registro existir, o Log Analytics tentará criar um novo registro com base nas propriedades existentes. Se o tipo de dados de uma propriedade no novo registro não corresponder e não puder ser convertida para o tipo existente, ou se o registro incluir uma propriedade que não existe, o Log Analytics criará uma nova propriedade que tenha o sufixo relevante.

Por exemplo, esta entrada de envio criaria um registro com três propriedades, **number_d**, **boolean_b** e **string_s**:

![Registro de exemplo 1](media/log-analytics-data-collector-api/record-01.png)

Se você enviasse essa próxima entrada, com todos os valores formatados como cadeias de caracteres, as propriedades não seriam alteradas. Esses valores podem ser convertidos para tipos de dados existentes:

![Registro de exemplo 2](media/log-analytics-data-collector-api/record-02.png)

Mas, se você fizesse esse próximo envio, o Log Analytics criaria as novas propriedades **boolean_d** e **string_d**. Esses valores não podem ser convertidos:

![Registro de exemplo 3](media/log-analytics-data-collector-api/record-03.png)

Se você enviasse a entrada a seguir, antes de o tipo de registro ter sido criado, o Log Analytics criaria um registro com três propriedades, **number_s**, **boolean_s** e **string_s**. Nesta entrada, cada um dos valores iniciais é formatado como uma cadeia de caracteres:

![Registro de exemplo 4](media/log-analytics-data-collector-api/record-04.png)

## <a name="return-codes"></a>Códigos de retorno

O código de status HTTP 202 significa que a solicitação foi aceita para processamento, mas o processamento ainda não foi concluído. Isso indica que a operação foi concluída com êxito.

Esta tabela lista o conjunto completo de códigos de status que o serviço pode retornar:

| Código | Status | Código do erro | Descrição |
|:--|:--|:--|:--|
| 202 | Aceita |  | A solicitação foi aceita com êxito. |
| 400 | Solicitação incorreta | InactiveCustomer | O espaço de trabalho foi fechado. |
| 400 | Solicitação incorreta | InvalidApiVersion | A versão da API que você especificou não foi reconhecida pelo serviço. |
| 400 | Solicitação incorreta | InvalidCustomerId | A ID do espaço de trabalho especificada é inválida. |
| 400 | Solicitação incorreta | InvalidDataFormat | JSON inválido foi enviado. O corpo da resposta pode conter mais informações sobre como resolver o erro. |
| 400 | Solicitação incorreta | InvalidLogType | O tipo de log especificado continha caracteres especiais ou numéricos. |
| 400 | Solicitação incorreta | MissingApiVersion | A versão da API não foi especificada. |
| 400 | Solicitação incorreta | MissingContentType | O tipo de conteúdo não foi especificado. |
| 400 | Solicitação incorreta | MissingLogType | O tipo de log de valor necessário não foi especificado. |
| 400 | Solicitação incorreta | UnsupportedContentType | O tipo de conteúdo não foi definido como **application/json**. |
| 403 | Proibido | InvalidAuthorization | O serviço falhou ao autenticar a solicitação. Verifique se a chave de conexão e a ID do espaço de trabalho são válidos. |
| 500 | Erro interno do servidor | UnspecifiedError | O serviço encontrou um erro interno. Tente novamente a solicitação. |
| 503 | Serviço indisponível | ServiceUnavailable | No momento, o serviço está indisponível para receber solicitações. Tente novamente a sua solicitação. |

## <a name="query-data"></a>Consultar dados

Para consultar os dados enviados pela API do coletor de dados HTTP do Log Analytics, procure registros com **Tipo** que é igual ao valor **LogType** valor que você especificou, acrescido com **_CL**. Por exemplo, se você usou **MyCustomLog**, você pode retornar todos os registros com **Type=MyCustomLog_CL**.


## <a name="sample-requests"></a>Solicitações de exemplo

Nas seções a seguir, você encontrará exemplos de como enviar dados para a AAPI do coletor de dados HTTP do Log Analytics usando diferentes linguagens de programação.

Para cada exemplo, realize essas etapas para definir as variáveis para o cabeçalho de autorização:

1. No portal do Operations Management Suite, selecione o bloco **Configurações** e, em seguida, selecione a guia **Fontes Conectadas**.
2. À direita da **ID do Espaço de Trabalho**, selecione o ícone de cópia e cole a ID como o valor da variável **Customer ID**.
3. À direita da **Chave Primária**, selecione o ícone de cópia e cole a ID como o valor da variável **Shared Key**.

Como alternativa, você pode alterar as variáveis para o tipo de log e dados JSON.

### <a name="powershell-sample"></a>Exemplo do PowerShell

```
# Replace with your Workspace ID
$CustomerId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"  

# Replace with your Primary Key
$SharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# Specify the name of the record type that you'll be creating
$LogType = "MyRecordType"

# Specify a field with the created time for the records
$TimeStampField = "DateValue"


# Create two records with the same set of properties to create
$json = @"
[{  "StringValue": "MyString1",
    "NumberValue": 42,
    "BooleanValue": true,
    "DateValue": "2016-05-12T20:00:00.625Z",
    "GUIDValue": "9909ED01-A74C-4874-8ABF-D2678E3AE23D"
},
{   "StringValue": "MyString2",
    "NumberValue": 43,
    "BooleanValue": false,
    "DateValue": "2016-05-12T20:00:00.625Z",
    "GUIDValue": "8809ED01-A74C-4874-8ABF-D2678E3AE23D"
}]
"@

# Create the function to create the authorization signature
Function Build-Signature ($customerId, $sharedKey, $date, $contentLength, $method, $contentType, $resource)
{
    $xHeaders = "x-ms-date:" + $date
    $stringToHash = $method + "`n" + $contentLength + "`n" + $contentType + "`n" + $xHeaders + "`n" + $resource

    $bytesToHash = [Text.Encoding]::UTF8.GetBytes($stringToHash)
    $keyBytes = [Convert]::FromBase64String($sharedKey)

    $sha256 = New-Object System.Security.Cryptography.HMACSHA256
    $sha256.Key = $keyBytes
    $calculatedHash = $sha256.ComputeHash($bytesToHash)
    $encodedHash = [Convert]::ToBase64String($calculatedHash)
    $authorization = 'SharedKey {0}:{1}' -f $customerId,$encodedHash
    return $authorization
}


# Create the function to create and post the request
Function Post-OMSData($customerId, $sharedKey, $body, $logType)
{
    $method = "POST"
    $contentType = "application/json"
    $resource = "/api/logs"
    $rfc1123date = [DateTime]::UtcNow.ToString("r")
    $contentLength = $body.Length
    $signature = Build-Signature `
        -customerId $customerId `
        -sharedKey $sharedKey `
        -date $rfc1123date `
        -contentLength $contentLength `
        -fileName $fileName `
        -method $method `
        -contentType $contentType `
        -resource $resource
    $uri = "https://" + $customerId + ".ods.opinsights.azure.com" + $resource + "?api-version=2016-04-01"

    $headers = @{
        "Authorization" = $signature;
        "Log-Type" = $logType;
        "x-ms-date" = $rfc1123date;
        "time-generated-field" = $TimeStampField;
    }

    $response = Invoke-WebRequest -Uri $uri -Method $method -ContentType $contentType -Headers $headers -Body $body -UseBasicParsing
    return $response.StatusCode

}

# Submit the data to the API endpoint
Post-OMSData -customerId $customerId -sharedKey $sharedKey -body ([System.Text.Encoding]::UTF8.GetBytes($json)) -logType $logType  
```

### <a name="c#-sample"></a>Exemplo de C#

```
using System;
using System.Net;
using System.Security.Cryptography;

namespace OIAPIExample
{
    class ApiExample
    {
// An example JSON object, with key/value pairs
        static string json = @"[{""DemoField1"":""DemoValue1"",""DemoField2"":""DemoValue2""},{""DemoField1"":""DemoValue3"",""DemoField2"":""DemoValue4""}]";

// Update customerId to your Operations Management Suite workspace ID
        static string customerId = "xxxxxxxx-xxx-xxx-xxx-xxxxxxxxxxxx";

// For sharedKey, use either the primary or the secondary Connected Sources client authentication key   
        static string sharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx";

// LogName is name of the event type that is being submitted to Log Analytics
        static string LogName = "DemoExample";

// You can use an optional field to specify the timestamp from the data. If the time field is not specified, Log Analytics assumes the time is the message ingestion time
        static string TimeStampField = "";

        static void Main()
        {
// Create a hash for the API signature
            var datestring = DateTime.UtcNow.ToString("r");
            string stringToHash = "POST\n" + json.Length + "\napplication/json\n" + "x-ms-date:" + datestring + "\n/api/logs";
            string hashedString = BuildSignature(stringToHash, sharedKey);
            string signature = "SharedKey " + customerId + ":" + hashedString;

            PostData(signature, datestring, json);
        }

// Build the API signature
        public static string BuildSignature(string message, string secret)
        {
            var encoding = new System.Text.ASCIIEncoding();
            byte[] keyByte = Convert.FromBase64String(secret);
            byte[] messageBytes = encoding.GetBytes(message);
            using (var hmacsha256 = new HMACSHA256(keyByte))
            {
                byte[] hash = hmacsha256.ComputeHash(messageBytes);
                return Convert.ToBase64String(hash);
            }
        }

// Send a request to the POST API endpoint
        public static void PostData(string signature, string date, string json)
        {
            string url = "https://"+ customerId +".ods.opinsights.azure.com/api/logs?api-version=2016-04-01";
            using (var client = new WebClient())
            {
                client.Headers.Add(HttpRequestHeader.ContentType, "application/json");
                client.Headers.Add("Log-Type", LogName);
                client.Headers.Add("Authorization", signature);
                client.Headers.Add("x-ms-date", date);
                client.Headers.Add("time-generated-field", TimeStampField);
                client.UploadString(new Uri(url), "POST", json);
            }
        }
    }
}
```

### <a name="python-sample"></a>Exemplo de Python

```
import json
import requests
import datetime
import hashlib
import hmac
import base64

# Update the customer ID to your Operations Management Suite workspace ID
customer_id = 'xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

# For the shared key, use either the primary or the secondary Connected Sources client authentication key   
shared_key = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# The log type is the name of the event that is being submitted
log_type = 'WebMonitorTest'

# An example JSON web monitor object
json_data = [{
   "slot_ID": 12345,
    "ID": "5cdad72f-c848-4df0-8aaa-ffe033e75d57",
    "availability_Value": 100,
    "performance_Value": 6.954,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "true"
},
{   
    "slot_ID": 67890,
    "ID": "b6bee458-fb65-492e-996d-61c4d7fbb942",
    "availability_Value": 100,
    "performance_Value": 3.379,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "false"
}]
body = json.dumps(json_data)

#####################
######Functions######  
#####################

# Build the API signature
def build_signature(customer_id, shared_key, date, content_length, method, content_type, resource):
    x_headers = 'x-ms-date:' + date
    string_to_hash = method + "\n" + str(content_length) + "\n" + content_type + "\n" + x_headers + "\n" + resource
    bytes_to_hash = bytes(string_to_hash).encode('utf-8')  
    decoded_key = base64.b64decode(shared_key)
    encoded_hash = base64.b64encode(hmac.new(decoded_key, string_to_hash, digestmod=hashlib.sha256).digest())
    authorization = "SharedKey {}:{}".format(customer_id,encoded_hash)
    return authorization

# Build and send a request to the POST API
def post_data(customer_id, shared_key, body, log_type):
    method = 'POST'
    content_type = 'application/json'
    resource = '/api/logs'
    rfc1123date = datetime.datetime.utcnow().strftime('%a, %d %b %Y %H:%M:%S GMT')
    content_length = len(body)
    signature = build_signature(customer_id, shared_key, rfc1123date, content_length, method, content_type, resource)
    uri = 'https://' + customer_id + '.ods.opinsights.azure.com' + resource + '?api-version=2016-04-01'

    headers = {
        'content-type': content_type,
        'Authorization': signature,
        'Log-Type': log_type,
        'x-ms-date': rfc1123date
    }

    response = requests.post(uri,data=body, headers=headers)
    if (response.status_code == 202):
        print 'Accepted'
    else:
        print "Response code: {}".format(response.status_code)

post_data(customer_id, shared_key, body, log_type)
```

## <a name="next-steps"></a>Próximas etapas

- Use o [Designer de modos de exibição](log-analytics-view-designer.md) para criar exibições personalizadas nos dados que você envia.



<!--HONumber=Oct16_HO2-->


