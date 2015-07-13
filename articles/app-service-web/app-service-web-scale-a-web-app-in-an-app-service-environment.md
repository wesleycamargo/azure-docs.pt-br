<properties 
	pageTitle="Como dimensionar um aplicativo Web em um Ambiente de Serviço de Aplicativo" 
	description="Dimensionando um aplicativo Web em um Ambiente de Serviço de Aplicativo" 
	services="app-service\web" 
	documentationCenter="" 
	authors="ccompy" 
	manager="stefsch" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="ccompy"/>

# Dimensionando aplicativos Web em um Ambiente de Serviço de Aplicativo #

Em um alto nível, os Ambientes de Serviço de Aplicativo são essencialmente implantações pessoais do Serviço de Aplicativo do Azure na sua VNET (Rede Virtual) e são gerenciáveis apenas pela sua assinatura. Eles oferecem novos recursos de rede porque estão na sua VNET e também podem ser dimensionados além do que normalmente está disponível nos ambientes de Serviço de Aplicativo do Azure. Se precisar de mais informações sobre o que é um ASE (Ambiente de Serviço de Aplicativo), consulte [O que é um Ambiente de Serviço de Aplicativo][WhatisASE]. Para obter detalhes sobre a criação de um Ambiente de Serviço de Aplicativo ou a criação de um aplicativo Web em um Ambiente de Serviço de Aplicativo, consulte [How to Create an App Service Environment (Como criar um Ambiente de Serviço de Aplicativo)][HowtoCreateASE] e [How to create a web app in an App Service Environment (Como criar um aplicativo Web em um Ambiente de Serviço de Aplicativo)][CreateWebappinASE]

Como um lembrete rápido, ao alterar um atributo de escala para um aplicativo Web normalmente, você o está alterando no nível de um Plano de Serviço de Aplicativo. Para obter detalhes sobre o dimensionamento de Planos do Serviço de Aplicativo ou apenas para obter detalhes sobre os Planos do Serviço de Aplicativo fora dos Ambientes de Serviço de Aplicativo, consulte [Dimensionar um aplicativo Web no Serviço de Aplicativo do Azure][ScaleWebapp] e [Visão geral detalhada dos Planos do Serviço de Aplicativo do Azure][Appserviceplans].

O dimensionamento de um aplicativo Web em um Ambiente de Serviço de Aplicativo é muito semelhante ao dimensionamento de aplicativos Web de maneira normal. No Serviço de Aplicativo do Azure normalmente há três itens que você pode dimensionar:

- plano de preços
- tamanho do trabalhador (para instâncias dedicadas)
- número de instâncias.

Em um ASE não é necessário selecionar ou alterar o plano de preços. Em termos de recursos, ele já está no nível de recurso de preços Premium. Em um Ambiente de Serviço de Aplicativo também não há nenhum trabalhador compartilhado. Eles estão todos os trabalhadores dedicados. Em vez de tamanhos fixos, o administrador do ASE pode atribuir o tamanho do recurso de computação a ser usado para cada pool de trabalhadores. Isso significa que você pode ter o Pool de Trabalhadores 1 com recursos de computação P4 e o Pool de Trabalhadores 2 com recursos de computação P1, se desejado. Eles não precisam estar em ordem de tamanho. Para obter detalhes sobre os tamanhos e seus preços, consulte o documento aqui [Preços do Serviço de Aplicativo do Azure][AppServicePricing]. Isso deixa as opções de dimensionamento para aplicativos Web e Planos do Serviço de Aplicativo em um Ambiente de Serviço de Aplicativo sendo:

- seleção do pool de trabalhadores
- número de instâncias

A alteração de qualquer um dos itens é feita por meio da interface do usuário apropriada mostrada com seu Plano do Serviço de Aplicativo.

![][1]

### Dimensionando o número de instâncias ###

Ao criar seu aplicativo Web em um Ambiente de Serviço de Aplicativo, você deve dimensioná-lo até pelo menos duas instâncias para fornecer tolerância a falhas.

Se seu ASE tiver capacidade suficiente, isso é muito simples. Acesse seu Plano do Serviço de Aplicativo que contém os sites que você deseja escalar verticalmente e selecione Escala. Isso abre a interface do usuário na qual você simplesmente desliza o indicador de instâncias até o valor desejado e salva.

![][2]

Você não pode escalar verticalmente seu Plano do Serviço de Aplicativo além do número de recursos de computação disponíveis no pool de trabalhadores no qual seu Plano do Serviço de Aplicativo está. Se precisar de mais, você deve providenciar para que o administrador do seu ASE adicione mais recursos de computação ao pool de trabalhadores no qual precisa deles. Para obter informações sobre a reconfiguração de seu ASE, leia as informações fornecidas aqui: [How to Configure an App Service environment (Como configurar um ambiente de Serviço de Aplicativo)][HowtoConfigureASE]
 

### Seleção do Pool de Trabalhadores ###

A seleção do pool de trabalhadores é acessada pela interface do usuário do Plano de Serviço de Aplicativo. Abra o Plano do Serviço de Aplicativo que você deseja dimensionar e selecione o pool de trabalhadores. Você verá todos os pools de trabalhadores que configurou no seu Ambiente de Serviço de Aplicativo. Se você tiver somente um pool de trabalhadores, verá somente o pool listado. Para alterar em qual pool de trabalhadores seu Plano do Serviço de Aplicativo está, basta selecionar o pool de trabalhadores para o qual deseja mover seu Plano do Serviço de Aplicativo.

![][3]

Antes de fazer isso, é importante garantir que você terá a capacidade adequada para seu Plano do Serviço do Aplicativo. Na lista de pools de trabalhadores, não apenas o nome do pool de trabalhadores está listado, mas você também pode ver quantos trabalhadores estão disponíveis nesse pool de trabalhadores. Certifique-se de que há instâncias suficientes disponíveis para conter o seu Plano do Serviço de Aplicativo. Se precisar de mais recursos de computação no pool de trabalhadores para o qual deseja mover, providencie para que o administrador do seu ASE os adicione.

Mover um aplicativo Web de um pool de trabalhadores causará a reinicialização de seus aplicativos Web. Isso pode causar uma pequena quantidade de tempo de inatividade para seu aplicativo dependendo de quanto tempo leva para reiniciar.

## Introdução

Para se familiarizar com os Ambientes de Serviços de Aplicativo, consulte [Como criar um Ambiente de Serviço de Aplicativo][HowtoCreateASE]

Para obter mais informações sobre a plataforma de Serviço de Aplicativo do Azure, consulte [Serviço de Aplicativo do Azure][AzureAppService].

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/scaleasp.png
[2]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/scaleinstances.png
[3]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/scalepool.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[ScaleWebapp]: http://azure.microsoft.com/documentation/articles/web-sites-scale/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoConfigureASE]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[CreateWebappinASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-a-web-app-in-an-ase/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
 

<!---HONumber=62-->