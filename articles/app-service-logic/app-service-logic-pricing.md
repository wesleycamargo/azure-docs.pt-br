---
title: Modelo de preços dos Aplicativos Lógicos | Microsoft Docs
description: Detalhes sobre como funciona o preço em Aplicativos Lógicos
author: kevinlam1
manager: dwrede
editor: ''
services: logic-apps
documentationcenter: ''

ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2016
ms.author: klam

---
# Modelo de preços de Aplicativos Lógicos
Os Aplicativos Lógicos do Azure permitem que você dimensione e execute fluxos de trabalho de integração na nuvem. A seguir, detalhes sobre a cobrança e os planos de preços para os Aplicativos Lógicos.

## Preço por consumo
Aplicativos Lógicos recém-criado usam um plano de consumo. Com o modelo de preços de consumo dos Aplicativos Lógicos, você só paga pelo que usa. Os Aplicativos Lógicos não sofrem limitações ao usar um plano de consumo. Todas as ações executadas em uma execução de uma instância de aplicativo lógico são limitadas.

### O que são execuções de ação?
Cada etapa em uma definição de aplicativo lógico é uma ação. Isso inclui gatilhos, etapas de fluxo de controle, como condições, escopos, loops for each e loops do until, chamadas a conectores e chamadas para ações nativas. Os gatilhos são apenas ações especiais projetadas para criar uma nova instância do aplicativo lógico quando um evento específico ocorrer. Há diversos comportamentos diferentes para gatilhos que podem afetar o modo como o aplicativo lógico é monitorado.

* **Gatilho de sondagem** – esse gatilho sonda continuamente um ponto de extremidade até receber uma mensagem que satisfaça os critérios para a criação de uma nova instância de um aplicativo lógico. O intervalo de sondagem pode ser configurado no gatilho no designer dos Aplicativos Lógicos. Cada solicitação de sondagem, mesmo se não criar uma nova instância de um aplicativo lógico, contará como uma execução da ação.
* **Gatilho de webhook** – esse disparador espera o envio de uma solicitação por parte do cliente em um ponto de extremidade específico. Cada solicitação enviada ao ponto de extremidade do webhook conta como uma execução da ação. A Solicitação e o gatilho Webhook HTTP são gatilhos de webhook.
* **Gatilho de recorrência** – esse gatilho criará uma nova instância do aplicativo lógico com base no intervalo de recorrência configurado gatilho. Por exemplo, um gatilho de recorrência pode ser configurado para executar a cada três dias ou até mesmo a cada minuto.

As execuções do gatilho podem ser vistas na folha de recursos dos Aplicativos Lógicos na parte do Histórico de Gatilhos.

Todas as ações executadas, bem-sucedidas ou não, são limitadas como uma execução de ação. As ações que foram ignoradas devido a uma condição não atendida, ou ações que não foram executadas porque o aplicativo lógico foi encerrado antes da conclusão, não são contadas como execuções de ação.

Ações executadas em loops são contadas por iteração do loop. Por exemplo, uma única ação em um loop for each iterando por uma lista de 10 itens será contada como a contagem de itens na lista (10) multiplicada pelo número de ações no loop (1), mais um para o início do loop que, neste exemplo, seria (10 * 1) + 1 = 11 execuções de ação.

Os Aplicativos Lógicos desabilitados não podem ter novas instâncias criadas e, portanto, durante o tempo em que estiverem desabilitados não serão cobrados. Lembre-se de que depois de desabilitar um aplicativo lógico pode demorar algum tempo para as instâncias fecharem para novas sessões antes de serem completamente desabilitadas.

## Planos do Serviço de Aplicativo
Os Planos do Serviço de Aplicativo não são mais necessários para criar um Aplicativo Lógico. Você também pode fazer referência um Plano do Serviço de Aplicativo com um aplicativo lógico existente. Os Aplicativos Lógicos criados anteriormente com um Plano do Serviço de Aplicativo continuarão se comportando como antes e, dependendo do plano escolhido, sofrerão uma limitação após um número de execuções diárias e não serão cobrados usando o medidor de execução da ação.

Planos do Serviço de Aplicativo e suas execuções diária de ação permitida:

|  | Gratuito/Compartilhado/Básico | Standard | Premium |
| --- | --- | --- | --- |
| Execuções de ação por dia |200 |10\.000 |50\.000 |

### Converter de consumo em preços do Plano do Serviço de Aplicativo
Para fazer referência a um Plano do Serviço de Aplicativo para um Aplicativo Lógico de consumo, você pode simplesmente [executar o script do PowerShell abaixo](https://github.com/logicappsio/ConsumptionToAppServicePlan). Primeiro, verifique se você tem as [ferramentas do Azure PowerShell](https://github.com/Azure/azure-powershell) instaladas.

``` powershell
Param(
    [string] $AppService_RG = '<app-service-resource-group>',
    [string] $AppService_Name = '<app-service-name>',
    [string] $LogicApp_RG = '<logic-app-resource-group>',
    [string] $LogicApp_Name = '<logic-app-name>',
    [string] $subscriptionId = '<azure-subscription-id>'
)

Login-AzureRmAccount 
$subscription = Get-AzureRmSubscription -SubscriptionId $subscriptionId
$appserviceplan = Get-AzureRmResource -ResourceType "Microsoft.Web/serverFarms" -ResourceGroupName $AppService_RG -ResourceName $AppService_Name
$logicapp = Get-AzureRmResource -ResourceType "Microsoft.Logic/workflows" -ResourceGroupName $LogicApp_RG -ResourceName $LogicApp_Name

$sku = @{
    "name" = $appservicePlan.Sku.tier;
    "plan" = @{
      "id" = $appserviceplan.ResourceId;
      "type" = "Microsoft.Web/ServerFarms";
      "name" = $appserviceplan.Name  
    }
}

$updatedProperties = $logicapp.Properties | Add-Member @{sku = $sku;} -PassThru

$updatedLA = Set-AzureRmResource -ResourceId $logicapp.ResourceId -Properties $updatedProperties -ApiVersion 2015-08-01-preview
```

### Converter de preços do Plano do Serviço de Aplicativo para consumo
Para alterar um Aplicativo Lógico que tem um Plano do Serviço de Aplicativo associado para um modelo de consumo, remova a referência ao Plano do Serviço de Aplicativo na definição do Aplicativo Lógico. Isso pode ser feito com uma chamada a um cmdlet do PowerShell:

`Set-AzureRmLogicApp -ResourceGroupName ‘rgname’ -Name ‘wfname’ –UseConsumptionModel -Force`

## Preços
Para obter detalhes sobre preços, confira [Preços de Aplicativos Lógicos](https://azure.microsoft.com/pricing/details/logic-apps/).

## Próximas etapas
* [Uma visão geral dos Aplicativos Lógicos][whatis]
* [Criar seu primeiro aplicativo lógico][create]

[pricing]: https://azure.microsoft.com/pricing/details/logic-apps/
[whatis]: app-service-logic-what-are-logic-apps.md
[create]: app-service-logic-create-a-logic-app.md

<!---HONumber=AcomDC_0810_2016-->