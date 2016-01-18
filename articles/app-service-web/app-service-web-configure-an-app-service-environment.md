<properties 
	pageTitle="Como configurar um Ambiente do Serviço de Aplicativo" 
	description="Configuração, gerenciamento e monitoramento de ambientes do serviço de aplicativo" 
	services="app-service" 
	documentationCenter="" 
	authors="ccompy" 
	manager="stefsch" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/04/2016" 
	ms.author="ccompy"/>


# Configurando um Ambiente do Serviço de Aplicativo #

## Visão geral ##

Os Ambientes do Serviço de Aplicativo são um recurso do tipo Premium no Serviço de Aplicativo do Azure que oferece novos recursos de dimensionamento e de acesso à rede. Essa nova funcionalidade de escala permite que você coloque uma instância do Serviço de Aplicativo do Azure em sua VNET. Esse recurso pode hospedar Aplicativos Web, Aplicativos Móveis e Aplicativos de API. Os Aplicativos Lógicos ainda não podem ser executados em um ASE.

Se você não estiver familiarizado com a capacidade do ASE (Ambiente de Serviço de Aplicativo), leia o documento aqui [O que é um ambiente de serviço de aplicativo](app-service-app-service-environment-intro.md). Para obter informações sobre como criar um ASE, leia o documento aqui [Como criar um ambiente de serviço de aplicativo](app-service-web-how-to-create-an-app-service-environment.md).

Em um nível elevado, um ambiente de serviço de aplicativo consiste em vários componentes principais:

- Recursos de computação em execução no serviço hospedado do Ambiente de Aplicativo do Azure
- Armazenamento
- Banco de dados
- Uma rede virtual clássica "v1" com pelo menos uma sub-rede
- Sub-rede na qual está em execução o serviço hospedado do ambiente de aplicativo do Azure

Os recursos de computação são usados para seus quatro pools de recursos. Cada Ambiente do Serviço de Aplicativo tem um conjunto de Front-ends e três Pools de Trabalho. Você não precisa usar todos os três Pools de trabalho e, se quiser, poderá usar apenas um. Os front-ends são os pontos de extremidade HTTP para os aplicativos mantidos em seu ASE. Os Trabalhadores são os locais onde seus aplicativos são realmente executados. Saber quando será necessário adicionar mais Front-ends ou mais Trabalhadores está ligado à execução dos aplicativos colocados no ASE. Como exemplo, digamos que você tenha apenas um aplicativo em seu ASE e que ele seja um aplicativo Olá mundo que obtém um grande número de solicitações. Nesse caso, você precisará expandir os Front-ends para lidar com a carga HTTP mas, por outro lado, não precisará expandir seus Trabalhadores. A tentativa de lidar com tudo isso de forma manual é especialmente assustadora quando você considerar que é provável que cada ASE tenha uma mistura de aplicativos em execução com critérios variados de desempenho. Felizmente, adicionamos a autoescala aos Ambientes de Serviço de Aplicativo e é isso que facilitará muito a vida. Para obter detalhes sobre o dimensionamento e a autoescala dos Ambientes do Serviço de Aplicativo, siga este link [Como configurar a autoescala em um Ambiente do Serviço de Aplicativo][ASEAutoscale]

Cada ASE é configurado com 500 Gb de armazenamento. Esse espaço é usado em todos os aplicativos do ASE. Esse espaço de armazenamento faz parte do ASE e atualmente não pode ser alternado para usar o espaço de armazenamento do cliente.

O banco de dados armazena as informações que definem o ambiente, bem como detalhes sobre os aplicativos executados nele. Isso também faz parte da assinatura mantida pelo Azure e não é algo que os clientes possam manipular diretamente.

A rede virtual usada com seu ASE pode ser uma das criadas junto com o ASE ou uma já existente. Se você quiser que o ASE esteja em um grupo de recursos separado daquele usado para o ASE, precisará criar sua Rede Virtual separadamente do fluxo de criação do ASE. É uma boa ideia criar a sub-rede que você deseja usar durante a criação da sub-rede, já que criar a sub-rede durante a criação do ASE forçará o ASE a estar no mesmo grupo de recursos que a Rede Virtual. No momento, há suporte apenas para as Redes Virtuais V1 “clássicas”.

