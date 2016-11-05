---
title: Como Criar um Ambiente do Serviço de Aplicativo
description: Descrição do fluxo de criação para ambientes do serviço de aplicativo
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
editor: ''

ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2016
ms.author: ccompy

---
# Como Criar um Ambiente do Serviço de Aplicativo
Ambientes de Serviço de Aplicativo (ASE) são uma opção de serviço Premium do Serviço de Aplicativo do Azure que fornece um recurso de configuração avançada não disponível em carimbos com vários locatários. O recurso ASE essencialmente implanta o Serviço de Aplicativo do Azure na rede virtual de um cliente. Para obter uma maior compreensão dos recursos oferecidos pelos ambientes do serviço de aplicativo, leia a documentação [O que é um ambiente do serviço de aplicativo][WhatisASE].

### Visão geral
Um ASE consiste nos recursos de computação de Front-End e de Trabalho. Os Front-Ends atuam como os pontos de extremidade HTTP/HTTPS e enviam tráfego para as Funções de Trabalho, que são funções que hospedam seus aplicativos.

A criação do ASE requer que os clientes forneçam as seguintes informações:

* nome do ASE
* assinatura a ser usada para o ASE
* grupo de recursos
* Uma VNet (Rede Virtual do Azure) com 8 ou mais endereços e uma sub-rede que será usada pelo ASE
* Tipo de VIP, interno ou externo
* Definição do pool de recursos do ASE

Há alguns detalhes importantes referentes a cada um desses itens.

* O nome do ASE será usado no subdomínio para quaisquer aplicativos feitos no ASE, se configurado com um VIP externo
* Um ASE Externo hospeda aplicativos acessíveis da Internet. Um ASE com um VIP Interno usa um ILB (Balanceador de Carga Interno)
* Todos os aplicativos feitos em um ASE estarão na mesma assinatura que o próprio ASE
* Se você não tiver acesso à assinatura usada para fazer o ASE, o ASE não poderá ser usado para criar aplicativos
* VNets usadas para hospedar um ASE devem ser VNets Regionais. Você pode usar VNets Clássicas ou do Resource Manager
* **A sub-rede usada para hospedar o ASE não deve conter outros recursos de computação**
* Somente um ASE pode existir em uma sub-rede
* Os ASEs agora podem ser implantados em redes virtuais que usam os intervalos de endereço público *ou* espaços de endereço RFC1918 (ou seja, endereços privados). Para usar uma rede virtual com um intervalo de endereços públicos, você precisará criar a VNet e a sub-rede antecipadamente, e selecioná-las na UX de criação do ASE.

Cada implantação de ASE é um serviço hospedado que o Azure gerencia e mantém. Os recursos de computação hospedando as funções de sistema ASE não podem ser acessadas pelo cliente, embora o cliente gerencie a quantidade de instâncias e seus tamanhos.

Há duas maneiras de acessar a interface do usuário de criação do ASE. Ela pode ser encontrada pesquisando no Azure Marketplace por ***ambiente de serviço de aplicativo*** ou acessando as opções de menu Novo -> Web + Móvel.

Se você quiser que a VNet tenha um grupo de recursos separado do ASE, precisará criar primeiro a VNet separadamente e então selecioná-la durante a criação do ASE. Além disso, se você quiser criar uma sub-rede em uma VNet existente durante a criação do ASE, este deverá ficar no mesmo grupo de recursos que a VNet.

### Criação Rápida
A experiência de criação para um ASE não tem um conjunto de padrões para habilitar a experiência de criação rápida. Você pode criar rapidamente um ASE simplesmente inserindo um nome para a implantação. Por sua vez, isso criará um ASE na região mais próxima a você com:

* VNet com 512 endereços usando um espaço de endereço privado RFC1918
* sub-rede com 256 endereços
* VIP Externo
* Pool de front-end com dois recursos de computação P2
* Pool de trabalho com dois recursos de computação P1
* endereço IP único a ser usado para SSL de IP

Os pools do Front End exigem P2 ou maior. Tenha cuidado ao selecionar a assinatura em que você deseja que ASE esteja. As únicas contas que podem usar o ASE para hospedar o conteúdo devem estar na assinatura usada para criá-lo.

![][1]

O nome especificado para o ASE será usado para os aplicativos Web criados no ASE. Se o nome do ASE for appsvcenvdemo, o nome do subdomínio será .*appsvcenvdemo.p.azurewebsites.net*. Se você tiver criado, portanto, um aplicativo Web chamado *meuaplicativodeteste*, ele seria endereçável em *meuaplicativodeteste.appsvcenvdemo.p.azurewebsites.net*. Você não pode usar espaços em branco no nome do ASE. Se você usar letras maiúsculas entre os caracteres do nome, o nome de domínio será a versão total em letras minúsculas desse nome. Se você usar um ILB, seu nome do ASE não será usado no subdomínio, mas sim declarado explicitamente durante a criação do ASE.

