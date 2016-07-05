<properties
	pageTitle="Consultas de gêmeo do gerenciamento de dispositivo Hub IoT | Microsoft Azure"
	description="Tutorial do Hub IoT do Azure para o gerenciamento de dispositivo que descreve como usar consultas para encontrar dispositivos gêmeos."
	services="iot-hub"
	documentationCenter=".net"
	authors="juanjperez"
	manager="timlt"
	editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="juanpere"/>

# Tutorial: Como encontrar dispositivos gêmeos usando consultas com o node.js (visualização)

[AZURE.INCLUDE [iot-hub-device-management-query-selector](../../includes/iot-hub-device-management-query-selector.md)]

O gerenciamento de dispositivo IoT do Azure permite encontrar dispositivos gêmeos, a representação de serviço de um dispositivo físico, com consultas. É possível consultar com base nas propriedades de dispositivo, nas propriedades de serviço ou nas marcas do dispositivo gêmeo. É possível consultar usando marcas e propriedades:

-   Para consultar dispositivos gêmeos usando marcas, você transmite uma matriz de cadeias de caracteres e a consulta retorna o conjunto de dispositivos marcados com todas essas cadeias de caracteres.

-   Para consultar dispositivos gêmeos usando as propriedades do serviço ou do dispositivo, é necessário usar uma expressão de consulta JSON.

Para obter mais informações sobre o dispositivo gêmeo e as consultas, consulte a [Visão geral do gerenciamento de dispositivo Hub IoT do Azure][lnk-dm-overview].

## Executando a amostra de consulta de dispositivo

A amostra a seguir estende a funcionalidade do tutorial [Introdução ao gerenciamento de dispositivo Hub IoT do Azure][lnk-get-started]. Iniciando com diferentes dispositivos simulados em execução, ele iniciará uma consulta para encontrar dispositivos específicos.

### Pré-requisitos 

Antes de executar esta amostra, é necessário concluir as etapas em [Introdução ao gerenciamento de dispositivo Hub IoT do Azure][lnk-get-started]. Isso significa que seus dispositivos simulados devem estar em execução. Se você concluiu o processo anteriormente, reinicie os dispositivos simulados agora.

### Iniciando a amostra

Para iniciar a amostra, é necessário executar `registry_queryDevices.js`. Isso executará algumas consultas diferentes. Siga as etapas abaixo para iniciar a amostra:

1.  Permita que os dispositivos simulados sejam executados durante, pelo menos, 1 minuto. Isso garante que as propriedades de dispositivo no gêmeo são sincronizadas com o dispositivo físico. Veja [Tutorial: Como usar o dispositivo gêmeo][lnk-twin-tutorial] para obter mais detalhes sobre a sincronização.

2.  Na pasta raiz em que você clonou o repositório **azure-iot-sdks**, navegue até o diretório **azure-iot-sdks/node/service/samples**.

3.  Abra **registry\_queryDevices.js** e substitua o espaço reservado pela sua cadeia de conexão do IoT Hub.

4.  Execute `node registry_queryDevices.js`.

Você deverá ver a saída na janela de linha de comando mostrando o resultado de consultas para objetos de dispositivo usando marcas, propriedades de serviço e propriedades de dispositivo.

## Estrutura de consulta (JSON)

As consultas sobre as propriedades de dispositivo e as propriedades de serviço são executadas com uma cadeia de caracteres JSON para representar a própria consulta. A cadeia de caracteres JSON é composta por 4 partes. Veja abaixo uma explicação de cada parte e o objeto do C# que pode ser serializado na cadeia de caracteres JSON correta.

- **Projeto**: a expressão que designa os campos do objeto de dispositivo a serem incluídos no conjunto de resultados da consulta (equivalente a SELECT no SQL):

	```
	var projectQuery = {
	    "project": {
	        "all": false,
	        "properties": [{
	            "name": "CustomerId",
	            "type": "service"
	        }, {
	            "name": "Weight",
	            "type": "service"
	        }]
	    }
	}
	```

