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
	ms.date="06/20/2016" 
	ms.author="ccompy"/>


# Configurando um Ambiente do Serviço de Aplicativo #

## Visão geral ##

Em um nível elevado, um ambiente de serviço de aplicativo consiste em vários componentes principais:

- Recursos de computação em execução no serviço hospedado do Ambiente de Aplicativo do Azure
- Armazenamento
- Banco de dados
- Uma rede virtual clássica "v1" com pelo menos uma sub-rede
- Sub-rede na qual está em execução o serviço hospedado do ambiente de aplicativo do Azure

### Recursos de computação

Os recursos de computação são usados para seus quatro pools de recursos. Cada Ambiente do Serviço de Aplicativo tem um conjunto de Front-ends e três Pools de Trabalho possíveis. Você não precisa usar todos os três Pools de trabalho e, se quiser, poderá usar apenas um ou dois. Os pools de recursos, os Front-Ends e os Trabalhadores não estão diretamente acessíveis para locatários. Você não pode acessá-los via RDP, alterar o provisionamento deles ou agir como administrador neles, mas você pode definir a quantidade e o tamanho. Em um ASE, você tem quatro opções de tamanho rotuladas de P1 a P4. Para obter detalhes sobre esses tamanhos e seus preços, consulte aqui o documento [Preços do Serviço de Aplicativo](../app-service/app-service-value-prop-what-is.md). Apenas uma operação de escala pode ocorrer por vez.

**Front-Ends** Os Front-Ends são os pontos de extremidade HTTP/HTTPS para os aplicativos mantidos em seu ASE. Não execute cargas de trabalho nos Front-Ends.

- Um ASE começa com (2) P2s, o que é suficiente para cargas de trabalho de desenvolvimento e teste e para cargas de trabalho de produção de nível baixo. Os P3s são altamente recomendados para cargas de trabalho de produção moderada a pesada.
- Para as cargas de trabalho de produção moderada a pesada, recomendamos pelo menos quatro P3s a fim de garantir uma quantidade suficiente de front-ends em execução durante a manutenção agendada. As atividades de manutenção agendadas desativarão (1) front-end por vez, reduzindo assim a quantidade geral de front-ends disponíveis durante as atividades de manutenção.
- Você não pode adicionar instantaneamente uma nova instância de Front-End. Pode demorar de duas a três horas para provisionar um.
- Para obter um ajuste mais fino do dimensionamento, os clientes devem monitorar a porcentagem de CPU, a porcentagem de Memória e as métricas de Solicitações Ativas do pool de front-end. Se porcentagem de CPU ou de Memória estiver acima de 70% durante a execução de P3s, adicione mais front-ends. Se a média de Solicitações Ativas for de 15K a 20K por front-end, você também deverá adicionar mais front-ends. O objetivo geral é manter os percentuais de CPU e de Memória abaixo de 70%, e as Solicitações Ativas com uma média abaixo de 15K de solicitações por front-end durante a execução de P3s.  

**Trabalhadores** Os Trabalhadores são os locais onde seus aplicativos são realmente executados. Ao escalar verticalmente seus Planos de Serviço de Aplicativo, você usa os trabalhadores no pool de trabalhadores associados.

- Não é possível adicionar trabalhadores instantaneamente. O provisionamento pode demorar de duas a três horas, independentemente de quantos estão sendo adicionados.
- O dimensionamento de um recurso de computação para qualquer pool demorará de duas a três horas por domínio de atualização. Há 20 domínios de atualização em um ASE. Se você tiver dimensionado o tamanho de computação de um pool de trabalho com 10 instâncias, talvez demore de 20 a 30 horas para a conclusão. 
- se você alterar o tamanho dos recursos de computação usados em um pool de trabalho, causará inicializações a frio dos aplicativos em execução nesse pool de trabalho

Veja a seguir a maneira mais rápida de alterar o tamanho do recurso de computação de um pool de trabalho que não esteja executando aplicativos:

