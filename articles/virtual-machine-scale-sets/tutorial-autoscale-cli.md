---
title: Tutorial - Dimensionar automaticamente um conjunto de dimensionamento com a CLI do Azure 2.0 | Microsoft Docs
description: Saiba como usar a CLI do Azure 2.0 para dimensionar automaticamente um conjunto de dimensionamento de máquinas virtuais conforme a demanda de CPU aumenta e diminui
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 10e5b1a261f28391bed8cf3f111b1124b52d7816
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2018
---
# <a name="tutorial-automatically-scale-a-virtual-machine-scale-set-with-the-azure-cli-20"></a>Tutorial: Dimensionamento automático de um conjunto de dimensionamento de máquinas virtuais com a CLI do Azure 2.0
Ao criar um conjunto de dimensionamento, o número de instâncias de VM que você deseja executar é definido. À medida que seu aplicativo precisar de alterações, você poderá aumentar ou diminuir automaticamente o número de instâncias de VM. A capacidade de autoescala permite acompanhar a demanda do cliente ou reagir a alterações de desempenho do aplicativo durante todo o ciclo de vida do aplicativo. Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Usar o dimensionamento automático com um conjunto de dimensionamento
> * Criar e usar regras de dimensionamento automático
> * Testar instâncias de VM sob estresse e disparar regras de dimensionamento automático
> * Redimensionamento automático com a redução da demanda

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e usar a CLI localmente, este tutorial exigirá que você esteja executando a CLI do Azure versão 2.0.29 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli). 


## <a name="create-a-scale-set"></a>Criar um conjunto de escala
Para ajudar a criar as regras de dimensionamento automático, defina alguns parâmetros de ID de assinatura, nome do grupo de recursos e do conjunto de dimensionamento, além de local:

```azurecli-interactive
sub=$(az account show --query id -o tsv)
resourcegroup_name="myResourceGroup"
scaleset_name="myScaleSet"
location_name="eastus"
```

