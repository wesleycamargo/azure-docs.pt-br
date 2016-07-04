<properties 
	pageTitle="Como Criar um Ambiente do Serviço de Aplicativo" 
	description="Descrição do fluxo de criação para ambientes do serviço de aplicativo" 
	services="app-service" 
	documentationCenter="" 
	authors="ccompy" 
	manager="stefsch" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/20/2016" 
	ms.author="ccompy"/>

# Como Criar um Ambiente do Serviço de Aplicativo #

Ambientes de Serviço de Aplicativo (ASE) são uma opção de serviço Premium do Serviço de Aplicativo do Azure que fornece um recurso de configuração avançada não disponível em carimbos com vários locatários. O recurso ASE essencialmente implanta o Serviço de Aplicativo do Azure na rede virtual de um cliente. Para obter uma maior compreensão dos recursos oferecidos pelos ambientes do serviço de aplicativo, leia a documentação [O que é um ambiente do serviço de aplicativo][WhatisASE].

### Visão geral ###

A criação do ASE requer que os clientes forneçam as seguintes informações:

- nome do ASE
- assinatura a ser usada para o ASE  
- grupo de recursos
- Seleção da Rede Virtual (VNET) do Azure junto com uma sub-rede
- Definição do pool de recursos do ASE

Há alguns detalhes importantes referentes a cada um desses itens.

- O nome do ASE será usado no subdomínio para quaisquer aplicativos feitos no ASE
- Todos os aplicativos feitos em um ASE estarão na mesma assinatura que o próprio ASE
- Se você não tiver acesso à assinatura usada para fazer o ASE, o ASE não poderá ser usado para criar aplicativos
- Redes Virtuais usadas para hospedar um ASE devem ser Redes Virtuais Regionais clássicas “v1” 
- **A sub-rede usada para hospedar o ASE não deve conter outros recursos de computação**
- Somente um ASE pode existir em uma sub-rede
- Com uma alteração recente feita em junho de 2016, os ASEs agora podem ser implantados em redes virtuais que usam *os* intervalos de endereço público *ou* espaços de endereço RFC1918 (ou seja, os endereços privados). Para usar uma rede virtual com um intervalo de endereços públicos, você precisará criar a sub-rede antecipadamente e selecioná-la na UX de criação do ASE.

Cada implantação de ASE é um serviço hospedado que o Azure gerencia e mantém. Os recursos de computação hospedando as funções de sistema ASE não podem ser acessadas pelo cliente, embora o cliente gerencie a quantidade de instâncias e seus tamanhos.

Há duas maneiras de acessar a interface do usuário de criação do ASE. Ela pode ser encontrada pesquisando no Azure Marketplace por ***ambiente de serviço de aplicativo*** ou acessando as opções de menu Novo -> Web + Móvel.

Se você quiser que a Rede Virtual tenha um grupo de recursos separado do ASE, precisará criar primeiro a Rede Virtual separadamente e então selecioná-la durante a criação do ASE. Além disso, se você quiser criar uma sub-rede em uma Rede Virtual existente durante a criação do ASE, o ASE deverá ficar no mesmo grupo de recursos que a Rede Virtual.

### Criação Rápida ###
A experiência de criação para um ASE não tem um conjunto de padrões para habilitar a experiência de criação rápida. Você pode criar rapidamente um ASE simplesmente inserindo um nome para a implantação. Por sua vez, isso criará um ASE na região mais próxima a você com:

- Rede virtual com 512 endereços usando um espaço de endereço privado RFC1918
- sub-rede com 256 endereços
- Pool de front-end com dois recursos de computação P2
- Pool de trabalho com dois recursos de computação P1
- endereço IP único a ser usado para SSL de IP

Os pools do Front End exigem P2 ou maior. Tenha cuidado ao selecionar a assinatura em que você deseja que ASE esteja. As únicas contas que podem usar o ASE para hospedar o conteúdo devem estar na assinatura usada para criá-lo.

![][1]

O nome especificado para o ASE será usado para os aplicativos Web criados no ASE. Se o nome do ASE for appsvcenvdemo, o nome de domínio será .*appsvcenvdemo.p.azurewebsites.net*. Se você tiver criado, portanto, um aplicativo Web chamado *meuaplicativodeteste*, ele seria endereçável em *meuaplicativodeteste.appsvcenvdemo.p.azurewebsites.net*. Você não pode usar espaços em branco no nome do ASE. Se você usar letras maiúsculas entre os caracteres do nome, o nome de domínio será a versão total em letras minúsculas desse nome.

