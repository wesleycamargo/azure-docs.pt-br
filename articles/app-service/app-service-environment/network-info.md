---
title: "Considerações de rede com um Ambiente do Serviço de Aplicativo do Azure"
description: "Explica o tráfego de rede do ASE e como definir NSGs e UDRs com seu ASE"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2017
ms.author: ccompy
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: d19590c23da43e08ea42cd278347e01d87433a58
ms.contentlocale: pt-br
ms.lasthandoff: 06/26/2017

---
# <a name="networking-considerations-for-an-app-service-environment"></a>Considerações de rede para um Ambiente do Serviço de Aplicativo #

## <a name="overview"></a>Visão geral ##

O [ASE][Intro] (Ambiente do Serviço de Aplicativo) é uma implantação do Serviço de Aplicativo do Azure em uma sub-rede da VNET (Rede Virtual) do Azure.  Há dois tipos de implantação para o ASE:

- _ASE externo_: expõe os aplicativos ASE hospedados em um endereço IP que pode ser acessado pela Internet. Para saber mais, confira [Criar um ASE externo][MakeExternalASE].
- _ASE ILB_: expõe os aplicativos hospedados no ASE em um endereço IP dentro de sua Vnet (rede virtual) do Azure.  O ponto de extremidade interno é um ILB (Load Balancer Interno) e, por isso, ele é chamado de ASE ILB. Para saber mais, confira [Criar e usar um ASE ILB][MakeILBASE].

No momento, existem duas versões do ASE. A versão inicial do ASE é chamada ASEv1 e a versão mais recente é chamada ASEv2. Para saber mais sobre o ASEv1, consulte [Introdução ao Ambiente do Serviço de Aplicativo v1][ASEv1Intro]. Um ASEv1 pode ser implantado em uma VNet clássica ou do Gerenciador de Recursos. Um ASEv2 só pode ser implantado em uma VNet do Gerenciador de Recursos.

Todas as chamadas de um ASE que vão para a Internet deixam a rede virtual por meio de um VIP atribuído ao ASE. O IP público desse VIP é o IP de origem para todas as chamadas do ASE que vão para a Internet.  Se os aplicativos em seu ASE fazem chamadas para recursos na sua rede virtual ou por uma VPN, o IP de origem será um dos IPs na sub-rede usada pelo ASE.  Como o ASE é na VNet, também pode acessar recursos na VNet sem nenhuma configuração adicional. Se a VNet está conectada à sua rede local, ela também tem acesso a recursos de lá sem nenhuma configuração adicional do ASE ou de seu aplicativo.

![][1] 

Se você tiver um ASE externo, esse VIP público também é o ponto de extremidade que seus aplicativos ASE resolverão para HTTP/S, FTP/S, implantação da Web e depuração remota.

![][2]

Se você tiver um ASE ILB, o endereço IP do ILB é o ponto de extremidade HTTP/S, FTP/S, de implantação da Web e depuração remota.

As portas de acesso normais do aplicativo são:

| Uso | Da | Para |
|----------|---------|-------------|
|  HTTP/HTTPS  | Configurável pelo usuário |  80, 443 |
|  FTP/FTPS    | Configurável pelo usuário |  21, 990, 10001-10020 |
|  Depuração remota no Visual Studio  |  Configurável pelo usuário |  4016, 4018, 4020, 4022 |

