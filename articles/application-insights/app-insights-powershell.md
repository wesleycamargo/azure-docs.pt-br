<properties 
	pageTitle="Criar recursos do Application Insights usando o PowerShell" 
	description="Crie recursos do Application Insights programaticamente como parte da compilação." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/02/2016" 
	ms.author="awills"/>
 
# Criar recursos do Application Insights usando o PowerShell

Este artigo mostra como criar um recurso do [Application Insights](app-insights-overview.md) no Azure automaticamente. Por exemplo, você pode fazer isso como parte de um processo de compilação. Juntamente com o recurso Application Insights básico, você pode criar [testes da web de disponibilidade](app-insights-monitor-web-app-availability.md), [configurar alertas](app-insights-alerts.md) e criar outros recursos do Azure.

A chave para criar esses recursos são os modelos de JSON para o [Gerenciador de Recursos do Azure](../powershell-azure-resource-manager.md). Em resumo, o procedimento é: baixar as definições de JSON dos recursos existentes; parametrizar certos valores como nomes; e, em seguida, executar o modelo sempre que você deseja criar um novo recurso. Você pode empacotar vários recursos juntos, criá-los de uma só vez - por exemplo, um monitor de aplicativo com testes de disponibilidade, alertas e armazenamento para exportação contínua. Existem algumas sutilezas para algumas das parametrizações, que vamos explicar aqui.

## Configuração única

Se você nunca usou o PowerShell com sua assinatura do Azure:

Instale o módulo do Azure Powershell no computador em que você deseja executar os scripts.