Ter os padrões será muito útil para um determinado número de situações, mas muitas vezes você precisará ajustar algo. As próximas seções percorrerão cada uma das seções de configuração relacionadas ao ASE.

### Rede Virtual ###
Embora haja uma opção de criação rápida que cria automaticamente uma nova VNET, o recurso também oferece suporte à seleção de uma VNET existente e à criação manual de uma VNET. Você poderá selecionar uma rede virtual existente (somente redes virtuais clássicas "v1" têm suporte neste momento) se ela for grande o suficiente para dar suporte à implantação de um Ambiente do Serviço de Aplicativo. A Rede Virtual deve ter oito endereços ou mais.

Com uma alteração recente feita em junho de 2016, os ASEs agora podem ser implantados em redes virtuais que usam *os* intervalos de endereço público *ou* espaços de endereço RFC1918 (ou seja, os endereços privados). Para usar uma rede virtual com um intervalo de endereços públicos, você precisará criar a sub-rede antecipadamente e selecioná-la na UX de criação do ASE.

Se você selecionar uma VNET pré-existente, você também terá que especificar uma sub-rede para usar ou então criar uma nova. A sub-rede precisa ter oito endereços ou mais e não pode ter qualquer outro recurso. A criação do ASE falhará se você tentar usar uma sub-rede que já tenha VMs alocadas para ele.

Se utilizar a interface de usuário de criação da VNET, você precisará fornecer:

- Nome da VNET
- Intervalo de endereços VNET na notação CIDR
- Local padrão

O local da Rede Virtual é o local do ASE porque o ASE é implantado para esta Rede Virtual.

Depois de criar sua Rede Virtual especificada ou selecionada, você precisará criar ou selecionar uma sub-rede como apropriado. Os detalhes que devem ser fornecidos aqui são os seguintes:
- Nome da sub-rede
- Intervalo de sub-rede em notação CIDR

Se você não estiver familiarizado com a notação CIDR (Classless Inter-Domain Routing), ela terá a forma de um Endereço IP, que é a barra invertida separada do valor CIDR. Ela se parece com *10.0.0.0/22*. O valor da CIDR indica o número de bits iniciais mascarados em comparação ao endereço IP mostrado. Uma maneira mais fácil de expressar o conceito aqui é que os valores CIDR fornecem um intervalo de IPs. Neste exemplo, um 10.0.0.0/22 significa um intervalo de 1024 endereços ou de 10.0.0.0 a 10.0.3.255. Um “/23” significa 512 endereços, e assim por diante.

Só um lembrete, se você quiser criar uma sub-rede em uma Rede Virtual existente, o ASE estará no mesmo grupo de recursos que a Rede Virtual. Para manter o ASE em um grupo de recursos separado da sua Rede Virtual simplesmente separe sua Rede Virtual e sua sub-rede antes da criação do ASE.

![][2]


### Pools de recursos de computação ###

Durante a criação do ASE, você pode definir o número de recursos de cada pool de recursos junto com seu tamanho. Embora você possa definir os tamanhos dos seus pools de recursos na criação do ASE, também poderá ajustá-los depois com opções manuais de dimensionamento ou opções de dimensionamento automático.

Como observado anteriormente, o ASE consiste em servidores e trabalhadores de Front-end. Os servidores Front-End lidam com a carga de conexão do aplicativo e os processadores executam o código do aplicativo. Os servidores Front-End são gerenciados em um pool de recursos de computação dedicado. Os processadores, por sua vez, são gerenciados em 3 pools de recursos de computação separado chamados

- Pool de trabalho 1
- Pool de trabalho 2
- Pool de trabalho 3

Se você tiver um grande número de solicitações para aplicativos Web simples, provavelmente aumentará seu número de Front-Ends e utilizará menos processadores. Se você tiver aplicativos de Web que façam uso intenso de CPU ou memória com tráfego leve, então você não precisa de muitos Front-Ends, mas provavelmente terá maiores ou mais processadores.

![][3]