- reduzir a contagem de instâncias até 0. Levará cerca de 30 minutos para desalocar suas instâncias
- selecionar o novo tamanho de computação e o número de instâncias. A partir daqui, a conclusão demorará de duas a três horas.

Se os aplicativos exigirem um recurso de computação maior, você não poderá aproveitar a orientação anterior. Em vez de alterar o tamanho do pool de trabalho que hospeda esses aplicativos, você pode preencher outro pool de trabalho com trabalhos do tamanho desejado e mover seus aplicativos para esse pool.

- criar instâncias adicionais com o tamanho de computação necessário em outro pool de trabalho. Isso levará de duas a três horas para ser concluído.
- reatribuir os Planos de Serviço de Aplicativo que hospedam os aplicativos que precisam de um tamanho maior para o pool de trabalho recém-configurado. Essa é uma operação rápida que deve demorar menos de um minuto para ser concluída.  
- reduzir verticalmente o primeiro pool de trabalho, se você não precisar mais dessas instâncias não utilizadas. Essa operação demora cerca de 30 minutos para ser concluída.

**Dimensionamento automático** Uma das ferramentas que pode ajudar a gerenciar o consumo de recursos de computação é o dimensionamento automático, que pode ser feito para pools de Front-End ou de Trabalho. Você pode realizar ações como aumentar suas instâncias de qualquer tipo de pool na manhã e reduzir à noite, ou talvez adicionar instâncias quando o número de trabalhos disponíveis em um pool de trabalho cair abaixo de um determinado nível. Se você quiser definir regras de dimensionamento automático com relação às métricas de pool de recursos de computação, lembre-se do tempo necessário para o provisionamento. Para obter mais detalhes sobre o dimensionamento automático dos Ambientes do Serviço de Aplicativo, acesse [Como configurar o dimensionamento automático em um Ambiente de Serviço de Aplicativo][ASEAutoscale]

### Armazenamento

Cada ASE é configurado com 500 Gb de armazenamento. Esse espaço é usado em todos os aplicativos do ASE. Esse espaço de armazenamento faz parte do ASE e atualmente não pode ser alternado para usar o espaço de armazenamento do cliente. Se você estiver fazendo ajustes no roteamento ou segurança de rede virtual, ainda será necessário permitir o acesso ao Armazenamento do Azure, ou o ASE não funcionará.

### Banco de dados

O banco de dados armazena as informações que definem o ambiente, bem como detalhes sobre os aplicativos executados nele. Isso também faz parte da assinatura mantida pelo Azure e não é algo que os clientes possam manipular diretamente. Se você estiver fazendo ajustes no roteamento ou segurança de rede virtual, ainda será necessário permitir o acesso ao SQL Azure, ou o ASE não funcionará.

### Rede

A rede virtual usada com seu ASE pode ser uma daquelas criadas durante a criação do ASE ou uma já existente. Se você quiser que a Rede Virtual esteja em um grupo de recursos separado daquele usado para o ASE, precisará criar sua Rede Virtual separadamente do fluxo de criação do ASE. Se você criar a sub-rede durante a criação do ASE, obrigará o ASE a estar no mesmo grupo de recursos que a rede virtual.

Há algumas restrições sobre a rede virtual usada para um ASE:

- No momento, há suporte apenas para as redes virtuais V1 “clássicas”.
- A VNET deve ser uma VNET regional
- Com uma alteração recente feita em junho de 2016, os ASEs agora podem ser implantados em redes virtuais que usam *os* intervalos de endereço público *ou* espaços de endereço RFC1918 (ou seja, os endereços privados). Para usar uma rede virtual com um intervalo de endereços públicos, você precisará criar a sub-rede antecipadamente e selecioná-la na UX de criação do ASE.
- Deve haver uma sub-rede com oito ou mais endereços nos quais o ASE é implantado
- Quando uma sub-rede é usada para hospedar um ASE, o intervalo de endereços da sub-rede não pode ser alterado. Por esse motivo, recomendamos que a sub-rede contenha pelo menos 64 endereços a fim acomodar qualquer crescimento futuro do ASE 
- **A sub-rede usada para hospedar o ASE não deve conter outros recursos de computação.**

