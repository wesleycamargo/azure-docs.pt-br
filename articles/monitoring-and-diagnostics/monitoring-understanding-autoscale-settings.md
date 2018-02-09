---
title: "Noções básicas sobre configurações de dimensionamento automático | Microsoft Docs"
description: "Uma análise detalhada das configurações de dimensionamento automático e como elas funcionam."
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ce2930aa-fc41-4b81-b0cb-e7ea922467e1
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2017
ms.author: ancav
ms.openlocfilehash: 79602cf053d834bf3d6dc6b4d5568637b179d5c7
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2018
---
# <a name="understand-autoscale-settings"></a>Noções básicas das configurações de dimensionamento automático
As configurações de dimensionamento automático permitem que você verifique se tem a quantidade certa de recursos em execução para manipular a carga flutuante do aplicativo. Você pode definir as configurações de dimensionamento automático para serem disparadas com base em métricas que indicam a carga ou o desempenho, ou para serem disparadas em uma data e hora agendadas. Este artigo analisa detalhadamente a anatomia de uma configuração de dimensionamento automático. O artigo começa com as noções básicas sobre o esquema e as propriedades de uma configuração, percorre os diferentes tipos de perfil que podem ser configurados e, por fim, discute como o dimensionamento automático avalia qual perfil deve ser executado em um determinado momento.

## <a name="autoscale-setting-schema"></a>Esquema de configuração de dimensionamento automático
Para ilustrar o esquema de configuração de dimensionamento automático, a seguinte configuração de dimensionamento automático é usada. É importante observar que essa configuração de dimensionamento automático tem:
- Um perfil 
- Há duas regras de métrica neste perfil, uma para aumentar e outra para reduzir.
- A regra para aumentar é disparada quando a média da métrica Percentual de CPU do conjunto de dimensionamento de máquinas virtuais é superior a 85% nos últimos 10 min.
- A regra para reduzir é disparada quando a média do conjunto de dimensionamento de máquinas virtuais é menor que 60% no último minuto.