Independentemente do tamanho dos recursos de computação, os requisitos mínimos são 2 servidores Front-End e 2 processadores. Um ASE pode ser configurado para usar até um total de 55 recursos de computação. Desses 55 recursos de computação, apenas 50 podem ser usados para cargas de trabalho do host. Há duas razões para isso. Há um mínimo de 2 recursos de computação Front-End. Com isso, restam até 53 para dar suporte a alocação do pool de trabalho. Para oferecer tolerância a falhas, você precisa ter um recurso de computação adicional alocado de acordo com as seguintes regras:

- cada pool de trabalho precisa de pelo menos um recurso de computação adicional ao qual nenhuma carga de trabalho pode ser atribuída
- quando a quantidade de recursos de computação em um pool ultrapassa um determinado valor, outro recurso de computação torna-se necessário

Dentro de qualquer pool de trabalho individual, os requisitos de tolerância a falhas são aqueles para um determinado valor de X recursos atribuídos a um pool de trabalho:

- se X estiver entre 2 a 20, a quantidade de recursos de computação utilizáveis que você pode usar para cargas de trabalho é X-1
- se X estiver entre 21 a 40, a quantidade de recursos de computação utilizáveis que você pode usar para cargas de trabalho é X-2
- se X estiver entre 41 a 53, a quantidade de recursos de computação utilizáveis que você pode usar para cargas de trabalho é X-3

Além de ser capaz de gerenciar a quantidade de recursos de computação que você pode atribuir a um determinado pool, você também tem controle sobre o tamanho. Com ambientes de serviços de aplicativo, você pode escolher entre 4 tamanhos diferentes rotulados de P1 a P4. Para obter detalhes sobre esses tamanhos e seus preços, consulte aqui o documento [Preços do Serviço de Aplicativo][AppServicePricing]. Os tamanhos P1 a P3 de recursos de computação são os mesmos que aqueles disponíveis em ambientes multilocatário. O recurso de computação P4 proporciona 8 núcleos com 14 GB de RAM e só está disponível em um ambiente do serviço de aplicativo.

Os preços para ambientes do serviço de aplicativo dependem dos recursos de computação atribuídos. Você paga pelos recursos de computação alocados para seu ambiente do serviço de aplicativo independentemente de eles estarem hospedando cargas de trabalho ou não.

Por padrão, um ASE vem com 1 endereço IP que está disponível para o SSL de IP. Se você souber que precisará de mais, você pode especificar isso aqui ou gerenciá-lo após a criação.
  
### Após a criação de um Ambiente do Serviço de Aplicativo ###

Após a criação do ASE é possível ajustar:

- Quantidade de Front-Ends (mínimo: 2)
- Quantidade de processadores (mínimo: 2)
- Quantidade de endereços IP disponíveis para SSL de IP
- Tamanho de recursos de computação usados pelos Front-Ends ou Processadores (o tamanho mínimo de Front-End é P2)

Você não pode alterar:

- Local
- Assinatura
- Grupo de recursos
- VNET usada
- Sub-rede usada

Há mais detalhes sobre o gerenciamento do dimensionamento manual e monitoramento de Ambientes de Serviço de Aplicativo aqui: [Como configurar um Ambiente de Serviço de Aplicativo][ASEConfig]

Para saber mais sobre o dimensionamento automático, há um guia aqui: [Como configurar o dimensionamento automático para um Ambiente de Serviço de Aplicativo][ASEAutoscale]

Há dependências adicionais que não estão disponíveis para personalização, como o banco de dados e o armazenamento. Esses são gerenciados pelo Azure e fornecidos com o sistema. O armazenamento do sistema dá suporte a até 500 GB para todo o Ambiente de Serviço do Aplicativo e o banco de dados é ajustado pelo Azure como necessário, por meio do dimensionamento do sistema.


## Introdução
Todos os artigos e instruções para os Ambientes do Serviço de Aplicativo estão disponíveis no [LEIAME para Ambientes do Serviço de Aplicativo](../app-service/app-service-app-service-environments-readme.md).

Para se familiarizar com os ambientes de serviço de aplicativo, consulte [Introdução ao ambiente de Serviço de Aplicativo][WhatisASE]

Para obter mais informações sobre a plataforma de Serviço de Aplicativo do Azure, consulte [Serviço de Aplicativo do Azure][AzureAppService].

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-basecreateblade.png
[2]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-vnetcreation.png
[3]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-resources.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[ASEConfig]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[ASEAutoscale]: http://azure.microsoft.com/documentation/articles/app-service-environment-auto-scale/

<!---HONumber=AcomDC_0622_2016-->