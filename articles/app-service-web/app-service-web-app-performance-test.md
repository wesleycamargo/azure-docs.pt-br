<properties
   pageTitle="Testar o desempenho do seu aplicativo Web do Azure | Microsoft Azure"
   description="Execute testes de desempenho de aplicativo Web do Azure para verificar como o seu aplicativo lida com a carga do usuário. Contabilize o tempo de resposta e encontre falhas que podem indicar problemas."
   services="app-service\web"
   documentationCenter=""
   authors="ecfan"
   manager="douge"
   editor="jimbe"/>

<tags
   ms.service="app-service-web"
   ms.workload="web"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="09/11/2015"
   ms.author="estfan; manasma"/>

# Teste o desempenho do seu aplicativo Web do Azure sob carga

Verifique o desempenho de seu aplicativo Web antes de iniciá-lo ou implantar atualizações na produção. Dessa forma, você pode avaliar melhor se o aplicativo está pronto para lançamento. Tenha mais confiança que seu aplicativo pode dar conta do tráfego durante o pico de uso ou na sua próxima ação de marketing.

Durante a visualização pública, você pode testar o desempenho de seu aplicativo gratuitamente no Portal de Visualização do Azure. Esses testes simulam carga de usuário em seu aplicativo por um período de tempo específico e medem a resposta desse aplicativo. Por exemplo, os resultados do teste mostram a velocidade com que seu aplicativo responde a um número específico de usuários. Eles também mostram quantas solicitações falharam, o que pode indicar problemas com seu aplicativo.

![Localizar problemas de desempenho em seu aplicativo Web][TestOverview]

## Antes de começar

*	Você precisará de uma [assinatura do Azure][AzureSubscription], se ainda não tiver uma. Saiba como [abrir uma conta do Azure gratuitamente][AzureFreeTrial].