A interface do usuário para gerenciar e monitorar o Ambiente do Serviço de Aplicativo está disponível no Portal do Azure. Se você tiver um ASE, provavelmente verá o símbolo do Serviço de Aplicativo em sua barra lateral. Esse símbolo é usado para representar Ambientes do Serviço de Aplicativo no Portal do Azure.

![][1]

Você pode usar o ícone ou selecionar a divisa (maior que o símbolo) na parte inferior da barra lateral e selecionar Ambientes do Serviço de Aplicativo. Ambos fazem a mesma coisa: abrir a interface do usuário que lista todos os seus Ambientes do Serviço de Aplicativo. A seleção dos ASEs listados abre a interface do usuário usada para monitorá-los e gerenciá-los.

![][2]

A primeira folha mostra algumas propriedades do seu ASE junto com um gráfico de métricas por pool de recursos. Algumas das propriedades mostradas no bloco Fundamentos também são hiperlinks que abrirão a folha associada a ele. Por exemplo, você pode selecionar o Nome da Rede Virtual para abrir a interface do usuário associada à Rede Virtual em que o ASE está sendo executado. Os Planos do Serviço de Aplicativo e os Aplicativos abrem folhas que listam esses itens em seu ASE.

## Monitoramento ##

Os gráficos oferecem a capacidade de ver uma variedade de métricas de desempenho em cada pool de recursos. Faz muito sentido monitorar a memória e a CPU médias para o pool de Front-ends. Os Front-ends têm uma carga distribuída, se você examinar uma média, poderá ter uma boa ideia do desempenho geral. No entanto, os pools de trabalho não são iguais. Vários Planos do Serviço de Aplicativo podem usar os trabalhadores em um pool de trabalho. Se esse for o caso, o uso de CPU e de memória não oferecem muito à respeito das informações úteis. É mais importante controlar quantos trabalhadores foram usados e estão disponíveis, especialmente se você estiver gerenciando esse sistema para outros usuários.

Todas as métricas que podem ser controladas nos gráficos também podem ser usadas para configurar Alertas. A configuração de Alertas funciona da mesma forma como em qualquer lugar no Serviço de Aplicativo. Você pode definir um alerta da interface do usuário de Alertas ou pressionando Adicionar Alerta na interface do usuário de métricas.
 
![][3]

As métricas que acabamos de discutir são as métricas do Ambiente do Serviço de Aplicativo. Também há métricas disponíveis no nível do Plano do Serviço de Aplicativo. Nesse local, o monitoramento de CPU e da memória faz muito sentido. Em um ASE, todos os ASPs são ASPs dedicados. Ou seja, os únicos aplicativos em execução nos hosts alocados ao ASP são os aplicativos desse ASP. Para ver detalhes sobre o ASP, simplesmente abra o ASP de qualquer uma das listas na interface do usuário do ASE ou navegue pelos Planos do Serviço de Aplicativo, o que listará todos eles.

Talvez você já conheça os recursos de autoescala disponíveis para os ASPs fora de um ASE e como eles aproveitam as métricas disponíveis para um recurso. O mesmo também é verdadeiro para a autoescala de um Ambiente do Serviço de Aplicativo. Não só você ainda pode dimensionar automaticamente o ASP com base em métricas em um ASE como também pode configurar regras de autoescala para o próprio ASE. Para obter detalhes sobre a configuração da autoescala, há um guia detalhado aqui: [Autoescala em um Ambiente do Serviço de Aplicativo][ASEAutoscale]


## Propriedades ##

A folha Configurações será aberta automaticamente quando você abrir a folha do seu ASE. As Propriedades estão na parte superior. Há alguns itens aqui que são redundantes para o que você vê em Fundamentos, mas o que é muito útil é o Endereço VIP, bem como o Endereço IP de Saída. Por enquanto, eles são iguais, mas em algum ponto no futuro provavelmente será possível ter um endereço IP de saída separado do endereço VIP e é por isso que ambos foram observados aqui. Dessa forma, se você não contar a configuração do SSL e a adição de um endereço IP para um único aplicativo em seu ASE, o IP que será definido no DNS para os aplicativos criados em seu ASE será o endereço VIP visto aqui em Propriedades.

![][4]

## Endereços IP ##