Ter os padrões será muito útil para um determinado número de situações, mas muitas vezes você precisará ajustar algo. As próximas seções percorrerão cada uma das seções de configuração relacionadas ao ASE.

### Rede Virtual
O processo de criação do ASE dá suporte à seleção de uma VNet Clássica ou do Resource Manager existente, bem como a criação de uma nova VNet Clássica.

Ao selecionar uma VNet existente, você verá suas VNets Clássicas e do Resource Manager listadas juntas. As VNets Clássicas indicam a palavra Clássica ao lado da localização. Se isso não for indicado, ela será uma VNet do Resource Manager.

![][2]

Se utilizar a interface do usuário de criação da VNet, você precisará fornecer:

* Nome da VNet
* Intervalo de endereços VNet na notação CIDR
* Local

A localização de VNet é a localização do ASE. Lembre-se de que isso cria uma VNet Clássica, não uma VNet do Resource Manager.

Os ASEs podem ser implantados em redes virtuais que usam os intervalos de endereço público *ou* espaços de endereço RFC1918 (ou seja, endereços privados). Para usar uma rede virtual com um intervalo de endereços públicos, você precisará criar a sub-rede antecipadamente e selecioná-la na UX de criação do ASE.

Se você selecionar uma VNET pré-existente, você também terá que especificar uma sub-rede para usar ou então criar uma nova. A sub-rede precisa ter oito endereços ou mais e não pode ter qualquer outro recurso. A criação do ASE falhará se você tentar usar uma sub-rede que já tenha VMs alocadas para ele.

Depois de criar sua VNet especificada ou selecionada, você precisará criar ou selecionar uma sub-rede conforme apropriado. Os detalhes que devem ser fornecidos aqui são os seguintes:

* Nome da sub-rede
* Intervalo de sub-rede em notação CIDR

Se você não estiver familiarizado com a notação CIDR (Classless Inter-Domain Routing), ela terá a forma de um Endereço IP, que é a barra invertida separada do valor CIDR. Ela se parece com *10.0.0.0/22*. O valor da CIDR indica o número de bits iniciais mascarados em comparação ao endereço IP mostrado. Uma maneira mais fácil de expressar o conceito aqui é que os valores CIDR fornecem um intervalo de IPs. Neste exemplo, um 10.0.0.0/22 significa um intervalo de 1024 endereços ou de 10.0.0.0 a 10.0.3.255. Um “/23” significa 512 endereços, e assim por diante.

Lembre-se que, se você quiser criar uma sub-rede em uma VNet existente, o ASE estará no mesmo grupo de recursos que a VNet. Para manter o ASE em um grupo de recursos separado da sua VNet, simplesmente crie sua VNet e sua sub-rede separadamente antes da criação do ASE.

#### VIP Interno ou Externo
Por padrão, a configuração de VNet é definida com um tipo de VIP Externo e 1 endereço IP. Se você quiser usar um ILB em vez de um VIP externo, vá para Configuração de VNet e altere o Tipo de VIP para Interno. Um VIP Externo é usado por padrão. Quando você altera o Tipo de VIP para Interno, você precisará especificar um subdomínio para o ASE. Há algumas compensações ao usar um ILB como o VIP para um ASE. Para saber mais sobre eles, leia [Usando um Balanceador de Carga Interno com um Ambiente de Serviço de Aplicativo][ILBASE].

![][4]

### Pools de recursos de computação
Durante a criação do ASE, você pode definir o número de recursos de cada pool de recursos junto com seu tamanho. Embora você possa definir os tamanhos dos seus pools de recursos na criação do ASE, também poderá ajustá-los depois com opções manuais de dimensionamento ou opções de dimensionamento automático.

Como observado anteriormente, o ASE consiste em servidores e trabalhadores de Front-end. Os servidores Front-End lidam com a carga de conexão do aplicativo e os processadores executam o código do aplicativo. Os servidores Front-End são gerenciados em um pool de recursos de computação dedicado. Os processadores, por sua vez, são gerenciados em 3 pools de recursos de computação separado chamados

* Pool de trabalho 1
* Pool de trabalho 2
* Pool de trabalho 3

Se você tiver um grande número de solicitações para aplicativos Web simples, provavelmente aumentará seu número de Front-Ends e utilizará menos processadores. Se você tiver aplicativos de Web que façam uso intenso de CPU ou memória com tráfego leve, então você não precisa de muitos Front-Ends, mas provavelmente terá maiores ou mais processadores.

![][3]

