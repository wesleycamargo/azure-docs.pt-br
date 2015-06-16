<properties 
	pageTitle="Como configurar um Ambiente de Serviço de Aplicativo" 
	description="Configuração, gerenciamento e monitoramento de ambientes de serviço de aplicativo" 
	services="app-services\web" 
	documentationCenter="" 
	authors="ccompy" 
	manager="stefsch" 
	editor=""/>

<tags 
	ms.service="app-services-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/27/2015" 
	ms.author="ccompy"/>

# Configurando um Ambiente de Serviço de Aplicativo #

## Visão geral ##

Ambiente de Serviço de Aplicativo é um novo recurso de nível Premium que está sendo oferecido no modo de Visualização. Ele oferece novos recursos de acesso a rede e de dimensionamento. Esse novo recurso de escala permite que você coloque uma instância do serviço de aplicativo do Azure em sua VNET. Se você não estiver familiarizado com a capacidade do ASE (Ambiente de Serviço de Aplicativo), leia o documento aqui [O que é um ambiente de serviço de aplicativo][WhatisASE]. Para obter informações sobre como criar um ASE, leia o documento aqui [Como criar um ambiente de serviço de aplicativo][HowtoCreateASE].

Em um nível elevado, um ambiente de serviço de aplicativo consiste em vários componentes principais:

- Recursos de computação em execução no serviço hospedado do Ambiente de Aplicativo do Azure
- Armazenamento
- Banco de dados
- Rede virtual com pelo menos uma sub-rede
- Sub-rede na qual está em execução o serviço hospedado do ambiente de aplicativo do Azure

Para ajudar a gerenciar e monitorar seus ambientes de serviço de aplicativo, você pode acessar a interface do usuário para essa finalidade em Procurar -> Ambientes de Serviço de Aplicativo, no portal do Azure. A versão inicial tem o que você precisa para gerenciar o sistema e continuará a melhorar com recursos adicionais nas próximas semanas.

![][1]

## Monitoramento ##

Não existem muitos recursos de métrica disponíveis na versão de visualização inicial, mas outros deles serão lançados em breve. Esses recursos de métrica ajudarão os administradores de sistema a tomar decisões sobre as operações e o dimensionamento do sistema.

Nesse instante, no portal, você pode listar todos os planos de serviço de aplicativo no ASE, bem como todos os aplicativos Web no ambiente de serviço de aplicativo. Para ver a lista, vá para Configurações e selecione o item em que você está interessado.

![][3]

Nas duas listas, você pode ver a atribuição de Pool de trabalho com o número de instâncias e o tamanho do recurso de computação que está sendo usado. Detalhes sobre o desempenho em um plano de serviço de aplicativo individual estarão disponíveis assim como na utilização normal, que é abrir a interface de usuário do plano de serviço de aplicativo.

![][4]

## Recursos de computação ##

Os recursos de computação, armazenamento e banco de dados são todos operados pelo serviço de aplicativo do Azure. Todavia, a quantidade e os tamanhos dos recursos de computação são decididos pelo usuário.

Independentemente do tamanho dos recursos de computação, os requisitos mínimos são 2 servidores Front-End e 2 processadores. Um ambiente de serviço de aplicativo pode ser configurado para usar até um total de 55 recursos de computação. Desses 55 recursos de computação, apenas 50 podem ser usados para cargas de trabalho do host. Há duas razões para isso. Há um mínimo de 2 recursos de computação Front-End. Com isso, restam até 53 para dar suporte a alocação do pool de trabalho. Para oferecer tolerância a falhas, no entanto, você precisa ter um recurso de computação adicional alocado de acordo com as seguintes regras:

- cada pool de trabalho precisa de pelo menos um recurso de computação adicional ao qual nenhuma carga de trabalho pode ser atribuída
- quando a quantidade de recursos de computação em um pool ultrapassa um determinado valor, outro recurso de computação torna-se necessário

Dentro de qualquer pool de trabalho individual, os requisitos de tolerância a falhas são aqueles para um determinado valor de X recursos atribuídos a um pool de trabalho:

- se X estiver entre 2 a 20, a quantidade de recursos de computação utilizáveis que você pode usar para cargas de trabalho é X-1
- se X estiver entre 21 a 40, a quantidade de recursos de computação utilizáveis que você pode usar para cargas de trabalho é X-2
- se X estiver entre 41 a 53, a quantidade de recursos de computação utilizáveis que você pode usar para cargas de trabalho é X-3

Além de ser capaz de gerenciar a quantidade de recursos de computação que você pode atribuir a um determinado pool, você também tem controle sobre o tamanho. Com ambientes de serviços de aplicativo, você pode escolher entre 4 tamanhos diferentes rotulados de P1 a P4. Para obter detalhes sobre esses tamanhos e seus preços, consulte aqui [Preços de serviços de aplicativo][AppServicePricing]. Os tamanhos de recursos de computação de P1 a P3 são os mesmos que aqueles normalmente disponíveis. O recurso de computação P4 proporciona 8 núcleos com 14 GB de RAM e só está disponível em um ambiente de serviço de aplicativo.

