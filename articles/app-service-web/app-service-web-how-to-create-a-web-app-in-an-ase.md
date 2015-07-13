<properties
	pageTitle="Como criar um aplicativo Web em um Ambiente de Serviço de Aplicativo"
	description="Fluxo de criação de aplicativos Web e planos de serviço de aplicativo examinado para um ambiente de serviço de aplicativo"
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
	ms.date="04/27/2015"
	ms.author="ccompy"/>

# Como criar um aplicativo Web em um Ambiente de Serviço de Aplicativo #

O procedimento para criar aplicativos Web em um ASE (ambiente de serviço de aplicativo) é praticamente o mesmo que o usado normalmente. Se você não estiver familiarizado com a capacidade do Ambiente de Serviço de Aplicativo, leia o documento aqui [O que é um ambiente de serviço de aplicativo](app-service-app-service-environment-intro.md).

Para criar um aplicativo Web em um ASE, você precisa primeiro ter um ASE. Para obter informações sobre como criar um ASE, leia aqui o documento [Como criar um ambiente de Serviço de Aplicativo](app-service-web-how-to-create-an-app-service-environment.md).

A primeira etapa para criar um aplicativo Web é criar ou selecionar um ASP (Plano de Serviço de Aplicativo). Criar um ASP em um ASE começa do mesmo modo que ao criá-lo normalmente, ou seja, seguindo o fluxo de criação de aplicativos Web, começando com Novo -> Web + Móvel -> Aplicativo Web.

![][1]


Se você estiver usando um plano de serviço de aplicativo que você já criou em seu ambiente de serviço de aplicativo, selecione esse plano, digite o nome do seu aplicativo Web e selecione Criar. É o mesmo fluxo pelo qual você passa quando cria um aplicativo Web normalmente. A principal diferença é que seu aplicativo Web será acessado em:

[*nomedosite*].[*nome do seu ambiente de Serviço de Aplicativo*].p.azurewebsites.net

em vez de

[*nomedosite*]. azurewebsites.net

Por enquanto, o nome de seu aplicativo Web precisa ser exclusivo em todo o Serviço de Aplicativo do Azure. Isso significa que, se você quiser criar um aplicativo Web chamado "meuaplicativoweb", então atualmente não pode existir nenhum outro aplicativo Web chamado "meuaplicativoweb" no serviço de aplicativo do Azure.

### Planos de serviço de aplicativo ###

Planos de serviço de aplicativo são um conjunto gerenciado de seus aplicativos Web. Quando você seleciona os preços, o preço cobrado é aplicado a um plano de serviço de aplicativo em vez de aplicado aos aplicativos individuais. Para aumentar o número de instâncias de um aplicativo Web, você aumenta as instâncias do seu ASP e isso afeta todos os aplicativos Web nesse plano. Alguns recursos, como slots de site ou integração de VNET, também têm restrições de quantidade dentro do plano. Você pode aprender mais sobre planos de serviço de aplicativo neste documento: [Planos de serviço de aplicativo do Azure em detalhes](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)

Se você estiver criando um novo plano de serviço de aplicativo, existem algumas diferenças em relação à criação de um ASP em um ambiente de serviço de aplicativo. Entre outras coisas, suas escolhas de processador são diferentes, pois não há processadores compartilhados em um ambiente de serviço do aplicativo. Os processadores que você precisa usar são aqueles que foram alocados para o ambiente de serviço de aplicativo pelo administrador. Isso significa que, para criar um novo ASP, você precisa ter mais processadores alocados para o seu ambiente de serviço de aplicativo que o número total de instâncias em todos os seus ASPs em seu ambiente de serviço do aplicativo. Se você não tem processadores suficientes em seu ambiente de serviço de aplicativo para criar o ASP, você precisa trabalhar com o administrador de seu ambiente de serviço de aplicativo para que eles sejam adicionados.