Isso é verdadeiro se você estiver em um ASE externo ou em um ASE ILB. Se você estiver usando um ASE externo, essas são as portas em que você chega no VIP público. Se você estiver usando um ASE ILB, essas portas são alcançadas no ILB. Observe que se você bloquear a porta 443, poderá causar impacto em alguns recursos apresentados no portal. Para saber mais, confira [Dependências do portal](#portaldep).

## <a name="ase-dependencies"></a>Dependências do ASE ##

Uma dependência de acesso de entrada do ASE é:

| Uso | Da | Para |
|-----|------|----|
| Gerenciamento | Internet | Sub-rede ASE: 454, 455 |
|  Comunicação interna ASE | Sub-rede ASE: todas as portas | Sub-rede ASE: todas as portas
|  Permitir a entrada do Azure Load Balancer | Azure Load Balancer | Qualquer

O tráfego de entrada fornece o comando e o controle do ASE, além de monitoramento do sistema. Os IPs de origem para esse tráfego não são constantes. Isso significa que a configuração de segurança de rede deve permitir o acesso de todos os IPs nas portas 454 e 455.

Para acesso de saída, um ASE depende de vários sistemas externos. Essas dependências de sistema são definidas com nomes DNS e não são mapeadas para um conjunto fixo de endereços IP. Isso significa que o ASE requer acesso de saída da sub-rede ASE para todos os IPs externos em uma variedade de portas. Um ASE tem as seguintes dependências de saída:

| Uso | Da | Para |
|-----|------|----|
| Armazenamento do Azure | Sub-rede ASE | table.core.windows.net, blob.core.windows.net, queue.core.windows.net, file.core.windows.net: 80, 443, 445 (445 só é necessário para o ASEv1) |
| Banco de Dados SQL | Sub-rede ASE | database.windows.net: 1433, 11000-11999, 14000-14999 (Para saber mais, confira [Uso da porta do Banco de Dados SQL V12](../../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md))|
| Gerenciamento do Azure | Sub-rede ASE | management.core.windows.net, management.azure.com: 443 
| Verificação de certificado SSL |  Sub-rede ASE            |  ocsp.msocsp.com, mscrl.microsoft.com, crl.microsoft.com: 443
| Azure Active Directory        | Sub-rede ASE            |  Internet: 443
| Gerenciamento do Serviço de Aplicativo        | Sub-rede ASE            |  Internet: 443
| DNS do Azure                     | Sub-rede ASE            | Internet: 53
| Comunicação interna ASE    | Sub-rede ASE: todas as portas | Sub-rede ASE: todas as portas

Se o ASE perde o acesso a essas dependências, ele deixa de funcionar. Quando isso acontece por muito tempo, o ASE fica suspenso.

**DNS do cliente**

Se a VNet é configurada com um servidor DNS definido pelo cliente, as cargas de trabalho de locatário usarão esse servidor DNS. O ASE ainda precisa se comunicar com o DNS do Azure para fins de gerenciamento. 

Se a VNet é configurada com um cliente DNS no outro lado de uma VPN, o servidor DNS deve poder ser acessado da sub-rede que contém o ASE.

<a name="portaldep"></a>
## <a name="portal-dependencies"></a>Dependências do portal ##

Além das dependências funcionais que um ASE tem, há alguns itens adicionais relacionadas à experiência do portal. Alguns dos recursos no portal do Azure dependem do acesso direto ao _site do SCM_. Para cada aplicativo no Serviço de Aplicativo do Azure, há duas URLs. A primeira URL é para acessar seu aplicativo. A segunda URL é para acessar o site do SCM, que também é chamado de _console Kudu_. Alguns dos recursos que usam o site do SCM incluem:

-   Trabalhos da Web
-   Funções
-   Logstream
-   Kudu
-   Extensões
-   Gerenciador de Processos
-   Console

Isso é mais difícil ao usar um ASE ILB, já que o site do SCM não pode ser acessado pela Internet fora da VNet. Por esse motivo, os recursos que dependem do acesso do site do SCM ficam esmaecidos no portal do Azure quando seu aplicativo está hospedado em um ASE ILB.

Muitos desses recursos que dependem do site do SCM também estão disponíveis diretamente no console Kudu. Você pode se conectar diretamente a ele em vez de usar o portal. Se seu aplicativo estiver hospedado em um ASE ILB, você precisará fazer logon usando suas credenciais de publicação. A URL para acessar o site do SCM de um aplicativo hospedado em um ASE ILB tem o seguinte formato: 

```
<appname>.scm.<domain name the ILB ASE was created with> 
```

Portanto, se o ASE ILB é o nome de domínio *contoso.net* e o nome do aplicativo é *testapp*, meu aplicativo é acessado em *testapp.contoso.net* e o site SCM que o acompanha é acessado em *testapp.scm.contoso.net*.

## <a name="ase-ip-addresses"></a>Endereços IP do ASE ##

Um ASE precisa conhecer alguns endereços IP. Eles são:

- Endereço IP público de entrada – usado para o tráfego de aplicativo em um ASE externo e o tráfego de gerenciamento em um ASE externo e em um ASE ILB.
- IP público de saída - usado como o IP “de” das conexões de saída do ASE que deixam a VNet, que não são roteadas por uma VPN.
- Endereço IP ILB - se você estiver usando um ASE ILB.
- Endereços SSL com base em IP atribuídos ao aplicativo - possível somente com um ASE externo e quando o SSL baseado em IP está configurado.

Todos esses endereços IP ficam visíveis em um ASEv2 no portal do Azure, na interface do usuário do ASE. Se você tiver um ASE ILB, o IP para o ILB estará listado.

![][3]

**Endereços IP atribuídos ao aplicativo**

Com um ASE externo, você pode atribuir endereços IP a aplicativos individuais. Você não pode fazer isso com um ASE ILB. Para saber mais sobre como configurar seu aplicativo para ter seu próprio endereço IP, confira [Associar um certificado SSL personalizado existente a aplicativos Web do Azure](../../app-service-web/app-service-web-tutorial-custom-ssl.md).

Quando um aplicativo tem seu próprio endereço SSL com base em IP, o ASE reserva duas portas para mapear para esse endereço IP. Uma porta é para o tráfego HTTP e a outra é para HTTPS. Essas portas estão listadas na interface de usuário do ASE na seção de endereços IP. O tráfego deve ser capaz de alcançar essas portas do VIP ou os aplicativos ficarão inacessíveis. Isso é importante para se ter em mente ao configurar Grupos de Segurança de Rede.

## <a name="network-security-groups"></a>Grupos de segurança de rede ##

Os [NSGs] [ NSGs] (Grupos de Segurança de Rede) permitem controlar o acesso de rede em uma VNet. Quando você usa o portal, há uma regra de negação implícita com a prioridade mais baixa para negar tudo e permitir que as regras de permissão sejam as que você criar.

Em um ASE, você não tem acesso às VMs usadas para hospedar o ASE em si. Elas estão em uma assinatura da Microsoft gerenciada. Se você quiser restringir o acesso aos aplicativos no ASE, precisará definir NSGs na sub-rede do ASE. Dessa forma, preste mais atenção nas dependências do ASE. Se você bloquear todas as dependências, o ASE vai parar de funcionar.

Os NSGs podem ser configurados por meio do portal do Azure ou por meio do PowerShell. Para simplificar, as informações aqui mostram o portal do Azure. Crie e gerencie os NSGs no portal como um recurso de nível superior em **Rede**.

Levando em conta os requisitos de entrada e saídos, os NSGs devem ser semelhantes ao que é mostrado abaixo. Neste exemplo, o intervalo de endereços da VNet é _192.168.250.0/16_ e a sub-rede em que o ASE está é _192.168.251.128/25_.

Os primeiro dois requisitos de entrada para que o ASE funcione estão no início deste exemplo. Eles habilitam o gerenciamento do ASE e permitem que o ASE se comunique com ele mesmo. As outras entradas podem ser configuradas pelo locatário e podem controlar o acesso de rede aos aplicativos do ASE hospedados.   

![][4]

Além de uma regra padrão para habilitar a comunicação dos IPs na VNet com a sub-rede do ASE, também há uma regra padrão que permite que o balanceador de carga, também conhecido como o VIP público, se comunique com o ASE.  Você pode ver as regras padrão clicando em *Regras padrão* ao lado do ícone *Adicionar*. Se você colocar uma regra para negar todo o resto após as regras de NSG mostradas, impedirá o tráfego entre o VIP e o ASE. Se você quiser impedir tráfego proveniente da VNet, adicione sua própria regra para permitir a entrada com uma origem igual a AzureLoadBalancer, com um destino Qualquer e um intervalo de portas \*.  Como a regra de NSG está sendo aplicada apenas à sub-rede ASE, você não precisa ser específico em relação ao destino.

Se você atribuiu um endereço IP ao seu aplicativo, também precisará manter as portas usadas com ele aberto. Você pode ver as portas usadas na interface do usuário **Ambiente do Serviço de Aplicativo** > **Endereços IP**.  

Tudo, exceto o último item mostrado nas regras de saída descritas abaixo, é obrigatório. Isso permite o acesso de rede às dependências do ASE que foram observadas anteriormente neste documento. Se você bloquear um deles, seu ASE vai parar de funcionar. O último item na lista permite que seu ASE se comunique com outros recursos em sua VNet.

![][5]

Depois que seus NSGs são definidos, você precisa atribuí-los à sub-rede em que está seu ASE. Se você não lembrar a rede ou sub-rede do ASE, poderá ver isso no portal de gerenciamento do ASE. Para atribuir o NSG à sua sub-rede, vá para a sub-rede da interface do usuário e selecione o NSG.

## <a name="routes"></a>Rotas ##

As rotas costumam se tornar problemáticas quando você configura a rede virtual com o ExpressRoute. Há três tipos de rotas em uma VNet do Azure. Existem:

-   Rotas do sistema
-   Rotas BGP
-   UDRs (Rotas Definidas pelo Usuário)

Rotas de BGP que substituem as rotas do sistema. UDRs que substituem as rotas de BGP. Para saber mais sobre rotas de redes virtuais do Azure, confira [Visão geral das rotas definidas pelo usuário][UDRs]

O banco de dados SQL que o ASE usa para gerenciar o sistema tem uma lista de permissões e requer que a comunicação venha do VIP público do ASE. Se as respostas às solicitações de gerenciamento são enviadas pelo ExpressRoute, o endereço de resposta é diferente do de destino, o que interrompe a comunicação TCP.

Isso significa que, para que seu ASE funcione quando a rede virtual está configurada com um ExpressRoute, o mais fácil a fazer é:

-   Configure o ExpressRoute para anunciar _0.0.0.0/0_ que, por padrão, encapsula à força todo o tráfego de saída no local.
-   Criar uma UDR e aplicá-la à sub-rede que contém o Ambiente do Serviço de Aplicativo, com um prefixo de endereço _0.0.0.0/0_ e o próximo salto do tipo _Internet_.

Se você fizer essas duas alterações, o tráfego destinado à Internet, que é originado da sub-rede do ASE, não será forçado pelo ExpressRoute e o ASE poderá funcionar. 

> [!IMPORTANT]
> As rotas definidas em uma UDR devem ser específicas o suficiente para ter precedência sobre todas as rotas anunciadas pela configuração do ExpressRoute. O exemplo anterior usa o intervalo de endereços 0.0.0.0/0 amplo, e potencialmente pode ser substituído por acidente pelos anúncios de rota que usam intervalos de endereços mais específicos.
>

Não há suporte para Ambientes de Serviço de Aplicativo com configurações do ExpressRoute que cruzem anúncios de rotas do caminho de emparelhamento público para o caminho de emparelhamento privado. As configurações do ExpressRoute com emparelhamento público definido receberão anúncios de rota da Microsoft para um grande conjunto de intervalos de endereços IP do Microsoft Azure. Se esses intervalos de endereços forem anunciados de modo cruzado no caminho de emparelhamento particular, o resultado final será que todos os pacotes de saída de rede da sub-rede do Ambiente do Serviço de Aplicativo serão encapsulados à força em uma infraestrutura de rede local do cliente. Esse fluxo de rede não tem suporte no momento com os Ambientes de Serviço de Aplicativo. Uma solução para esse problema é parar as rotas de anúncios cruzados do caminho de emparelhamento público para o caminho de emparelhamento particular.

Para criar uma UDR conforme descrito:

1.  Vá para o portal do Azure > **rede** > **Tabelas de Rotas**.
2.  Crie uma nova Tabela de Rota na mesma região da sua VNet.
3.  De dentro de sua interface de usuário de Tabela de Rota, selecione **Rotas** > **Adicionar**.
4.  Defina o **Tipo do próximo salto** como **Internet**, o **Prefixo de endereço** como _0.0.0.0/0_ e clique em **Salvar**.

Você verá algo semelhante a:

![][6]

Depois de criar a nova Tabela de Rota, vá para a sub-rede que contém seu ASE. Selecione sua Tabela de Rota da lista obtida no portal. Depois de salvar a alteração, você deve ver os NSGs e as Rotas anotadas com a sua sub-rede.

![][7]

### <a name="deploying-into-existing-azure-virtual-networks-that-are-integrated-with-expressroute"></a>Implantando em redes virtuais existentes do Azure integradas com o ExpressRoute ###

Se você quiser implantar seu ASE em uma rede virtual que já está integrada com o ExpressRoute, precisará configurar previamente a sub-rede em que deseja implantar o ASE e implantá-la usando um modelo do Resource Manager. Para criar um ASE em uma rede virtual que já tem o ExpressRoute configurado:

- Crie uma sub-rede para hospedar o ASE.

    > [!NOTE]
    > Nada mais pode estar na sub-rede além do ASE e não deixe de escolher um espaço de endereço grande o suficiente para permitir o crescimento, pois ela não poderá ser alterada posteriormente. Um `/25` com 128 endereços é o tamanho recomendado.
- Crie UDRs (ou seja, Tabelas de Rota) conforme descrito anteriormente e defina-as na sub-rede.
- Criar o ASE usando um modelo do Resource Manager conforme descrito aqui: [Criando um ASE usando um modelo do ARM][MakeASEfromTemplate].

<!--Image references-->
[1]: ./media/network_considerations_with_an_app_service_environment/networkase-overflow.png
[2]: ./media/network_considerations_with_an_app_service_environment/networkase-overflow2.png
[3]: ./media/network_considerations_with_an_app_service_environment/networkase-ipaddresses.png
[4]: ./media/network_considerations_with_an_app_service_environment/networkase-inboundnsg.png
[5]: ./media/network_considerations_with_an_app_service_environment/networkase-outboundnsg.png
[6]: ./media/network_considerations_with_an_app_service_environment/networkase-udr.png
[7]: ./media/network_considerations_with_an_app_service_environment/networkase-subnet.png

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
[ConfigureSSL]: ../../app-service-web/web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../../app-service-web/web-sites-deploy.md
[ASEWAF]: ../../app-service-web/app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md