*	Você precisará de uma conta do [VSTS (Visual Studio Team Services)][WhatIsVSTS] para manter o histórico de seus testes de desempenho. Crie sua nova conta quando você configura o teste de desempenho ou, se você é o proprietário da conta, use uma conta existente. [O que mais posso fazer com uma conta do Visual Studio Team Services?](#VSTSAccount)

*	Implante seu aplicativo para teste em um ambiente de não produção. Faça com que seu aplicativo use um plano do Serviço de Aplicativo que não seja o plano usado na produção. Desse modo, você não afeta nenhum cliente existente e nem desacelera o seu aplicativo em produção.

## Configurar e executar o teste de desempenho

0.	Entre no [Portal de Visualização do Azure][AzurePortal]. Para usar uma conta do Visual Studio Team Services que você possui, entre como o proprietário da conta.

0.	Vá para seu aplicativo Web.

	![Vá para Procurar Tudo, Aplicativos Web, seu aplicativo Web][WebApp]

0.	Vá para o **Teste de Desempenho**.

	![Vá para Ferramentas, Teste de Desempenho][ExpandedTools]
 
0.	Agora você vinculará uma conta do [VSTS (Visual Studio Team Services)][WhatIsVSTS] para manter o histórico de seus testes de desempenho.

	Se você tem uma conta do VSTS para usar, selecione essa conta. Se não tem, crie uma nova conta.

	![Selecione a conta do VSTS existente ou crie uma nova conta][ExistingNewVSTSAccount]

0.	Crie o teste de desempenho. Defina os detalhes e execute o teste. Você pode observar os resultados em tempo real enquanto o teste é executado.

	Por exemplo, suponha que temos um aplicativo que distribuiu cupons nas vendas de Natal do ano passado. Esse evento durou 15 minutos com uma carga de pico de 100 clientes simultâneos. Gostaríamos de duplicar o número de clientes neste ano. Também queremos melhorar a satisfação do cliente, reduzindo o tempo de carregamento de página de 5 segundos para 2 segundos. Portanto, testaremos desempenho do nosso aplicativo atualizado com 250 usuários por 15 minutos.

	Simularemos a carga em nosso aplicativo gerando usuários virtuais (clientes) que visitam nosso site simultaneamente. Isso nos mostrará quantas solicitações estão falhando ou respondendo com lentidão.

	![Crie, configure e execute seu teste de desempenho][NewTest]

	 *	A URL do seu aplicativo Web padrão é adicionada automaticamente. Você pode alterar a URL para testar outras páginas (somente solicitações HTTP GET).

	 *	Para simular condições locais e reduzir a latência, selecione um local mais próximo de seus usuários para a geração de carga.

	Aqui está o teste em andamento. Durante o primeiro minuto, nossa página carrega mais lentamente do que gostaríamos.

	![Teste de desempenho em andamento com dados em tempo real][TestRunning]

	Após a conclusão do teste, aprendemos que a página carrega muito mais rapidamente após o primeiro minuto. Isso ajuda a identificar no qual é mais provável que desejemos começar a solucionar o problema.

	![Um teste de desempenho concluído mostra os resultados, incluindo solicitações com falha][TestDone]
	
Adoraríamos receber seus comentários. Para perguntas ou problemas, entre em contato conosco em <vsoloadtest@microsoft.com>

##	Perguntas e respostas

#### P: Existe um limite para o tempo pelo qual posso executar um teste? 

R: Sim, você pode executar o teste por até uma hora no Portal de Visualização do Azure.

#### P: Por quanto tempo posso executar testes de desempenho? 

R: Após a visualização pública, você obtém 20.000 VUMs (minutos de usuário virtual) gratuitamente, todo mês, com sua conta do Visual Studio Team Services. Um VUM é o número de usuários virtuais multiplicados pelo número de minutos em seu teste. Se suas necessidades excederem o limite gratuito, você pode adquirir mais tempo e pagar somente pelo que usar.

#### P: Onde posso verificar quantos VUMs usei até agora?

R: Você pode verificar esse valor no portal de Visualização do Azure.

![Vá para a sua conta do VSTS][VSTSAccount]

![Verifique os VUMs usados][CheckTestTime]

<a name="VSOAccount"></a>
#### P: O que mais posso fazer com uma conta do Visual Studio Team Services?

R: para encontrar a nova conta, vá para ```https://{accountname}.visualstudio.com```. Compartilhe seu código, compile, teste, acompanhe o trabalho e despache o software – tudo na nuvem, usando qualquer ferramenta ou idioma. Saiba mais sobre como os recursos e serviços do [Visual Studio Team Services][WhatIsVSTS] ajudam sua equipe a colaborar mais facilmente e implantar de modo contínuo.

<!--Image references-->
[WebApp]: ./media/app-service-web-app-performance-test/azure-np-web-apps.png
[TestOverview]: ./media/app-service-web-app-performance-test/azure-np-perf-test-overview.png
[ExpandedTools]: ./media/app-service-web-app-performance-test/azure-np-web-app-details-tools-expanded.png
[ExistingNewVSTSAccount]: ./media/app-service-web-app-performance-test/azure-np-no-vso-account.png
[NewTest]: ./media/app-service-web-app-performance-test/azure-np-new-performance-test.png
[TestRunning]: ./media/app-service-web-app-performance-test/azure-np-running-perf-test.png
[TestDone]: ./media/app-service-web-app-performance-test/azure-np-perf-test-done.png
[VSTSAccount]: ./media/app-service-web-app-performance-test/azure-np-vso-accounts.png
[CheckTestTime]: ./media/app-service-web-app-performance-test/azure-np-vso-accounts-vum-summary.png

<!--Reference links -->
[AzurePortal]: https://portal.azure.com
[AzureSubscription]: https://account.windowsazure.com/subscriptions
[AzureFreeTrial]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[WhatIsVSTS]: https://www.visualstudio.com/products/what-is-visual-studio-online-vs

<!---HONumber=Nov15_HO4-->