<properties 
	pageTitle="Como escalar um aplicativo em um Ambiente do Serviço de Aplicativo" 
	description="Escalando um aplicativo em um Ambiente do Serviço de Aplicativo" 
	services="app-service" 
	documentationCenter="" 
	authors="ccompy" 
	manager="stefsch" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/26/2015" 
	ms.author="ccompy"/>

# Escalando aplicativos em um Ambiente do Serviço de Aplicativo #

Em um alto nível, os Ambientes do Serviço de Aplicativo são essencialmente implantações pessoais do Serviço de Aplicativo do Azure na sua VNET (Rede Virtual) e são gerenciáveis apenas pela sua assinatura. Eles oferecem novos recursos de rede porque estão na sua VNET e também podem ser dimensionados além do que normalmente está disponível nos ambientes do Serviço de Aplicativo do Azure. Se precisar de mais informações sobre o que é um ASE (Ambiente do Serviço de Aplicativo), consulte [O que é um Ambiente do Serviço de Aplicativo][WhatisASE]. Para obter detalhes sobre a criação de um Ambiente do Serviço de Aplicativo ou a criação de um aplicativo Web em um Ambiente do Serviço de Aplicativo, consulte [How to Create an App Service Environment (Como criar um Ambiente do Serviço de Aplicativo)][HowtoCreateASE] e [How to create a web app in an App Service Environment (Como criar um aplicativo Web em um Ambiente do Serviço de Aplicativo)][CreateWebappinASE]

Como pequeno lembrete, ao alterar normalmente um atributo de escala de um aplicativo Web, aplicativo móvel ou de API no Serviço de Aplicativo do Azure, você estará alterando-o no nível de um ASP (Plano do Serviço de Aplicativo). Para obter detalhes sobre o dimensionamento de Planos do Serviço de Aplicativo ou apenas para obter detalhes sobre os Planos do Serviço de Aplicativo fora dos Ambientes de Serviço de Aplicativo, consulte [Dimensionar um aplicativo Web no Serviço de Aplicativo do Azure][ScaleWebapp] e [Visão geral detalhada dos Planos do Serviço de Aplicativo do Azure][Appserviceplans].

A escala de um aplicativo em um Ambiente do Serviço de Aplicativo é muito semelhante à escala normal de aplicativos. No Serviço de Aplicativo do Azure normalmente há três itens que você pode dimensionar:

- plano de preços
- tamanho do trabalhador (para instâncias dedicadas)
- número de instâncias.

Em um ASE não é necessário selecionar ou alterar o plano de preços. Em termos de recursos, ele já está no nível de recurso de preços Premium. Em um Ambiente do Serviço de Aplicativo também não há nenhum trabalhador compartilhado. Eles estão todos os trabalhadores dedicados.

Com relação aos tamanhos de trabalho, o administrador do ASE pode atribuir o tamanho do recurso de computação a ser usado para cada pool de trabalho. Isso significa que você pode ter o Pool de Trabalhadores 1 com recursos de computação P4 e o Pool de Trabalhadores 2 com recursos de computação P1, se desejado. Eles não precisam estar em ordem de tamanho. Para obter detalhes sobre os tamanhos e seus preços, consulte o documento aqui [Preços do Serviço de Aplicativo do Azure][AppServicePricing]. Isso deixa as opções de dimensionamento para aplicativos Web e Planos do Serviço de Aplicativo em um Ambiente do Serviço de Aplicativo sendo:

- seleção do pool de trabalhadores
- número de instâncias

A alteração de qualquer um dos itens é feita por meio da interface do usuário apropriada mostrada para os seus Planos do Serviço de Aplicativo hospedados no ASE. Você não pode escalar verticalmente seu ASP além do número de recursos de computação disponíveis no pool de trabalho em que seu ASP está localizado. Se precisar de mais, você deve providenciar para que o administrador do seu ASE adicione mais recursos de computação ao pool de trabalhadores no qual precisa deles. Para obter informações sobre a reconfiguração de seu ASE, leia as informações fornecidas aqui: [Como configurar um ambiente do Serviço de Aplicativo][HowtoConfigureASE]. Também convém aproveitar os recursos de autoescala do ASE para adicionar capacidade com base no agendamento ou em métricas. Para obter mais detalhes sobre como configurar a autoescala para o ambiente ASE propriamente dito, veja [Como configurar a autoescala para um Ambiente do Serviço de Aplicativo][ASEAutoscale].