Crie um grupo de recursos com [az group create](/cli/azure/group#create) da seguinte forma:

```azurecli-interactive
az group create --name $resourcegroup_name --location $location_name
```

Crie um conjunto de dimensionamento de máquinas virtuais com [az vmss create](/cli/azure/vmss#create). O exemplo abaixo criará um conjunto de dimensionamento com contagem de instâncias de *2* e gerará chaves SSH se elas não existirem:

```azurecli-interactive
az vmss create \
  --resource-group $resourcegroup_name \
  --name $scaleset_name \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --instance-count 2 \
  --admin-username azureuser \
  --generate-ssh-keys
```


## <a name="define-an-autoscale-profile"></a>Definir um perfil de autoescala
As regras de autoescala são implantadas como JSON (JavaScript Object Notation) com a CLI do Azure 2.0. Vamos examinar cada parte do perfil de dimensionamento automático para depois criar um exemplo completo.

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


## <a name="create-a-rule-to-autoscale-out"></a>Criar uma regra de dimensionamento automático para aumento
Se a demanda do aplicativo aumentar, a carga em instâncias de VM no seu conjunto de dimensionamento também aumentará. Se esse aumento de carga for consistente, em vez de apenas uma demanda breve, configure as regras de dimensionamento automático para aumentar o número de instâncias de VM no conjunto de dimensionamento. Quando essas instâncias de VM forem criadas e os aplicativos implantados, o conjunto de dimensionamento começará a distribuir o tráfego para eles por meio do balanceador de carga. Você controla quais métricas são monitoradas, como CPU ou disco, por quanto tempo a carga do aplicativo deve atender a determinado limite e quantas instâncias de VM devem ser adicionadas ao conjunto de dimensionamento.

Vamos criar uma regra que aumenta o número de instâncias de VM em um conjunto de dimensionamento quando a carga da CPU média for maior que 70% por um período de cinco minutos. Quando a regra é disparada, o número de instâncias de VM aumenta em 20%.

Os seguintes parâmetros são usados para essa regra:

| Parâmetro         | Explicação                                                                                                         | Valor           |
|-------------------|---------------------------------------------------------------------------------------------------------------------|-----------------|
| *metricName*      | A métrica de desempenho a ser monitorada, na qual as ações do conjunto de dimensionamento serão aplicadas.                                                   | Porcentagem de CPU  |
| *timeGrain*       | Com que frequência as métricas são coletadas para análise.                                                                   | 1 minuto        |
| *timeAggregation* | Define como as métricas coletadas devem ser agregadas para análise.                                                | Média         |
| *timeWindow*      | O tempo monitorado antes de comparar os valores de métrica e de limite.                                   | 5 minutos       |
| *operator*        | Operador usado para comparar os dados da métrica com o limite.                                                     | Maior que    |
| *threshold*       | O valor que faz com que a regra de autoescala dispare uma ação.                                                      | 70%             |
| *direction*       | Define se o conjunto de dimensionamento deve ser reduzido ou escalado horizontalmente quando a regra se aplicar.                                              | Aumento        |
| *tipo*            | Indica que o número de instâncias de VM deve ser modificado por um valor específico.                                    | Alterar contagem    |
| *valor*           | Quantas instâncias de VM devem ser reduzidas ou escaladas horizontalmente quando a regra se aplicar.                                             | 3               |
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
    "timeWindow": "PT5M",
    "timeAggregation": "Average",
    "operator": "GreaterThan",
    "threshold": 70
  },
  "scaleAction": {
    "direction": "Increase",
    "type": "ChangeCount",
    "value": "3",
    "cooldown": "PT5M"
  }
}
```


## <a name="create-a-rule-to-autoscale-in"></a>Criar uma regra de dimensionamento automático para redução
À noite ou durante o fim de semana, a demanda do seu aplicativo pode diminuir. Se essa diminuição de carga for consistente durante determinado período, configure as regras de dimensionamento automático para reduzir o número de instâncias de VM no conjunto de dimensionamento. Esta ação de redução diminui o custo para executar seu conjunto de dimensionamento, visto que apenas o número de instâncias necessárias para atender à demanda atual é executado.

Crie outra regra que diminua o número de instâncias de VM em um conjunto de dimensionamento quando a carga da CPU média for menor que 30% por um período de cinco minutos. O exemplo a seguir define a regra para reduzir o número de instâncias de VM em um. O *metricResourceUri* usa as variáveis definidas anteriormente para a ID de assinatura, o nome do grupo de recursos e o nome do conjunto de dimensionamento:

```json
{
  "metricTrigger": {
    "metricName": "Percentage CPU",
    "metricNamespace": "",
    "metricResourceUri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'",
    "metricResourceLocation": "'$location_name'",
    "timeGrain": "PT1M",
    "statistic": "Average",
    "timeWindow": "PT5M",
    "timeAggregation": "Average",
    "operator": "LessThan",
    "threshold": 30
  },
  "scaleAction": {
    "direction": "Decrease",
    "type": "ChangeCount",
    "value": "1",
    "cooldown": "PT5M"
  }
}
```


## <a name="apply-autoscale-rules-to-a-scale-set"></a>Aplicar regras de autoescala a um conjunto de dimensionamento
A etapa final é aplicar o perfil e regras de autoescala ao seu conjunto de dimensionamento. O conjunto de dimensionamento poderá ser expandido ou reduzido automaticamente com base na demanda do aplicativo. Aplique o perfil de autoescala com [az monitor autoscale-settings create](/cli/azure/monitor/autoscale-settings#az_monitor_autoscale_settings_create) da seguinte maneira. O JSON completo abaixo usa o perfil e as regras observados nas seções anteriores:

```azurecli-interactive
az monitor autoscale-settings create \
    --resource-group $resourcegroup_name \
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
                "timeWindow": "PT5M",
                "timeAggregation": "Average",
                "operator": "GreaterThan",
                "threshold": 70
              },
              "scaleAction": {
                "direction": "Increase",
                "type": "ChangeCount",
                "value": "3",
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
                "timeWindow": "PT5M",
                "timeAggregation": "Average",
                "operator": "LessThan",
                "threshold": 30
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
      ],
      "tags": {},
      "target_resource_uri": "/subscriptions/'$sub'/resourceGroups/'$resourcegroup_name'/providers/Microsoft.Compute/virtualMachineScaleSets/'$scaleset_name'"
    }'
