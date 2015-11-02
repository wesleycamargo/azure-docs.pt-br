<properties
	pageTitle="Introdução ao teste em produção para Aplicativos Web"
	description="Saiba mais sobre o recurso TiP (Teste em Produção) nos Aplicativos Web do Serviço de Aplicativo do Azure."
	services="app-service\web"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/16/2015"
	ms.author="cephalin"/>

# Introdução ao teste em produção para Aplicativos Web

O teste em produção, ou teste dinâmico do seu aplicativo Web usando o tráfego do cliente em tempo real, é uma estratégia de teste que está cada vez mais sendo integrada pelos desenvolvedores de aplicativos em sua metodologia de [desenvolvimento ágil](https://en.wikipedia.org/wiki/Agile_software_development). Ele permite que você teste a qualidade dos seus aplicativos com o tráfego do usuário em tempo real em seu ambiente de produção, em vez de dados sintetizados em um ambiente de teste. Ao expor seu novo aplicativo para usuários reais, você pode ser informado dos problemas reais que o aplicativo poderá enfrentar após sua implantação. Você pode verificar a funcionalidade, desempenho e valor de suas atualizações de aplicativo em relação ao volume, velocidade e variedade do tráfego de usuário real, que você nunca pode aproximar em um ambiente de teste.

## Roteamento de Tráfego nos Aplicativos Web do Serviço de Aplicativo

Com o recurso de Roteamento de Tráfego no [Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714), é possível direcionar uma parte do tráfego do usuário em tempo real para um ou mais [slots de implantação](web-sites-staged-publishing.md) e analisar seu aplicativo com o [Azure Application Insights](/services/application-insights/), [Azure HDInsight](/services/hdinsight/) ou uma ferramenta de terceiros como o [New Relic](/marketplace/partners/newrelic/newrelic/) para validar a alteração. Por exemplo, é possível implementar os seguintes cenários com o Serviço de Aplicativo:

- Descobrir bugs funcionais ou identificar afunilamentos de desempenho em suas atualizações antes da implantação em todo o site
- Execute “voos de teste controlado” de suas alterações, medindo métricas de usabilidade no aplicativo beta
- Aumente gradualmente para uma nova atualização e normalmente volte para a versão atual se ocorrer um erro 
- Otimize os resultados de negócios do seu aplicativo executando [testes A/B](https://en.wikipedia.org/wiki/A/B_testing) ou [testes multivariados](https://en.wikipedia.org/wiki/Multivariate_testing_in_marketing) em vários slots de implantação

### Requisitos para usar o Roteamento de Tráfego em Aplicativos Web

- Seu aplicativo Web deve ser executado na camada **Standard** ou **Premium**, pois ela é necessária para vários slots de implantação.
- Para funcionar corretamente, o Roteamento de Tráfego exige que os cookies estejam habilitados no navegador dos usuários. O Roteamento de Tráfego usa cookies para fixar um navegador do cliente em um slot de implantação durante toda a sessão do cliente.
- O Roteamento de Tráfego dá suporte a cenários avançados do TiP por meio de cmdlets do Azure PowerShell.

## Encaminhar segmento de tráfego para um slot de implantação

No nível básico em cada cenário do TiP, encaminhe um percentual predefinido do seu tráfego em tempo real em um slot de implantação que não seja de produção. Para fazer isso, siga as etapas abaixo:

>[AZURE.NOTE]Nessas etapas, pressupomos que você já tenha um [slot de implantação de não produção](web-sites-staged-publishing.md) e que o conteúdo do aplicativo Web desejado já esteja [implantado](web-sites-publish-source-control.md) nele.

1. Faça logon no [portal de visualização](https://portal.azure.com) do Azure.
2. Na folha de seu aplicativo Web, clique em **Configurações** > **Roteamento de Tráfego**. ![](./media/app-service-web-test-in-production/01-traffic-routing.png)
3. Selecione o slot para o qual você deseja encaminhar o tráfego e o percentual do tráfego total desejado e clique em **Salvar**.

	![](./media/app-service-web-test-in-production/02-select-slot.png)

4. Vá para a folha do slot de implantação. Agora você deverá ver o tráfego em tempo real ser encaminhado para ele.

	![](./media/app-service-web-test-in-production/03-traffic-routed.png)

Depois de configurar o Roteamento de Tráfego, o percentual especificado de clientes será encaminhado aleatoriamente para o slot de não produção. No entanto, é importante observar que quando um cliente é encaminhado automaticamente para um slot específico, ele será “fixado” nesse slot durante a vida útil da sessão do cliente. Isso pode ser feito usando um cookie para fixar a sessão do usuário. Se você inspecionar as solicitações HTTP, você encontrará um cookie `TipMix` em cada solicitação subsequente.

![](./media/app-service-web-test-in-production/04-tip-cookie.png)

## Forçar solicitações de cliente em um slot específico

Além do roteamento de tráfego automático, o Serviço de Aplicativo pode encaminhar solicitações para um slot específico. Isso é útil quando você deseja que os usuários consigam aceitar ou recusar seu aplicativo beta. Para fazer isso, use o parâmetro de consulta `x-ms-routing-name`.

Para redirecionar os usuários para um slot específico usando `x-ms-routing-name`, certifique-se de que o slot já foi adicionado à lista de Roteamento de Tráfego. Como você deseja encaminhar para um slot explicitamente, não importa o percentual de roteamento real que será definido. Se desejar, você pode criar um “link beta” que os usuários podem clicar para acessar o aplicativo beta.

![](./media/app-service-web-test-in-production/06-enable-x-ms-routing-name.png)

### Recusar usuários do aplicativo beta

Para permitir que os usuários recusem seu aplicativo beta, é possível colocar este link na página da Web:

    <a href="<webappname>.azurewebsites.net/?x-ms-routing-name=self">Go back to production app</a>

A cadeia de caracteres `x-ms-routing-name=self` especifica o local de produção. Depois que o navegador do cliente acessar o link, ele não só é redirecionado para o slot de produção, mas todas as solicitações subsequentes conterão o cookie `x-ms-routing-name=self` que fixa a sessão no slot de produção.

![](./media/app-service-web-test-in-production/05-access-production-slot.png)

### Aceitar usuários no aplicativo beta

Para permitir que os usuários aceitem seu aplicativo beta, defina o mesmo parâmetro de consulta como o nome do slot de não produção, por exemplo:

		<webappname>.azurewebsites.net/?x-ms-routing-name=staging

## Mais recursos ##

-   [Configurar ambientes de preparo para aplicativos Web no Serviço de Aplicativo do Azure](web-sites-staged-publishing.md)
-	[Implantar um aplicativo complexo de modo previsível no Azure](app-service-deploy-complex-application-predictably.md)
-   [Desenvolvimento de software Agile com o Serviço de Aplicativo do Azure](app-service-agile-software-development.md)
-	[Usar ambientes de Operações de Desenvolvimento com eficiência em seus aplicativos Web](app-service-web-staged-publishing-realworld-scenarios.md)

<!---HONumber=Oct15_HO4-->