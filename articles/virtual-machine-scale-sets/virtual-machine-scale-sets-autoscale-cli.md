---
title: "Dimensionar automaticamente conjuntos de dimensionamento de máquina virtual com a CLI do Azure | Microsoft Docs"
description: "Como criar regras de autoescala para conjuntos de dimensionamento de máquinas virtuais com a CLI do Azure 2.0"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 83e93d9c-cac0-41d3-8316-6016f5ed0ce4
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: iainfou
ms.openlocfilehash: 6e8fadd54a78d432ed802f4c4880c2f77bb28c37
ms.sourcegitcommit: 2d1153d625a7318d7b12a6493f5a2122a16052e0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/20/2017
---
# <a name="automatically-scale-a-virtual-machine-scale-set-with-the-azure-cli-20"></a>Dimensionamento automático de um conjunto de dimensionamento de máquinas virtuais com a CLI do Azure 2.0
Ao criar um conjunto de dimensionamento, o número de instâncias de VM que você deseja executar é definido. À medida que seu aplicativo precisar de alterações, você poderá aumentar ou diminuir automaticamente o número de instâncias de VM. A capacidade de autoescala permite acompanhar a demanda do cliente ou reagir a alterações de desempenho do aplicativo durante todo o ciclo de vida do aplicativo.

Este artigo mostra como criar regras de autoescala com a CLI do Azure 2.0 que monitoram o desempenho das instâncias de VM no seu conjunto de dimensionamento. Essas regras de autoescala aumentam ou diminuem o número de instâncias de VM em resposta a essas métricas de desempenho. Você também pode concluir estas etapas usando o [Azure PowerShell](virtual-machine-scale-sets-autoscale-powershell.md) ou o [portal do Azure](virtual-machine-scale-sets-autoscale-portal.md).