Ao contrário do serviço hospedado que contém o ASE, a [Rede Virtual][virtualnetwork] e a sub-rede estão sob o controle do usuário. A administração da sua Rede Virtual é feita por meio da interface do usuário da Rede Virtual ou do Powershell.

Já que esse recurso coloca o serviço de aplicativo do Azure em seu VNET, isso significa que seus aplicativos hospedados em seu ASE agora podem acessar diretamente recursos disponibilizados por meio de VPNs Site a Site ou Rota Expressa. Os aplicativos em seus ambientes do serviço de aplicativo não exigem recursos de rede adicionais para acessar os recursos disponíveis para a VNET hospedando seu ambiente do serviço de aplicativo. Isso significa que você não precisa usar a Integração de Rede Virtual ou as Conexões Híbridas para acessar recursos na sua Rede Virtual ou conectados a ela. Entretanto, você ainda pode usar ambos os recursos para acessar recursos em redes que não estejam conectadas à sua Rede Virtual. Por exemplo, você pode usar a Integração à Rede Virtual para integrar-se a uma Rede Virtual que esteja em sua assinatura mas que não esteja conectada à Rede Virtual em que seu ASE está. Você também ainda pode usar as Conexões Híbridas para acessar recursos em outras redes, como faria normalmente.

Caso você tenha configurado sua Rede Virtual com uma VPN da Rota Expressa, deverá estar ciente das necessidades de rotas de um ASE. Há algumas configurações de rota definida pelo usuário (UDR) que são incompatíveis com um ASE. Para mais detalhes sobre como executar um ASE em uma Rede Virtual com a Rota Expressa, consulte este documento: [Executando um Ambiente do Serviço de Aplicativo em uma Rede Virtual com a Rota Expressa][ExpressRoute]

Você também pode controlar o acesso a seus aplicativos usando Grupos de Segurança de Rede. Esse recurso permite que você bloqueie seu ambiente do serviço de aplicativo, permitindo acesso apenas aos endereços IP para os quais você deseja restringi-lo. Para obter mais informações sobre como fazer isso, consulte o documento aqui [Como Controlar o Tráfego de Entrada em um Ambiente de Serviço de Aplicativo](app-service-app-service-environment-control-inbound-traffic.md).

## Portal

A interface do usuário para gerenciar e monitorar o Ambiente do Serviço de Aplicativo está disponível no Portal do Azure. Se você tiver um ASE, provavelmente verá o símbolo do Serviço de Aplicativo em sua barra lateral. Esse símbolo é usado para representar Ambientes do Serviço de Aplicativo no Portal do Azure.

![][1]

Você pode usar o ícone ou selecionar a divisa (maior que o símbolo) na parte inferior da barra lateral e selecionar Ambientes do Serviço de Aplicativo. Ambos fazem a mesma coisa: abrir a interface do usuário que lista todos os seus Ambientes do Serviço de Aplicativo. A seleção dos ASEs listados abre a interface do usuário usada para monitorá-los e gerenciá-los.

![][2]

A primeira folha mostra algumas propriedades do seu ASE junto com um gráfico de métricas por pool de recursos. Algumas das propriedades mostradas no bloco Fundamentos também são hiperlinks que abrirão a folha associada a ele. Por exemplo, você pode selecionar o Nome da Rede Virtual para abrir a interface do usuário associada à Rede Virtual em que o ASE está sendo executado. Os Planos do Serviço de Aplicativo e os Aplicativos abrem folhas que listam esses itens em seu ASE.

### Monitoramento

