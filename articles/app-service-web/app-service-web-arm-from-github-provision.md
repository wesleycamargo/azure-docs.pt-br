<properties 
	pageTitle="Implantar um aplicativo Web vinculado a um repositório GitHub" 
	description="Use um modelo do Gerenciador de Recursos do Azure para implantar um aplicativo Web que contém um projeto de um repositório GitHub." 
	services="app-service\web" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/02/2015" 
	ms.author="tomfitz"/>

# Implantar um aplicativo Web vinculado a um repositório GitHub

Neste tópico, você aprenderá a criar um modelo do Gerenciador de Recursos do Azure que implanta um aplicativo Web vinculado a um projeto em um repositório GitHub. Você aprenderá a definir quais recursos são implantados e a definir os parâmetros especificados quando a implantação é executada. Você pode usar esse modelo para suas próprias implantações ou personalizá-lo para atender às suas necessidades.

Para obter mais informações sobre a criação de modelos, consulte [Criação de modelos do Gerenciador de Recursos do Azure](../resource-group-authoring-templates.md).

Para obter o modelo completo, consulte [Modelo de aplicativo Web vinculado ao GitHub](https://github.com/tfitzmac/AppServiceTemplates/blob/master/WebAppLinkedToGithub.json).

## O que você implantará

Com esse modelo, você implantará um aplicativo Web que contém o código de um projeto no GitHub.

## Parâmetros

[AZURE.INCLUDE [app-service-web-deploy-web-parameters](../../includes/app-service-web-deploy-web-parameters.md)]
    
## Recursos a implantar

[AZURE.INCLUDE [app-service-web-deploy-web-host](../../includes/app-service-web-deploy-web-host.md)]

### Aplicativo Web

Cria o aplicativo Web vinculado ao projeto no GitHub.

Especifique o nome do aplicativo Web por meio do parâmetro **siteName** e o local do aplicativo Web por meio do parâmetro **siteLocation**. No elemento **dependsOn**, o modelo define o aplicativo Web como dependente do plano de hospedagem do serviço. Como depende do plano de hospedagem, o aplicativo Web não é criado até a conclusão da criação do plano de hospedagem. O elemento **dependsOn** só é usado para especificar a ordem de implantação. Se você não marcar o aplicativo Web como dependente do plano de hospedagem, o Gerenciador de Recursos do Azure tentará criar ambos os recursos ao mesmo tempo e você receberá um erro se o aplicativo Web for criado antes do plano de hospedagem.

O aplicativo Web também tem um recurso filho que é definido na seção de **recursos** a seguir. Esse recurso filho define o controle de código-fonte para o projeto implantado com o aplicativo Web. Nesse modelo, o controle de código-fonte está vinculado a um repositório GitHub específico. O repositório GitHub é definido com o código **"RepoUrl":"https://github.com/davidebbo-test/Mvc52Application.git"** Você poderá embutir a URL do repositório quando quiser criar um modelo que implanta repetidamente um único projeto, exigindo o número mínimo de parâmetros. Em vez de embutir a URL do repositório, você pode adicionar um parâmetro para a URL do repositório e usar esse valor para a propriedade **RepoUrl**. Você pode ver um exemplo de parâmetro de URL do repositório no [modelo de aplicativo Web com trabalhos da Web](../app-service-web-deploy-web-app-with-webjobs.md).

    {
      "apiVersion":"2015-04-01",
      "name":"[parameters('siteName')]",
      "type":"Microsoft.Web/sites",
      "location":"[parameters('siteLocation')]",
      "dependsOn":[
         "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
      ],
      "properties":{
        "serverFarmId":"[parameters('hostingPlanName')]"
      },
       "resources":[
         {
           "apiVersion":"2015-04-01",
           "name":"web",
           "type":"sourcecontrols",
           "dependsOn":[
             "[resourceId('Microsoft.Web/Sites', parameters('siteName'))]"
           ],
           "properties":{
             "RepoUrl":"https://github.com/davidebbo-test/Mvc52Application.git",
             "branch":"master"
           }
         }
       ]
     }

## Comandos para executar a implantação

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### PowerShell

    New-AzureResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/tfitzmac/AppServiceTemplates/master/WebAppLinkedToGithub.json -siteName ExampleSite -hostingPlanName ExamplePlan -siteLocation "West US" -ResourceGroupName ExampleDeployGroup

### CLI do Azure

    azure group deployment create --template-uri https://raw.githubusercontent.com/tfitzmac/AppServiceTemplates/master/WebAppLinkedToGithub.json


 

<!---HONumber=62-->