Aqui você pode adicionar mais endereços IP ao seu ASE para que sejam usados por seus aplicativos. Quando um aplicativo for criado em seu ASE e você desejar configurá-lo com o IP SSL, será necessário ter um endereço IP reservado apenas para esse aplicativo. Para fazer isso, seu ASE precisará ter alguns endereços IP próprios que possam ser alocados. Quando um ASE é criado, tem um endereço para essa finalidade. Se você precisar de mais, entre aqui e adicione mais. Agora, antes de simplesmente arrastá-lo para a alocação máxima caso algum dia você queira mais, saiba que os endereços IP adicionais serão cobrados. Os detalhes sobre esses valores são controlados na página de preços: [Preços do Serviço de Aplicativo][AppServicePricing] Basta rolar para baixo até a seção de conexões SSL. O preço adicional é o preço do IP SSL.

**Observação:** se você adicionar mais endereços IP, esteja ciente de que se trata de uma operação em escala. Pode haver somente uma operação em escala em andamento por vez. Existem três operações em escala:

- alteração do número de endereços IP no ASE que estão disponíveis para uso do IP SSL
- alteração do tamanho do recurso de computação usado em um pool de recursos
- alteração do número de recursos de computação usados em um pool de recursos manualmente ou por meio de autoescala

## Pools de recursos ##

Em Configurações, você pode chegar ao pool Front-end ou à interface do usuário do Pool de Trabalho. Cada uma dessas folhas do pool de recursos só mostra as informações daquele pool de recursos, além de fornecer controles para o dimensionamento completo do pool de recursos.

A folha base de cada pool de recursos oferece um gráfico com métricas para o pool de recursos. Assim como acontece com os gráficos da folha ASE, você pode entrar no gráfico e configurar alertas como desejado. A configuração de um alerta da folha ASE para um pool de recursos específico é igual à configuração feita do pool de recursos. Da folha Configurações do pool de trabalho, você tem acesso à listagem de todos os Aplicativos ou os Planos do Serviço de Aplicativo em execução nesse pool de trabalho.

![][5]

### Dimensionamento da quantidade de recursos de computação ###

Para fornecer uma perspectiva melhor sobre como dimensionar aplicativos em um ASE, veja este guia: [Dimensionando aplicativos em um Ambiente do Serviço de Aplicativo](app-service-web-scale-a-web-app-in-an-app-service-environment.md). Se quiser saber mais sobre como configurar a autoescala para os pools de recursos ASE, comece por aqui: [Autoescala em um Ambiente do Serviço de Aplicativo][ASEAutoscale]. Essa descrição detalha as operações de escala manual em seus pools de recursos.

Os pools de recursos, os Front-Ends e os Trabalhadores não estão diretamente acessíveis para locatários. Ou seja, você não pode acessá-los via RDP, alterar o provisionamento deles ou agir como administrador neles. Eles são operados e mantidos pelo Azure. Com isso, a quantidade e os tamanhos dos recursos de computação são decididos pelo usuário.

Há três maneiras de controlar quantos servidores você tem em seus pools de recursos - a operação de escala da folha ASE principal na parte superior - a operação de escala manual da folha Escala do pool de recursos individual, sob Configurações - a autoescala, que é configurada na folha Escala do pool de recursos individual

Para usar a operação Escala na folha ASE, bastará clicar nela, arrastar o controle deslizante até a quantidade desejada e salvar. Essa interface do usuário também oferece suporte à alteração do tamanho.

![][6]

Para usar os recursos de escala manual ou autoescala no pool de recursos específicos, comece da folha ASE e vá para Configurações. Em Configurações, abra o pool Front-end ou os Pools de Trabalho como desejado e abra o pool que você deseja alterar. Em Configurações, há uma divisa Escala. Isso abrirá a folha que pode ser usada para escala manual ou autoescala.

![][7]

Um ambiente do serviço de aplicativo pode ser configurado para usar até um total de 55 recursos de computação. Desses 55 recursos de computação, apenas 50 podem ser usados para cargas de trabalho do host. Há duas razões para isso. Há um mínimo de 2 recursos de computação Front-End. Com isso, restam até 53 para dar suporte a alocação do pool de trabalho. Para oferecer tolerância a falhas, você precisa ter um recurso de computação adicional alocado de acordo com as seguintes regras:

