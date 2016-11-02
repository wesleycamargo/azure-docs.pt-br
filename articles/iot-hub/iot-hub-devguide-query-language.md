<properties
 pageTitle="Guia do desenvolvedor – linguagem de consulta | Microsoft Azure"
 description="Guia do desenvolvedor do Hub IoT do Azure – descrição da linguagem de consulta usada para recuperar informações sobre gêmeos, métodos e trabalhos no Hub IoT"
 services="iot-hub"
 documentationCenter=".net"
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="elioda"/>


# <a name="reference---query-language-for-twins-and-jobs"></a>Referência – linguagem de consulta para gêmeos e trabalhos

## <a name="overview"></a>Visão geral

O Hub IoT fornece uma linguagem semelhante à SQL poderosa para recuperar informações sobre [dispositivos gêmeos][lnk-twins] e [trabalhos][lnk-jobs]. Este artigo apresenta:

* Uma introdução aos principais recursos da linguagem de consulta do Hub IoT e
* Uma descrição mais detalhada da linguagem.

## <a name="getting-started-with-twin-queries"></a>Introdução às consultas gêmeas

Os [dispositivos gêmeos][lnk-twins] podem conter objetos JSON arbitrários como tags e propriedades. O Hub IoT permite consultar dispositivos gêmeos como um único documento JSON que contém todas as informações do gêmeo.
Por exemplo, suponha que seus gêmeos do Hub IoT tenham a seguinte estrutura:

        {                                                                      
            "deviceId": "myDeviceId",                                            
            "etag": "AAAAAAAAAAc=",                                              
            "tags": {                                                            
                "location": {                                                      
                    "region": "US",                                                  
                    "plant": "Redmond43"                                             
                }                                                                  
            },                                                                   
            "properties": {                                                      
                "desired": {                                                       
                    "telemetryConfig": {                                             
                        "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",            
                        "sendFrequencyInSecs": 300                                          
                    },                                                               
                    "$metadata": {                                                   
                    ...                                                     
                    },                                                               
                    "$version": 4                                                    
                },                                                                 
                "reported": {                                                      
                    "connectivity": {                                                
                        "type": "cellular"                            
                    },                                                               
                    "telemetryConfig": {                                             
                        "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",            
                        "sendFrequencyInSecs": 300,                                         
                        "status": "Success"                                            
                    },                                                               
                    "$metadata": {                                                   
                    ...                                                
                    },                                                               
                    "$version": 7                                                    
                }                                                                  
            }                                                                    
        }

O Hub IoT expõe os dispositivos gêmeos como uma coleção de documentos chamada **dispositivos**.
Então, a consulta a seguir recupera o conjunto completo de dispositivos gêmeos:

        SELECT * FROM devices

> [AZURE.NOTE] Os [SDKs do Hub IoT][lnk-hub-sdks] dão suporte à paginação de resultados grandes.

O Hub IoT permite recuperar a filtragem de gêmeos com condições arbitrárias. Por exemplo,

        SELECT * FROM devices
        WHERE tags.location.region = 'US'

recupera os gêmeos com a marcação **location.region** definida como **US**.
Os operadores boolianos e as comparações aritméticas também têm suporte, por exemplo,

        SELECT * FROM devices
        WHERE tags.location.region = 'US'
            AND properties.reported.telemetryConfig.sendFrequencyInSecs >= 60

recupera todos os gêmeos localizados nos Estados Unidos configurados para enviar telemetria com menos frequência do que a cada minuto. Como uma conveniência, também é possível usar constantes de matriz em conjunto com os operadores **IN** e **NIN** (não in). Por exemplo,

        SELECT * FROM devices
        WHERE property.reported.connectivity IN ['wired', 'wifi']

recupera todos os gêmeos que relataram conectividade wi-fi ou com fio. Consulte a seção [Cláusula WHERE][lnk-query-where] para encontrar a referência completa dos recursos de filtragem.

Também há suporte para agrupamento e agregações. Por exemplo,

        SELECT properties.reported.telemetryConfig.status AS status,
            COUNT() AS numberOfDevices
        FROM devices
        GROUP BY properties.reported.telemetryConfig.status