> [!NOTE]
> Uma configuração pode ter vários perfis, confira a seção [perfis](#autoscale-profiles) para saber mais.
> Um perfil também pode ter várias regras para aumentar e reduzir definidas, confira a [seção de avaliação](#autoscale-evaluation) para ver como elas são avaliadas

```JSON
{
  "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/autoscalesettings/setting1",
  "name": "setting1",
  "type": "Microsoft.Insights/autoscaleSettings",
  "location": "East US",
  "properties": {
    "enabled": true,
    "targetResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/vmss1",
    "profiles": [
      {
        "name": "mainProfile",
        "capacity": {
          "minimum": "1",
          "maximum": "4",
          "default": "1"
        },
        "rules": [
          {
            "metricTrigger": {
              "metricName": "Percentage CPU",
              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/vmss1",
              "timeGrain": "PT1M",
              "statistic": "Average",
              "timeWindow": "PT10M",
              "timeAggregation": "Average",
              "operator": "GreaterThan",
              "threshold": 85
            },
            "scaleAction": {
              "direction": "Increase",
              "type": "ChangeCount",
              "value": "1",
              "cooldown": "PT5M"
            }
          },
    {
            "metricTrigger": {
              "metricName": "Percentage CPU",
              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/vmss1",
              "timeGrain": "PT1M",
              "statistic": "Average",
              "timeWindow": "PT10M",
              "timeAggregation": "Average",
              "operator": "LessThan",
              "threshold": 60
            },
            "scaleAction": {
              "direction": "Decrease",
              "type": "ChangeCount",
              "value": "1",
              "cooldown": "PT5M"
            }
          }
        ]
      }
    ]
  }
}
```

| Seção | Nome do elemento | DESCRIÇÃO |
| --- | --- | --- |
| Configuração | ID | A ID do recurso da configuração de dimensionamento automático. As configurações de dimensionamento automático são um recurso do Azure Resource Manager. |
| Configuração | Nome | O nome da configuração de dimensionamento automático. |
| Configuração | location | O local da configuração de dimensionamento automático. Esse local pode ser diferente do local em que o recurso está sendo dimensionado. |
| propriedades | targetResourceUri | A ID do recurso que está sendo dimensionado. Você só pode ter uma configuração de dimensionamento automático por recurso. |
| propriedades | perfis | Uma configuração de dimensionamento automático é composta de um ou mais perfis. Cada vez que o mecanismo de dimensionamento automático é executado, ele executa um perfil. |
| Perfil | Nome | O nome do perfil. Você pode escolher qualquer nome que o ajudará a identificar o perfil. |
| Perfil | Capacity.maximum | A capacidade máxima permitida. Garante que o dimensionamento automático, ao executar este perfil, não dimensione os recursos acima desse limite. |
| Perfil | Capacity.minimum | A capacidade mínima permitida. Garante que o dimensionamento automático, ao executar este perfil, não dimensione os recursos abaixo desse limite. |
| Perfil | Capacity.default | Se houver algum problema ao ler a métrica do recurso (nesse caso, a CPU do "vmss1") e a capacidade atual estiver abaixo da capacidade padrão, para garantir a disponibilidade do recurso, o dimensionamento automático aumentará para o padrão. Se a capacidade atual já for maior do que a capacidade padrão, o dimensionamento automático não será reduzido. |
| Perfil | regras | O dimensionamento automático dimensiona entre as capacidades máximas e mínimas usando as regras do perfil. Pode haver várias regras em um perfil. O cenário básico é que haja duas regras, uma para determinar quando aumentar e outra para determinar quando reduzir. |
| Regra | metricTrigger | Define a condição de métrica da regra. |
| metricTrigger | metricName | O nome da métrica. |
| metricTrigger |  metricResourceUri | A ID do recurso que emite a métrica. Na maioria dos casos, é o mesmo que o do recurso que está sendo dimensionado. Em alguns casos, pode ser diferente, por exemplo, você pode dimensionar um conjunto de dimensionamento de máquinas virtuais com base no número de mensagens em uma fila de armazenamento. |
| metricTrigger | timeGrain | A duração de métrica de amostragem. Por exemplo, TimeGrain = "PT1M" significa que a métrica deve ser agregada a cada 1 minuto usando o método de agregação especificado em "estatística". |
| metricTrigger | statistic | O método de agregação dentro do período de timeGrain. Por exemplo, a estatística = "Average" e timeGrain = "PT1M" significa que as métricas devem ser agregadas a cada 1 minuto ao considerar a média. Essa propriedade determina como a métrica é amostrada. |
| metricTrigger | timeWindow | O período de tempo no qual as métricas devem ser consultadas. Por exemplo, timeWindow = "PT10M" significa que sempre que o dimensionamento automático for executado, ele consultará as métricas dos últimos 10 minutos. O período de tempo permite que as métricas sejam normalizadas e evita uma reação a picos transitórios. |
| metricTrigger | timeAggregation | O método de agregação usado para agregar as métricas amostradas. Por exemplo, TimeAggregation = "Average" deve agregar as métricas amostradas considerando a média. No caso acima, é obtida a média das dez amostras de um minuto. |
| Regra | scaleAction | A ação a ser executada quando o metricTrigger da regra for disparado. |
| scaleAction | direction | "Increase" para aumentar, "Decrease" para reduzir|
| scaleAction | value | Em quanto aumentar ou diminuir a capacidade do recurso |
| scaleAction | cooldown | O período de tempo a esperar após uma operação de dimensionamento antes de dimensionar novamente. Por exemplo, se cooldown = "PT10M", após uma operação de dimensionamento, o dimensionamento automático não tentará dimensionar novamente nos próximos 10 minutos. O resfriamento deve permitir que as métricas se estabilizem após a adição ou a remoção de instâncias. |

## <a name="autoscale-profiles"></a>Perfis de dimensionamento automático

Há três tipos de perfis de dimensionamento automático:

1. **Perfil regular:** o perfil mais comum. Se você não precisar dimensionar seu recurso de maneira diferente com base no dia da semana ou em um dia específico, bastará configurar um perfil regular na configuração de dimensionamento automático. Este perfil poderá ser configurado com regras da métrica que determinam quando aumentar e quando reduzir. Deve haver somente um perfil regular definido.

    O perfil de exemplo usado anteriormente neste artigo é um exemplo de um perfil regular. Observe que também é possível definir um perfil para dimensionar para uma contagem de instância estática do recurso.

2. **Perfil de data fixa:** com o perfil regular definido, digamos que você tenha um evento importante em 26 de dezembro de 2017 (PST) e deseje que as capacidades mínima/máxima do recurso sejam diferentes nesse dia, mas ainda sejam dimensionadas com as mesmas métricas. Nesse caso, você deve adicionar um perfil de data fixa à lista de perfis da configuração. O perfil será configurado para ser executado somente no dia do evento. Nos outros dias, o perfil regular será executado.

    ``` JSON
    "profiles": [{
    "name": " regularProfile",
    "capacity": {
    ...
    },
    "rules": [{
    ...
    },
    {
    ...
    }]
    },
    {
    "name": "eventProfile",
    "capacity": {
    ...
    },
    "rules": [{
    ...
    }, {
    ...
    }],
    "fixedDate": {
        "timeZone": "Pacific Standard Time",
               "start": "2017-12-26T00:00:00",
               "end": "2017-12-26T23:59:00"
    }}
    ]
    ```
    
3. **Perfil de recorrência:** esse tipo de perfil permite garantir que esse perfil sempre seja usado em um determinado dia da semana. Os perfis de recorrência têm apenas uma hora de início, assim, eles são executados até que o próximo perfil de recorrência ou de data fixa esteja definido para ser iniciado. Uma configuração de dimensionamento automático com apenas um perfil de recorrência executa esse perfil, mesmo se há um perfil regular definido na mesma configuração. Os dois exemplos a seguir ilustram o uso do perfil:

    **Exemplo 1 – dias da semana em relação a finais de semana** Digamos que nos finais de semana você deseje que a capacidade máxima seja 4, mas nos dias da semana, em que espera uma carga maior, você deseje que a capacidade máxima seja 10. Nesse caso, a configuração conterá dois perfis de recorrência, um para ser executado nos finais de semana e o outro nos dias da semana.
    A configuração deve ser como esta:

    ``` JSON
    "profiles": [
    {
    "name": "weekdayProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }],
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Monday"
            ],
            "hours": [
                0
            ],
            "minutes": [
                0
            ]
        }
    }}
    },
    {
    "name": "weekendProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }]
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Saturday"
            ],
            "hours": [
                0
            ],
            "minutes": [
                0
            ]
        }
    }
    }]
    ```

    Ao examinar a configuração anterior, você observará que cada perfil de recorrência tem uma agenda, essa agenda determina quando o perfil começa a ser executado. O perfil deixa de ser executado quando chega a hora de executar outro perfil.

    Por exemplo, a configuração anterior, "weekdayProfile" é definido para ser iniciado às segundas-feiras à meia-noite, o que significa que esse perfil começará a ser executado na segunda-feira à meia-noite. Ele continuará a ser executado até sábado à meia-noite, quando "weekendProfile" está agendado para iniciar sua execução.

    **Exemplo 2 – horário comercial** Vejamos outro exemplo, é possível definir o limite de métrica = 'x' durante o horário comercial, das 9h às 17h e, depois, das 17h às 9h do dia seguinte, definir o limite de métrica como 'y'.
    A configuração deve ser como esta:
    
    ``` JSON
    "profiles": [
    {
    "name": "businessHoursProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }],
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Monday", “Tuesday”, “Wednesday”, “Thursday”, “Friday”
            ],
            "hours": [
                9
            ],
            "minutes": [
                0
            ]
        }
    }
    },
    {
    "name": "nonBusinessHoursProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }]
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Monday", “Tuesday”, “Wednesday”, “Thursday”, “Friday”
            ],
            "hours": [
                17
            ],
            "minutes": [
                0
            ]
        }
    }
    }]
    ```
    
    Ao examinar a configuração anterior, "businessHoursProfile" começa sua execução às segundas-feiras às 9h e mantém a execução até às 17h, que é quando “nonBusinessHoursProfile” inicia sua execução. O "nonBusinessHoursProfile" é executado até às 9h de terça-feira e, em seguida, o "businessHoursProfile" retorna. Isso é repetido até sexta-feira, 17h, quando o "nonBusinessHoursProfile" começa a ser executado até segunda-feira às 9h, pois o "businessHoursProfile" apenas iniciará sua execução na segunda-feira às 9h.
    
> [!Note]
> O UX de dimensionamento automático UX no portal do Azure impõe horários de término para perfis de recorrência e começa a executar o perfil de padrão da configuração de dimensionamento automático entre os perfis de recorrência.
    
## <a name="autoscale-evaluation"></a>Avaliação do dimensionamento automático
Considerando que as configurações de dimensionamento automático podem ter vários perfis de dimensionamento automático, e que cada perfil pode ter várias regras de métrica, é importante entender como uma configuração de dimensionamento automático é avaliada. Sempre que o trabalho de dimensionamento automático é executado, ele começa escolhendo o perfil aplicável e, depois de escolher o perfil, ele avalia os valores mínimo e máximo, as regras de métrica do perfil e decide se uma ação de dimensionamento é necessária.

### <a name="which-profile-will-autoscale-pick"></a>Qual perfil o dimensionamento automático escolherá?
- Primeiro o dimensionamento automático procurará um perfil de data fixa configurado para ser executado no momento e, se houver, o executará. Se houver vários perfis de data fixa para serem executados, o dimensionamento automático selecionará o primeiro deles.
- Se não houver nenhum perfil de data fixa, o dimensionamento automático examinará os perfis de recorrência e, se encontrar algum, o executará.
- Se não houver nenhum perfil de data fixa nem de recorrência, o dimensionamento automático executará o perfil regular.

### <a name="how-does-autoscale-evaluate-multiple-rules"></a>Como o dimensionamento automático avalia várias regras?

Depois que o dimensionamento automático determina qual perfil deve ser executado, ele começa a avaliar a regra para aumentar no perfil (regras com a orientação = "Increase").
- Se uma ou mais regras para aumentar forem disparadas, o dimensionamento automático calculará a nova capacidade determinada pelo scaleAction de cada uma dessas regras. Em seguida, ele escalará horizontalmente até o máximo dessas capacidades para garantir a disponibilidade do serviço.
- Por exemplo: se houver um conjunto de dimensionamento de máquinas virtuais com uma capacidade atual igual a 10 e houver duas regras para aumentar, uma que aumenta a capacidade em 10% e outra que aumenta a capacidade em 3. A primeira regra resultará em uma nova capacidade igual a 11 e a segunda regra resultará em uma capacidade igual a 13. Para garantir a disponibilidade do serviço, o dimensionamento automático escolhe a ação que resulta na capacidade máxima, portanto, a segunda regra é escolhida.

Se nenhuma regra para aumentar é disparada, o dimensionamento automático avalia todas as regras para reduzir (regras com a orientação = "Decrease"). O dimensionamento automático só executará uma ação de redução se todas as regras para reduzir forem disparadas.
- O dimensionamento automático calculará a nova capacidade determinada pelo scaleAction de cada uma dessas regras. Em seguida, ele escolherá a ação de dimensionamento que resultará no máximo dessas capacidades para garantir a disponibilidade do serviço.
- Por exemplo: se houver um conjunto de dimensionamento de máquinas virtuais com uma capacidade atual igual a 10 e houver duas regras para reduzir, uma que diminui a capacidade em 50% e outra que diminui a capacidade em 3. A primeira regra resultará em uma nova capacidade igual a 5 e a segunda regra resultará em uma capacidade igual a 7. Para garantir a disponibilidade do serviço, o dimensionamento automático escolhe a ação que resulta na capacidade máxima, portanto, a segunda regra é escolhida.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o dimensionamento automático, consulte os seguintes recursos:

* [Visão geral do dimensionamento automático](monitoring-overview-autoscale.md)
* [Métricas comuns de dimensionamento automático do Azure Monitor](insights-autoscale-common-metrics.md)
* [Práticas recomendadas para dimensionamento automático do Azure Monitor](insights-autoscale-best-practices.md)
* [Usar ações de dimensionamento automático para enviar notificações de alerta por email e webhook](insights-autoscale-to-webhook-email.md)
* [API REST do Dimensionamento Automático](https://msdn.microsoft.com/library/dn931953.aspx)
