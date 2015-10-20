<properties
	pageTitle="Como criar um aplicativo Web em um Ambiente do Serviço de Aplicativo"
	description="Fluxo de criação de aplicativos Web e planos do serviço de aplicativo examinado para um ambiente do serviço de aplicativo"
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
	ms.topic="get-started-article" 
	ms.date="09/15/2015"
	ms.author="ccompy"/>

# Como criar um aplicativo Web em um Ambiente do Serviço de Aplicativo #

O procedimento para criar aplicativos Web em um ASE (ambiente do serviço de aplicativo) é praticamente o mesmo que o usado normalmente. Se você não estiver familiarizado com a capacidade do Ambiente de Serviço de Aplicativo, leia o documento aqui [O que é um ambiente de serviço de aplicativo](app-service-app-service-environment-intro.md).

Para criar um aplicativo Web em um ASE, você precisa primeiro ter um ASE. Para obter informações sobre como criar um ASE, leia aqui o documento [Como criar um ambiente de Serviço de Aplicativo](app-service-web-how-to-create-an-app-service-environment.md).

A primeira etapa para criar um aplicativo Web é selecionar sua Assinatura. Se você tiver várias assinaturas, lembre-se de que, para criar um aplicativo no ASE, você precisa usar a mesma assinatura que usou ao criar o ASE. A próxima etapa envolve selecionar ou criar um grupo de recursos. Se você não estiver familiarizado com os grupos de recursos, há mais informações aqui: [Gerenciando os recursos do Azure][ResourceGroups]. Além de ajudar a gerenciar seus recursos, os grupos de recursos também são importantes ao estabelecer regras RBAC para seus aplicativos.

Depois de selecionar sua assinatura e o grupo de recursos, você precisa criar ou selecionar um ASP (Plano de Serviço de Aplicativo). Se precisar criar um novo ASP no ASE, você precisará fornecer um nome para o ASP, selecionar o ASE desejado em Locais e selecionar o Pool de Trabalho em que você deseja que o ASP esteja. Isso é descrito em mais detalhes abaixo. Se você selecionar um ASP em um ASE, o fluxo de criação de aplicativos será o mesmo que criar um aplicativo normalmente. Isso consiste em acessar o fluxo de criação de aplicativos Web começando com Novo -> Web + Móvel -> Aplicativo Web.

![][1]


Se você estiver usando um Plano do Serviço de Aplicativo que você já criou em seu Ambiente do Serviço de Aplicativo, basta selecionar esse plano, digitar o nome do seu aplicativo Web e selecionar Criar. É o mesmo fluxo pelo qual você passa quando cria um aplicativo Web normalmente. Você pode identificar ASPs no ASE examinando o local indicado abaixo do nome do ASP.

![][5]

Quando você criar um aplicativo, ele será acessado em:

[*nomedosite*].[*nome do seu ambiente de Serviço de Aplicativo*].p.azurewebsites.net

em vez de

[*nomedosite*]. azurewebsites.net

Por enquanto, o nome de seu aplicativo Web precisa ser exclusivo em todo o Serviço de Aplicativo do Azure. Isso significa que, se você quiser criar um aplicativo Web chamado "meuaplicativoweb", então atualmente não pode existir nenhum outro aplicativo Web chamado "meuaplicativoweb" no serviço de aplicativo do Azure.

### Planos do serviço de aplicativo ###

Planos do serviço de aplicativo são um conjunto gerenciado de seus aplicativos Web. Quando você seleciona os preços, o preço cobrado é aplicado a um plano do serviço de aplicativo em vez de aplicado aos aplicativos individuais. Para aumentar o número de instâncias de um aplicativo Web, você aumenta as instâncias do seu ASP e isso afeta todos os aplicativos Web nesse plano. Alguns recursos, como slots de site ou integração de VNET, também têm restrições de quantidade dentro do plano. Você pode aprender mais sobre planos de serviço de aplicativo neste documento: [Planos de serviço de aplicativo do Azure em detalhes](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)

Se você estiver criando um novo plano do serviço de aplicativo, existem algumas diferenças em relação à criação de um ASP em um ambiente do serviço de aplicativo. Entre outras coisas, suas escolhas de processador são diferentes, pois não há processadores compartilhados em um ASE. Os trabalhadores que você precisa usar são aqueles que foram alocados para o ASE pelo administrador. Isso significa que, para criar um novo ASP, você precisa ter mais trabalhadores alocados para o pool de trabalho ASE do que o número total de instâncias em todas as suas páginas ASP já nesse pool de trabalho. Se não tem trabalhadores suficientes em seu pool de trabalho ASE para criar o ASP, você precisa trabalhar com seu administrador do ASE para adicioná-los.