- cada pool de trabalho precisa de pelo menos um recurso de computação adicional que não está disponível para atribuição a uma carga de trabalho
- quando a quantidade de recursos de computação em um pool de trabalho ultrapassa um determinado valor, outro recurso de computação torna-se necessário para tolerância a falhas. Esse não é o caso no pool de front-end.

Dentro de qualquer pool de trabalho individual, os requisitos de tolerância a falhas são aqueles para um determinado valor de X recursos atribuídos a um pool de trabalho:

- se X estiver entre 2 a 20, a quantidade de recursos de computação utilizáveis que você pode usar para cargas de trabalho é X-1
- se X estiver entre 21 a 40, a quantidade de recursos de computação utilizáveis que você pode usar para cargas de trabalho é X-2
- se X estiver entre 41 a 53, a quantidade de recursos de computação utilizáveis que você pode usar para cargas de trabalho é X-3

Lembre-se de que a superfície mínima tem dois servidores de Front-end e dois Trabalhadores. Com as instruções acima, veja alguns exemplos para que tudo fique mais claro.

- Se você tiver 30 Trabalhadores em um único pool, então 28 deles poderão ser usados para hospedar cargas de trabalho. 
- Se você tiver dois Trabalhadores em um único pool, então um deles poderá ser usado para hospedar cargas de trabalho.
- Se você tiver 20 Trabalhadores em um único pool, então 19 deles poderão ser usados para hospedar cargas de trabalho.  
- Se você tiver 21 Trabalhadores em um único pool, então ainda assim 19 deles poderão ser usados para hospedar cargas de trabalho.  

O aspecto de tolerância a falhas é importante, mas você precisará tê-lo em mente quando dimensionar acima de determinados limites. Se quiser adicionar mais capacidade a partir de 20 instâncias, insira 22 ou mais, já que 21 não adicionam mais capacidade. O mesmo será verdadeiro se o número de instâncias for maior do que 40, onde o próximo número que adicionará capacidade será 42.

### Escala de tamanho de recursos de computação ###

Além de ser capaz de gerenciar a quantidade de recursos de computação que você pode atribuir a um determinado pool, você também tem controle sobre o tamanho. Com ambientes de serviços de aplicativo, você pode escolher entre 4 tamanhos diferentes rotulados de P1 a P4. Para obter detalhes sobre esses tamanhos e seus preços, consulte aqui [Preços de serviços de aplicativo](../app-service/app-service-value-prop-what-is.md). Os tamanhos de recursos de computação de P1 a P3 são os mesmos que aqueles normalmente disponíveis. O recurso de computação P4 proporciona 8 núcleos com 14 GB de RAM e só está disponível em um ambiente do serviço de aplicativo.

Se você quiser alterar o tamanho dos recursos de computação usados em seus pools, existem duas maneiras de fazer isso. Há o comando scale, que está disponível na folha ASE e também na folha Camadas de Preço, que pode ser obtida nas Configurações, no pool de recursos individual.

![][8]

Antes de fazer alterações, no entanto, é importante observar algumas coisas:

- potencialmente, a conclusão das alterações feitas pode levar algumas horas, dependendo do tamanho da alteração
- quando já houver uma alteração de configuração do ambiente do serviço de aplicativo em andamento, você não pode iniciar outra alteração
- se você alterar o tamanho dos recursos de computação usados em um pool de trabalho, poderá causar interrupções em aplicativos Web em execução nesse pool de trabalho

Adicionar mais instâncias a um pool de trabalho é uma operação benigna e não gera interrupções de aplicativo nos recursos do pool. No entanto, alterar o tamanho do recurso de computação usado em um pool de trabalho é a outra história. Para evitar qualquer tempo de inatividade em aplicativos durante uma alteração de tamanho em um pool de trabalho, é melhor:

- usar um pool de trabalho não utilizado para trazer as instâncias requeridas no tamanho desejado
- dimensionar os planos do serviço de aplicativo para o novo pool de trabalho.  
 
Isso é muito menos perturbador para os aplicativos do que alterar o tamanho de recursos de computação com cargas de trabalho em execução. Para obter detalhes sobre o dimensionamento de aplicativos em um ambiente do serviço de aplicativo, acesse aqui [Dimensionamento de aplicativos em um ambiente do serviço de aplicativo](app-service-web-scale-a-web-app-in-an-app-service-environment.md)