Os gráficos oferecem a capacidade de ver uma variedade de métricas de desempenho em cada pool de recursos. Para o pool de Front-Ends, monitore a média de memória e de CPU . Para os pools de trabalho, monitore a quantidade usada e a quantidade disponível. Vários Planos do Serviço de Aplicativo podem usar os trabalhadores em um pool de trabalho. A carga de trabalho não é distribuída da mesma forma que ocorre com servidores Front-End. Portanto, o uso de CPU e de memória não oferece muitas informações úteis. É mais importante controlar quantos trabalhadores foram usados e estão disponíveis, especialmente se você estiver gerenciando esse sistema para outros usuários.

Todas as métricas que podem ser controladas nos gráficos também podem ser usadas para configurar Alertas. A configuração de Alertas funciona da mesma forma como em qualquer lugar no Serviço de Aplicativo. Você pode definir um alerta da interface do usuário de Alertas ou pressionando Adicionar Alerta na interface do usuário de métricas.
 
![][3]

As métricas que acabamos de discutir são as métricas do Ambiente do Serviço de Aplicativo. Também há métricas disponíveis no nível do Plano do Serviço de Aplicativo. Nesse local, o monitoramento de CPU e da memória faz muito sentido. Em um ASE, todos os ASPs são ASPs dedicados. Ou seja, os únicos aplicativos em execução nos hosts alocados ao ASP são os aplicativos desse ASP. Para ver detalhes sobre o ASP, simplesmente abra o ASP de qualquer uma das listas na interface do usuário do ASE ou navegue pelos Planos do Serviço de Aplicativo, o que listará todos eles.

### Configurações

Na folha do ASE, há uma seção Configurações que contém vários recursos importantes

**Configurações > Propriedades** A folha Configurações será aberta automaticamente quando você abrir a folha de seu ASE. As Propriedades estão na parte superior. Há alguns itens aqui que são redundantes para o que você vê em Fundamentos, mas o que é muito útil é o Endereço VIP, bem como o Endereço IP de Saída.

![][4]

**Configurações > Endereços IP** Ao criar um aplicativo de IP SSL em seu ASE, você precisa de um endereço IP SSL. Para fazer isso, seu ASE precisa de endereços IP SSL próprios que possam ser alocados. Quando um ASE é criado, ele conta com um endereço IP SSL para essa finalidade, mas você pode adicionar mais. Há uma cobrança por endereços IP SSL adicionais, conforme indicado aqui [Preços do Serviço de Aplicativo][AppServicePricing] na seção Conexões SSL. O preço adicional é o preço do IP SSL.

**Configurações > Pool de Front-Ends/Pools de Trabalho** Cada uma dessas folhas do pool de recursos só mostra as informações daquele pool de recursos, além de fornecer controles para o dimensionamento completo do pool de recursos.

A folha base de cada pool de recursos oferece um gráfico com métricas para o pool de recursos. Assim como acontece com os gráficos da folha ASE, você pode entrar no gráfico e configurar alertas como desejado. A configuração de um alerta da folha ASE para um pool de recursos específico é igual à configuração feita do pool de recursos. Da folha Configurações do pool de trabalho, você tem acesso à listagem de todos os Aplicativos ou os Planos do Serviço de Aplicativo em execução nesse pool de trabalho.

![][5]

### Recursos de dimensionamento do portal  

Existem três operações em escala:

- alteração do número de endereços IP no ASE que estão disponíveis para uso do IP SSL
- alteração do tamanho do recurso de computação usado em um pool de recursos
- alteração do número de recursos de computação usados em um pool de recursos manualmente ou por meio de autoescala

O portal oferece três maneiras para controlar quantos servidores você tem nos pools de recursos

- Operação de dimensionamento da folha do ASE principal na parte superior. Você pode fazer várias alterações de configuração de dimensionamento para os Pools Front-End e de Trabalho, e todas elas são aplicadas como uma operação única.
- Operação de dimensionamento manual da folha de Escala de pool de recursos individuais, que está em Configurações
- Dimensionamento Automático que você configura na folha Escala do pool de recursos individuais