```


## <a name="generate-cpu-load-on-scale-set"></a>Gerar carga de CPU no conjunto de dimensionamento
Para testar as regras de dimensionamento automático, gere carga da CPU nas instâncias de VM no conjunto de dimensionamento. Essa carga de CPU simulada faz com que o dimensionamento automático escale horizontalmente e aumente o número de instâncias de VM. Conforme a carga simulada de CPU vai sendo reduzida, as regras de dimensionamento automático para redução diminuem o número de instâncias de VM.

Primeiro, liste os endereços e as portas para se conectar a instâncias de VM em um conjunto de dimensionamento, use [az vmss list-instance-connection-info](/cli/azure/vmss#az_vmss_list_instance_connection_info):

```azurecli-interactive
az vmss list-instance-connection-info \
  --resource-group $resourcegroup_name \
  --name $scaleset_name
```

A saída de exemplo a seguir mostra o nome da instância, o endereço IP público do balanceador de carga e o número da porta para onde as regras NAT (Conversão de Endereços de Rede) encaminham o tráfego:

```json
{
  "instance 1": "13.92.224.66:50001",
  "instance 3": "13.92.224.66:50003"
}
```

SSH para a primeira instância de VM. Especifique seu próprio endereço IP público e o número da porta com o parâmetro `-p`, conforme mostrado no comando anterior:

```azurecli-interactive
ssh azureuser@13.92.224.66 -p 50001
```

Depois de conectado, instale o utilitário **stress**. Inicie *10* trabalhos de **stress** que geram carga de CPU. Esses trabalhos são executados por *420* segundos, o que é suficiente para fazer com que as regras de dimensionamento automático implementem a ação desejada.

```azurecli-interactive
sudo apt-get -y install stress
sudo stress --cpu 10 --timeout 420 &
```

Quando o **stress** mostrar a saída semelhante a *stress: info: [2688] dispatching hogs: 10 cpu, 0 io, 0 vm, 0 hdd*, pressione a tecla *Enter* para retornar ao prompt.

Para confirmar que o **stress** gera carga de CPU, examine a carga do sistema ativo com o utilitário **top**:

```azuecli-interactive
top
```

Saia do **top** e feche a conexão com a instância de VM. **stress** continuará a ser executado na instância de VM.

```azurecli-interactive
Ctrl-c
exit
```

Conecte-se à segunda instância de VM com o número da porta listado no [az vmss list-instance-connection-info](/cli/azure/vmss#az_vmss_list_instance_connection_info) anterior:

```azurecli-interactive
ssh azureuser@13.92.224.66 -p 50003
```

Instale e execute **stress** e inicie os dez trabalhos nessa segunda instância de VM.

```azurecli-interactive
sudo apt-get -y install stress
sudo stress --cpu 10 --timeout 420 &
```

Novamente, quando o **stress** mostrar a saída semelhante a *stress: info: [2713] dispatching hogs: 10 cpu, 0 io, 0 vm, 0 hdd*, pressione a tecla *Enter* para retornar ao prompt.

Feche a conexão com a segunda instância de VM. **stress** continuará a ser executado na instância de VM.

```azurecli-interactive
exit
```

## <a name="monitor-the-active-autoscale-rules"></a>Monitorar as regras de dimensionamento automático ativas
Para monitorar o número de instâncias de VM em seu conjunto de dimensionamento, use **watch**. Demoram 5 minutos para as regras de dimensionamento automático começarem o processo de escala horizontal em resposta à carga da CPU gerada pelo **stress** em cada uma das instâncias de VM:

```azurecli-interactive
watch az vmss list-instances \
  --resource-group $resourcegroup_name \
  --name $scaleset_name \
  --output table