- **Filtro**: a expressão que limita os objetos de dispositivo incluídos no conjunto de resultados da consulta (equivalente a WHERE no SQL):

	```
	var filterQuery = {
	  filter: {
	    property: {
	      name: "CustomerId",
	      type: "service"
	    },
	    value: "123456",
	    comparisonOperator: "eq",
	    type: "comparison"
	  },
	  project: null,
	  aggregate: null,
	  sort: null
	};
	```

- **Agregação**: a expressão que determina como agrupar o conjunto de resultados da consulta (equivalente a GROUPBY no SQL):

	```
	var aggregateQuery = {
	filter: null,
	project: null,
	aggregate: {
	keys: [
	  {
	    name: "CustomerId",
	    type: "service"
	  }
	],
	properties: [
	  {
	    operator: "sum",
	    property: {
	      name: "Weight",
	      type: "service"
	    },
	    columnName: "TotalWeight"
	  }
	]
	},
	sort: null
	};
	```

- **Classificação**: a expressão que define qual propriedade deve ser usada para classificar o conjunto de resultados da consulta (equivalente a ORDER BY no SQL). Se a classificação for nula, **deviceID** será usado por padrão:

```
  var sortQuery = {
  filter: null,
  project: null,
  aggregate: null,
  sort: [
      {
          order: "asc",
          property: {
              name: "QoS",
              type: "service"
          }
      }
  ]
  };
```

### Limitações

Existem algumas limitações na implementação de consultas da preview pública.

-   Não há nenhuma validação na expressão de consulta.

-   As consultas diferenciam maiúsculas de minúsculas.

-   Apenas 100 dispositivos serão retornados ao usar expressões de consulta para consultar por propriedades de serviço ou de dispositivo. Um exemplo de como implementar a paginação está disponível em [nossa biblioteca de consultas][lnk-query-samples].

Mais detalhes sobre a sintaxe e os campos disponíveis para o JSON foram [disponibilizados][lnk-query-expression-guide]. Também é possível ver consultas de exemplo em nossa [biblioteca de expressões de consulta][lnk-query-samples].

### Consultar por propriedades de dispositivo e de serviço

Assim que você tiver a expressão de consulta JSON, será possível consultar os dispositivos gêmeos. Chame **queryDevices** e verifique o objeto **resultado** para consultas agregadas e o objeto **devices** para todas as outras consultas.

```
registry.queryDevices(query, done)
```

### Consultar por marcas

Consultar por marcas permite encontrar objetos de dispositivo sem o uso de uma expressão de consulta JSON. Se mais de uma marca for transmitida, somente os objetos de dispositivo com todas as marcas serão retornados. O segundo parâmetro é **maxCount**, o número máximo de dispositivos a serem retornados. O valor máximo de **maxCount** é 1000.

```
registry.queryDevicesByTags(['bacon'], 100, done)
```

### Implementação de dispositivo

A consulta é habilitada pela biblioteca de cliente do gerenciamento de dispositivo Hub IoT do Azure. Desde que as propriedades do dispositivo sejam sincronizadas (conforme descrito em [Tutorial: Como usar o dispositivo gêmeo][lnk-twin-tutorial]), é possível consultá-las. As propriedades do dispositivo estão disponíveis somente depois que o dispositivo físico se conecta ao Hub IoT e fornece os valores iniciais.

## Próximas etapas

Para saber mais sobre os recursos de gerenciamento de dispositivo Hub IoT do Azure, é possível acompanhar os tutoriais:

- [Como usar o dispositivo gêmeo][lnk-twin-tutorial]
- [Como usar trabalhos do dispositivo para atualizar o firmware do dispositivo][lnk-jobs-tutorial]



<!-- images and links -->
[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-twin-tutorial]: iot-hub-device-management-device-twin.md
[lnk-jobs-tutorial]: iot-hub-device-management-device-jobs.md
[lnk-query-spec]: https://github.com/Azure/azure-iot-sdks/blob/dmpreview/node/service/devdoc/query_expression_requirements.md
[lnk-query-samples]: https://github.com/Azure/azure-iot-sdks/blob/dmpreview/doc/get_started/dm_queries/query-samples.md
[lnk-query-expression-guide]: https://github.com/Azure/azure-iot-sdks/blob/dmpreview/node/service/devdoc/query_expression_requirements.md

<!---HONumber=AcomDC_0622_2016-->