Para usar a operação Escala na folha ASE, arraste o controle deslizante até a quantidade desejada e salve. Essa interface do usuário também oferece suporte à alteração do tamanho.

![][6]

Para usar os recursos manuais ou de dimensionamento automático em um pool de recursos específico, acesse *Configurações > Pool de Front-Ends / Pools de Trabalho*, como apropriado, e abra o pool que você quer alterar. Acesse *Configurações > Escalar Horizontalmente ou Configurações > Escalar Verticalmente*. A folha *Escalar Horizontalmente* permite o controle da quantidade de instâncias. *Escalar Verticalmente* permite que você controle o tamanho do recurso.

![][7]

## Considerações sobre a tolerância a falhas

Um ambiente do serviço de aplicativo pode ser configurado para usar até um total de 55 recursos de computação. Desses 55 recursos de computação, apenas 50 podem ser usados para cargas de trabalho do host. Há duas razões para isso. Há um mínimo de 2 recursos de computação Front-End. Com isso, restam até 53 para dar suporte a alocação do pool de trabalho. Para oferecer tolerância a falhas, você precisa ter um recurso de computação adicional alocado de acordo com as seguintes regras:

- cada pool de trabalho precisa de pelo menos um recurso de computação adicional que não está disponível para atribuição a uma carga de trabalho
- quando a quantidade de recursos de computação em um pool de trabalho ultrapassa um determinado valor, outro recurso de computação torna-se necessário para tolerância a falhas. Esse não é o caso no pool de front-end.

Dentro de qualquer pool de trabalho individual, os requisitos de tolerância a falhas são aqueles para um determinado valor de X recursos atribuídos a um pool de trabalho:

- se X estiver entre 2 a 20, a quantidade de recursos de computação utilizáveis que você pode usar para cargas de trabalho é X-1
- se X estiver entre 21 a 40, a quantidade de recursos de computação utilizáveis que você pode usar para cargas de trabalho é X-2
- se X estiver entre 41 a 53, a quantidade de recursos de computação utilizáveis que você pode usar para cargas de trabalho é X-3

A superfície mínima tem dois servidores Front-end e dois Trabalhadores. Com as instruções acima, veja alguns exemplos para que tudo fique mais claro.

- Se você tiver 30 Trabalhadores em um único pool, então 28 deles poderão ser usados para hospedar cargas de trabalho. 
- Se você tiver dois Trabalhadores em um único pool, então um deles poderá ser usado para hospedar cargas de trabalho.
- Se você tiver 20 Trabalhadores em um único pool, então 19 deles poderão ser usados para hospedar cargas de trabalho.  
- Se você tiver 21 Trabalhadores em um único pool, então ainda assim 19 deles poderão ser usados para hospedar cargas de trabalho.  

O aspecto de tolerância a falhas é importante, mas você precisará tê-lo em mente quando dimensionar acima de determinados limites. Se quiser adicionar mais capacidade a partir de 20 instâncias, insira 22 ou mais, já que 21 não adicionam mais capacidade. O mesmo será verdadeiro se o número de instâncias for maior do que 40, onde o próximo número que adicionará capacidade será 42.

## Excluindo um Ambiente do Serviço de Aplicativo ##

Se você deseja excluir um ambiente do serviço de aplicativo, simplesmente use a ação de exclusão na parte superior da folha Ambiente do Serviço de Aplicativo. Quando você fizer isso, será solicitada a inserção do nome do seu Ambiente do Serviço de Aplicativo para confirmar a operação. OBSERVAÇÃO: quando você exclui um Ambiente do Serviço de Aplicativo, exclui todo o conteúdo dentro dele também.

![][9]

## Introdução
Todos os artigos e instruções para os Ambientes do Serviço de Aplicativo estão disponíveis no [LEIAME para Ambientes do Serviço de Aplicativo](../app-service/app-service-app-service-environments-readme.md).

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

<!---HONumber=AcomDC_0622_2016-->