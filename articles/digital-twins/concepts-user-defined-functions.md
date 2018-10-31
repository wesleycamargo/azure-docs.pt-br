---
title: Processamento de dados e funções definidas pelo usuário com Gêmeos Digitais do Azure | Microsoft Docs
description: Visão geral do processamento de dados, correspondentes e funções definidas pelo usuário com Gêmeos Digitais do Azure
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: alinast
ms.openlocfilehash: b7561848ffd0158e22e97530774112dcee2a9864
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323656"
---
# <a name="data-processing-and-user-defined-functions"></a>Processamento de dados e funções definidas pelo usuário

O recurso Gêmeos Digitais do Azure oferece funcionalidades de computação avançadas. Os desenvolvedores podem definir e executar funções personalizadas em mensagens de telemetria de entrada para enviar eventos para pontos de extremidade predefinidos.

## <a name="data-processing-flow"></a>Fluxo de processamento de dados

Depois que os dispositivos enviam dados de telemetria para Gêmeos Digitais, os desenvolvedores podem processar dados em quatro fases: _validar_, _corresponder_, _computar_ e _expedir_:

![Fluxo de Processamento de Dados dos Gêmeos Digitais][1]

1. A fase de _validar_ transforma a mensagem de telemetria de entrada em um formato [`data transfer object`](https://en.wikipedia.org/wiki/Data_transfer_object) de fácil compreensão. Essa fase também executa validação de dispositivo e sensor.
1. A fase _corresponder_ localiza as Funções Definidas pelo Usuário apropriadas a serem executadas. Os correspondentes predefinidos descobrirão as Funções Definidas pelo Usuário com base em informações de dispositivo, sensor e espaço da mensagem de telemetria de entrada.
1. A fase de _computação_ executa as Funções Definidas pelo Usuário correspondente na fase anterior. Essas funções podem ler e atualizar valores computados em nós do grafo espacial e emitir notificações personalizadas.
1. A fase de _expedição_ roteia quaisquer notificações personalizadas da fase de computação para pontos de extremidade definidos no grafo.

## <a name="data-processing-objects"></a>Objetos de processamento de dados

Processamento de dados em Gêmeos Digitais do Azure consiste em definir três objetos: _correspondentes_, _funções definidas pelo usuário_ e _atribuições de função_:

![Fluxo de Processamento de Dados dos Gêmeos Digitais][2]

### <a name="matchers"></a>Correspondências

_Correspondentes_ definem um conjunto de condições que avaliam quais ações serão executadas com base na telemetria recebida do sensor. Essas condições para determinar a correspondência podem incluir propriedades do sensor, do dispositivo pai do sensor e do espaço do pai do sensor. As condições são expressas como comparações em relação a um [caminho JSON](http://jsonpath.com/) conforme descrito no exemplo a seguir:

- Todos os sensores do tipo de dados `Temperature`.
- Tendo `01` em sua porta.
- Quais pertencem a dispositivos com a chave de propriedade estendida `Manufacturer` definida como o valor `GoodCorp`.
- Quais pertencem aos espaços do tipo `Venue`.
- Quais são descendentes do pai `SpaceId` `DE8F06CA-1138-4AD7-89F4-F782CC6F69FD`.

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
> - Caminhos JSON diferenciam maiúsculas de minúsculas.
> - O payload JSON é igual ao payload que seria retornado por:
>   - `/sensors/{id}?includes=properties,types` para o sensor.
>   - `/devices/{id}?includes=properties,types,sensors,sensorsproperties,sensorstypes` para o dispositivo pai do sensor.
>   - `/spaces/{id}?includes=properties,types,location,timezone` para o espaço do pai do sensor.
> - As comparações não diferenciam maiúsculas de minúsculas.

### <a name="user-defined-functions"></a>Funções definidas pelo usuário

Uma _função definida pelo usuário_, ou _UDF_, é uma função personalizada executada dentro de um ambiente isolado em Gêmeos Digitais do Azure. UDFs têm acesso à mensagem de telemetria de sensor bruto como ela foi recebida, bem como para o grafo espacial e o serviço de dispatcher. Depois que o UDF está registrado no grafo, um correspondente (detalhado acima) deve ser criado para especificar quando executar o UDF. Quando o recurso Gêmeos Digitais recebe telemetria nova de um sensor determinado, o UDF correspondente pode calcular uma média móvel das últimas leituras de sensor, por exemplo.

Os UDFs podem ser escritos em JavaScript e permitir que os desenvolvedores executem trechos personalizados do código em relação a mensagens de telemetria do sensor. Também há métodos auxiliares para interagir com o grafo no ambiente de execução definido pelo usuário. Com uma UDF, os desenvolvedores podem:

- Defina a leitura do sensor diretamente para o objeto do sensor no grafo.
- Execute uma ação com base em diferentes leituras de sensor dentro de um espaço no grafo.
- Crie uma notificação quando determinadas condições forem atendidas para uma leitura de sensor de entrada.
- Anexe metadados de grafo à leitura do sensor antes de enviar uma notificação do sensor.

Veja [Como usar funções definidas pelo usuário](how-to-user-defined-functions.md) para obter mais detalhes.

### <a name="role-assignment"></a>Atribuição de função

Ações de um UDF estão sujeitas a controle de acesso baseado em função de Gêmeos Digitais para proteger os dados dentro do serviço. As atribuições de função garantem que um dado UDF tenha as permissões adequadas para interagir com o grafo espacial. Por exemplo, um UDF pode tentar criar, ler, atualizar ou excluir dados do grafo em um determinado espaço. O nível de acesso de um UDF é verificado quando o UDF solicita dados ao grafo ou tenta realizar uma ação. Para saber mais, confira [Controle de Acesso Baseado em Função](security-create-manage-role-assignments.md).

É possível uma correspondência disparar um UDF que não tenha atribuições de função. Nesse caso, o UDF consegue ler todos os dados do grafo.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como rotear eventos e mensagens de telemetria para outros serviços do Azure, leia [Como rotear eventos e mensagens](concepts-events-routing.md).

Para saber mais sobre como criar correspondentes, funções definidas pelo usuário e atribuições de função, leia o [Guia para usar funções definidas pelo usuário](how-to-user-defined-functions.md).

<!-- Images -->
[1]: media/concepts/digital-twins-data-processing-flow.png
[2]: media/concepts/digital-twins-user-defined-functions.png