Outra diferença em relação a ASPs hospedados em ambientes de serviço de aplicativo é a ausência de seleção de preços. Quando você tem um ambiente de serviço de aplicativo, você está pagando por recursos de computação usados pelo sistema e não tem encargos adicionais para os ASPs nesse ambiente. Normalmente, quando você cria um ASP, você seleciona um plano de preços que determina sua cobrança. Um ambiente de serviço de aplicativo é essencialmente um local privado onde você pode criar conteúdo. Você paga pelo ambiente e não para hospedar seu conteúdo.

### Selecionando seu Ambiente de Serviço de Aplicativo ###

Como um ambiente de aplicativo de serviço é essencialmente um local de implantação particular, você começa selecionando o ASE que deseja usar a partir de seu seletor de local.

![][2]

Após a seleção, a interface de usuário será atualizada e substituirá o seletor de plano de preços por um seletor de pool de trabalho. O local mostra o nome do sistema ASE e a região em que ele está. Na URL, o nome de domínio para o ASE substitui o .azurewebsites.net normalmente presente pelo nome do ambiente de serviço de aplicativo.

![][3]

### Selecionando o pool de trabalho ###

Normalmente no serviço de aplicativo do Azure e fora de um ambiente de serviço de aplicativo, há 3 tamanhos disponíveis com a seleção de um plano de preços dedicado. De maneira semelhante, os clientes que possuem um ASE podem definir até 3 pools de processadores e especificar o tamanho da VM que é usada para esse pool de trabalho. Em vez de selecionar um plano de preços para o ASP, você seleciona o que chamamos de um pool de trabalho.

A interface de usuário do pool de trabalho mostra o tamanho das máquinas virtuais usadas para esse pool de trabalho, abaixo do nome. A quantidade disponível se refere a quantas VMs estão disponíveis para uso nesse pool. O pool pode na realidade ter mais VMs que esse número, mas esse valor se refere simplesmente a quantas não estão em uso. Se você precisar ajustar o seu ambiente de serviço de aplicativo para adicionar mais recursos de computação, consulte aqui o documento [Configurando seu ambiente de serviço de aplicativo](app-service-web-configure-an-app-service-environment.md).

![][4]

Neste exemplo, você pode ver apenas dois pools de trabalho disponíveis. Isso ocorre porque o administrador do ASE alocou VMs apenas nesses dois pools de trabalho. O terceiro aparecerá quando houver VMs alocadas para ele.

### Após a criação do aplicativo Web ###

Há algumas considerações para executar aplicativos Web e gerenciar ASPs em um ASE que precisam ser levadas em conta.

Conforme observado anteriormente, o proprietário do ASE é responsável pelo tamanho do sistema e, como resultado, responsável também por garantir que haja capacidade suficiente para hospedar os ASPs desejados. Se não houver nenhum processador disponível, você não poderá criar o ASP. Isso também é verdadeiro ao dimensionar seu aplicativo Web. Se você precisar de mais instâncias, será necessário que o administrador de seu ambiente de serviço de aplicativo adicione mais processadores.

Depois de criar seu aplicativo Web e ASP, é uma boa ideia dimensioná-lo, aumentando-o. Em um ASE, é sempre necessário ter pelo menos 2 instâncias do seu ASP para proporcionar tolerância a falhas para seus aplicativos. Dimensionar um ASP em um ASE é o igual ao método normal, por meio da interface de usuário do ASP. Para obter mais detalhes sobre dimensionamento, leia aqui o documento [Como dimensionar um aplicativo Web em um ambiente de Serviço de Aplicativo](app-service-web-scale-a-web-app-in-an-app-service-environment.md)

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspnewwebapp.png
[2]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createasplocation.png
[3]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspselected.png
[4]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspworkerpool.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoScale]: http://azure.microsoft.com/documentation/articles/app-service-web-scale-a-web-app-in-an-app-service-environment
[HowtoConfigureASE]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment
 

<!---HONumber=62-->