1. Instale o [Microsoft Web Platform Installer (v5 ou superior)](http://www.microsoft.com/web/downloads/platform.aspx).
2. Use-o para instalar o Microsoft Azure PowerShell

## Copie o JSON para os recursos existentes

1. Configure [Application Insights](app-insights-overview.md) para um projeto semelhante àqueles que você deseja gerar automaticamente. Adicione alertas e testes da web se você desejar.
2. Criar um novo arquivo .json - vamos chamá-lo de `template1.json` neste exemplo. Copie este conteúdo nele:


    ```JSON

        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "appName": { "type": "string" },
            "webTestName": { "type": "string" },
            "url": { "type": "string" },
            "text": { "type" : "string" }
          },
          "variables": {
			"testName": "[concat(parameters('webTestName'), 
               '-', toLower(parameters('appName')))]"
            "alertRuleName": "[concat(parameters('webTestName'), 
               '-', toLower(parameters('appName')), 
               '-', subscription().subscriptionId)]"
          },
          "resources": [
            {
              // component JSON file contents
            },
            {
              //web test JSON file contents
            },
            {
              //alert rule JSON file contents
            }
 
            // Any other resources go here
          ]
        }
    
    ```

    Esse modelo irá configurar a disponibilidade de um teste além do recurso principal.


2. Abra o [Gerenciador de Recursos do Azure](https://resources.azure.com/). Navegue pela assinaturas, resourceGroups, componentes, até o seu recurso de aplicativo.

    ![](./media/app-insights-powershell/01.png)

    *Componentes* são os recursos básicos do Application Insights para exibir aplicativos. Há recursos separados para as regras de alerta associadas e testes da web de disponibilidade.

3. Copie o JSON do componente para o local apropriado no `template1.json`.
6. Exclua essas propriedades:
  * `id`
  * `InstrumentationKey`
  * `CreationDate`
4. Abra as seções webtests e alertrules e copie o JSON para itens individuais no seu modelo. (Não copie de nós webtests ou alertrules: vá para os itens sob os mesmos.)

    Cada teste da web tem uma regra de alerta associada, então você precisa copiar ambos.

    O teste da web deve estar antes da regra de alerta.

5. Para satisfazer o esquema, insira esta linha em cada recurso:

    `"apiVersion": "2014-04-01",`

    (O esquema também reclama sobre as maiúsculas/minúsculas dos nomes de tipo de recurso `Microsoft.Insights/*` – mas *não* altere-os.)


## Parametrizar o modelo

Agora você tem que substituir os nomes específicos por parâmetros. Para [parametrizar um modelo](../resource-group-authoring-templates.md), escreva expressões que usam um [conjunto de funções auxiliares](../resource-group-template-functions.md).

Você não pode parametrizar apenas uma parte de uma cadeia de caracteres, então use `concat()` para criar cadeias de caracteres.

Estes são exemplos das substituições que você vai querer fazer. Há várias ocorrências de cada substituição. Talvez seja necessário outras em seu modelo. Esses exemplos usam os parâmetros e variáveis definidos na parte superior do modelo.

find | substitua por
---|---
`"hidden-link:/subscriptions/.../components/MyAppName"`| `"[concat('hidden-link:',`<br/>` resourceId('microsoft.insights/components',` <br/> ` parameters('appName')))]"`
`"/subscriptions/.../alertrules/myAlertName-myAppName-subsId",` | `"[resourceId('Microsoft.Insights/alertrules', variables('alertRuleName'))]",`
`"/subscriptions/.../webtests/myTestName-myAppName",` | `"[resourceId('Microsoft.Insights/webtests', parameters('webTestName'))]",`
`"myWebTest-myAppName"` | `"[variables(testName)]"'`
`"myTestName-myAppName-subsId"` | `"[variables('alertRuleName')]"`
`"myAppName"` | `"[parameters('appName')]"`
`"myappname"` (minúscula) | `"[toLower(parameters('appName'))]"`
`"<WebTest Name="myWebTest" ...`<br/>` Url="http://fabrikam.com/home" ...>"`|`[concat('<WebTest Name="',` <br/> `parameters('webTestName'),` <br/> `'" ... Url="', parameters('Url'),` <br/> `'"...>')]" `

## Se seu aplicativo for um Aplicativo Web do Azure

Adicione esse recurso, ou se já houver um recurso `siteextensions`, parametrize-o desta maneira:

```json
    {
      "apiVersion": "2014-04-01",
      "name": "Microsoft.ApplicationInsights.AzureWebSites",
      "type": "siteextensions",
      "dependsOn": [
        "[resourceId('Microsoft.Web/Sites', parameters('siteName'))]",
        "[resourceId('Microsoft.Web/Sites/config', parameters('siteName'), 'web')]",
        "[resourceId('Microsoft.Web/sites/sourcecontrols', parameters('siteName'), 'web')]"
      ],
      "properties": { }
    }

```

Esse recurso implanta o SDK do Application Insights em seu aplicativo Web do Azure.

## Definir dependências entre os recursos

O Azure deve configurar os recursos na ordem explícita. Para certificar-se de que a instalação é concluída antes do início da próxima, adicione linhas de dependência:

* No recurso de teste da web:

    `"dependsOn": ["[resourceId('Microsoft.Insights/components', parameters('appName'))]"],`

* No recurso de alerta:

    `"dependsOn": ["[resourceId('Microsoft.Insights/webtests', variables('testName'))]"],`

## Criar recursos do Application Insights

1. No PowerShell, entre no Azure

    `Login-AzureRmAccount`

2. Execute um comando como este:

    ```PS

        New-AzureRmResourceGroupDeployment -ResourceGroupName Fabrikam `
               -templateFile .\template1.json `
               -appName myNewApp `
               -webTestName aWebTest `
               -Url http://myapp.com `
               -text "Welcome!"
               -siteName "MyAzureSite"

    ``` 

    * -ResourceGroupName é o grupo onde você deseja criar novos recursos.
    * -templateFile deve ocorrer antes dos parâmetros personalizados.
    * -appName O nome do recurso a ser criado.
    * -webTestName O nome do teste da web a ser criado.
    * -Url A url do seu aplicativo web.
    * -text Uma cadeia de caracteres que aparece na página da web.
    * -siteName - usado se for um site do Azure


## Definir alertas de métrica

Há um [método do PowerShell para definição de alertas](app-insights-alerts.md#set-alerts-by-using-powershell).


## Um exemplo

Aqui está o total do componente, o teste da web e o alerta de teste da web que criei:

``` JSON

{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "webTestName": { "type": "string" },
    "appName": { "type": "string" },
    "URL": { "type": "string" },
    "text": { "type" : "string" }
  },
  "variables": {
    "alertRuleName": "[concat(parameters('webTestName'), '-', toLower(parameters('appName')), '-', subscription().subscriptionId)]",
    "testName": "[concat(parameters('webTestName'), '-', toLower(parameters('appName')))]"
  },
  "resources": [
    {
      //"id": "[resourceId('Microsoft.Insights/components', parameters('appName'))]",
      "apiVersion": "2014-04-01",
      "kind": "web",
      "location": "Central US",
      "name": "[parameters('appName')]",
      "properties": {
        "TenantId": "9122605a-471fc50f8438",
        "Application_Type": "web",
        "Flow_Type": "Brownfield",
        "Request_Source": "VSIX3.3.1.0",
        "Name": "[parameters('appName')]",
        //"CreationDate": "2015-10-14T15:55:10.0917441+00:00",
        "PackageId": null,
        "ApplicationId": "[parameters('appName')]"
      },
      "tags": { },
      "type": "microsoft.insights/components"
    },
    {
      //"id": "[resourceId('Microsoft.Insights/webtests', variables('testName'))]",
      "name": "[variables('testName')]",
      "apiVersion": "2014-04-01",
      "type": "microsoft.insights/webtests",
      "location": "Central US",
      "tags": {
        "[concat('hidden-link:', resourceId('microsoft.insights/components', parameters('appName')))]": "Resource"
      },
      "properties": {
        "provisioningState": "Succeeded",
        "Name": "[parameters('webTestName')]",
        "Description": "",
        "Enabled": true,
        "Frequency": 900,
        "Timeout": 120,
        "Kind": "ping",
        "RetryEnabled": true,
        "Locations": [
          {
            "Id": "us-va-ash-azr"
          },
          {
            "Id": "emea-nl-ams-azr"
          },
          {
            "Id": "emea-gb-db3-azr"
          }
        ],
        "Configuration": {
          "WebTest": "[concat(
             '<WebTest   Name="', 
                parameters('webTestName'), 
              '"  Id="32cfc791-aaad-4b50-9c8d-993c21beb218"   Enabled="True"         CssProjectStructure=""    CssIteration=""  Timeout="120"  WorkItemIds=""         xmlns="http://microsoft.com/schemas/VisualStudio/TeamTest/2010"         Description=""  CredentialUserName=""  CredentialPassword=""         PreAuthenticate="True"  Proxy="default"  StopOnError="False"         RecordedResultFile=""  ResultsLocale="">  <Items>  <Request Method="GET"         Guid="a6f2c90b-61bf-b28hh06gg969"  Version="1.1"  Url="', 
              parameters('Url'), 
              '" ThinkTime="0"  Timeout="300" ParseDependentRequests="True"         FollowRedirects="True" RecordResult="True" Cache="False"         ResponseTimeGoal="0"  Encoding="utf-8"  ExpectedHttpStatusCode="200"         ExpectedResponseUrl="" ReportingName="" IgnoreHttpStatusCode="False" />        </Items>  <ValidationRules> <ValidationRule  Classname="Microsoft.VisualStudio.TestTools.WebTesting.Rules.ValidationRuleFindText, Microsoft.VisualStudio.QualityTools.WebTestFramework, Version=10.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" DisplayName="Find Text"         Description="Verifies the existence of the specified text in the response."         Level="High"  ExectuionOrder="BeforeDependents">  <RuleParameters>        <RuleParameter Name="FindText" Value="', 
              parameters('text'), 
              '" />  <RuleParameter Name="IgnoreCase" Value="False" />  <RuleParameter Name="UseRegularExpression" Value="False" />  <RuleParameter Name="PassIfTextFound" Value="True" />  </RuleParameters> </ValidationRule>  </ValidationRules>  </WebTest>')]"
        },
        "SyntheticMonitorId": "[variables('testName')]"
      }
    },
    {
      //"id": "[resourceId('Microsoft.Insights/alertrules', variables('alertRuleName'))]",
      "name": "[variables('alertRuleName')]",
      "apiVersion": "2014-04-01",
      "type": "microsoft.insights/alertrules",
      "location": "East US",
      "dependsOn": [
        "[resourceId('Microsoft.Insights/components', parameters('appName'))]",
        "[resourceId('Microsoft.Insights/webtests', variables('testName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource",
        "[concat('hidden-link:', resourceId('Microsoft.Insights/webtests', variables('testName')))]": "Resource"
      },
      "properties": {
        "name": "[variables('alertRuleName')]",
        "description": "",
        "isEnabled": true,
        "condition": {
          "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.LocationThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
          "odata.type": "Microsoft.Azure.Management.Insights.Models.LocationThresholdRuleCondition",
          "dataSource": {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
            "resourceUri": "[resourceId('microsoft.insights/webtests', variables('testName'))]",
            "metricName": "GSMT_AvRaW"
          },
          "windowSize": "PT15M",
          "failedLocationCount": 2
        },
        "action": {
          "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
          "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
          "sendToServiceOwners": true,
          "customEmails": [ ]
        },
        "provisioningState": "Succeeded",
        "actions": [ ]
      }

    }
  ]
}

```

## Consulte também

Outros artigos sobre automação:

* [Criar um recurso do Application Insights](app-insights-powershell-script-create-resource.md) -método rápido sem usar um modelo.
* [Configurar alertas](app-insights-powershell-alerts.md)
* [Criar testes na Web](https://azure.microsoft.com/blog/creating-a-web-test-alert-programmatically-with-application-insights/)
* [Enviar o Diagnóstico do Azure para o Application Insights](app-insights-powershell-azure-diagnostics.md)
* [Criar anotações de versão](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)

<!---HONumber=AcomDC_0504_2016-->