Conforme observado anteriormente, o recurso de ambiente de serviço de aplicativo está atualmente em modo de Visualização e, como tal, ele ainda tem espaço para crescer. Além de recursos adicionais de monitoramento, mais recursos de gerenciamento serão lançados conforme os ambientes de Serviço de Aplicativo movem-se para GA. Por enquanto, há apenas algumas coisas que podem ser gerenciadas nessa interface:

- Número de recursos de computação em cada pool
- Tamanho dos recursos de computação em cada pool
- Número de endereços IP disponíveis

Para controlar essas coisas, selecione o item de configuração Escala na parte superior.

![][2]

A quantidade de recursos de computação em cada pool e seus tamanhos podem ser ajustados aqui. Antes de fazer alterações, no entanto, é importante observar algumas coisas:

- as alterações feitas podem levar horas para a conclusão, dependendo de quão grande é a alteração solicitada
- quando já houver uma alteração de configuração do ambiente de serviço de aplicativo em andamento, você não pode iniciar outra alteração
- se você alterar o tamanho dos recursos de computação usados em um pool de trabalho, poderá causar interrupções em aplicativos Web em execução nesse pool de trabalho

Adicionar mais instâncias a um pool de trabalho é uma operação benigna e não tem impacto no sistema. No entanto, alterar o tamanho do recurso de computação usado em um pool de trabalho é a outra história. Para evitar qualquer tempo de inatividade em aplicativos durante uma alteração de tamanho em um pool de trabalho, é melhor:

- usar um pool de trabalho não utilizado para trazer as instâncias requeridas no tamanho desejado
- dimensionar os planos de serviço de aplicativo para o novo pool de trabalho.  
 
Isso é muito menos perturbador para os aplicativos do que alterar o tamanho de recursos de computação com cargas de trabalho em execução. Para detalhes sobre o dimensionamento de aplicativos Web em um ambiente de serviço de aplicativo, acesse aqui [Dimensionamento de aplicativos Web em um ambiente de serviço de aplicativo][HowtoScale]

## Rede Virtual ##

A [rede Virtual][virtualnetwork] e sub-rede estão todas sob controle do usuário. Os ambientes de aplicativo de serviço têm alguns requisitos de rede, mas o resto deve ser controlado pelo usuário. Esses requisitos de ASE são:

- uma VNET com pelo menos 512 endereços
- uma sub-rede com pelo menos 256 endereços 
- a VNET deve ser uma VNET regional  
 
A administração de sua VNET é realizada por meio da interface de usuário normal de rede Virtual.

Já que esse recurso coloca o serviço de aplicativo do Azure em seu VNET, isso significa que seus aplicativos hospedados em seu ASE agora podem acessar diretamente recursos disponibilizados por meio de VPNs Site a Site ou Rota Expressa. Os aplicativos em seus ambientes de serviço de aplicativo não exigem recursos de rede adicionais para acessar os recursos disponíveis para a VNET hospedando seu ambiente de serviço de aplicativo.

Se desejado, você também pode controlar o acesso usando grupos de segurança de rede. Esse recurso permite que você bloqueie seu ambiente de serviço de aplicativo, permitindo acesso apenas aos endereços IP para os quais você deseja restringi-lo. Para obter mais informações sobre como fazer isso, consulte o documento aqui [Como Controlar o Tráfego de Entrada em um Ambiente de Serviço de Aplicativo][ControlInbound].

## Excluindo um Ambiente de Serviço de Aplicativo ##

Se você deseja excluir um ambiente de serviço de aplicativo, simplesmente use a ação de exclusão na parte superior da folha Ambiente de Serviço de Aplicativo. Porém, você não pode excluir um ASE que ainda tenha conteúdo. Certifique-se de remover todos os aplicativos Web e planos de serviço de aplicativo para então excluir o seu ambiente de serviço de aplicativo.

## Introdução

Para se familiarizar com os Ambientes de Serviço de Aplicativo, consulte [Como Criar um Ambiente de Serviço de Aplicativo][HowtoCreateASE]

Para obter mais informações sobre a plataforma de Serviço de Aplicativo do Azure, consulte [Serviço de Aplicativo do Azure][AzureAppService].


<!--Image references-->
[1]: ./media/app-service-web-configure-an-app-service-environment/configureaseblade.png
[2]: ./media/app-service-web-configure-an-app-service-environment/configurescale.png
[3]: ./media/app-service-web-configure-an-app-service-environment/configureasplist.png
[4]: ./media/app-service-web-configure-an-app-service-environment/configurewebapplist.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment-in-an-ase/
[HowtoScale]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-scale-a-web-app-in-an-app-service-environment/
[ControlInbound]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[virtualnetwork]: https://msdn.microsoft.com/library/azure/dn133803.aspx
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
<!--HONumber=52-->
 