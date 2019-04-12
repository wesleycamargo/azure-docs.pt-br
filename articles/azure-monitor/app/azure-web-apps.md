---
title: Monitorar desempenho dos serviços de aplicativos do Azure | Microsoft Docs
description: Monitoramento do desempenho de aplicativos para serviços de aplicativos do Azure. Gráfico de carga e o tempo de resposta, as informações de dependência e definir alertas de desempenho.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: mbullwin
ms.openlocfilehash: 25f620cb36c2bfb548ecf08c33dc04b37118a256
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/11/2019
ms.locfileid: "59489615"
---
# <a name="monitor-azure-app-service-performance"></a>Monitorar o desempenho do Serviço de Aplicativo do Azure

Habilitando o monitoramento no .NET e .NET Core com base em aplicativos web executados [serviços de aplicativo do Azure](https://docs.microsoft.com/azure/app-service/) agora é mais fácil do que nunca. Enquanto que anteriormente você precisava instalar manualmente uma extensão de site, o mais recente/agente de extensão agora é interno à imagem do serviço de aplicativo por padrão. Este artigo irá orientá-lo durante a habilitação do monitoramento do Application Insights, bem como fornecer diretrizes preliminares para automatizar o processo para implantações em larga escala.

> [!NOTE]
> Adicionar manualmente uma extensão de site do Application Insights por meio **ferramentas de desenvolvimento** > **extensões** foi preterido. Esse método de instalação da extensão era dependente de atualizações manuais para cada nova versão. A versão estável mais recente da extensão é agora [pré-instalado](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions) como parte da imagem do serviço de aplicativo. Os arquivos estão localizados em `d:\Program Files (x86)\SiteExtensions\ApplicationInsightsAgent` e são atualizados automaticamente com cada versão estável. Se você seguir as instruções de agente com base para habilitar o monitoramento abaixo, ele removerá automaticamente a extensão preterida para você.

## <a name="enable-application-insights"></a>Habilitar o Application Insights

Há duas maneiras de habilitar o monitoramento de aplicativos para aplicativos de serviços de aplicativo do Azure hospedado:

* **Monitoramento de aplicativos baseados em agente** (ApplicationInsightsAgent).  
    * Esse método é mais fácil de habilitar e é necessária nenhuma configuração avançada. Ele é conhecido como "runtime" monitoramento. Para serviços de aplicativo do Azure recomendamos no mínimo habilitando esse nível de monitoramento e, em seguida, com base em seu cenário específico, que você pode avaliar se o monitoramento mais avançado por meio da instrumentação manual é necessária.

* **Instrumentar manualmente o aplicativo por meio de código** instalando o SDK do Application Insights.

    * Essa abordagem é muito mais personalizável, mas requer [adicionar uma dependência nos pacotes NuGet do SDK do Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net). Esse método, também significa que você precisa gerenciar as atualizações para a versão mais recente dos pacotes por conta própria.

    * Se você precisar fazer chamadas à API personalizadas para acompanhar eventos/dependências não são capturadas por padrão com o monitoramento baseado em agente, você precisa usar esse método. Confira a [API para o artigo de métricas e eventos personalizado](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics) para saber mais.

> [!NOTE]
> Se o agente de monitoramento com base e o SDK manual com base em instrumentação é detectado que somente as configurações de instrumentação manual serão respeitadas. Isso é para evitar dados duplicados do enviados. Para saber mais sobre esse check-out a [seção solução de problemas](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting) abaixo.

## <a name="enable-agent-based-monitoring-net"></a>Habilitar com base em agente de monitoramento .NET

1. **Selecione o Application Insights** no painel de controle do Azure para seu serviço de aplicativo.

    ![Em Configurações, escolha Insights do aplicativo](./media/azure-web-apps/settings-app-insights-01.png)

   * Opte por criar um novo recurso, a menos que você já tenha configurado um recurso do Application Insights para este aplicativo. 

     > [!NOTE]
     > Ao clicar em **OK** para criar o novo recurso, você será solicitado a **Aplicar configurações de monitoramento**. Se você selecionar **Continuar**, vinculará seu novo recurso do Application Insights ao seu serviço de aplicativo. Fazer isso também **disparará uma reinicialização do seu serviço de aplicativo**. 

     ![Instrumentar seu aplicativo Web](./media/azure-web-apps/create-resource-01.png)

2. Depois de especificar qual recurso usar, você pode escolher como deseja que o Application Insights colete dados por plataforma para seu aplicativo. Monitoramento de aplicativo do ASP.NET é ativado por padrão com dois níveis diferentes de coleção.

    ![Escolha as opções por plataforma](./media/azure-web-apps/choose-options-new.png)

   * O nível **Coleção básica** do .NET oferece recursos essenciais de APM de instância única.

   * O nível **Coleção recomendada** do .NET:
       * Adiciona as tendências de uso de CPU, memória e E/S.
       * Correlaciona microsserviços entre limites de solicitação/dependência.
       * Coleta as tendências de uso e permite a correlação entre resultados de disponibilidade e transações.
       * Coleta as exceções não tratadas pelo processo de host.
       * Aumenta a precisão de métricas de APM com carga quando a amostragem é usada.

3. Para definir as configurações, como amostragem, que anteriormente, você pode controlar por meio do arquivo applicationinsights. config agora você pode interagir com essas mesmas configurações por meio das configurações de aplicativo com um prefixo correspondente. 

    * Por exemplo, para alterar o percentual de amostragem inicial, você pode criar uma configuração de aplicativo de: `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_InitialSamplingPercentage` e um valor de `100`.

    * Para obter a lista de configurações de processador de telemetria de amostragem adaptável com suporte, você pode consultar a [código](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/master/src/ServerTelemetryChannel/AdaptiveSamplingTelemetryProcessor.cs) e [documentação associada](https://docs.microsoft.com/azure/azure-monitor/app/sampling).

## <a name="enable-agent-based-monitoring-net-core"></a>Habilitar com base em agente de monitoramento do .NET Core

Há suporte para as seguintes versões do .NET Core: ASP.NET Core 2.0, ASP.NET Core 2.1, ASP.NET Core 2.2

Direcionamento de estrutura completa do .NET Core, implantação autocontida e ASP.NET Core 3.0 estão atualmente **não tem suporte** com/extensão do agente com base em monitoramento. ([Instrumentação manual](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) por meio do código funcionará em todos os cenários anteriores.)

1. **Selecione o Application Insights** no painel de controle do Azure para seu serviço de aplicativo.

    ![Em Configurações, escolha Insights do aplicativo](./media/azure-web-apps/settings-app-insights-01.png)

   * Opte por criar um novo recurso, a menos que você já tenha configurado um recurso do Application Insights para este aplicativo. 

     > [!NOTE]
     > Ao clicar em **OK** para criar o novo recurso, você será solicitado a **Aplicar configurações de monitoramento**. Se você selecionar **Continuar**, vinculará seu novo recurso do Application Insights ao seu serviço de aplicativo. Fazer isso também **disparará uma reinicialização do seu serviço de aplicativo**. 

     ![Instrumentar seu aplicativo Web](./media/azure-web-apps/create-resource-01.png)

2. Depois de especificar qual recurso usar, você pode escolher como deseja que o Application Insights para coletar dados por plataforma para seu aplicativo. O .NET core oferece **recomendado coleta** ou **desabilitado** para .NET Core 2.0, 2.1 e 2.2.

    ![Escolha as opções por plataforma](./media/azure-web-apps/choose-options-new-net-core.png)

## <a name="enable-client-side-monitoring-net"></a>Habilitar o .NET de monitoramento do lado do cliente

Monitoramento do lado do cliente é uma opção para o ASP.NET. Para habilitar o monitoramento do lado do cliente:

* Selecione **configurações** > * * * * configurações do aplicativo *
   * Em configurações do aplicativo, adicione um novo **nome da configuração de aplicativo** e **valor**:

     Nome: `APPINSIGHTS_JAVASCRIPT_ENABLED`

     Valor: `true`

   * **Salve** as configurações e **Reinicie** seu aplicativo.

![Captura de tela do aplicativo de configurações de interface do usuário](./media/azure-web-apps/appinsights-javascript-enabled.png)

Para desabilitar o monitoramento de remover o par de valor de chave associado, configurações do aplicativo do lado cliente, ou defina o valor como false.

## <a name="enable-client-side-monitoring-net-core"></a>Habilitar o monitoramento .NET Core do lado do cliente

Monitoramento do lado do cliente é **habilitado por padrão** para aplicativos .NET Core com **recomendado coleção**, independentemente de se a configuração 'APPINSIGHTS_JAVASCRIPT_ENABLED' do aplicativo está presente.

Se por algum motivo você gostaria de desabilitar o monitoramento do lado do cliente:

* Selecione **as configurações** > **as configurações do aplicativo**
   * Em configurações do aplicativo, adicione um novo **nome da configuração de aplicativo** e **valor**:

     nome: `APPINSIGHTS_JAVASCRIPT_ENABLED`

     Valor: `false`

   * **Salve** as configurações e **Reinicie** seu aplicativo.

![Captura de tela do aplicativo de configurações de interface do usuário](./media/azure-web-apps/appinsights-javascript-disabled.png)

## <a name="automate-monitoring"></a>Automatizar o monitoramento

Para habilitar a coleta de telemetria com o Application Insights, somente as configurações do aplicativo precisam ser definido:

   ![Configurações do aplicativo de serviço de aplicativo com as configurações disponíveis do Application Insights](./media/azure-web-apps/application-settings.png)

### <a name="application-settings-definitions"></a>Definições de configurações do aplicativo

|Nome da configuração do aplicativo |  Definição | Valor |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | Extensão principal, que controla o tempo de execução de monitoramento. | `~2` |
|XDT_MicrosoftApplicationInsights_Mode |  Em default recursos essenciais, apenas em modo são habilitados para garantir um desempenho ideal. | `default` ou `recommended`. |
|InstrumentationEngine_EXTENSION_VERSION | Controla se o mecanismo de regravação de binário `InstrumentationEngine` será ativado. Essa configuração tem implicações de desempenho e afeta o tempo de início/inicialização a frio. | `~1` |
|XDT_MicrosoftApplicationInsights_BaseExtensions | Controla se o texto de tabela do SQL e o Azure será capturada junto com as chamadas de dependência. Aviso de desempenho: essa configuração requer que o `InstrumentationEngine`. | `~1` |

### <a name="app-service-application-settings-with-azure-resource-manager"></a>Configurações do aplicativo de serviço de aplicativo com o Azure Resource Manager

Configurações de aplicativo para serviços de aplicativos podem ser gerenciadas e configuradas com [modelos do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). Esse método pode ser usado durante a implantação de novos recursos de serviço de aplicativo com a automação do Azure Resource Manager ou para modificar as configurações dos recursos existentes.

A estrutura básica de configurações do aplicativo JSON para um serviço de aplicativo está abaixo:

```JSON
      "resources": [
        {
          "name": "appsettings",
          "type": "config",
          "apiVersion": "2015-08-01",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites', variables('webSiteName'))]"
          ],
          "tags": {
            "displayName": "Application Insights Settings"
          },
          "properties": {
            "key1": "value1",
            "key2": "value2"
          }
        }
      ]

```

Para obter um exemplo de um modelo do Azure Resource Manager com configurações de aplicativo configurado para o Application Insights, isso [modelo](https://github.com/Andrew-MSFT/BasicImageGallery) podem ser úteis, especificamente a seção começando na [linha 238](https://github.com/Andrew-MSFT/BasicImageGallery/blob/c55ada54519e13ce2559823c16ca4f97ddc5c7a4/CoreImageGallery/Deploy/CoreImageGalleryARM/azuredeploy.json#L238).

### <a name="automate-the-creation-of-an-application-insights-resource-and-link-to-your-newly-created-app-service"></a>Automatize a criação de um recurso do Application Insights e um link para seu serviço de aplicativo recém-criado.

Para criar um modelo do Azure Resource Manager com todas as configurações de Insights de aplicativo padrão configuradas, inicie o processo de como se você fosse criar um novo aplicativo Web com o Application Insights habilitado.

Selecione **opções de automação**

   ![Menu de criação de aplicativo do serviço de aplicativo web](./media/azure-web-apps/create-web-app.png)

Essa opção gera o modelo mais recente do Azure Resource Manager com todas as configurações necessárias.

  ![Modelo de aplicativo do serviço de aplicativo web](./media/azure-web-apps/arm-template.png)

Abaixo está um exemplo, substitua todas as instâncias de `AppMonitoredSite` com o nome do site:

```json
{
    "resources": [
        {
            "name": "[parameters('name')]",
            "type": "Microsoft.Web/sites",
            "properties": {
                "siteConfig": {
                    "appSettings": [
                        {
                            "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
                            "value": "[reference('microsoft.insights/components/AppMonitoredSite', '2015-05-01').InstrumentationKey]"
                        },
                        {
                            "name": "ApplicationInsightsAgent_EXTENSION_VERSION",
                            "value": "~2"
                        }
                    ]
                },
                "name": "[parameters('name')]",
                "serverFarmId": "[concat('/subscriptions/', parameters('subscriptionId'),'/resourcegroups/', parameters('serverFarmResourceGroup'), '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "hostingEnvironment": "[parameters('hostingEnvironment')]"
            },
            "dependsOn": [
                "[concat('Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "microsoft.insights/components/AppMonitoredSite"
            ],
            "apiVersion": "2016-03-01",
            "location": "[parameters('location')]"
        },
        {
            "apiVersion": "2016-09-01",
            "name": "[parameters('hostingPlanName')]",
            "type": "Microsoft.Web/serverfarms",
            "location": "[parameters('location')]",
            "properties": {
                "name": "[parameters('hostingPlanName')]",
                "workerSizeId": "[parameters('workerSize')]",
                "numberOfWorkers": "1",
                "hostingEnvironment": "[parameters('hostingEnvironment')]"
            },
            "sku": {
                "Tier": "[parameters('sku')]",
                "Name": "[parameters('skuCode')]"
            }
        },
        {
            "apiVersion": "2015-05-01",
            "name": "AppMonitoredSite",
            "type": "microsoft.insights/components",
            "location": "West US 2",
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Request_Source": "IbizaWebAppExtensionCreate"
            }
        }
    ],
    "parameters": {
        "name": {
            "type": "string"
        },
        "hostingPlanName": {
            "type": "string"
        },
        "hostingEnvironment": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "sku": {
            "type": "string"
        },
        "skuCode": {
            "type": "string"
        },
        "workerSize": {
            "type": "string"
        },
        "serverFarmResourceGroup": {
            "type": "string"
        },
        "subscriptionId": {
            "type": "string"
        }
    },
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0"
}
```

> [!NOTE]
> O modelo gerará as configurações do aplicativo no modo de "default". Esse modo é o desempenho otimizado, embora você pode modificar o modelo para ativar qualquer recursos que você preferir.

### <a name="enabling-through-powershell"></a>Habilitar por meio do PowerShell

Para permitir que o aplicativo de monitoramento por meio do PowerShell, somente as configurações de aplicativo subjacente precisam ser alterado. Abaixo está um exemplo, que habilita o monitoramento de aplicativo para um site chamado "AppMonitoredSite" no grupo de recursos "AppMonitoredRG", e configura os dados a serem enviados para a chave de instrumentação "012345678-abcd-ef01-2345-6789abcd".

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

```powershell
$app = Get-AzWebApp -ResourceGroupName "AppMonitoredRG" -Name "AppMonitoredSite" -ErrorAction Stop
$newAppSettings = @{} # case-insensitive hash map
$app.SiteConfig.AppSettings | %{$newAppSettings[$_.Name] = $_.Value} #preserve non Application Insights Application settings.
$newAppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"] = "012345678-abcd-ef01-2345-6789abcd"; # enable the ApplicationInsightsAgent
$newAppSettings["ApplicationInsightsAgent_EXTENSION_VERSION"] = "~2"; # enable the ApplicationInsightsAgent
$app = Set-AzWebApp -AppSettings $newAppSettings -ResourceGroupName $app.ResourceGroup -Name $app.Name -ErrorAction Stop
```

## <a name="upgrade-monitoring-extensionagent"></a>Atualização de extensão/agente de monitoramento

### <a name="upgrading-from-versions-289-and-up"></a>Atualizando de versões 2.8.9 e backup

Atualização de versão 2.8.9 ocorre automaticamente, sem nenhuma ação adicional. Os novos bits de monitoramento são entregues em segundo plano para o serviço de aplicativo de destino e na reinicialização do aplicativo eles serão selecionados.

Para verificar qual versão da extensão, você está executando a visita `http://yoursitename.scm.azurewebsites.net/ApplicationInsights`

![Captura de tela do caminho da url http://yoursitename.scm.azurewebsites.net/ApplicationInsights](./media/azure-web-apps/extension-version.png)

### <a name="upgrade-from-versions-100---265"></a>Atualização das versões 1.0.0 - 2.6.5

A extensão de site previamente instalado a partir da versão 2.8.9 é usada. Se você for uma versão anterior, você pode atualizar por meio de uma das duas maneiras:

* [Atualização, permitindo por meio do portal](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights). (Mesmo se você tiver a extensão do Application Insights para o serviço de aplicativo do Azure instalado, a interface do usuário mostra apenas **habilitar** botão. Nos bastidores, a extensão de site privado antigo será removida.)

* [Atualização por meio do PowerShell](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell):

    1. Defina as configurações do aplicativo para habilitar a extensão de site previamente instalado ApplicationInsightsAgent. Ver [habilitar por meio do powershell](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell).
    2. Remova manualmente a extensão de site privado chamada extensão do Application Insights para o serviço de aplicativo do Azure.

Se a atualização é feita de uma versão anterior à 2.5.1, verifique se as dlls de ApplicationInsigths são removidas da pasta bin do aplicativo [ver as etapas de solução de problemas](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting).

## <a name="troubleshooting"></a>solução de problemas

Abaixo está o nosso guia passo a passo de solução de problemas para o monitoramento/agente de extensão com base em .NET e .NET Core com base em aplicativos em execução nos serviços de aplicativo do Azure.

> [!NOTE]
> Aplicativos Java e Node. js só têm suporte nos serviços de aplicativo do Azure por meio de instrumentação manual do SDK com base e, portanto, as etapas a seguir não se aplicam a esses cenários.

1. Verifique se o aplicativo é monitorado por meio do `ApplicationInsightsAgent`.
    * Verifique se `ApplicationInsightsAgent_EXTENSION_VERSION` configuração de aplicativo é definida como um valor de "~ 2".
2. Certifique-se de que o aplicativo atende aos requisitos a serem monitorados.
    * Navegue até `https://yoursitename.scm.azurewebsites.net/ApplicationInsights`

    ![Captura de tela de https://yoursitename.scm.azurewebsites/applicationinsights página de resultados](./media/azure-web-apps/app-insights-sdk-status.png)

    * Confirme se o `Application Insights Extension Status` é `Pre-Installed Site Extension, version 2.8.12.1527, is running.`
        * Se ele não está em execução, execute o [habilitar monitoramento de instruções do Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights)

    * Confirme se a fonte de status existe e se parece com: `Status source D:\home\LogFiles\ApplicationInsights\status\status_RD0003FF0317B6_4248_1.json`
        * Se um valor semelhante não estiver presente, isso significa que o aplicativo não estiver em execução ou não é suportado. Para garantir que o aplicativo está em execução, tente visitar manualmente os pontos de extremidade de url/aplicativo do aplicativo, que permitirão que as informações de tempo de execução se torne disponível.

    * Confirme se `IKeyExists` é `true`
        * Se for false, adicione ' APPINSIGHTS_INSTRUMENTATIONKEY com sua guid ikey para as configurações do aplicativo.

    * Confirme se não houver nenhuma entrada para `AppAlreadyInstrumented`, `AppContainsDiagnosticSourceAssembly`, e `AppContainsAspNetTelemetryCorrelationAssembly`.
        * Se existir alguma dessas entradas, remova os seguintes pacotes de seu aplicativo: `Microsoft.ApplicationInsights`, `System.Diagnostics.DiagnosticSource`, e `Microsoft.AspNet.TelemetryCorrelation`.

A tabela a seguir fornece uma explicação mais detalhada do que significam esses valores, seus subjacente faz com que e correções recomendadas:

|Valor de problema|Explicação|Correção
|---- |----|---|
| `AppAlreadyInstrumented:true` | Esse valor indica que a extensão detectou que alguns aspectos do SDK do já está presente no aplicativo e serão retirada. Ele pode ser devido a uma referência a `System.Diagnostics.DiagnosticSource`, `Microsoft.AspNet.TelemetryCorrelation`, ou `Microsoft.ApplicationInsights`  | Remova as referências. Algumas dessas referências são adicionadas por padrão de determinados modelos do Visual Studio e as versões mais antigas do Visual Studio podem adicionar referências a `Microsoft.ApplicationInsights`.
|`AppAlreadyInstrumented:true` | Se o aplicativo está direcionando o .NET Core 2.1 ou 2.2 e refere-se ao [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) meta-package, em seguida, ele traz no Application Insights e extensão será retirada. | Os clientes no .NET Core 2.1,2.2 [recomendado](https://github.com/aspnet/Announcements/issues/287) usar meta-package Microsoft em vez disso.|
|`AppAlreadyInstrumented:true` | Esse valor também pode ser causado pela presença de dlls na pasta do aplicativo de uma implantação anterior acima. | Limpe a pasta de aplicativo para garantir que essas dlls são removidas.|
|`AppContainsAspNetTelemetryCorrelationAssembly: true` | Esse valor indica que a extensão detectou referências a `Microsoft.AspNet.TelemetryCorrelation` no aplicativo e será retirada. | Remova a referência.
|`AppContainsDiagnosticSourceAssembly**:true`|Esse valor indica que a extensão detectou referências a `System.Diagnostics.DiagnosticSource` no aplicativo e será retirada.| Remova a referência.
|`IKeyExists:false`|Esse valor indica que a chave de instrumentação não está presente na AppSetting, `APPINSIGHTS_INSTRUMENTATIONKEY`. Possíveis causas: Os valores pode ter sido removida acidentalmente, se esqueceu de definir os valores no script de automação, etc. | Verifique se que a configuração está presente nas configurações do aplicativo de serviço de aplicativo.

Para obter as informações mais recentes sobre a Application Insights/extensão do agente, confira a [notas de versão](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/app-insights-web-app-extensions-releasenotes.md).

## <a name="next-steps"></a>Próximas etapas
* [Executar o criador de perfil em seu aplicativo ativo](../app/profiler.md).
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample) – monitorar o Azure Functions com o Application Insights
* [Permita que o diagnóstico do Azure](../platform/diagnostics-extension-to-application-insights.md) seja enviado ao Application Insights.
* [Monitore as métricas de integridade do serviço](../platform/data-platform.md) para se certificar de que o serviço esteja disponível e responsivo.
* [Receba notificações de alerta](../platform/alerts-overview.md) sempre que ocorrerem eventos operacionais ou métricas ultrapassarem um limite.
* Use o [Application Insights para aplicativos JavaScript e páginas Web](javascript.md) para obter a telemetria do cliente dos navegadores que visitam uma página da Web.
* [Configure testes de disponibilidade da Web](monitor-web-app-availability.md) para ser alertado se o seu site for desativado.