Outra diferença em relação a ASPs hospedados em ambientes do serviço de aplicativo é a ausência de seleção de preços. Quando você tem um ambiente do serviço de aplicativo, você está pagando por recursos de computação usados pelo sistema e não tem encargos adicionais para os ASPs nesse ambiente. Normalmente, quando você cria um ASP, você seleciona um plano de preços que determina sua cobrança. Um ambiente do serviço de aplicativo é essencialmente um local privado onde você pode criar conteúdo. Você paga pelo ambiente e não para hospedar seu conteúdo.

### Criando um ASP para seu Ambiente do Serviço de Aplicativo ###

Para criar um ASP em um ASE, comece selecionando Criar Novo na interface do usuário de seleção de ASP e forneça um nome para o ASP, assim como faria normalmente fora de um ASE. A próxima etapa é selecionar o ASE que você deseja usar no seletor de local. Como um Ambiente do Serviço de Aplicativo é, essencialmente, um local de implantação particular, ele é mostrado no Local.

![][2]

Após a seleção de um ASE no seletor de local, a interface do usuário de criação de ASP será atualizada. O local agora mostrará o nome do sistema ASE e a região em que ele está, e o seletor de plano de preços será substituído por um seletor de pool de trabalho.

![][3]

### Selecionando o pool de trabalho ###

Normalmente no Serviço de Aplicativo do Azure e fora de um Ambiente do Serviço de Aplicativo, há três tamanhos de computação disponíveis com a seleção de um plano de preços dedicado. De maneira semelhante, os clientes que possuem um ASE podem definir até 3 pools de trabalhadores e especificar o tamanho de computação que é usado para esse pool de trabalho. O que isso significa para o locatário é que, em vez de selecionar um plano de preços com tamanho de computação para o ASP, selecione o que chamamos de um Pool de Trabalho.

A interface do usuário de seleção de pool de trabalho mostra o tamanho de computação usado para esse pool de trabalho abaixo do nome. A quantidade disponível se refere a quantas instâncias de computação estão disponíveis para uso no pool. O pool pode, na realidade, ter mais instâncias que esse número, mas esse valor se refere simplesmente a quantas não estão em uso. Se você precisar ajustar o seu ambiente de serviço de aplicativo para adicionar mais recursos de computação, consulte aqui o documento [Configurando seu ambiente de serviço de aplicativo](app-service-web-configure-an-app-service-environment.md).

![][4]

Neste exemplo, você pode ver apenas dois pools de trabalho disponíveis. Isso ocorre porque o administrador do ASE alocou hosts apenas nesses dois pools de trabalho. O terceiro aparecerá quando houver VMs alocadas para ele.

### Após a criação do aplicativo Web ###

Há algumas considerações para executar aplicativos Web e gerenciar ASPs em um ASE que precisam ser levadas em conta.

Conforme observado anteriormente, o proprietário do ASE é responsável pelo tamanho do sistema e, como resultado, responsável também por garantir que haja capacidade suficiente para hospedar os ASPs desejados. Se não houver nenhum processador disponível, você não poderá criar o ASP. Isso também é verdadeiro ao dimensionar seu aplicativo Web. Se você precisar de mais instâncias, será necessário que o administrador de seu ambiente do serviço de aplicativo adicione mais processadores.

Depois de criar seu aplicativo Web e ASP, é uma boa ideia dimensioná-lo, aumentando-o. Em um ASE, é sempre necessário ter pelo menos 2 instâncias do seu ASP para proporcionar tolerância a falhas para seus aplicativos. Dimensionar um ASP em um ASE é o igual ao método normal, por meio da interface de usuário do ASP. Para obter mais detalhes sobre dimensionamento, leia aqui o documento [Como dimensionar um aplicativo Web em um ambiente de Serviço de Aplicativo](app-service-web-scale-a-web-app-in-an-app-service-environment.md)


[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspnewwebapp.png
[2]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createasplocation.png
[3]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspselected.png
[4]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspworkerpool.png
[5]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/selectaspinase.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoScale]: http://azure.microsoft.com/documentation/articles/app-service-web-scale-a-web-app-in-an-app-service-environment
[HowtoConfigureASE]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment
[ResourceGroups]: http://azure.microsoft.com/documentation/articles/resource-group-portal/
[AzurePowershell]: http://azure.microsoft.com/documentation/articles/powershell-install-configure/

<!---HONumber=Oct15_HO3-->