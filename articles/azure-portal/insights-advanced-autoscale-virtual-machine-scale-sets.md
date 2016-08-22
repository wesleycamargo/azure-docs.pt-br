<properties
	pageTitle="Azure Insights: configuração de dimensionamento automático avançada usando modelos do Resource Manager para conjuntos de escala de VM | Microsoft Azure"
	description="Configure o dimensionamento automático para conjuntos de escala de VM com base em várias regras e perfis com notificações por email e webhooks para ações de dimensionamento."
	authors="kamathashwin"
	manager=""
	editor=""
	services="monitoring-and-diagnostics"
	documentationCenter="monitoring-and-diagnostics"/>

<tags
	ms.service="monitoring-and-diagnostics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/04/2016"
	ms.author="ashwink"/>

# Configuração avançada de Dimensionamento Automático usando modelos do Resource Manager para conjuntos de escala de VM

Você pode escalar e reduzir horizontalmente VMSS (conjuntos de dimensionamento de máquina virtual) baseado nos limites de métrica de desempenho, em uma agenda recorrente ou em determinada data. Você também pode configurar notificações por email e webhook para ações de escala. Este passo a passo mostra um exemplo de configuração de todos os itens acima usando um modelo do Resource Manager em um conjunto de dimensionamento de VM.

>[AZURE.NOTE] Este passo a passo explica as etapas para conjuntos de dimensionamento de VM. Você pode aplicá-las para dimensionar automaticamente aplicativos Web e Serviços de Nuvem. Se você quiser ter uma configuração simples de redução/escala horizontal em um dimensionamento de VM com base em um métrica de desempenho simples, como CPU, consulte os documentos [Linux](../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md) e [Windows](../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md)