Independentemente do tamanho dos recursos de computação, os requisitos mínimos são 2 servidores Front-End e 2 processadores. Um ASE pode ser configurado para usar até um total de 55 recursos de computação. Desses 55 recursos de computação, apenas 50 podem ser usados para cargas de trabalho do host. Há duas razões para isso. Há um mínimo de 2 recursos de computação Front-End. Com isso, restam até 53 para dar suporte a alocação do pool de trabalho. Para oferecer tolerância a falhas, você precisa ter um recurso de computação adicional alocado de acordo com as seguintes regras:

* cada pool de trabalho precisa de pelo menos um recurso de computação adicional ao qual nenhuma carga de trabalho pode ser atribuída
* quando a quantidade de recursos de computação em um pool ultrapassa um determinado valor, outro recurso de computação torna-se necessário

Dentro de qualquer pool de trabalho individual, os requisitos de tolerância a falhas são aqueles para um determinado valor de X recursos atribuídos a um pool de trabalho:

* se X estiver entre 2 a 20, a quantidade de recursos de computação utilizáveis que você pode usar para cargas de trabalho é X-1
* se X estiver entre 21 a 40, a quantidade de recursos de computação utilizáveis que você pode usar para cargas de trabalho é X-2
* se X estiver entre 41 a 53, a quantidade de recursos de computação utilizáveis que você pode usar para cargas de trabalho é X-3

Além de ser capaz de gerenciar a quantidade de recursos de computação que você pode atribuir a um determinado pool, você também tem controle sobre o tamanho. Com ambientes de serviços de aplicativo, você pode escolher entre 4 tamanhos diferentes rotulados de P1 a P4. Para obter detalhes sobre esses tamanhos e seus preços, consulte aqui o documento [Preços do Serviço de Aplicativo][AppServicePricing]. Os tamanhos P1 a P3 de recursos de computação são os mesmos que aqueles disponíveis em ambientes multilocatário. O recurso de computação P4 proporciona 8 núcleos com 14 GB de RAM e só está disponível em um ambiente do serviço de aplicativo.

Os preços para ambientes do serviço de aplicativo dependem dos recursos de computação atribuídos. Você paga pelos recursos de computação alocados para seu ambiente do serviço de aplicativo independentemente de eles estarem hospedando cargas de trabalho ou não.

Por padrão, um ASE vem com 1 endereço IP que está disponível para o SSL de IP. Se você souber que precisará de mais, você pode especificar isso aqui ou gerenciá-lo após a criação.

### Após a criação de um Ambiente do Serviço de Aplicativo
Após a criação do ASE é possível ajustar:

* Quantidade de Front-Ends (mínimo: 2)
* Quantidade de processadores (mínimo: 2)
* Quantidade de endereços IP disponíveis para SSL de IP
* Tamanho de recursos de computação usados pelos Front-Ends ou Processadores (o tamanho mínimo de Front-End é P2)

Você não pode alterar:

* Local
* Assinatura
* Grupo de recursos
* VNET usada
* Sub-rede usada

Há mais detalhes sobre o gerenciamento do dimensionamento manual e monitoramento de Ambientes de Serviço de Aplicativo aqui: [Como configurar um Ambiente de Serviço de Aplicativo][ASEConfig]

Para saber mais sobre o dimensionamento automático, há um guia aqui: [Como configurar o dimensionamento automático para um Ambiente de Serviço de Aplicativo][ASEAutoscale]

Há dependências adicionais que não estão disponíveis para personalização, como o banco de dados e o armazenamento. Esses são gerenciados pelo Azure e fornecidos com o sistema. O armazenamento do sistema dá suporte a até 500 GB para todo o Ambiente de Serviço do Aplicativo e o banco de dados é ajustado pelo Azure como necessário, por meio do dimensionamento do sistema.

## Introdução
Todos os artigos e os Como fazer para Ambientes de Serviço de Aplicativo estão disponíveis no [LEIAME para Ambientes de Serviço de Aplicativo](../app-service/app-service-app-service-environments-readme.md).

Para se familiarizar com os ambientes de serviço de aplicativo, consulte [Introdução ao ambiente de Serviço de Aplicativo][WhatisASE]

Para obter mais informações sobre a plataforma de Serviço de Aplicativo do Azure, consulte [Serviço de Aplicativo do Azure][AzureAppService].

[!INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[!INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-basecreateblade.png
[2]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-vnetcreation.png
[3]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-resources.png
[4]: ./media/app-service-web-how-to-create-an-app-service-environment/asecreate-externalvip.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[ASEConfig]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[ASEAutoscale]: http://azure.microsoft.com/documentation/articles/app-service-environment-auto-scale/
[ILBASE]: http://azure.microsoft.com/documentation/articles/app-service-environment-with-internal-load-balancer/

<!---HONumber=AcomDC_0713_2016-->