```

Depois que o limite de CPU foi atingido, as regras de dimensionamento automático aumentam o número de instâncias de VM no conjunto de dimensionamento. A saída abaixo mostra três VMs criadas com o aumento de dimensionamento do conjunto de dimensionamento:

```bash
Every 2.0s: az vmss list-instances --resource-group myResourceGroup --name myScaleSet --output table

  InstanceId  LatestModelApplied    Location    Name          ProvisioningState    ResourceGroup    VmId
------------  --------------------  ----------  ------------  -------------------  ---------------  ------------------------------------
           1  True                  eastus      myScaleSet_1  Succeeded            MYRESOURCEGROUP  4f92f350-2b68-464f-8a01-e5e590557955
           2  True                  eastus      myScaleSet_2  Succeeded            MYRESOURCEGROUP  d734cd3d-fb38-4302-817c-cfe35655d48e
           4  True                  eastus      myScaleSet_4  Creating             MYRESOURCEGROUP  061b4c90-0d73-49fc-a066-19eab0b3d95c
           5  True                  eastus      myScaleSet_5  Creating             MYRESOURCEGROUP  4beff8b9-4e65-40cb-9652-43899309da27
           6  True                  eastus      myScaleSet_6  Creating             MYRESOURCEGROUP  9e4133dd-2c57-490e-ae45-90513ce3b336
```

Assim que o **stress** é interrompido nas instâncias de VM inicias, a carga média da CPU volta ao normal. Após mais cinco minutos, as regras de dimensionamento automático reduzem o número de instâncias de VM. As ações de redução removem instâncias de VM começando pelas IDs mais altas. A saída de exemplo abaixo mostra uma instância de VM excluída pela redução do conjunto de dimensionamento:

```bash
           6  True                  eastus      myScaleSet_6  Deleting             MYRESOURCEGROUP  9e4133dd-2c57-490e-ae45-90513ce3b336
```

Saia do *watch* usando `Ctrl-c`. O conjunto de dimensionamento continua a reduzir a cada cinco minutos e a remover uma instância de VM até que a contagem mínima de instâncias, duas, seja alcançada.


## <a name="clean-up-resources"></a>Limpar recursos
Para remover o conjunto de dimensionamento e outros recursos, exclua o grupo de recursos e todos os seus recursos com [az group delete](/cli/azure/group#az_group_delete). O parâmetro `--no-wait` retorna o controle ao prompt sem aguardar a conclusão da operação. O parâmetro `--yes` confirma que você deseja excluir os recursos sem um prompt adicional para fazer isso.

```azurecli-interactive
az group delete --name $resourcegroup_name --yes --no-wait
```


## <a name="next-steps"></a>Próximas etapas
Neste tutorial você aprendeu a reduzir ou escalar aplicativos horizontal e automaticamente um conjunto de dimensionamento com a CLI do Azure 2.0:

> [!div class="checklist"]
> * Usar o dimensionamento automático com um conjunto de dimensionamento
> * Criar e usar regras de dimensionamento automático
> * Testar instâncias de VM sob estresse e disparar regras de dimensionamento automático
> * Redimensionamento automático com a redução da demanda

Para obter mais exemplos de conjuntos de dimensionamento de máquinas virtuais em ação, consulte os seguintes scripts de exemplo do exemplo da CLI do Azure 2.0:

> [!div class="nextstepaction"]
> [Exemplos de script de conjunto de dimensionamento para a CLI do Azure 2.0](cli-samples.md)