## Passo a passo
Neste passo a passo, usamos [Gerenciador de Recursos do Azure](https://resources.azure.com/) para configurar e atualizar a configuração de dimensionamento automático para um VMSS. O Gerenciador de Recursos do Azure é uma maneira fácil de gerenciar recursos do Azure por meio de modelos do Resource Manager. Se você não conhece bem o Gerenciador de Recursos do Azure, a ferramenta, leia [esta introdução](https://azure.microsoft.com/blog/azure-resource-explorer-a-new-tool-to-discover-the-azure-api/).

1. Implante um novo VMSS com configuração básica de dimensionamento automático. Este artigo usa um da Galeria de Início Rápido do Azure que tem um VMSS do Windows com o modelo básico de dimensionamento automático. Isso funciona da mesma maneira para instâncias VMSS de Linux.

2. Depois que o VMSS é criado, navegue até o recurso VMSS do Gerenciador de Recursos do Azure. Você verá o que segue no nó Microsoft.Insights.

	![Azure Explorer](./media/insights-advanced-autoscale-vmss/azure_explorer_navigate.png)

	A execução do modelo criou uma configuração padrão de dimensionamento automático com o nome **'autoscalewad'**. No lado direito, você pode exibir a definição completa dessa configuração de dimensionamento automático. Nesse caso, a configuração de dimensionamento automático padrão vem com uma regra de escala e redução baseada em % CPU.

3. Agora você pode adicionar mais perfis e regras com base no agendamento ou em requisitos específicos. Podemos criar uma configuração de dimensionamento automático com três perfis. Para entender as regras de dimensionamento automático e perfis, confira [Práticas recomendadas de dimensionamento automático](./insights-autoscale-best-practices.md).

    | Perfis e regras | Descrição |
	|---------|-------------------------------------|
	| **Perfil** | **Baseado em desempenho/métrica** |
	| Regra | Contagem de mensagens de fila de Barramento de Serviço > x |
	| Regra | Contagem de mensagens de fila de Barramento de Serviço < y |
	| Regra | % CPU, < n |
	| Regra | % CPU < p |
	| **Perfil** | **Horas da manhã de dia de semana,(sem regras)** |
	| **Perfil** | **Dia de lançamento do produto (sem regras)** |

4. Eis um cenário hipotético de dimensionamento que usaremos para este passo a passo.
	- _**Baseado em carga** -gostaria de escalar ou reduzir horizontalmente com base na carga do aplicativo hospedado no VMSS._
	- _**amanho da fila de mensagem** - usei uma fila do Barramento de Serviço para as mensagens recebidas pelo meu aplicativo. Uso contagem de mensagens da fila e o percentual de CPU e configuro um perfil padrão para disparar uma ação de escala se a contagem de mensagens ou CPU atingir o limite._
	- _**Dia e hora da semana** - desejo ter um perfil baseado na 'hora do dia' recorrente semanal chamado 'Horas da manhã de dias da semana'. Com base nos dados históricos, sei que é melhor ter determinado número de instâncias de VM para lidar com a carga do meu aplicativo durante esse período._
	- _**Datas especiais** - adicionei um perfil de 'Dia de lançamento de produto'. Planejo com antecedência em relação a datas específicas para que meu aplicativo esteja pronto para lidar com a carga devido a anúncios de marketing e quando colocamos um novo produto no aplicativo._
	- _Os últimos dois perfis também podem ter outras regras baseadas em métricas de desempenho neles, mas, no meu caso, decidi não ter um e confiar nas regras baseadas em métricas de desempenho padrão. As regras são opcionais para perfis baseados em recorrência e data._

	A priorização de perfis e regras do mecanismo de dimensionamento automático também é vista no artigo [Práticas recomendadas de dimensionamento automático](insights-autoscale-best-practices.md). Para obter uma lista de métricas comuns para dimensionamento automático, confira [Métricas comuns para o dimensionamento automático](insights-autoscale-common-metrics.md)

5. Verifique se você está usando o modo de **leitura/gravação** no Gerenciador de Recursos

	![Autoscalewad, configuração de dimensionamento automático padrão](./media/insights-advanced-autoscale-vmss/autoscalewad.png)

6. Clique em Editar. **Substitua** o elemento 'perfis' na configuração de dimensionamento automático pelo seguinte:

	![perfis](./media/insights-advanced-autoscale-vmss/profiles.png)

	```
	{
	        "name": "Perf_Based_Scale",
	        "capacity": {
	          "minimum": "2",
	          "maximum": "12",
	          "default": "2"
	        },
	        "rules": [
	          {
	            "metricTrigger": {
	              "metricName": "MessageCount",
	              "metricNamespace": "",
	              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue",
	              "timeGrain": "PT5M",
	              "statistic": "Average",
	              "timeWindow": "PT5M",
	              "timeAggregation": "Average",
	              "operator": "GreaterThan",
	              "threshold": 10
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
	              "metricName": "MessageCount",
	              "metricNamespace": "",
	              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue",
	              "timeGrain": "PT5M",
	              "statistic": "Average",
	              "timeWindow": "PT5M",
	              "timeAggregation": "Average",
	              "operator": "LessThan",
	              "threshold": 3
	            },
	            "scaleAction": {
	              "direction": "Decrease",
	              "type": "ChangeCount",
	              "value": "1",
	              "cooldown": "PT5M"
	            }
	          },
	          {
	            "metricTrigger": {
	              "metricName": "\\Processor(_Total)\\% Processor Time",
	              "metricNamespace": "",
	              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/<this_vmss_name>",
	              "timeGrain": "PT5M",
	              "statistic": "Average",
	              "timeWindow": "PT30M",
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
	              "metricName": "\\Processor(_Total)\\% Processor Time",
	              "metricNamespace": "",
	              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/<this_vmss_name>",
	              "timeGrain": "PT5M",
	              "statistic": "Average",
	              "timeWindow": "PT30M",
	              "timeAggregation": "Average",
	              "operator": "LessThan",
	              "threshold": 60
	            },
	            "scaleAction": {
	              "direction": "Increase",
	              "type": "ChangeCount",
	              "value": "1",
	              "cooldown": "PT5M"
	            }
	          }
	        ]
	      },
	      {
	        "name": "Weekday_Morning_Hours_Scale",
	        "capacity": {
	          "minimum": "4",
	          "maximum": "12",
	          "default": "4"
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
	              6
	            ],
	            "minutes": [
	              0
	            ]
	          }
	        }
	      },
	      {
	        "name": "Product_Launch_Day",
	        "capacity": {
	          "minimum": "6",
	          "maximum": "20",
	          "default": "6"
	        },
	        "rules": [],
	        "fixedDate": {
	          "timeZone": "Pacific Standard Time",
	          "start": "2016-06-20T00:06:00Z",
	          "end": "2016-06-21T23:59:00Z"
	        }
	      }
	```
	Para campos com suporte e seus valores, confira [Documentação da API REST de Dimensionamento Automático](https://msdn.microsoft.com/pt-BR/library/azure/dn931928.aspx).

	Agora, sua configuração de dimensionamento automático contém os três perfis explicados acima.

7. 	Finalmente, vejamos a seção **Notificação** de Dimensionamento Automático. As notificações de dimensionamento automático permitem fazer três coisas quando uma ação de escala ou redução horizontal é disparada com êxito.

	1. Notificar o administrador e coadministradores de sua assinatura

	2. Enviar email a um conjunto de usuários

	3. Disparar uma chamada webhook. Quando disparado, esse webhook enviará metadados sobre a condição de dimensionamento automático e do recurso VMSS. Para saber mais sobre a carga de webhook de dimensionamento automático, confira [Configurar webhook e notificações por email para dimensionamento automático](./insights-autoscale-to-webhook-email.md).

	Adicionar o seguinte à configuração de dimensionamento automático substituindo o elemento **notificação** cujo valor é nulo

	```
	"notifications": [
	      {
	        "operation": "Scale",
	        "email": {
	          "sendToSubscriptionAdministrator": true,
	          "sendToSubscriptionCoAdministrators": false,
	          "customEmails": [
	              "user1@mycompany.com",
	              "user2@mycompany.com"
	              ]
	        },
	        "webhooks": [
	          {
	            "serviceUri": "https://foo.webhook.example.com?token=abcd1234",
	            "properties": {
	              "optional_key1": "optional_value1",
	              "optional_key2": "optional_value2"
	            }
	          }
	        ]
	      }
	    ]

	```

	Pressione o botão **Colocar** no Gerenciador de Recursos para atualizar a configuração de dimensionamento automático.

Você atualizou uma configuração de dimensionamento automático em um conjunto de escala de VM definido para incluir vários perfis e notificações de escala.

## Próximas etapas

Use estes links para saber mais sobre o dimensionamento automático.

[Métricas comuns para o dimensionamento automático](./insights-autoscale-common-metrics.md)

[Práticas Recomendadas para o Serviço de Aplicativo do Azure](./insights-autoscale-best-practices.md)

[Gerenciar o dimensionamento automático usando o PowerShell](./insights-powershell-samples.md#create-and-manage-autoscale-settings)

[Gerenciar o dimensionamento automático usando a CLI](./insights-cli-samples.md#autoscale)

[Configurar webhooks e notificações por email para dimensionamento automático](./insights-autoscale-to-webhook-email.md)

<!---HONumber=AcomDC_0810_2016-->