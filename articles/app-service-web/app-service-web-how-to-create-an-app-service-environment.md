<properties 
	pageTitle="Como Criar um Ambiente do Serviço de Aplicativo" 
	description="Descrição do fluxo de criação para ambientes do serviço de aplicativo" 
	services="app-service\web" 
	documentationCenter="" 
	authors="ccompy" 
	manager="stefsch" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="09/11/2015" 
	ms.author="ccompy"/>

# Como Criar um Ambiente do Serviço de Aplicativo #

Ambientes de Serviço de Aplicativo (ASE) são uma opção de serviço Premium do Serviço de Aplicativo do Azure que fornece um recurso de configuração avançada não disponível em carimbos com vários locatários. Para obter uma maior compreensão dos recursos oferecidos pelos ambientes do serviço de aplicativo, leia a documentação [O que é um ambiente do serviço de aplicativo][WhatisASE].

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

### Visão geral ###

O recurso ASE essencialmente implanta o serviço de aplicativo do Azure na VNET de um cliente. Para fazer isso, o cliente precisa de:

- É necessária uma rede virtual regional clássica "v1" com 512 (/23) ou mais endereços
- Nessa rede virtual, será necessária uma sub-rede com 8 (/29) ou mais endereços
- A sub-rede **não deve conter nenhum outro recurso de computação**. Somente um Ambiente do Serviço de Aplicativo pode ser implantado em uma sub-rede. A tentativa de criação falhará se houver outros recursos de computação que já residam na sub-rede.

Se você ainda não tiver uma VNET que deseja usar para hospedar seu ambiente do serviço de aplicativo, você pode criar uma durante a criação do ambiente do serviço de aplicativo.

Cada implantação de ASE é um serviço hospedado que o Azure gerencia e mantém. Os recursos de computação hospedando as funções de sistema ASE não podem ser acessadas pelo cliente, embora o cliente gerencie a quantidade de instâncias e seus tamanhos.

## Criação de um Ambiente do Serviço de Aplicativo ##

Há duas maneiras de acessar a interface do usuário de criação do ASE. Ela pode ser encontrada pesquisando no Azure Marketplace por ***ambiente do serviço de aplicativo*** ou acessando as opções de menu Novo -> Web + Móvel.

### Criação Rápida ###
Depois de inserir a interface do usuário de criação, você pode criar rapidamente um ASE simplesmente digitando um nome para a implantação. Isso criará, por sua vez, uma VNET com 512 endereços, uma sub-rede com 256 endereços nessa VNET e um ambiente ASE com 2 Front-Ends e 2 processadores no pool de trabalho 1. Certifique-se de selecionar o local onde você deseja que o sistema esteja localizado e a assinatura em que você quer que ele esteja. As únicas contas que podem usar o ASE para hospedar o conteúdo devem estar na assinatura usada para criá-lo.

O nome especificado para o ASE será usado para aplicativos Web criados no ASE. Se o nome do ASE for appsvcenvdemo, o nome de domínio será .*appsvcenvdemo.p.azurewebsites.net*. Se você criou, portanto, um aplicativo Web chamado mytestapp, ele seria endereçável em *mytestapp.appsvcenvdemo.p.azurewebsites.net*. Você não pode usar espaços em branco no nome. Se você usar letras maiúsculas entre os caracteres do nome, o nome de domínio será a versão total em letras minúsculas desse nome.


![][1]

### Pools de recursos de computação ###

Os recursos de computação que são usados para o ambiente do serviço de aplicativo são gerenciados nos pools de recursos de computação, o que permite a configuração de como serão as instâncias dos recursos de computação que você tem no pool e também de seu tamanho. Um ambiente do serviço de aplicativo consiste em servidores Front-End e processadores. Os servidores Front-End lidam com a carga de conexão do aplicativo e os processadores executam o código do aplicativo. Os servidores Front-End são gerenciados em um pool de recursos de computação dedicado. Os processadores, por sua vez, são gerenciados em 3 pools de recursos de computação separado chamados

- Pool de trabalho 1
- Pool de trabalho 2
- Pool de trabalho 3

Se você tem um grande número de solicitações para aplicativos Web simples, você provavelmente aumentará seu número de Front-Ends e utilizará menos processadores. Se você tiver aplicativos de Web que façam uso intenso de CPU ou memória com tráfego leve, então você não precisa de muitos Front-Ends, mas provavelmente terá maiores ou mais processadores.

Independentemente do tamanho dos recursos de computação, os requisitos mínimos são 2 servidores Front-End e 2 processadores. Um ambiente do serviço de aplicativo pode ser configurado para usar até um total de 55 recursos de computação. Desses 55 recursos de computação, apenas 50 podem ser usados para cargas de trabalho do host. Há duas razões para isso. Há um mínimo de 2 recursos de computação Front-End. Com isso, restam até 53 para dar suporte a alocação do pool de trabalho. Para oferecer tolerância a falhas, no entanto, você precisa ter um recurso de computação adicional alocado de acordo com as seguintes regras:

- cada pool de trabalho precisa de pelo menos um recurso de computação adicional ao qual nenhuma carga de trabalho pode ser atribuída
- quando a quantidade de recursos de computação em um pool ultrapassa um determinado valor, outro recurso de computação torna-se necessário

Dentro de qualquer pool de trabalho individual, os requisitos de tolerância a falhas são aqueles para um determinado valor de X recursos atribuídos a um pool de trabalho:

- se X estiver entre 2 a 20, a quantidade de recursos de computação utilizáveis que você pode usar para cargas de trabalho é X-1
- se X estiver entre 21 a 40, a quantidade de recursos de computação utilizáveis que você pode usar para cargas de trabalho é X-2
- se X estiver entre 41 a 53, a quantidade de recursos de computação utilizáveis que você pode usar para cargas de trabalho é X-3