![][1]

### Dimensionando o número de instâncias ###

Ao criar seu aplicativo Web em um Ambiente do Serviço de Aplicativo, você deve dimensioná-lo até pelo menos duas instâncias para fornecer tolerância a falhas.

Se seu ASE tiver capacidade suficiente, isso é muito simples. Acesse seu Plano do Serviço de Aplicativo que contém os sites que você deseja escalar verticalmente e selecione Escala. Isso abre a interface do usuário em que você pode definir manualmente a escala para o ASP ou configurar as regras de autoescala para ele. Para escalar manualmente seu aplicativo, basta definir ***Escalar por*** como ***uma contagem de instância inserida manualmente***. Daqui, arraste o controle deslizante para a quantidade desejada ou insira-a na caixa ao lado do controle deslizante.

![][2]

As regras de autoescala para um ASP em um ASE funcionam da mesma forma que em uma condição normal. É possível selecionar ***Percentual de CPU*** em ***Escalar por*** e criar regras de autoescala para o ASP com base no percentual de CPU ou criar regras mais complexas usando ***regras de agendamento e desempenho***. Para ver detalhes mais completos sobre como configurar a autoescala, use o guia [Escalar um aplicativo no Serviço de Aplicativo do Azure][AppScale].


### Seleção do Pool de Trabalhadores ###

Como observamos anteriormente, a seleção do pool de trabalho é acessada da interface do usuário do ASP. Abra a folha do ASP que deseja escalar e selecione o pool de trabalho. Você verá todos os pools de trabalhadores que configurou no seu Ambiente do Serviço de Aplicativo. Se você tiver somente um pool de trabalhadores, verá somente o pool listado. Para alterar a localização de pool de trabalho do ASP, basta selecionar o pool de trabalho para o qual deseja mover seu Plano do Serviço de Aplicativo.

![][3]

Antes de mover seu ASP de um pool de trabalho para outro, é importante se certificar de que você terá a capacidade adequada para o ASP. Na lista de pools de trabalhadores, não apenas o nome do pool de trabalhadores está listado, mas você também pode ver quantos trabalhadores estão disponíveis nesse pool de trabalhadores. Certifique-se de que há instâncias suficientes disponíveis para conter o seu Plano do Serviço de Aplicativo. Se precisar de mais recursos de computação no pool de trabalhadores para o qual deseja mover, providencie para que o administrador do seu ASE os adicione.

> [AZURE.NOTE]Mover um ASP de um pool de trabalho causará uma reinicialização dos aplicativos nesse ASP. Isso pode causar uma pequena quantidade de tempo de inatividade para seu aplicativo, dependendo de quanto tempo ele leva para ser reiniciado.

## Introdução

Para se familiarizar com os Ambientes de Serviços de Aplicativo, consulte [Como criar um Ambiente de Serviço de Aplicativo][HowtoCreateASE]

Para obter mais informações sobre a plataforma de Serviço de Aplicativo do Azure, consulte [Serviço de Aplicativo do Azure][AzureAppService].

<!--Image references-->
[1]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-aspblade.png
[2]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-manualscale.png
[3]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-sizescale.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[ScaleWebapp]: http://azure.microsoft.com/documentation/articles/web-sites-scale/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoConfigureASE]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[CreateWebappinASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-a-web-app-in-an-ase/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[ASEAutoscale]: http://azure.microsoft.com/documentation/articles/app-service-environment-auto-scale/
[AppScale]: http://azure.microsoft.com/documentation/articles/web-sites-scale/

<!---HONumber=Nov15_HO1-->