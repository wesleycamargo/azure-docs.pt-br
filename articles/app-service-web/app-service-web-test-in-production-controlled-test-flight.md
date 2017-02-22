---
title: "Implantação flighting (teste beta) no Serviço de Aplicativo do Azure"
description: "Saiba como realizar a implantação flighting de novos recursos em seu aplicativo ou realizar teste beta em suas atualizações neste tutorial completo. Ele reúne os recursos do Serviço de Aplicativo como publicação contínua, slots, roteamento de tráfego e integração do Application Insights."
services: app-service\web
documentationcenter: 
author: cephalin
manager: wpickett
editor: 
ms.assetid: 17953c51-38f8-442d-bb0b-f69c1542f0e9
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/02/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: 84b5f28fcd0640fd85b5f8c9d655105790c70d62


---
# <a name="flighting-deployment-beta-testing-in-azure-app-service"></a>Implantação flighting (teste beta) no Serviço de Aplicativo do Azure
Este tutorial mostra como realizar *implantações de liberação de versões de pré-lançamento* integrando os vários recursos do [Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714) e do [Azure Application Insights](/services/application-insights/).

*Flighting* é um processo de implantação que valida um novo recurso ou alteração com um número limitado de clientes reais, e é um teste importante no cenário de produção. É semelhante ao teste beta e às vezes é conhecido como “voo de teste controlado”. Muitas grandes empresas com uma presença na Web usam essa abordagem para obter validação antecipada sobre suas atualizações de aplicativo em sua prática de [desenvolvimento ágil](https://en.wikipedia.org/wiki/Agile_software_development). O Serviço de Aplicativo do Azure permite a integração de teste em produção com a publicação contínua e o Application Insights para implementar o mesmo cenário de DevOps. Entre os benefícios dessa abordagem estão:

* **Obtenha comentários reais*antes* que as atualizações sejam lançadas na produção** - a única coisa melhor do que obter comentários após o lançamento é obter comentários antes dele. Você pode testar atualizações com o tráfego e comportamentos de usuário real em qualquer etapa desejada do ciclo de vida do produto.
* **Aprimore o [CTDD (desenvolvimento contínuo controlado por testes)](https://en.wikipedia.org/wiki/Continuous_test-driven_development)** - ao integrar o teste em produção com a integração e instrumentação contínuas com o Application Insights, a validação do usuário ocorre automaticamente e desde o início do ciclo de vida do produto. Isso ajuda a reduzir os investimentos em tempo na execução de teste manual.
* **Otimizar o fluxo de trabalho de teste** - ao automatizar o teste em produção com a instrumentação de monitoramento contínuo, é possível potencialmente atingir os objetivos de vários tipos de testes em um único processo, como [integração](https://en.wikipedia.org/wiki/Integration_testing), [regressão](https://en.wikipedia.org/wiki/Regression_testing), [usabilidade](https://en.wikipedia.org/wiki/Usability_testing), acessibilidade, localização, [desempenho](https://en.wikipedia.org/wiki/Software_performance_testing), [segurança](https://en.wikipedia.org/wiki/Security_testing) e[ aceitação](https://en.wikipedia.org/wiki/Acceptance_testing).

Uma implantação flighting não se trata apenas do roteamento de tráfego em tempo real. Nesse tipo de implantação, você deseja obter informações o mais rápido possível, seja um bug inesperado, degradação de desempenho ou problemas de experiência do usuário. Lembre-se de que você está lidando com clientes reais. Portanto, para fazer isso da maneira certa, certifique-se de que você configurou sua implantação flighting para coletar todos os dados necessários para tomar uma decisão informada para a próxima etapa. Este tutorial mostra como coletar dados com o Application Insights, mas você pode usar o New Relic ou outras tecnologias que se adaptem ao seu cenário.

## <a name="what-you-will-do"></a>O que você fará
Neste tutorial, você aprenderá a reunir os seguintes cenários para testar seu aplicativo do Serviço de Aplicativo em produção:

* [Encaminhar o tráfego de produção](app-service-web-test-in-production-get-start.md) para o seu aplicativo beta
* [Instrumentar seu aplicativo](../application-insights/app-insights-web-track-usage.md) para obter métricas úteis
* Implantar continuamente seu aplicativo beta e rastrear métricas de aplicativo em tempo real
* Compare as métricas entre o aplicativo de produção e o aplicativo de beta para ver como as alterações de código são convertidas para resultados

## <a name="what-you-will-need"></a>O que será necessário
* Uma conta do Azure
* Uma conta do [GitHub](https://github.com/)
* Visual Studio 2015 - você pode baixar a [Community edition](https://www.visualstudio.com/en-us/products/visual-studio-express-vs.aspx).
* Git Shell (instalado com o [GitHub para Windows](https://windows.github.com/)) – isso permite que você execute comandos do Git e do PowerShell na mesma sessão
* Bits do [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/download/v0.9.8-September2015/azure-powershell.0.9.8.msi) mais recentes
* Noções básicas sobre:
  * Implantação do modelo do [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) (veja [Implantar um aplicativo complexo de modo previsível no Azure](app-service-deploy-complex-application-predictably.md))
  * [Git](http://git-scm.com/documentation)
  * [PowerShell](https://technet.microsoft.com/library/bb978526.aspx)

> [!NOTE]
> Você de uma conta do Azure para concluir este tutorial:
>
> * É possível [abrir uma conta do Azure gratuitamente](https://azure.microsoft.com/pricing/free-trial/) – você obtém créditos que podem ser usados para experimentar serviços pagos do Azure e, mesmo após eles serem utilizados, é possível manter a conta e utilizar os serviços gratuitos do Azure, como Aplicativos Web.
> * É possível [ativar os benefícios para assinantes do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) – todos os meses, sua assinatura do Visual Studio concede créditos que podem ser usados para experimentar serviços pagos do Azure.
>
> Se você deseja começar com o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, vá até [Experimentar o Serviço de Aplicativo](https://azure.microsoft.com/try/app-service/), em que você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.
>
>

## <a name="set-up-your-production-web-app"></a>Configurar seu aplicativo Web de produção
> [!NOTE]
> O script usado neste tutorial configurará automaticamente a publicação contínua de seu repositório GitHub. Isso requer que as credenciais do GitHub já estejam armazenadas no Azure; caso contrário, a implantação de scripts falhará ao tentar definir configurações de controle de origem para aplicativos Web.
>
> Para armazenar suas credenciais do GitHub no Azure, crie um aplicativo Web no [Portal do Azure](https://portal.azure.com/) e [configure a implantação do GitHub](app-service-continuous-deployment.md). Você só precisa fazer isso uma vez.
>
>

Em um cenário típico de DevOps, você tem um aplicativo que está em execução em tempo real no Azure e deseja fazer alterações nele por meio de publicação contínua. Neste cenário, você implantará na produção um modelo que você desenvolveu e testou.

1. Crie sua própria bifurcação do repositório do [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) . Para obter informações sobre como criar a bifurcação, consulte [Bifurcar um repositório](https://help.github.com/articles/fork-a-repo/). Depois que a bifurcação é criada, você pode vê-la no navegador.

   ![](./media/app-service-agile-software-development/production-1-private-repo.png)
2. Abra uma sessão do Git Shell. Se ainda não tiver o Git Shell, instale o [GitHub para Windows](https://windows.github.com/) agora.
3. Crie um clone local de seu bifurcação executando o seguinte comando:

     git clone https://github.com/<your_fork>/ToDoApp.git
4. Depois que tiver seu clone local, navegue até a *&lt;raiz_do_repositório>*\ARMTemplates e execute o script deploy.ps1 com um sufixo exclusivo, como mostrado abaixo:

     .\deploy.ps1 –RepoUrl https://github.com/<your_fork>/todoapp.git -ResourceGroupSuffix <your_suffix>
5. Quando solicitado, digite o nome de usuário desejado e a senha para acesso ao banco de dados. Lembre-se de suas credenciais do banco de dados, porque você precisará especificá-las novamente ao atualizar o grupo de recursos.

   Você deverá ver o progresso de provisionamento de vários recursos do Azure. Quando a implantação for concluída, o script iniciará o aplicativo no navegador e emitirá um aviso sonoro.
   ![](./media/app-service-web-test-in-production-controlled-test-flight/00.1-app-in-browser.png)
6. De volta à sessão do Git Shell, execute:

     .\swap –Name ToDoApp<your_suffix>

   ![](./media/app-service-web-test-in-production-controlled-test-flight/00.2-swap-to-production.png)
7. Quando o script for concluído, volte para navegar até endereço do front-end (http://ToDoApp*&lt;seu_sufixo>*.azurewebsites.net/) para ver o aplicativo em execução na produção.
8. Faça logon no [Portal do Azure](https://portal.azure.com/) e veja o que foi criado.

   Você deverá ver dois aplicativos Web no mesmo grupo de recursos, um com o sufixo `Api` no nome. Se examinar o modo de exibição do grupo de recursos, você também verá o banco de dados SQL e o servidor, o plano do Serviço de Aplicativo e os slots de preparo dos aplicativos Web. Navegue pelos diferentes recursos e compare-os com *&lt;raiz_do_repositório>*\ARMTemplates\ProdAndStage.json para ver como eles são configurados no modelo.

   ![](./media/app-service-web-test-in-production-controlled-test-flight/00.3-resource-group-view.png)

Você configurou o aplicativo de produção.  Agora, vamos imaginar que você receba comentários de que a usabilidade é insatisfatória para o aplicativo. Portanto, você decide investigar. Você instrumentará seu aplicativo para que ele forneça comentários.

## <a name="investigate-instrument-your-client-app-for-monitoringmetrics"></a>Investigue: instrumentar o aplicativo cliente para monitoramento/métricas
1. Abra *&lt;raiz_do-repositório>*\src\MultiChannelToDo.sln in Visual Studio.
2. Restaure todos os pacotes NuGet clicando com o botão direito do mouse na solução > **Gerenciar Pacotes NuGet da Solução** > **Restaurar**.
3. Clique com o botão direito do mouse em **MultiChannelToDo.Web** > **Adicionar Application Insights Telemetry** > **Definir Configurações** > Alterar grupo de recursos para ToDoApp*&lt;seu_sufixo>* > **Adicionar Application Insights ao Projeto**.
4. No Portal do Azure, abra a folha do recurso **MultiChannelToDo.Web** do Application Insight. Em seguida, na seção **Integridade do aplicativo**, clique em **Saiba como coletar dados de carregamento da página do navegador** > copiar código.
5. Adicione o código de instrumentação JS copiado à *&lt;raiz_repositório>*\src\MultiChannelToDo.Web\app\Index.cshtml, logo antes da marca `<heading>` de fechamento. Ele deve conter a chave exclusiva de instrumentação do recurso do Application Insight.

        <script type="text/javascript">
        var appInsights=window.appInsights||function(config){
            function s(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},r=document,f=window,e="script",o=r.createElement(e),i,u;for(o.src=config.url||"//az416426.vo.msecnd.net/scripts/a/ai.0.js",r.getElementsByTagName(e)[0].parentNode.appendChild(o),t.cookie=r.cookie,t.queue=[],i=["Event","Exception","Metric","PageView","Trace"];i.length;)s("track"+i.pop());return config.disableExceptionTracking||(i="onerror",s("_"+i),u=f[i],f[i]=function(config,r,f,e,o){var s=u&&u(config,r,f,e,o);return s!==!0&&t["_"+i](config,r,f,e,o),s}),t
        }({
            instrumentationKey:"<your_unique_instrumentation_key>"
        });

        window.appInsights=appInsights;
        appInsights.trackPageView();
        </script>
6. Envie eventos personalizados para o Application Insights para cliques do mouse adicionando o seguinte código à parte inferior do corpo:

       <script>
           $(document.body).find("*").click(function(event) {

               appInsights.trackEvent(event.target.tagName + ": " + event.target.className);
           });
       </script>

   Este trecho de código em JavaScript envia um evento personalizado para o Application Insights sempre que um usuário clica em algum lugar no aplicativo Web.
7. No Git Shell, confirme e envie por push as alterações à bifurcação no GitHub. Em seguida, aguarde até que os clientes atualizem o navegador.

       git add -A :/
       git commit -m "add AI configuration for client app"
       git push origin master
8. Alterne as alterações do aplicativo implantado para a produção:

     .\swap –Name ToDoApp<your_suffix>
9. Navegue até o recurso do Application Insights que você configurou. Clique em Eventos personalizados.

   ![](./media/app-service-web-test-in-production-controlled-test-flight/01-custom-events.png)

   Se você não vir as métricas para eventos personalizados, aguarde alguns minutos e clique em **Atualizar**.

Suponha que você veja um gráfico como o mostrado abaixo:

![](./media/app-service-web-test-in-production-controlled-test-flight/02-custom-events-chart-view.png)

E a grade de eventos abaixo dele:

![](./media/app-service-web-test-in-production-controlled-test-flight/03-custom-event-grid-view.png)

De acordo com o código do aplicativo ToDoApp, o evento **BUTTON** corresponde ao botão Enviar e o evento **INPUT** corresponde à caixa de texto. Até agora, as coisas fazem sentido. No entanto, parece que há uma boa quantidade de cliques e muito poucos cliques nos itens de tarefas pendentes (os eventos **LI** ).

Com base nisso, você forma a hipótese de que alguns usuários estão confusos sobre qual parte da interface do usuário é clicável, e isso ocorre porque o cursor tem o estilo de seleção de texto quando ele é passado sobre os itens da lista e seus ícones.

![](./media/app-service-web-test-in-production-controlled-test-flight/04-to-do-list-item-ui.png)

Isso pode ser um exemplo forçado. No entanto, você fará uma melhoria em seu aplicativo e realizará uma implantação flighting para obter comentários de usabilidade dos clientes em tempo real.

### <a name="instrument-your-server-app-for-monitoringmetrics"></a>Instrumentar seu aplicativo de servidor para monitoramento/métricas
Essa é uma tangente, pois o cenário demonstrado neste tutorial lida apenas com o aplicativo cliente. No entanto, para fins de integridade, você configurará o aplicativo do lado do servidor.

1. Clique com o botão direito do mouse em **MultiChannelToDo** > **Adicionar Application Insights Telemetry** > **Definir Configurações** > Alterar grupo de recursos para ToDoApp*&lt;seu_sufixo>* > **Adicionar Application Insights ao Projeto**.
2. No Git Shell, confirme e envie por push as alterações à bifurcação no GitHub. Em seguida, aguarde até que os clientes atualizem o navegador.

       git add -A :/
       git commit -m "add AI configuration for server app"
       git push origin master
3. Alterne as alterações do aplicativo implantado para a produção:

     .\swap –Name ToDoApp<your_suffix>

É isso!

## <a name="investigate-add-slot-specific-tags-to-your-client-app-metrics"></a>Investigue: adicionar marcas específicas do slot às métricas do aplicativo cliente
Nesta seção, você configurará os diferentes slots de implantação para enviar telemetria específica do slot para o mesmo recurso do Application Insights. Dessa forma, você pode comparar os dados de telemetria entre o tráfego de slots diferentes (ambientes de implantação) para ver o efeito das alterações de seu aplicativo com facilidade. Ao mesmo tempo, você pode separar o tráfego de produção do restante, para que você possa continuar a monitorar seu aplicativo de produção, conforme necessário.

Já que você está reunindo dados sobre o comportamento do cliente, você poderá [adicionar um inicializador de telemetria ao seu código do JavaScript](../application-insights/app-insights-api-filtering-sampling.md) em index.cshtml. Se desejar testar o desempenho do lado do servidor, por exemplo, também é possível realizar o mesmo em seu código do servidor (veja [API do Application Insights para métricas e eventos personalizados](../application-insights/app-insights-api-custom-events-metrics.md).

1. Primeiro, adicione o código entre os dois comentários `//` abaixo no bloco de JavaScript adicionado à marca `<heading>` anteriormente.

        window.appInsights = appInsights;

        // Begin new code
        appInsights.queue.push(function () {
            appInsights.context.addTelemetryInitializer(function (envelope) {
                var telemetryItem = envelope.data.baseData;
                telemetryItem.properties = telemetryItem.properties || {};
                telemetryItem.properties["Environment"] = "@System.Configuration.ConfigurationManager.AppSettings["environment"]";
            });
        });
        // End new code

        appInsights.trackPageView();

    Esse código do inicializador faz com que o objeto `appInsights` adicione uma propriedade personalizada chamada `Environment` a cada item de telemetria enviado.
2. Em seguida, adicione essa propriedade personalizada como uma [configuração do slot](web-sites-staged-publishing.md#AboutConfiguration) de seu aplicativo Web no Azure. Para fazer isso, execute os comandos a seguir em sua sessão do Git Shell.

        $app = Get-AzureWebsite -Name todoapp<your_suffix> -Slot production
        $app.AppSettings.Add("environment", "Production")
        $app.SlotStickyAppSettingNames.Add("environment")
        $app | Set-AzureWebsite -Name todoapp<your_suffix> -Slot production

    O Web.config em seu projeto já define a configuração `environment` do aplicativo. Com essa configuração, quando você testar o aplicativo localmente, suas métricas serão marcadas com `VS Debugger`. No entanto, quando você enviar por push suas alterações para o Azure, o Azure encontrará e usará a configuração do aplicativo `environment` na configuração do aplicativo Web e suas métricas serão marcadas com `Production`.
3. Confirme e envie por push as alterações de código à bifurcação no GitHub e aguarde até que os usuários usem o novo aplicativo (é necessário atualizar o navegador). Demora cerca de 15 minutos para que a nova propriedade apareça em seu recurso `MultiChannelToDo.Web` do Application Insights.

        git add -A :/
        git commit -m "add environment property to AI events for client app"
        git push origin master
4. Agora, vá novamente para a folha **Eventos personalizados** e filtre as métricas em `Environment=Production`. Agora, você deverá ver todos os novos eventos personalizados no slot de produção com este filtro.

    ![](./media/app-service-web-test-in-production-controlled-test-flight/05-filter-on-production-environment.png)
5. Clique no botão **Favoritos** para salvar as configurações atuais do Metrics Explorer como algo como **Eventos personalizados: Produção**. Você pode alternar facilmente entre esta exibição e uma exibição de slot de implantação posteriormente.

   > [!TIP]
   > Para uma análise ainda mais eficiente, considere [integrar seu recurso do Application Insights com o Power BI](../application-insights/app-insights-export-power-bi.md).
   >
   >

### <a name="add-slot-specific-tags-to-your-server-app-metrics"></a>Adicionar marcas específicas do slot às suas métricas de aplicativo de servidor
Novamente, para fins de exatidão, você configurará o aplicativo do lado do servidor. Ao contrário do aplicativo cliente que é instrumentado em JavaScript, as marcas específicas do slot para o aplicativo de servidor são instrumentadas com o código do .NET.

1. Abra *&lt;raiz_do_repositório>*\src\MultiChannelToDo\Global.asax.cs. Adicione o bloco de código abaixo, logo antes da chave do namespace de fechamento.

        namespace MultiChannelToDo
        {
                ...

                // Begin new code
            public class ConfigInitializer
            : ITelemetryInitializer
            {
                void ITelemetryInitializer.Initialize(ITelemetry telemetry)
                {
                    telemetry.Context.Properties["Environment"] = System.Configuration.ConfigurationManager.AppSettings["environment"];
                }
            }
                // End new code
        }
2. Corrija os erros de resolução de nomes adicionando as instruções `using` abaixo ao início do arquivo:

        using Microsoft.ApplicationInsights.Channel;
        using Microsoft.ApplicationInsights.Extensibility;
3. Adicione o código abaixo ao início do método `Application_Start()` :

        TelemetryConfiguration.Active.TelemetryInitializers.Add(new ConfigInitializer());
4. Confirme e envie por push as alterações de código à bifurcação no GitHub e aguarde até que os usuários usem o novo aplicativo (é necessário atualizar o navegador). Demora cerca de 15 minutos para que a nova propriedade apareça em seu recurso `MultiChannelToDo` do Application Insights.

        git add -A :/
        git commit -m "add environment property to AI events for server app"
        git push origin master

## <a name="update-set-up-your-beta-branch"></a>Atualização: configurar sua ramificação beta
1. Abra *&lt;raiz_do_repositório>*\ARMTemplates\ProdAndStagetest.json e encontre os recursos do `appsettings` (procure `"name": "appsettings"`). Há quatro deles, um para cada slot.
2. Para cada recurso do `appsettings`, adicione uma configuração do aplicativo `"environment": "[parameters('slotName')]"` ao final da matriz `properties`. Não se esqueça de terminar a linha anterior com uma vírgula.

    ![](./media/app-service-web-test-in-production-controlled-test-flight/06-arm-app-setting-with-slot-name.png)

    Você acabou de adicionar a configuração de aplicativo `environment` a todos os slots no modelo.
3. No mesmo arquivo, encontre os recursos do `slotconfignames` (procure `"name": "slotconfignames"`). Há dois deles, um para cada aplicativo.
4. Para cada recurso do `slotconfignames`, adicione `"environment"` ao final da matriz `appSettingNames`. Não se esqueça de terminar a linha anterior com uma vírgula.

    Você acabou de fixar a configuração de aplicativo `environment` em seu respectivo slot de implantação para os dois aplicativos.  
5. Em sua sessão do Git Shell, execute os comandos a seguir com o mesmo sufixo do grupo de recursos que você usou anteriormente.

        git checkout -b beta
        git push origin beta
        .\deploy.ps1 -RepoUrl https://github.com/<your_fork>/ToDoApp.git -ResourceGroupSuffix <your_suffix> -SlotName beta -Branch beta
6. Quando solicitado, especifique as mesmas credenciais do banco de dados SQL que anteriormente. Em seguida, quando solicitado a atualizar o grupo de recursos, digite `Y` e `ENTER`.

    Depois que o script for concluído, todos os seus recursos no grupo de recursos original serão mantidos, mas um novo slot chamado “beta” será criado nele com a mesma configuração que o slot de “Preparação” que foi criado no início.

   > [!NOTE]
   > Esse método de criação de diferentes ambientes de implantação é diferente do método no [Desenvolvimento de software Agile com o Serviço de Aplicativo do Azure](app-service-agile-software-development.md). Aqui, você cria ambientes de implantação com slots de implantação, enquanto que lá você cria ambientes de implantação com grupos de recursos. O gerenciamento de ambientes de implantação com grupos de recursos permite que você mantenha o ambiente de produção fora dos limites para os desenvolvedores; porém, não é fácil realizar testes em produção, o que pode ser feito facilmente com slots.
   >
   >

Se desejar, você também pode criar um aplicativo alfa executando

    git checkout -b alpha
    git push origin alpha
    .\deploy.ps1 -RepoUrl https://github.com/<your_fork>/ToDoApp.git -ResourceGroupSuffix <your_suffix> -SlotName beta -Branch alpha

Para este tutorial, você continuará usando seu aplicativo beta.

## <a name="update-push-your-updates-to-the-beta-app"></a>Atualização: enviar por push as atualizações para o aplicativo beta
Volte ao aplicativo que você deseja melhorar.

1. Verifique se você está agora em sua ramificação beta

        git checkout beta
2. Em *&lt;raiz_repositório>*\src\MultiChannelToDo.Web\app\Index.cshtml, encontre a marca `<li>` e adicione o atributo `style="cursor:pointer"`, como mostrado abaixo.

    ![](./media/app-service-web-test-in-production-controlled-test-flight/07-change-cursor-style-on-li.png)
3. confirme e envie por push para o Azure.
4. Verifique se a alteração é agora refletida no slot beta navegando até http://todoapp*&lt;seu_sufixo>*-beta.azurewebsites.net/. Se você ainda não vir a alteração, atualize o navegador para obter o novo código JavaScript.

    ![](./media/app-service-web-test-in-production-controlled-test-flight/08-verify-change-in-beta-site.png)

Agora que a alteração está em execução no slot beta, você está pronto para realizar uma implantação flighting.

## <a name="validate-route-traffic-to-the-beta-app"></a>Validar: encaminhar o tráfego para o aplicativo beta
Nesta seção, você encaminhará o tráfego para o aplicativo beta. Para fins de clareza de demonstração, vamos encaminhar uma parte significativa do tráfego de usuário para ele. Na realidade, a quantidade de tráfego que você deseja encaminhar dependerá de sua situação específica. Por exemplo, se o seu site estiver na escala do microsoft.com, talvez seja necessário menos de&1;% do tráfego total para obter dados úteis.

1. Em sua sessão do Git Shell, execute os seguintes comandos para encaminhar metade do tráfego de produção para o slot beta:

        $siteName = "ToDoApp<your suffix>"
        $rule = New-Object Microsoft.WindowsAzure.Commands.Utilities.Websites.Services.WebEntities.RampUpRule
        $rule.ActionHostName = "$siteName-beta.azurewebsites.net"
        $rule.ReroutePercentage = 50
        $rule.Name = "beta"
        Set-AzureWebsite $siteName -Slot Production -RoutingRules $rule

   A propriedade `ReroutePercentage=50` especifica que 50% do tráfego de produção será encaminhado para a URL do aplicativo beta (especificado pela propriedade `ActionHostName`).
2. Agora, navegue até http://ToDoApp*&lt;seu_sufixo>*.azurewebsites.net. 50% do tráfego agora deve ser redirecionado para o slot beta.
3. Em seu recurso do Application Insights, filtre as métricas pelo ambiente = “beta”.

   > [!NOTE]
   > Se você salvar esta exibição filtrada como outro favorito, você poderá inverter facilmente as exibições do Metric Explorer entre as exibições de produção e beta.
   >
   >

Suponha que no Application Insights você veja algo semelhante ao seguinte:

![](./media/app-service-web-test-in-production-controlled-test-flight/09-test-beta-site-in-production.png)

Isso não só mostra que há muito mais cliques nas marcas `<li>`, mas também que parece haver um aumento de cliques nas marcas `<li>`. Em seguida, você pode concluir que as pessoas descobriram que as novas marcas `<li>` são clicáveis e agora estão limpando todas as suas tarefas concluídas anteriormente no aplicativo.

Com base nos dados de sua implantação flighting, você decide que a nova interface do usuário está pronta para produção.

## <a name="go-live-move-your-new-code-into-production"></a>Ativação: mover seu novo código para a produção
Agora você está pronto para mover a atualização para a produção. A boa notícia é que agora você sabe que a atualização já foi validada *antes* de ser enviada para a produção. Agora você pode implantá-la com confiança. Já que você fez uma atualização ao aplicativo de cliente AngularJS, você apenas validou o código do lado do cliente. Se você fosse fazer alterações no aplicativo de API da Web de back-end, você poderia validar as alterações da mesma forma e com facilidade.

1. No Git Shell, remova a regra de roteamento de tráfego executando o seguinte comando:

        Set-AzureWebsite $siteName -Slot Production -RoutingRules @()
2. Execute os comandos do Git:

        git checkout master
        git pull origin master
        git merge beta
        git push origin master
3. Aguarde alguns minutos até que o novo código seja implantado no slot de preparo e inicie http://ToDoApp*&lt;seu_sufixo>*-staging.azurewebsites.net para verificar se a nova atualização está pronta no slot de preparo. Lembre-se de que a ramificação mestre da bifurcação está vinculada ao slot de preparo do aplicativo.
4. Agora, permute o slot de preparo para a produção

        cd <ROOT>\ToDoApp\ARMTemplates
        .\swap.ps1 -Name todoapp<your_suffix>

## <a name="summary"></a>Resumo
O Serviço de Aplicativo do Azure facilita para as empresas de pequeno a médio porte testar seus aplicativos voltados para o cliente em produção, algo que tradicionalmente era feito em grandes empresas. Espero que este tutorial tenha fornecido o conhecimento necessário para reunir o Serviço de Aplicativo e o Application Insights e possibilitar a implantação flighting, e até mesmo outros cenários de teste na produção, em seu mundo DevOps.

## <a name="more-resources"></a>Mais recursos
* [Desenvolvimento de software Agile com o Serviço de Aplicativo do Azure](app-service-agile-software-development.md)
* [Configurar ambientes de preparo para aplicativos Web no Serviço de Aplicativo do Azure](web-sites-staged-publishing.md)
* [Implantar um aplicativo complexo de modo previsível no Azure](app-service-deploy-complex-application-predictably.md)
* [Criando modelos do Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md)
* [JSONLint - o validador JSON](http://jsonlint.com/)
* [Ramificação Git – Conceitos básicos de ramificação e mesclagem](http://www.git-scm.com/book/en/v2/Git-Branching-Basic-Branching-and-Merging)
* [PowerShell do Azure](/powershell/azureps-cmdlets-docs)
* [Projeto Kudu Wiki](https://github.com/projectkudu/kudu/wiki)



<!--HONumber=Jan17_HO3-->