## Rede Virtual ##

Ao contrário do serviço hospedado que contém o ASE, a [Rede Virtual][virtualnetwork] e a sub-rede estão sob o controle do usuário. Os Ambientes do Serviço de Aplicativo têm alguns requisitos de rede, mas o resto deve ser controlado pelo usuário. Esses requisitos de ASE são:

- uma Rede Virtual “v1” clássica 
- uma sub-rede com pelo menos 8 endereços 
- a VNET deve ser uma VNET regional  
 
A administração da sua Rede Virtual é feita por meio da interface do usuário da Rede Virtual ou do Powershell.

Já que esse recurso coloca o serviço de aplicativo do Azure em seu VNET, isso significa que seus aplicativos hospedados em seu ASE agora podem acessar diretamente recursos disponibilizados por meio de VPNs Site a Site ou Rota Expressa. Os aplicativos em seus ambientes do serviço de aplicativo não exigem recursos de rede adicionais para acessar os recursos disponíveis para a VNET hospedando seu ambiente do serviço de aplicativo. Isso significa que você não precisa usar a Integração de Rede Virtual ou as Conexões Híbridas para acessar recursos na sua Rede Virtual ou conectados a ela. Entretanto, você ainda pode usar ambos os recursos para acessar recursos em redes que não estejam conectadas à sua Rede Virtual. Por exemplo, você pode usar a Integração à Rede Virtual para integrar-se a uma Rede Virtual que esteja em sua assinatura mas que não esteja conectada à Rede Virtual em que seu ASE está. Você também ainda pode usar as Conexões Híbridas para acessar recursos em outras redes, como faria normalmente.

Caso você tenha configurado sua Rede Virtual com uma VPN da Rota Expressa, deverá estar ciente das necessidades de rotas de um ASE. Há algumas configurações de rota definida pelo usuário (UDR) que são incompatíveis com um ASE. Para mais detalhes sobre como executar um ASE em uma Rede Virtual com a Rota Expressa, consulte este documento: [Executando um Ambiente do Serviço de Aplicativo em uma Rede Virtual com a Rota Expressa][ExpressRoute]

Você também pode controlar o acesso a seus aplicativos usando Grupos de Segurança de Rede. Esse recurso permite que você bloqueie seu ambiente do serviço de aplicativo, permitindo acesso apenas aos endereços IP para os quais você deseja restringi-lo. Para obter mais informações sobre como fazer isso, consulte o documento aqui [Como Controlar o Tráfego de Entrada em um Ambiente de Serviço de Aplicativo](app-service-app-service-environment-control-inbound-traffic.md).

## Excluindo um Ambiente do Serviço de Aplicativo ##

Se você deseja excluir um ambiente do serviço de aplicativo, simplesmente use a ação de exclusão na parte superior da folha Ambiente do Serviço de Aplicativo. Quando você fizer isso, será solicitada a inserção do nome do seu Ambiente do Serviço de Aplicativo para confirmar a operação. OBSERVAÇÃO: quando você exclui um Ambiente do Serviço de Aplicativo, exclui todo o conteúdo dentro dele também.

![][9]

## Introdução

Para se familiarizar com os Ambientes de Serviços de Aplicativo, consulte [Como criar um Ambiente de Serviço de Aplicativo](app-service-web-how-to-create-an-app-service-environment.md)

Para obter mais informações sobre a plataforma do Serviço de Aplicativo do Azure, consulte [Serviço de Aplicativo do Azure](../app-service/app-service-value-prop-what-is.md).

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-configure-an-app-service-environment/ase-icon.png
[2]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-aseblade.png
[3]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolchart.png
[4]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-properties.png
[5]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolblade.png
[6]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-scalecommand.png
[7]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolscale.png
[8]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-pricingtiers.png
[9]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-deletease.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoScale]: http://azure.microsoft.com/documentation/articles/app-service-web-scale-a-web-app-in-an-app-service-environment/
[ControlInbound]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[ASEAutoscale]: http://azure.microsoft.com/documentation/articles/app-service-environment-auto-scale/
[ExpressRoute]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/

<!---HONumber=AcomDC_0107_2016-->