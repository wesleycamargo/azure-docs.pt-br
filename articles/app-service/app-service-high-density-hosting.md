<properties 
	pageTitle="Hospedagem de alta densidade no Serviço de Aplicativo do Azure" 
	description="Hospedagem de alta densidade no Serviço de Aplicativo do Azure" 
	authors="btardif" 
	manager="wpickett" 
	editor="" 
	services="app-service\web" 
	documentationCenter=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="05/17/2016" 
	ms.author="byvinyal"/>

#Hospedagem de alta densidade no Serviço de Aplicativo do Azure#

##Entendendo o dimensionamento de aplicativos##

Ao usar o Serviço de Aplicativo, o aplicativo será desassociado da capacidade alocada por 2 conceitos:
 
>**O aplicativo:** representa o Aplicativo e sua configuração de tempo de execução. Por exemplo: o tempo de execução deve carregar qual versão do .NET? Quais são as configurações do aplicativo?

>**O Plano do Serviço de Aplicativo:** define as características de capacidade, o conjunto de recursos disponível e a localidade do aplicativo. Por exemplo: computador grande (4 núcleos), 4 instâncias, recursos premium no Leste dos EUA

Um aplicativo sempre está vinculado a um **Plano do Serviço de Aplicativo**, mas um **Plano do Serviço de Aplicativo** pode fornecer capacidade a um ou mais aplicativos.

Isso significa que a plataforma fornece flexibilidade para isolar um único aplicativo ou permitir que vários aplicativos compartilhem recursos com um mesmo **Plano do Serviço de Aplicativo**.

No entanto, quando vários aplicativos compartilham um **Plano do Serviço de Aplicativo**, haverá uma instância do aplicativo em execução em cada instância desse **Plano do Serviço de Aplicativo**.

##Dimensionamento por aplicativo##
**Escala por Aplicativo** é um recurso que pode ser habilitado no nível do **Plano do Serviço de Aplicativo**, sendo posteriormente aproveitado por aplicativo.

A **Escala por Aplicativo** permite escalar um aplicativo independentemente do **Plano do Serviço de Aplicativo** usado para hospedá-lo. Dessa forma, um **Plano do Serviço de Aplicativo** podem ser configurado para fornecer 10 instâncias, mas um aplicativo pode ser definido para escalar para apenas 5 delas.

O modelo ARM abaixo criará uma **Plano do Serviço de Aplicativo** escalado verticalmente para 10 instâncias e um aplicativo configurado para usar **Escala por Aplicativo** para escalar para apenas 5 instâncias.

Para fazer isso, o Plano do Serviço de Aplicativo define a propriedade de escala por site verdadeira ( `"perSiteScaling": true`) e o Aplicativo define o número de trabalhadores a serem usado como 1 `"properties": { "numberOfWorkers": "1" }`

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters":{
            "appServicePlanName": { "type": "string" },
            "appName": { "type": "string" }
         },
        "resources": [
        {
            "comments": "App Service Plan with per site perSiteScaling = true",
            "type": "Microsoft.Web/serverFarms",
            "sku": {
                "name": "S1",
                "tier": "Standard",
                "size": "S1",
                "family": "S",
                "capacity": 10
                },
            "name": "[parameters('appServicePlanName')]",
            "apiVersion": "2015-08-01",
            "location": "West US",
            "properties": {
                "name": "[parameters('appServicePlanName')]",
                "perSiteScaling": true 
            }
        },
        {
            "type": "Microsoft.Web/sites",
            "name": "[parameters('appName')]",
            "apiVersion": "2015-08-01-preview",
            "location": "West US",
            "dependsOn": [ "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" ],
            "properties": { "serverFarmId": "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" },
            "resources": [ {
                    "comments": "",
                    "type": "config",
                    "name": "web",
                    "apiVersion": "2015-08-01",
                    "location": "West US",
                    "dependsOn": [ "[resourceId('Microsoft.Web/Sites', parameters('appName'))]" ],
                    "properties": { "numberOfWorkers": "1" }
             } ]
         }]
    }


##Configuração recomendada para Hospedagem de Alta Densidade##

A **Escala por Aplicativo** é um recurso que está habilitado tanto em regiões públicas do Azure quanto em Ambientes do Serviço de Aplicativo, no entanto, a estratégia recomendada é usar Ambientes de Serviço de Aplicativo para aproveitar seus recursos avançados e pools de maior capacidade.

Siga as etapas listadas abaixo como orientação sobre como configurar **Hospedagem de Alta Densidade** para seus aplicativos.

1. Configure o **Ambiente de Serviço de Aplicativo** e escolha um **pool de trabalho** que será dedicado ao cenário de *Hospedagem de Alta Densidade*.

1. Crie um único **Plano do Serviço de Aplicativo** e escale-o para usar toda a capacidade disponível no **pool de trabalho**.

1. Defina a marca de Escala por site como verdadeira no **Plano do Serviço de Aplicativo**.

1. Novos sites são criados e atribuídos ao **Plano do Serviço de Aplicativo** com a propriedade *numberOfWorkers* definida como *1*. Isso produzirá a mair densidade possível neste **pool de trabalho**

1. O número de trabalhadores pode ser configurado independentemente por site, a fim de conceder recursos adicionais conforme necessário. Por exemplo, um site de alto uso poderia definir *numberOfWorkers* para *3* para ter mais capacidade de processamento para este aplicativo, enquanto sites de baixo uso poderiam definir *numberOfWorkers* para *1*.

<!---HONumber=AcomDC_0518_2016-->