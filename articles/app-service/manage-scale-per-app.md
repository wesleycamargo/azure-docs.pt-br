---
title: Hospedagem de alta densidade usando dimensionamento por aplicativo – Serviço de Aplicativo do Azure| Microsoft Docs
description: Hospedagem de alta densidade no Serviço de Aplicativo do Azure
author: btardif
manager: erikre
editor: ''
services: app-service\web
documentationcenter: ''
ms.assetid: a903cb78-4927-47b0-8427-56412c4e3e64
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 01/22/2018
ms.author: byvinyal
ms.custom: seodec18
ms.openlocfilehash: 08d6d0c31e1cff799e952c50bae3446e41477aba
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2019
ms.locfileid: "56104562"
---
# <a name="high-density-hosting-on-azure-app-service-using-per-app-scaling"></a>Hospedagem de alta densidade no Serviço de Aplicativo do Azure usando escalonamento por aplicativo

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Por padrão, você escalona aplicativos do Serviço de Aplicativo escalando [Plano de Serviço do Aplicativo](overview-hosting-plans.md) que executam. Quando vários aplicativos são executados no mesmo plano de serviço de aplicativo, cada instância dimensionada executa todos os aplicativos no plano.

Você pode habilitar *escalonamento por aplicativo* no nível de plano do Serviço do Aplicativo. Escalona um aplicativo independentemente do plano do Serviço de Aplicativo que o hospeda. Dessa forma, um Plano do Serviço de Aplicativo pode ser dimensionado para 10 instâncias, mas um aplicativo pode ser configurado para usar apenas cinco.

> [!NOTE]
> O dimensionamento por aplicativo está disponível somente para camadas de preço **Standard**, **Premium**, **Premium V2** e **Isolado** 
>

## <a name="per-app-scaling-using-powershell"></a>Dimensionamento por aplicativo usando PowerShell

Crie um plano com dimensionamento por aplicativo passando o parâmetro ```-PerSiteScaling $true``` para o ```New-AzAppServicePlan``` cmdlet.

```powershell
New-AzAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan `
                            -Location $Location `
                            -Tier Premium -WorkerSize Small `
                            -NumberofWorkers 5 -PerSiteScaling $true
```

Habilite o escalonamento por aplicativo com um Plano do Serviço de Aplicativo existente passando o parâmetro `-PerSiteScaling $true` para o ```Set-AzAppServicePlan``` cmdlet.

```powershell
# Enable per-app scaling for the App Service Plan using the "PerSiteScaling" parameter.
Set-AzAppServicePlan -ResourceGroupName $ResourceGroup `
   -Name $AppServicePlan -PerSiteScaling $true
```

No nível do aplicativo, configure o número de instâncias que o aplicativo pode usar no plano do serviço de aplicativo.

No exemplo abaixo, o aplicativo está limitado a duas instâncias, independentemente de para quantas instâncias o plano do serviço de aplicativo subjacente pode ser dimensionado.

```powershell
# Get the app we want to configure to use "PerSiteScaling"
$newapp = Get-AzWebApp -ResourceGroupName $ResourceGroup -Name $webapp
    
# Modify the NumberOfWorkers setting to the desired value.
$newapp.SiteConfig.NumberOfWorkers = 2
    
# Post updated app back to azure
Set-AzWebApp $newapp
```

> [!IMPORTANT]
> `$newapp.SiteConfig.NumberOfWorkers` é diferente de `$newapp.MaxNumberOfWorkers`. O dimensionamento por aplicativo usa `$newapp.SiteConfig.NumberOfWorkers` para determinar as características de dimensionamento do aplicativo.

## <a name="per-app-scaling-using-azure-resource-manager"></a>Dimensionamento por aplicativo usando o Azure Resource Manager

O seguinte modelo do Azure Resource Manager cria:

- Um Plano de Serviço de Aplicativo que é dimensionado para 10 instâncias
- Um aplicativo configurado para dimensionar para um máximo de cinco instâncias.

O Plano do Serviço de Aplicativo está definindo a propriedade **PerSiteCalling** como verdadeira (`"perSiteScaling": true`). O aplicativo está definindo o **número de trabalhadores** a usar como 5 (`"properties": { "numberOfWorkers": "5" }`).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
            "name": "P1",
            "tier": "Premium",
            "size": "P1",
            "family": "P",
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
                "properties": { "numberOfWorkers": "5" }
            } ]
        }]
}
```

## <a name="recommended-configuration-for-high-density-hosting"></a>Configuração recomendada para hospedagem de alta densidade
O dimensionamento por aplicativo é um recurso que está habilitado em regiões globais do Azure e Ambientes do [Serviço de Aplicativo](environment/app-service-app-service-environment-intro.md). No entanto, a estratégia recomendada é usar Ambientes de Serviço de Aplicativo para aproveitar os recursos avançados e os pools de maior capacidade.  

Siga estas etapas para configurar a hospedagem de alta densidade para seus aplicativos:

1. Configure o Ambiente de Serviço de Aplicativo e escolha um pool de trabalho dedicado ao cenário de hospedagem de alta densidade.
2. Crie um único plano do Serviço de Aplicativo e o dimensione para usar toda a capacidade disponível no pool de trabalho.
3. Defina o sinalizador `PerSiteScaling` como verdadeiro no Plano do Serviço de Aplicativo.
4. Novos aplicativos são criados e atribuídos a esse Plano do Serviço de Aplicativo com a propriedade **numberOfWorkers** definida como **1**. O uso dessa configuração produz a densidade mais alta possível neste pool de trabalho.
5. O número de trabalhadores pode ser configurado independentemente por aplicativo, a fim de conceder recursos adicionais conforme necessário. Por exemplo: 
    - Um aplicativo de alto consumo pode definir o **numberOfWorkers** como **3** para ter mais capacidade de processamento para esse aplicativo. 
    - Os aplicativos de baixo consumo definiriam o **numberOfWorkers** como **1**.

## <a name="next-steps"></a>Próximas etapas

- [Visão geral detalhada de planos de Serviço de aplicativo do Azure](overview-hosting-plans.md)
- [Introdução ao ambiente de Serviço de Aplicativo](environment/app-service-app-service-environment-intro.md)