Além de ser capaz de gerenciar a quantidade de recursos de computação que você pode atribuir a um determinado pool, você também tem controle sobre o tamanho. Com ambientes de serviços de aplicativo, você pode escolher entre 4 tamanhos diferentes rotulados de P1 a P4. Para obter detalhes sobre esses tamanhos e seus preços, consulte aqui o documento [Preços do Serviço de Aplicativo][AppServicePricing]. Os tamanhos P1 a P3 de recursos de computação são os mesmos que aqueles disponíveis em ambientes multilocatário. O recurso de computação P4 proporciona 8 núcleos com 14 GB de RAM e só está disponível em um ambiente do serviço de aplicativo.

Os preços para ambientes do serviço de aplicativo dependem dos recursos de computação atribuídos. Você paga pelos recursos de computação alocados para seu ambiente do serviço de aplicativo independentemente de eles estarem hospedando cargas de trabalho ou não.



### Criação de VNET ###
Embora haja uma opção de criação rápida que cria automaticamente uma nova VNET, o recurso também oferece suporte à seleção de uma VNET existente e à criação manual de uma VNET. Você poderá selecionar uma rede virtual existente (somente redes virtuais clássicas "v1" têm suporte neste momento) se ela for grande o suficiente para dar suporte à implantação de um Ambiente do Serviço de Aplicativo. A VNET deve ter 512 endereços ou mais. Se você selecionar uma VNET pré-existente, você também terá que especificar uma sub-rede para usar ou então criar uma nova. A sub-rede deve ter 8 endereços ou mais.

Se utilizar a interface de usuário de criação da VNET, você precisará fornecer:

- Nome da VNET
- Intervalo de endereços VNET na notação CIDR
- Nome da sub-rede
- Intervalo de sub-rede em notação CIDR

Se você não está familiarizado com a notação CIDR, ela assume a forma de 10.0.0.0/22, onde “/22” especifica o intervalo. Neste exemplo, um “/22” significa um intervalo de 1024 endereços ou de 10.0.0.0 a 10.0.3.255. Um “/23” significa 512 endereços, e assim por diante.

![][2]

### Definição de tamanho de ambiente do serviço de aplicativo ###

O próximo item a configurar é a escala do sistema. Por padrão, há 2 recursos de computação P2 de Front-End, 2 processadores P1 e 1 endereço IP. Há 2 Front-Ends para proporcionar alta disponibilidade e distribuir a carga. O tamanho mínimo para os Front-Ends é P2, para garantir que eles tenham capacidade suficiente para dar suporte a um sistema modesto. Se você souber que o sistema precisa oferecer suporte a um grande número de solicitações, você poderá então ajustar a quantidade de Front-Ends e o tamanho do servidor usado.

Conforme observado anteriormente, em um ASE há 3 pools de trabalho que um cliente pode definir. O tamanho dos recursos de computação pode ser de P1 a P4. Por padrão existem somente 2 processadores P1 configurados no pool de trabalho 1. Isso é o suficiente para dar suporte a um plano do serviço de aplicativo único com 1 instância.

Os controles deslizantes se ajustam automaticamente para refletir a capacidade de computação total disponível no ambiente do serviço de aplicativo. Conforme os controles deslizantes são ajustados em qualquer um dos pools, os outros controles deslizantes mudam para refletir a quantidade de recursos de computação disponíveis restante antes de atingir 55.
 
![][3]

Ao adicionar novas instâncias, elas não ficam disponíveis rapidamente. Se você souber que vai precisar de recursos de computação adicionais, deverá provisioná-los com bastante antecedência. O tempo de provisionamento pode levar várias horas, dependendo da quantidade sendo adicionada ao sistema. Lembre-se que, para garantir que seu sistema atenda aos requisitos de tolerância a falhas, cada ASE precisa ter uma instância de reserva disponível em cada pool de trabalho.

Por padrão, um ASE vem com 1 endereço IP que está disponível para o SSL de IP. Se você souber que precisará de mais, você pode especificar isso aqui ou gerenciá-lo após a criação.
  
### Após a criação de um Ambiente do Serviço de Aplicativo ###

Após a criação do ASE é possível ajustar:

- Quantidade de Front-Ends (mínimo: 2)
- Quantidade de processadores (mínimo: 2)
- Quantidade de endereços IP
- Tamanho de recursos de computação usados pelos Front-Ends ou Processadores (o tamanho mínimo de Front-End é P2)

Você não pode alterar:

- Local
- Assinatura
- Grupo de recursos
- VNET usada
- Sub-rede usada

Há mais detalhes sobre o gerenciamento e monitoramento de ambientes do serviço de aplicativo aqui: [Como configurar um Ambiente do Serviço de Aplicativo][ASEConfig]

Há dependências adicionais que não estão disponíveis para personalização, como o banco de dados e o armazenamento. Esses são gerenciados pelo Azure e fornecidos com o sistema. O armazenamento do sistema oferece suporte a até 500 GB para todo o ambiente do serviço de aplicativo.


## Introdução

Para se familiarizar com os ambientes do serviço de aplicativo, consulte [Introdução ao ambiente do Serviço de Aplicativo][WhatisASE]

Para obter mais informações sobre a plataforma do Serviço de Aplicativo do Azure, consulte [Serviço de Aplicativo do Azure][AzureAppService].

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-an-app-service-environment/createaseblade.png
[2]: ./media/app-service-web-how-to-create-an-app-service-environment/createasenetwork.png
[3]: ./media/app-service-web-how-to-create-an-app-service-environment/createasescale.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[ASEConfig]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/

<!---HONumber=Oct15_HO2-->