retorna a contagem dos dispositivos em cada status de configuração de telemetria.

        [
            {
                "numberOfDevices": 3,
                "status": "Success"
            },
            {
                "numberOfDevices": 2,
                "status": "Pending"
            },
            {
                "numberOfDevices": 1,
                "status": "Error"
            }
        ]

O exemplo acima ilustra uma situação em que três dispositivos relataram a configuração bem-sucedida, dois ainda estão aplicando a configuração e um relatou um erro.

### <a name="c#-example"></a>Exemplo de C#

A funcionalidade de consulta é exposta pelo [SDK de serviço do C#][lnk-hub-sdks] na classe **RegistryManager**.
Aqui está um exemplo de uma consulta simples:

        var query = registryManager.CreateQuery("SELECT * FROM devices", 100);
        while (query.HasMoreResults)
        {
            var page = await query.GetNextAsTwinAsync();
            foreach (var twin in page) 
            {
                // do work on twin object
            }
        }

Observe como o objeto **query** é instanciado com um tamanho de página (até 1000) e, em seguida, várias páginas podem ser recuperadas chamando os métodos **GetNextAsTwinAsync** várias vezes.
É importante observar que o objeto de consulta expõe vários **Next\***, dependendo da opção de desserialização necessária para a consulta, ou seja, objetos gêmeos ou de trabalho ou Json simples a ser usado ao utilizar projeções.

### <a name="node-example"></a>Exemplo de nó

A funcionalidade de consulta é exposta pelo [SDK de serviço de nó][lnk-hub-sdks] no objeto **Registry**.
Aqui está um exemplo de uma consulta simples:

        var query = registry.createQuery('SELECT * FROM devices', 100);
        var onResults = function(err, results) {
            if (err) {
                console.error('Failed to fetch the results: ' + err.message);
            } else {
                // Do something with the results
                results.forEach(function(twin) {
                    console.log(twin.deviceId);
                });

                if (query.hasMoreResults) {
                    query.nextAsTwin(onResults);
                }
            }
        };
        query.nextAsTwin(onResults);

Observe como o objeto **query** é instanciado com um tamanho de página (até 1000) e, em seguida, várias páginas podem ser recuperadas chamando os métodos **nextAsTwin** várias vezes.
É importante observar que o objeto de consulta expõe vários **next\***, dependendo da opção de desserialização necessária para a consulta, ou seja, objetos gêmeos ou de trabalho ou Json simples a ser usado ao utilizar projeções.

### <a name="limitations"></a>Limitações

Atualmente, as projeções têm suporte apenas ao usar agregações, ou seja, consultas não agregadas podem usar apenas `SELECT *`. Além disso, a agregação tem suporte apenas em conjunto com o agrupamento.

## <a name="getting-started-with-jobs-queries"></a>Introdução às consultas de trabalhos

Os [trabalhos][lnk-jobs] fornecem uma maneira de executar operações em conjuntos de dispositivos. Cada dispositivo gêmeo contém as informações dos trabalhos dos quais ele faz parte em uma coleção chamada **jobs**.
Logicamente,

        {                                                                      
            "deviceId": "myDeviceId",                                            
            "etag": "AAAAAAAAAAc=",                                              
            "tags": {                                                            
                ...                                                              
            },                                                                   
            "properties": {                                                      
                ...                                                                 
            },
            "jobs": [
                { 
                    "deviceId": "myDeviceId",
                    "jobId": "myJobId",    
                    "jobType": "scheduleTwinUpdate",            
                    "status": "completed",                    
                    "startTimeUtc": "2016-09-29T18:18:52.7418462",
                    "endTimeUtc": "2016-09-29T18:20:52.7418462",
                    "createdDateTimeUtc": "2016-09-29T18:18:56.7787107Z",
                    "lastUpdatedDateTimeUtc": "2016-09-29T18:18:56.8894408Z",
                    "outcome": {
                        "deviceMethodResponse": null   
                    }                                         
                },
                ...
            ]                                                             
        }

Atualmente, essa coleção pode ser consultada como **devices.jobs** na linguagem de consulta do Hub IoT.

Por exemplo, para obter todos os trabalhos (agendados e anteriores) que afetam um único dispositivo, você pode usar a seguinte consulta:

        SELECT * FROM devices.jobs
        WHERE devices.jobs.deviceId = 'myDeviceId'

Observe como essa consulta fornece o status específico do dispositivo (e possivelmente a resposta do método direto) de cada trabalho retornado.
Também é possível filtrar com condições boolianas arbitrárias em todas as propriedades dos objetos na coleção **devices.jobs**.
Por exemplo, a consulta a seguir:

        SELECT * FROM devices.jobs
        WHERE devices.jobs.deviceId = 'myDeviceId'
            AND devices.jobs.jobType = 'scheduleTwinUpdate'
            AND devices.jobs.status = 'completed'
            AND devices.jobs.createdTimeUtc > '2016-09-01'

recupera todos os trabalhos de atualização gêmeos concluídos para o dispositivo **myDeviceId** que foram criados depois de setembro de 2016.

Também é possível recuperar os resultados por dispositivo de um único trabalho.

        SELECT * FROM devices.jobs
        WHERE devices.jobs.jobId = 'myJobId'

### <a name="limitations"></a>Limitações
No momento, as consultas em **devices.jobs** não dão suporte a:

* Projeções, ou seja, apenas `SELECT *` é possível;
* Condições que se referem ao dispositivo gêmeo além das propriedades de trabalho, conforme mostrado acima;
* Realização de agregações, por exemplo, count, avg, group by.

## <a name="basics-of-an-iot-hub-query"></a>Noções básicas de uma consulta de Hub IoT

Todas as consultas de Hub IoT são compostas por cláusulas SELECT e FROM cláusulas WHERE e GROUP BY opcionais. Cada consulta é executada em uma coleção de documentos JSON, por exemplo, dispositivos gêmeos. A cláusula FROM indica a coleção de documentos a ser iterada em (**devices** ou **devices.jobs**). Em seguida, o filtro na cláusula WHERE é aplicado. No caso de agregações, os resultados desta etapa são agrupados como especificado na cláusula GROUP BY e, para cada grupo, uma linha é gerada conforme especificado na cláusula SELECT.

        SELECT <select_list>
        FROM <from_specification>
        [WHERE <filter_condition>]
        [GROUP BY <group_specification>]

## <a name="from-clause"></a>Cláusula FROM

A cláusula **FROM <from_specification>** pode assumir somente dois valores: **FROM devices**, para consultar dispositivos gêmeos, ou **FROM devices.jobs**, para consultar os detalhes de trabalho por dispositivo.

## <a name="where-clause"></a>Cláusula WHERE

A cláusula **WHERE <filter_condition>** é opcional. Ela especifica as condições que os documentos JSON na coleção FROM devem satisfazer para serem incluídos como parte dos resultados. Todos os documentos JSON devem avaliar as condições especificadas como “true” para ser incluídos no resultado.

As condições permitidas são descritas na seção [Expressões e condições][lnk-query-expressions].

## <a name="select-clause"></a>Cláusula SELECT

A cláusula SELECT (**SELECT <select_list>**) é obrigatória e especifica quais valores serão retornados pela consulta. Ela especifica os valores JSON a serem usados para gerar novos objetos JSON para cada elemento do subconjunto filtrado (e opcionalmente agrupado) da coleção FROM, a fase de projeção gera um novo objeto JSON, construído com os valores especificados na cláusula SELECT.

Esta é a gramática da cláusula SELECT:

        SELECT [TOP <max number>] <projection list>

        <projection_list> ::=
            '*'
            | <projection_element> AS alias [, <projection_element> AS alias]+

        <projection_element> :==
            attribute_name
            | <projection_element> '.' attribute_name
            | <aggregate>

        <aggregate> :==
            count(<projection_element>) | count()
            | avg(<projection_element>) | avg()
            | sum(<projection_element>) | sum()
            | min(<projection_element>) | min()
            | max(<projection_element>) | max()

em que **attribute_name** refere-se a qualquer propriedade do documento JSON na coleção FROM. Alguns exemplos de cláusulas SELECT podem ser encontrados na seção [Introdução às consultas gêmeas][lnk-query-getstarted].

No momento, as cláusulas de seleção diferentes de **SELECT \*** têm suporte apenas em consultas de agregação em gêmeos.

## <a name="group-by-clause"></a>Cláusula GROUP BY

A cláusula **GROUP BY <group_specification>** é uma etapa opcional que pode ser executada após o filtro especificado na cláusula WHERE e antes da projeção especificada em SELECT. Ela agrupa documentos com base no valor de um atributo. Esses grupos são usados para gerar valores agregados conforme especificado na cláusula SELECT.

Um exemplo de uma consulta usando GROUP BY é:

        SELECT properties.reported.telemetryConfig.status AS status,
            COUNT() AS numberOfDevices
        FROM devices
        GROUP BY properties.reported.telemetryConfig.status

A sintaxe formal para GROUP BY é:

        GROUP BY <group_by_element>
        <group_by_element> :==
            attribute_name
            | < group_by_element > '.' attribute_name

em que **attribute_name** refere-se a qualquer propriedade do documento JSON na coleção FROM. 

No momento, a cláusula GROUP BY tem suporte apenas ao consultar gêmeos.

## <a name="expressions-and-conditions"></a>Expressões e condições

Em um alto nível, uma *expressão*:

* Avalia para uma instância de um tipo JSON (ou seja, booliano, número, cadeia de caracteres, matriz ou objeto) e
* É definida pela manipulação de dados provenientes do documento JSON de dispositivo e constantes usando as funções e operadores internos.

*Condições* são expressões que são avaliadas como um booliano, ou seja, qualquer constante diferente de booliana **true** é considerada como **false** (incluindo **null**, **undefined**, qualquer instância de matriz ou objeto, qualquer cadeia de caracteres e claramente o booliano **false**).

A sintaxe de expressões é:

        <expression> ::=
            <constant> |
            attribute_name |
            unary_operator <expression> |
            <expression> binary_operator <expression> |
            <create_array_expression> |
            '(' <expression> ')'

        <constant> ::=
            <undefined_constant>
            | <null_constant> 
            | <number_constant> 
            | <string_constant> 
            | <array_constant> 

        <undefined_constant> ::= undefined
        <null_constant> ::= null
        <number_constant> ::= decimal_literal | hexadecimal_literal
        <string_constant> ::= string_literal
        <array_constant> ::= '[' <constant> [, <constant>]+ ']'

onde:

| Símbolo | Definição |
| -------------- | -----------------|
| attribute_name | Qualquer propriedade do documento JSON na coleção FROM. |
| unary_operator | Qualquer operador unário de acordo com a seção Operadores. |
| binary_operator | Qualquer operador binário de acordo com a seção Operadores. |
| decimal_literal | Um float expresso em notação decimal. |
| hexadecimal_literal | Um número expresso pela cadeia de caracteres '0x' seguida por uma cadeia de caracteres de dígitos hexadecimais. |
| string_literal | Literais de cadeia de caracteres são cadeias de caracteres Unicode representadas por uma sequência de zero ou mais caracteres Unicode ou sequências de escape. As literais de cadeia de caracteres são colocadas entre aspas simples (apóstrofe: ') ou aspas duplas (aspas: "). Escapes permitidos: `\'`, `\"`, `\\`, `\uXXXX` para caracteres Unicode definidos por quatro dígitos hexadecimais. |

### <a name="operators"></a>Operadores

Há suporte para os seguintes operadores:

| Família | Operadores |
| -------------- | -----------------|
| Aritmético | +,-,*,/,% |
| Lógico | AND, OR, NOT |
| Comparação |  =, !=, <, >, <=, >=, <> |

## <a name="next-steps"></a>Próximas etapas

Saiba como executar consultas em seus aplicativos usando [SDKs do Hub IoT][lnk-hub-sdks].

[lnk-query-where]: iot-hub-devguide-query-language.md#where-clause
[lnk-query-expressions]: iot-hub-devguide-query-language.md#expressions-and-conditions
[lnk-query-getstarted]: iot-hub-devguide-query-language.md#getting-started-with-twin-queries

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md


<!--HONumber=Oct16_HO2-->


