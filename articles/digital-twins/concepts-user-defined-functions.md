---
title: Processamento de dados e funções definidas pelo usuário com Gêmeos Digitais do Azure | Microsoft Docs
description: Visão geral do processamento de dados, dos correspondentes e das funções definidas pelo usuário com os Gêmeos Digitais do Azure
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: alinast
ms.openlocfilehash: 2703778cd2eab582a9e7311aaf2024f100261889
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51624515"
---
# <a name="data-processing-and-user-defined-functions"></a>Processamento de dados e funções definidas pelo usuário

O recurso Gêmeos Digitais do Azure oferece funcionalidades de computação avançadas. Os desenvolvedores podem definir e executar funções personalizadas contra mensagens de telemetria recebidas para enviar eventos para pontos de extremidade predefinidos.

## <a name="data-processing-flow"></a>Fluxo de processamento de dados

Depois que os dispositivos enviam dados de telemetria para o Azure Digital Twins, os desenvolvedores podem processar dados em quatro fases: validar, corresponder, computar e despachar.

![Fluxo de processamento de dados gêmeos Digital do Azure][1]

1. A fase de validação transforma a mensagem de telemetria recebida em um formato de objeto de transferência de dados [comumente entendido](https://en.wikipedia.org/wiki/Data_transfer_object). Essa fase também executa validação de dispositivo e sensor.
1. A fase de correspondência encontra as funções definidas pelo usuário (UDFs) apropriadas para serem executadas. Correspondentes predefinidos localizam as UDFs com base nas informações de dispositivo, sensor e espaço da mensagem de telemetria recebida.
1. A fase de computação executa as UDFs correspondidas na fase anterior. Essas funções podem ler e atualizar valores calculados em nós de gráfico espacial e podem emitir notificações personalizadas.
1. A fase de expedição encaminha quaisquer notificações personalizadas da fase de cálculo para os pontos finais definidos no gráfico.

## <a name="data-processing-objects"></a>Objetos de processamento de dados

O processamento de dados no Gêmeos Digitais do Azure consiste na definição de três objetos: correspondentes, funções definidas pelo usuário e atribuições de funções.

![Objetos de processamento de dados dos Gêmeos Digitais do Azure][2]

### <a name="matchers"></a>Correspondências

Correspondentes definem um conjunto de condições que avaliam quais ações ocorrem com base na telemetria do sensor de entrada. As condições para determinar a correspondência podem incluir propriedades do sensor, do dispositivo pai do sensor e do espaço pai do sensor. As condições são expressas como comparações em relação a um [caminho JSON](http://jsonpath.com/), conforme descrito neste exemplo:

- Todos os sensores do tipo de dados **temperatura**
- Tendo `01` em sua porta
- Que pertencem a dispositivos com a chave de propriedade estendida **Fabricante** definido para o valor`"GoodCorp"`
- Que pertencem aos espaços de tipo `"Venue"`
- Quais são descendentes do pai **SpaceId**`DE8F06CA-1138-4AD7-89F4-F782CC6F69FD`

```JSON
{
  "SpaceId": "DE8F06CA-1138-4AD7-89F4-F782CC6F69FD",
  "Name": "My custom matcher",
  "Description": "All sensors of datatype Temperature with 01 in their port that belong to devices with the extended property key Manufacturer set to the value GoodCorp and that belong to spaces of type Venue that are somewhere below space Id DE8F06CA-1138-4AD7-89F4-F782CC6F69FD",
  "Conditions": [
    {
      "target": "Sensor",
      "path": "$.dataType",
      "value": "\"Temperature\"",
      "comparison": "Equals"
    },
    {
      "target": "Sensor",
      "path": "$.port",
      "value": "01",
      "comparison": "Contains"
    },
    {
      "target": "SensorDevice",
      "path": "$.properties[?(@.name == 'Manufacturer')].value",
      "value": "\"GoodCorp\"",
      "comparison": "Equals"
    },
    {
      "target": "SensorSpace",
      "path": "$.type",
      "value": "\"Venue\"",
      "comparison": "Equals"
    }
  ]
}
```

> [!IMPORTANT]
> - Demarcadores JSON diferenciam maiúsculas de minúsculas.
> - O conteúdo JSON é o mesmo que o conteúdo que é retornado por:
>   - `/sensors/{id}?includes=properties,types` para o sensor.
>   - `/devices/{id}?includes=properties,types,sensors,sensorsproperties,sensorstypes` para o dispositivo pai do sensor.
>   - `/spaces/{id}?includes=properties,types,location,timezone` para o espaço do pai do sensor.
> - As comparações são insensíveis a maiúsculas e minúsculas.

### <a name="user-defined-functions"></a>Funções definidas pelo usuário

Uma função definida pelo usuário é uma função personalizada executada em um ambiente isolado nos Gêmeos Digitais do Azure. As UDFs têm acesso à mensagem de telemetria do sensor bruto à medida que foi recebida. As UDFs também têm acesso ao serviço de gráfico espacial e despachante. Depois que a UDF é registrada no gráfico, um correspondente (detalhado acima) deve ser criado para especificar quando executar a UDF. Quando o Azure Digital Twins recebe nova telemetria de um determinado sensor, a UDF correspondente pode calcular uma média móvel das últimas poucas leituras do sensor, por exemplo.

UDFs podem ser escritos em JavaScript. Os desenvolvedores podem executar snippets personalizados de código contra mensagens de telemetria do sensor. Os métodos auxiliares interagem com o gráfico no ambiente de execução definido pelo usuário. Com uma UDF, os desenvolvedores podem:

- Defina a leitura do sensor diretamente para o objeto do sensor no grafo.
- Execute uma ação com base em diferentes leituras de sensor dentro de um espaço no grafo.
- Crie uma notificação quando determinadas condições forem atendidas para uma leitura de sensor de entrada.
- Anexe metadados de grafo à leitura do sensor antes de enviar uma notificação do sensor.

Para obter mais informações, consulte [Como usar funções definidas pelo usuário](how-to-user-defined-functions.md).

### <a name="role-assignment"></a>Atribuição de função

Ações de um UDF estão sujeitas a controle de acesso baseado em função de Gêmeos Digitais para proteger os dados dentro do serviço. As atribuições de função garantem que uma determinada UDF tenha as permissões adequadas para interagir com o gráfico espacial. Por exemplo, um UDF pode tentar criar, ler, atualizar ou excluir dados do grafo em um determinado espaço. O nível de acesso de um UDF é verificado quando o UDF solicita dados ao grafo ou tenta realizar uma ação. Para obter mais informações, consulte [Controle de acesso baseado em função](security-create-manage-role-assignments.md).

É possível uma correspondência disparar um UDF que não tenha atribuições de função. Nesse caso, a UDF não consegue ler nenhum dado do gráfico.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre como rotear eventos e mensagens de telemetria para outros serviços do Azure, leia [Rotear eventos e mensagens](concepts-events-routing.md).

* Para saber mais sobre como criar correspondentes, funções definidas pelo usuário e atribuições de função, leia o [Guia para usar funções definidas pelo usuário](how-to-user-defined-functions.md).

<!-- Images -->
[1]: media/concepts/digital-twins-data-processing-flow.png
[2]: media/concepts/digital-twins-user-defined-functions.png