## <a name="prerequisites"></a>Pré-requisitos
Para criar regras de autoescala, você precisará de um conjunto de dimensionamento de máquinas virtuais existente. É possível criar um conjunto de dimensionamento com o [portal do Azure](virtual-machine-scale-sets-portal-create.md), a [CLI do Azure 2.0](virtual-machine-scale-sets-create.md#create-from-azure-cli) ou o [Azure PowerShell](virtual-machine-scale-sets-create.md#create-from-powershell).

Para facilitar a criação de regras de autoescala, defina algumas variáveis para o conjunto de dimensionamento. O exemplo a seguir define variáveis para o conjunto de dimensionamento chamado *myScaleSet* no grupo de recursos denominado *myResourceGroup* e na região *eastus*. Sua ID da assinatura é obtida com [az account show](/cli/azure/account#az_account_show). Se você tiver várias assinaturas associadas à sua conta, somente a primeira será retornada. Ajuste os nomes e a ID da assinatura da seguinte maneira:

```azurecli
sub=$(az account show --query id -o tsv)
resourcegroup_name="myResourceGroup"
scaleset_name="myScaleSet"
location_name="eastus"
```

## <a name="define-an-autoscale-profile"></a>Definir um perfil de autoescala
As regras de autoescala são implantadas como JSON (JavaScript Object Notation) com a CLI do Azure 2.0. O JSON completo que define e implementa as regras de autoescala pode ser encontrado posteriormente neste artigo. 

O início do perfil de autoescala define a capacidade do conjunto de dimensionamento padrão, mínima e máxima. O exemplo a seguir define a capacidade padrão e mínima de *2* instâncias de VM, bem como um máximo de *10*:

```json
{
  "name": "autoscale rules",
  "capacity": {
    "minimum": "2",
    "maximum": "10",
    "default": "2"
  }
}
```


## <a name="create-a-rule-to-automatically-scale-out"></a>Crie uma regra para expandir automaticamente
Se a demanda do aplicativo aumentar, a carga em instâncias de VM no seu conjunto de dimensionamento também aumentará. Se esse aumento de carga for consistente, em vez de apenas uma demanda breve, configure as regras de dimensionamento automático para aumentar o número de instâncias de VM no conjunto de dimensionamento. Quando essas instâncias de VM forem criadas e os aplicativos implantados, o conjunto de dimensionamento começará a distribuir o tráfego para eles por meio do balanceador de carga. Você controla quais métricas são monitoradas, como CPU ou disco, por quanto tempo a carga do aplicativo deve atender a determinado limite e quantas instâncias de VM devem ser adicionadas ao conjunto de dimensionamento.

Vamos criar uma regra que aumenta o número de instâncias de VM em um conjunto de dimensionamento definido quando a carga da CPU média for maior que 70% por um período de 10 minutos. Quando a regra dispara, o número de instâncias de VM aumentará em 20%. Em conjuntos de dimensionamento com um pequeno número de instâncias de VM, você poderia definir `type` como *ChangeCount* e aumentar `value` em *1* ou *2* instâncias. Em conjuntos de dimensionamento com um grande número de instâncias de VM, um aumento de 10% ou 20% de instâncias de VM pode ser mais apropriado.

Os seguintes parâmetros são usados para essa regra:

| Parâmetro         | Explicação                                                                                                         | Valor           |
|-------------------|---------------------------------------------------------------------------------------------------------------------|-----------------|
| *metricName*      | A métrica de desempenho a ser monitorada, na qual as ações do conjunto de dimensionamento serão aplicadas.                                                   | Porcentagem de CPU  |
| *timeGrain*       | Com que frequência as métricas são coletadas para análise.                                                                   | 1 minuto        |
| *timeAggregation* | Define como as métricas coletadas devem ser agregadas para análise.                                                | Média         |
| *timeWindow*      | O tempo monitorado antes de comparar os valores de métrica e de limite.                                   | 10 minutos      |
| *operator*        | Operador usado para comparar os dados da métrica com o limite.                                                     | Maior que    |
| *threshold*       | O valor que faz com que a regra de autoescala dispare uma ação.                                                      | 70%             |
| *direction*       | Define se o conjunto de dimensionamento deve ser dimensionado expandido ou reduzido quando a regra se aplica.                                             | Aumento        |
| *tipo*            | Indica que o número de instâncias de VM que deve ser modificado por um valor de percentual.                                 | Porcentagem de modificação  |
| *valor*           | Quantas instâncias de VM devem ser reduzidas ou ampliadas quando a regra se aplica.                                            | 20              |
| *cooldown*        | O tempo de espera antes da regra ser aplicada novamente para que as ações de autoescala tenham tempo para entrar em vigor. | 5 minutos       |

O exemplo a seguir define a regra para expandir o número de instâncias de VM. O *metricResourceUri* usa as variáveis definidas anteriormente para a ID de assinatura, o nome do grupo de recursos e o nome do conjunto de dimensionamento:

```json
{
  "metricTrigger": {
    "metricName": "Percentage CPU",
    "metricNamespace": "",
    "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
    "metricResourceLocation": "'$location_name'",
    "timeGrain": "PT1M",
    "statistic": "Average",
    "timeWindow": "PT10M",
    "timeAggregation": "Average",
    "operator": "GreaterThan",
    "threshold": 70
  },
  "scaleAction": {
    "direction": "Increase",
    "type": "PercentChangeCount",
    "value": "20",
    "cooldown": "PT5M"
  }
}
```


## <a name="create-a-rule-to-automatically-scale-in"></a>Criar uma regra para reduzir automaticamente
À noite ou durante o fim de semana, a demanda do seu aplicativo pode diminuir. Se essa diminuição de carga for consistente durante determinado período, configure as regras de dimensionamento automático para reduzir o número de instâncias de VM no conjunto de dimensionamento. Esta ação de redução diminui o custo para executar seu conjunto de dimensionamento, visto que apenas o número de instâncias necessárias para atender à demanda atual é executado.

Crie outra regra que diminua o número de instâncias de VM em um conjunto de dimensionamento definido quando a carga da CPU média for menor que 30% por um período de 10 minutos. O exemplo a seguir define a regra para expandir o número de instâncias de VM. O *metricResourceUri* usa as variáveis definidas anteriormente para a ID de assinatura, o nome do grupo de recursos e o nome do conjunto de dimensionamento:

```json
{
  "metricTrigger": {
    "metricName": "Percentage CPU",
    "metricNamespace": "",
    "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
    "metricResourceLocation": "'$location_name'",
    "timeGrain": "PT1M",
    "statistic": "Average",
    "timeWindow": "PT10M",
    "timeAggregation": "Average",
    "operator": "LessThan",
    "threshold": 30
  },
  "scaleAction": {
    "direction": "Decrease",
    "type": "PercentChangeCount",
    "value": "20",
    "cooldown": "PT5M"
  }
}
```


## <a name="apply-autoscale-rules-to-a-scale-set"></a>Aplicar regras de autoescala a um conjunto de dimensionamento
A etapa final é aplicar o perfil e regras de autoescala ao seu conjunto de dimensionamento. Sua escala poderá ser expandida ou reduzida automaticamente com base na demanda de aplicativo. Aplique o perfil de autoescala com [az monitor autoscale-settings create](/cli/azure/monitor/autoscale-settings#az_monitor_autoscale_settings_create) da seguinte maneira. O JSON completo usa o perfil e as regras observados nas seções anteriores.

```azurecli
az monitor autoscale-settings create \
    --resource-group myResourceGroup \
    --name autoscale \
    --parameters '{"autoscale_setting_resource_name": "autoscale",
      "enabled": true,
      "location": "'$location_name'",
      "notifications": [],
      "profiles": [
        {
          "name": "autoscale by percentage based on CPU usage",
          "capacity": {
            "minimum": "2",
            "maximum": "10",
            "default": "2"
          },
          "rules": [
            {
              "metricTrigger": {
                "metricName": "Percentage CPU",
                "metricNamespace": "",
                "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
                "metricResourceLocation": "'$location_name'",
                "timeGrain": "PT1M",
                "statistic": "Average",
                "timeWindow": "PT10M",
                "timeAggregation": "Average",
                "operator": "GreaterThan",
                "threshold": 70
              },
              "scaleAction": {
                "direction": "Increase",
                "type": "PercentChangeCount",
                "value": "20",
                "cooldown": "PT5M"
              }
            },
            {
              "metricTrigger": {
                "metricName": "Percentage CPU",
                "metricNamespace": "",
                "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
                "metricResourceLocation": "'$location_name'",
                "timeGrain": "PT1M",
                "statistic": "Average",
                "timeWindow": "PT10M",
                "timeAggregation": "Average",
                "operator": "LessThan",
                "threshold": 30
              },
              "scaleAction": {
                "direction": "Decrease",
                "type": "PercentChangeCount",
                "value": "20",
                "cooldown": "PT5M"
              }
            }
          ]
        }
      ],
      "tags": {},
      "target_resource_uri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'"
    }'
```


## <a name="monitor-number-of-instances-in-a-scale-set"></a>Monitorar o número de instâncias em um conjunto de dimensionamento
Para ver o número e o status de instâncias de VM, exiba uma lista de instâncias no seu conjunto de dimensionamento com [az vmss list-instances](/cli/azure/vmss#az_vmss_list_instances). O status indica se a instância VM é provisionada à medida que o conjunto de dimensionamento é expandido automaticamente ou desprovisionada à medida que a escala é reduzida automaticamente. O exemplo a seguir exibe o status da instância VM para o conjunto de dimensionamento chamado *myScaleSet* no grupo de recursos denominado *myResourceGroup*:

```azurecli
az vmss list-instances \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --output table
```


## <a name="autoscale-based-on-a-schedule"></a>Dimensionamento automático baseado em agendamento
Os exemplos anteriores dimensionaram automaticamente um conjunto de dimensionamento com métricas de host básicas como uso da CPU. Também é possível criar regras de dimensionamento automático baseadas em agendamentos. Essas regras baseadas em agendamento permitem expandir automaticamente o número de instâncias de VM antes de um aumento previsto na demanda do aplicativo, como horas de trabalho principal e, depois, reduz automaticamente o número de instâncias no momento em que uma demanda menor for prevista, como durante o fim de semana.

Para usar regras de dimensionamento automático com base em agenda, crie um perfil JSON que define o número de instâncias de VM a ser executadas para uma janela de tempo de início e fim fixa. O exemplo a seguir define uma regra para extensão para *10* instâncias às *9* da manhã de cada dia útil (de segunda-feira a sexta-feira).

```json
{
  "name": "Scale out during each work day",
  "capacity": {
      "minimum": "10",
      "maximum": "10",
      "default": "10"
  },
  "rules": [],
  "recurrence": {
      "frequency": "Week",
      "schedule": {
          "timeZone": "Pacific Standard Time",
          "days": [
              "Monday",
              "Tuesday",
              "Wednesday",
              "Thursday",
              "Friday"
          ],
          "hours": [
              9
          ],
          "minutes": [
              0
          ]
      }
  }
}
```

Para reduzir durante a noite, crie outra regra que especifique um número menor de instâncias de VM e uma hora de início apropriada.

O seguinte exemplo completo define as regras de expansão e então de redução, depois aplique o perfil de autoescala com [az monitor autoscale-settings create](/cli/azure/monitor/autoscale-settings#az_monitor_autoscale_settings_create). Este exemplo substitui as regras de autoescala com base em métrica criadas nos exemplos anteriores. O *metricResourceUri* usa as variáveis definidas anteriormente para a ID de assinatura, o nome do grupo de recursos e o nome do conjunto de dimensionamento:

```azurecli
az monitor autoscale-settings create \
    --resource-group myResourceGroup \
    --name autoscale \
    --parameters '{"autoscale_setting_resource_name": "autoscale",
      "enabled": true,
      "location": "'$location_name'",
      "notifications": [],
      "profiles": [
        {
          "name": "Scale out during each work day",
          "capacity": {
            "minimum": "10",
            "maximum": "10",
            "default": "10"
          },
          "rules": [],
          "recurrence": {
            "frequency": "Week",
            "schedule": {
              "timeZone": "Pacific Standard Time",
              "days": [
                "Monday",
                "Tuesday",
                "Wednesday",
                "Thursday",
                "Friday"
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
          "name": "Scale in during the evening",
          "capacity": {
            "minimum": "3",
            "maximum": "3",
            "default": "3"
          },
          "rules": [],
          "recurrence": {
            "frequency": "Week",
            "schedule": {
              "timeZone": "Pacific Standard Time",
              "days": [
                "Monday",
                "Tuesday",
                "Wednesday",
                "Thursday",
                "Friday"
              ],
              "hours": [
                18
              ],
              "minutes": [
                0
              ]
            }
          }
        }
      ],
      "tags": {},
      "target_resource_uri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'"
    }'
```


## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu a usar as regras de autoescala para escalar horizontalmente e expandir ou diminuir o *número* de instâncias de VM no seu conjunto de dimensionamento. Também é possível escalar verticalmente para aumentar ou diminuir o *tamanho* da instância VM. Para saber mais, veja [Dimensionamento vertical automático com conjuntos de dimensionamento de máquinas virtuais](virtual-machine-scale-sets-vertical-scale-reprovision.md).

Para obter informações sobre como gerenciar suas instâncias de VM, consulte [Gerenciar conjuntos de dimensionamento de máquinas virtuais com o Azure PowerShell](virtual-machine-scale-sets-windows-manage.md).

Para saber como gerar alertas quando suas regras de dimensionamento automático forem disparadas, consulte [Usar ações de dimensionamento automático para enviar notificações de alerta por email e webhook no Azure Monitor](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md). Também é possível [Usar logs de auditoria para enviar notificações de alerta por email e webhook no Azure Monitor](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md).
