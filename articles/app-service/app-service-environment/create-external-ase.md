---
title: "Criar um Ambiente do Serviço de Aplicativo do Azure Externo"
description: "Explica como criar um Ambiente do Serviço de Aplicativo do Azure durante a criação de um aplicativo ou autônomo"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 94dd0222-b960-469c-85da-7fcb98654241
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: 3f1418b71a7327264e29e3f08ef97b254ee9930d
ms.contentlocale: pt-br
ms.lasthandoff: 06/26/2017

---
# <a name="create-an-external-app-service-environment"></a>Criar um Ambiente do Serviço de Aplicativo externo #

O ASE (Ambiente do Serviço de Aplicativo) é uma implantação do Serviço de Aplicativo do Azure em uma sub-rede da VNet (Rede Virtual) do Azure. Há duas maneiras de implantar um ASE:

- com um VIP em um endereço IP externo, geralmente chamado de _ASE Externo_.
- com o VIP em um endereço IP interno, geralmente chamado de _ASE ILB_ devido ao ponto de extremidade interno ser um ILB (Balanceador de Carga Interno).

Este artigo mostra como criar um ASE Externo. Para obter uma visão geral do ASE, comece com [Uma introdução ao Ambiente do Serviço de Aplicativo][Intro] Para obter detalhes sobre como criar um ASE ILB, consulte [Criar e usar um ASE ILB][MakeILBASE].

## <a name="before-you-create-your-ase"></a>Antes de criar seu ASE ##

É importante estar ciente dos itens que não podem ser alterados após a criação do ASE, que são:

- Local padrão
- Assinatura
- Grupo de recursos
- Rede Virtual utilizada
- Sub-rede usada
- Tamanho da sub-rede

> [!NOTE]
> Ao escolher uma Rede Virtual e especificar uma sub-rede, verifique se ela é grande o suficiente para acomodar qualquer crescimento futuro. O tamanho recomendado é um `/25` com 128 endereços.
>

## <a name="three-ways-to-create-an-ase"></a>Três maneiras de criar um ASE ##

Há três maneiras de criar um ASE. Você pode criar um ASE:

- Durante a criação de um plano do Serviço de Aplicativo, que cria o ASE e o plano do Serviço de Aplicativo em uma única etapa.
- Na interface do usuário de criação autônoma de ASE, que cria um ASE em branco. Essa é uma experiência mais avançada de interface do usuário de criação de ASE e é usada para criar um ASE com um ILB (Balanceador de Carga Interno).
- Com base em um modelo do Resource Manager. Isso se destina ao usuário Advanced e é abordado em [Criar um ASE com base em um modelo][MakeASEfromTemplate].

Um ASE criado sem um ILB tem um VIP público. Isso significa que todo o tráfego HTTP para os aplicativos no ASE passará por um endereço IP acessível pela Internet. Um ASE com um ILB tem um ponto de extremidade no endereço IP de uma Rede Virtual. Esses aplicativos não são expostos diretamente à Internet.

## <a name="create-an-ase-and-an-app-service-plan-together"></a>Criar um ASE e um plano do Serviço de Aplicativo simultaneamente ##

O plano do Serviço de Aplicativo é um contêiner de aplicativos. Ao criar um aplicativo no Serviço de Aplicativo, você sempre precisa escolher ou criar um plano do Serviço de Aplicativo. O modelo de contêiner é: os ambientes mantêm os planos do Serviço de Aplicativo e os planos do Serviço de Aplicativo mantêm os aplicativos.

Para criar um ASE durante a criação do plano do Serviço de Aplicativo:

1. No [portal do Azure](https://portal.azure.com/), clique em **Novo &gt; Web + Móvel &gt; Aplicativo Web**

    ![][1]
1. Selecione sua assinatura. Se você tiver várias assinaturas, lembre-se de que, para criar um aplicativo no ASE, precisará usar a mesma assinatura que usou ao criar o ASE.
1. Selecione ou crie um grupo de recursos. Os *grupos de recursos* permitem gerenciar recursos do Azure relacionados como uma unidade e são úteis durante o estabelecimento de regras de *controle de acesso baseado em função* (RBAC) para seus aplicativos. Para saber mais, confira [Visão geral do Azure Resource Manager][ARMOverview].
1. Clique no plano do Serviço de Aplicativo e, em seguida, selecione **Criar Novo**.

    ![][2]
1. Na lista suspensa **Localização**, escolha a região na qual você deseja que o ASE seja criado. Se você escolher um ASE existente, ele não permitirá a criação de um novo ASE; o plano do Serviço de Aplicativo simplesmente será criado no ASE selecionado.
1. Clique na interface do usuário de **Plano de Preços** e escolha um dos SKUs de preços **Isolados**. A escolha de um cartão de SKU **Isolado** e uma localização que não seja um ASE significa que você deseja criar um novo ASE nessa localização. Fazer isso revela a interface do usuário de criação de ASE depois de clicar em **Selecionar** na página do cartão de preços. O SKU **Isolado** só está disponível em conjunto com um ASE. Também não é possível usar nenhum outro SKU de preços em um ASE além de **Isolado**.

    ![][3]
1. Insira o nome do ASE. O nome do ASE é usado no nome endereçável dos aplicativos. Se o nome do ASE for _appsvcenvdemo_, o nome do subdomínio será *.appsvcenvdemo.p.azurewebsites.net*. Se, em seguida, você criar um aplicativo chamado *mytestapp*, ele será endereçável em *mytestapp.appsvcenvdemo.p.azurewebsites.net*. Você não pode usar espaços em branco no nome do ASE. Se você usar letras maiúsculas entre os caracteres do nome, o nome de domínio será a versão total em letras minúsculas desse nome.

    ![][4]
1. Escolha **Criar Nova** ou **Selecionar Existente**. A opção para selecionar uma Rede Virtual existente somente estará disponível se você tiver uma Rede Virtual na região selecionada. Se você selecionar **Criar Nova**, forneça um nome para a Rede Virtual e uma nova Rede Virtual do Resource Manager com esse nome será criada com o espaço de endereço `192.168.250.0/23` na região selecionada. Se você escolher **Selecionar Existente**, precisará:
    1. Selecionar o bloco de endereço da Rede Virtual, caso tenha mais de um.
    2. Fornecer um novo nome de sub-rede.
    3. Selecionar o tamanho da sub-rede. **Lembrete: isso deve ser um número grande o suficiente para acomodar qualquer crescimento futuro do ASE.** O tamanho recomendado é um `/25`, que tem 128 endereços e pode manipular um ASE com tamanho máximo. `/28` não é recomendável, por exemplo, porque somente 16 endereços estão disponíveis. As necessidades de infraestrutura consumirão, pelo menos, 5 endereços, deixando você com apenas um dimensionamento máximo de 11 instâncias em uma sub-rede `/28`.
    4. Selecione o intervalo de IP da sub-rede.

O processo de criação de ASE será iniciado depois que você selecionar **Criar**. Isso também criará o plano do Serviço de Aplicativo e o aplicativo. O ASE, o plano do Serviço de Aplicativo e o aplicativo estarão na mesma assinatura e também no mesmo grupo de recursos. Se precisar que o ASE esteja em um grupo de recursos separado do plano do Serviço de Aplicativo e do aplicativo ou se precisar de um ASE ILB, use a experiência de criação autônoma de ASE.

## <a name="create-an-ase-by-itself"></a>Criar um ASE sozinho ##

Percorrer o fluxo de criação autônoma de ASE criará um ASE em branco. Um ASE vazio ainda incorrerá em um encargo mensal pela infraestrutura. Os principais motivos para percorrer esse fluxo de trabalho é criar um ASE com um ILB ou criar um ASE em seu próprio grupo de recursos. Depois de criar o ASE, você poderá criar aplicativos no ASE usando as experiências de criação normal de aplicativo e selecionando o novo ASE como a localização.

Acesse a interface do usuário de criação do ASE pesquisando ***Ambiente do Serviço de Aplicativo*** no Azure Marketplace ou acessando Novo – &gt; Web Móvel –&gt; Ambiente do Serviço de Aplicativo. Para criar um ASE usando a experiência de criação autônoma:

1. Forneça o nome do seu ASE. O nome especificado para o ASE será usado para os aplicativos Web criados no ASE. Se o nome do ASE for *mynewdemoase*, o nome do subdomínio será *.mynewdemoase.p.azurewebsites.net*. Se, em seguida, você criar um aplicativo chamado *mytestapp*, ele será endereçável em *mytestapp.mynewdemoase.p.azurewebsites.net*. Você não pode usar espaços em branco no nome do ASE. Se você usar caracteres maiúsculos no nome, o nome de domínio será a versão toda em minúsculas desse nome. Se você usar um ILB, o nome do ASE não será usado no subdomínio, mas sim declarado explicitamente durante a criação do ASE.

    ![][5]
1.  Selecione sua assinatura. A assinatura usada para seu ASE também é aquela com a qual serão criados todos os aplicativos no ASE. Não é possível colocar o ASE em uma Rede Virtual que está em outra assinatura.
1.  Selecione ou especifique um novo grupo de recursos. O grupo de recursos usado para o ASE deve ser o mesmo usado para a Rede Virtual. Se você selecionar uma Rede Virtual existente, a seleção de grupo de recursos para o ASE será atualizada para refletir a da Rede Virtual.

    ![][6]
1. Faça suas seleções de Rede Virtual e Local. Você pode optar por criar uma nova Rede Virtual ou selecionar uma Rede Virtual existente. Se você selecionar uma nova Rede Virtual, poderá especificar um nome e uma localização. A nova Rede Virtual terá o intervalo de endereços 192.168.250.0/23 e uma sub-rede chamada **padrão** que é definida como 192.168.250.0/24. É possível selecionar apenas uma Rede Virtual do Resource Manager. A seleção de **Tipo de VIP** determina se o ASE pode ser acessado diretamente pela Internet (Externo) ou se ele usa um ILB (Balanceador de Carga Interno). Para saber mais sobre eles, leia [Criar e usar um Balanceador de Carga Interno com um Ambiente do Serviço de Aplicativo][MakeILBASE]. Se você selecionar um tipo de VIP **Externo**, poderá selecionar com quantos endereços IP externos o sistema será criado para fins do SSL baseado em IP. Se você selecionar **Interno**, precisará especificar o subdomínio que o ASE usará. Os ASEs podem ser implantados em redes virtuais que usam os intervalos de endereço público *ou* espaços de endereço *RFC1918* (ou seja, endereços privados). Para usar uma rede virtual com um intervalo de endereços públicos, você precisará criar a Rede Virtual antecipadamente. Ao selecionar uma Rede Virtual existente, você precisará criar uma nova sub-rede durante a criação do ASE. **Você não pode usar uma sub-rede criada previamente no portal. Você poderá criar um ASE com uma sub-rede existente se criar o ASE usando um modelo do Resource Manager.** Para criar um ASE com base em um modelo, consulte [Criar um Ambiente do Serviço de Aplicativo com base em um modelo][MakeASEfromTemplate]

## <a name="app-service-environment-v1"></a>Ambiente do Serviço de Aplicativo v1 ##

Ainda é possível criar instâncias da primeira versão do recurso ASE (ASEv1). Para acessar essa experiência, pesquise **Ambiente do Serviço de Aplicativo v1** no marketplace. A experiência de criação é a mesma que a experiência de criação autônoma de ASE. Quando concluído, o ASEv1 será criado com dois Front-Ends e duas Funções de Trabalho. Com o ASEv1, você precisa gerenciar os Front-Ends e as Funções de Trabalho. Eles não são adicionados automaticamente durante a criação dos planos do Serviço de Aplicativo. Os Front-Ends atuam como os pontos de extremidade HTTP/HTTPS e enviam o tráfego para as Funções de Trabalho, que são as funções que hospedam os aplicativos. Você pode ajustar a quantidade após a criação do ASE. Para saber mais sobre o ASEv1, consulte [Introdução ao Ambiente do Serviço de Aplicativo v1][ASEv1Intro]. Para obter mais detalhes sobre dimensionamento, gerenciamento e monitoramento do ASEv1, consulte [Como configurar um Ambiente do Serviço de Aplicativo][ConfigureASEv1].

<!--Image references-->
[1]: ./media/how_to_create_an_external_app_service_environment/createexternalase-create.png
[2]: ./media/how_to_create_an_external_app_service_environment/createexternalase-aspcreate.png
[3]: ./media/how_to_create_an_external_app_service_environment/createexternalase-pricing.png
[4]: ./media/how_to_create_an_external_app_service_environment/createexternalase-embeddedcreate.png
[5]: ./media/how_to_create_an_external_app_service_environment/createexternalase-standalonecreate.png
[6]: ./media/how_to_create_an_external_app_service_environment/createexternalase-network.png



<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[ASEReadme]: ./readme.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/virtual-networks-nsg.md
[ConfigureASEv1]: ../../app-service-web/app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: ../../app-service-web/app-service-app-service-environment-intro.md
[webapps]: ../../app-service-web/app-service-web-overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[APIapps]: ../../app-service-api/app-service-api-apps-why-best-platform.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md

