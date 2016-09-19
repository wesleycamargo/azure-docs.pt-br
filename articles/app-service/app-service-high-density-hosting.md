<properties
	pageTitle="Hospedagem de alta densidade no Serviço de Aplicativo do Azure | Microsoft Azure"
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
	ms.date="08/07/2016"
	ms.author="byvinyal"/>

# Hospedagem de alta densidade no Serviço de Aplicativo do Azure#

Ao usar o Serviço de Aplicativo, o aplicativo será desassociado da capacidade alocada por 2 conceitos:

- **O Aplicativo:** representa o Aplicativo e sua configuração de tempo de execução. Por exemplo, ele inclui a versão do .NET que o tempo de execução deve carregar, as configurações de aplicativo etc.

- **O Plano do Serviço de Aplicativo:** define as características de capacidade, o conjunto de recursos disponível e a localidade do aplicativo. Por exemplo, as características podem ser computador grande (quatro núcleos), quatro instâncias, recursos Premium no Leste dos EUA.

Um aplicativo sempre está vinculado a um Plano do Serviço de Aplicativo, mas um Plano do Serviço de Aplicativo pode fornecer capacidade a um ou mais aplicativos.

Isso significa que a plataforma fornece flexibilidade para isolar um único aplicativo ou permitir que vários aplicativos compartilhem recursos com um mesmo plano do Serviço de Aplicativo.

No entanto, quando vários aplicativos compartilharem um plano do Serviço de Aplicativo, uma instância desse aplicativo será executada em todas as instâncias do plano do Serviço de Aplicativo.

## Dimensionamento por aplicativo##
*Escala por Aplicativo* é um recurso que pode ser habilitado no nível do plano do Serviço de Aplicativo, sendo posteriormente usado por aplicativo.

O dimensionamento por aplicativo escalona um aplicativo independentemente do plano do Serviço de Aplicativo que o hospeda. Dessa forma, um plano do Serviço de Aplicativo podem ser configurado para fornecer 10 instâncias, mas um aplicativo pode ser definido para escalar para apenas cinco delas.

O modelo do Azure Resource Manager a seguir criará um plano do Serviço de Aplicativo dimensionado para 10 instâncias e um aplicativo que está configurado para usar o dimensionamento por dimensionamento de aplicativo e dimensionar para apenas cinco instâncias.

Para fazer isso, o plano do Serviço de Aplicativo define a propriedade **escala por site** como verdadeira (`"perSiteScaling": true`) e o aplicativo define o **número de trabalhadores** a ser usado como 1 (`"properties": { "numberOfWorkers": "1" }`).

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


## Configuração recomendada para Hospedagem de Alta Densidade

O dimensionamento por aplicativo é um recurso que está habilitado em regiões públicas do Azure e Ambientes de Serviço de Aplicativo. No entanto, a estratégia recomendada é usar Ambientes de Serviço de Aplicativo para aproveitar os recursos avançados e os pools de maior capacidade.

Siga estas etapas para configurar a hospedagem de alta densidade para seus aplicativos:

1. Configure o Ambiente de Serviço de Aplicativo e escolha um pool de trabalho que será dedicado ao cenário de hospedagem de alta densidade.

1. Crie um único plano do Serviço de Aplicativo e o dimensione para usar toda a capacidade disponível no pool de trabalho.

1. Defina o sinalizador de dimensionamento por site como verdadeira no plano do Serviço de Aplicativo.

1. Novos sites são criados e atribuídos ao plano do Serviço de Aplicativo com a propriedade **numberOfWorkers** definida como **1**. Isso produzirá a maior densidade possível neste pool de trabalho.

1. O número de trabalhadores pode ser configurado independentemente por site, a fim de conceder recursos adicionais conforme necessário. Por exemplo, um site de alto uso poderia definir **numberOfWorkers** como **3** para ter mais capacidade de processamento para este aplicativo, enquanto sites de baixo uso poderiam definir **numberOfWorkers** como **1**.

<!---HONumber=AcomDC_0907_2016-->