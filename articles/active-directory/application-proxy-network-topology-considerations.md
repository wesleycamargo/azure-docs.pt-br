---
title: "Considerações de topologia de rede ao usar o Proxy de Aplicativo do Azure Active Directory | Microsoft Docs"
description: "Cobre as considerações de topologia de rede ao usar o Proxy de Aplicativo do Azure AD."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/12/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 4b21bf6bc1be59facd503000a4f83a56189d55d3
ms.openlocfilehash: aea1b35348bec0affe2288ff683e0320e2b0f714
ms.lasthandoff: 02/28/2017


---

# <a name="network-topology-considerations-when-using-azure-active-directory-application-proxy"></a>Considerações de topologia de rede ao usar o Proxy de Aplicativo do Azure Active Directory
> [!NOTE]
> O Proxy de Aplicativo é um recurso que está disponível somente se você tiver atualizado para a edição Premium ou Básica do Active Directory do Azure. Para obter mais informações, consulte [Edições do Active Directory do Azure](active-directory-editions.md).
>

Este artigo explica as considerações de topologia de rede ao usar o Proxy de Aplicativo do Azure AD (Azure Active Directory) para publicar e acessar seus aplicativos remotamente.

## <a name="traffic-flow"></a>Fluxo de tráfego

Quando um aplicativo é publicado por meio do Proxy de Aplicativo do Azure AD, todo o tráfego dos usuários para os aplicativos back-end de destino flui por meio dos saltos a seguir:

* Salto 1: Usuário para o ponto de extremidade público do serviço Proxy de Aplicativo do Azure AD no Azure
* Salto 2: Serviço Proxy de Aplicativo para o conector
* Salto 3: Conector para o aplicativo de destino

 ![Diagrama mostrando o fluxo de tráfego de usuário para o aplicativo de destino](./media/application-proxy-network-topologies/application-proxy-three-hops.png)

## <a name="tenant-location-and-application-proxy-service"></a>Local do locatário e serviço Proxy de Aplicativo

Quando você se inscreve para um locatário do Azure AD, a região do seu locatário é determinada pelo país especificado. Quando você habilita o Proxy de Aplicativo, as instâncias do serviço Proxy de Aplicativo de seu locatário são exibidas na mesma região que seu locatário do Azure AD, ou na região mais próxima a ele.

Por exemplo, se a região do seu locatário Azure AD é a União Europeia (EU), todos os conectores de Proxy de Aplicativo estiver usando instâncias de serviço em datacenters do Azure na UE. Isso também significa que todos os seus usuários passarão por instâncias do serviço Proxy de Aplicativo nesse local ao tentar acessar os aplicativos publicados.

## <a name="considerations-for-reducing-latency"></a>Considerações para redução da latência

Todas as soluções de proxy introduzem a latência em sua conexão de rede. Não importa a solução de proxy ou VPN escolhida como sua solução de acesso remoto, ela sempre incluirá um conjunto de servidores, permitindo a conexão dentro de sua rede corporativa.

As organizações geralmente incluem pontos de extremidade do servidor em sua rede de perímetro. Mas com o Proxy de Aplicativo do Azure AD, nenhuma rede de perímetro é necessário. Isso porque o tráfego flui por meio do serviço de proxy na nuvem, enquanto os conectores residem em sua rede corporativa.

### <a name="connector-placement"></a>Posicionamento do conector

O serviço Proxy de Aplicativo escolhe o local das instâncias para você, com base no local de seu locatário. Portanto, você pode decidir onde instalar o conector, permitindo que você defina as características de latência de seu tráfego de rede.

Ao configurar o serviço de Proxy de Aplicativo, você deve fazer as seguintes perguntas:

* Onde o aplicativo está localizado?
* Onde está localizada a maioria dos usuários que acessam o aplicativo?
* Onde a instância do Proxy de Aplicativo está localizada (isso se baseia em seu locatário)?
* Você já tem uma conexão de rede dedicada com os data centers configurados do Azure (como o Azure ExpressRoute ou uma VPN semelhante)?

O posicionamento do conector determina a latência de saltos de 2 e 3 (descrito na seção anterior). Ao avaliar o posicionamento do Conector, considere o seguinte:

* O conector precisa de uma linha de visão para um data center. Isso permite que o conector execute operações KCD (delegação restrita de Kerberos), quando você quiser SSO (logon único) para aplicativos de back-end.
* O conector é normalmente instalado mais perto do aplicativo, a fim de reduzir o tempo do conector até o aplicativo.

### <a name="general-approach-to-minimize-latency"></a>Abordagem geral para minimizar a latência

Você pode tentar minimizar a latência do tráfego de ponta a ponta otimizando cada um dos saltos de rede. Cada salto pode ser otimizado fazendo o seguinte:

* Reduzindo a distância entre as duas extremidades do salto.
* Escolhendo a rede certa para percorrer. Por exemplo, percorrer uma rede privada, em vez da Internet pública, pode ser mais rápido devido aos links dedicados.

Se você tiver um link dedicado de VPN ou do ExpressRoute entre o Azure e sua rede corporativa, convém usá-lo.

## <a name="focus-your-optimizing-strategy"></a>Concentre sua estratégia de otimização

Uma vez que os usuários podem acessar aplicativos remotamente pela Internet, concentre-se sempre na otimização dos saltos 2 e 3. A seguir, alguns dos padrões comuns que você pode incorporar.

### <a name="pattern-1-optimize-hop-3"></a>Padrão 1: Otimizar o salto 3

Para otimizar o salto 3, o conector é colocado perto do aplicativo de destino na rede do cliente. A vantagem de fazer isso é que provavelmente o conector precisa de uma linha de visão para o controlador de domínio. Essa abordagem é suficiente para a maioria dos cenários. (Na verdade, a maioria de nossos clientes segue esse padrão).

 ![Diagrama mostrando a otimização de saltos 3, com o conector colocada perto do aplicativo de destino](./media/application-proxy-network-topologies/application-proxy-hop3.png)


> [!NOTE]
Há alguns cenários nos quais você precisará otimizar os saltos 2 e 3 a fim de obter as características de latência desejadas. Por exemplo, se você tiver uma VPN ou ExpressRoute definida entre sua rede e do datacenter do Azure, você pode otimizar ambos esses saltos.
>

### <a name="pattern-2-take-advantage-of-expressroute-with-public-peering"></a>Padrão 2: Aproveitar o ExpressRoute com o emparelhamento público

Se você tiver configurado com o emparelhamento público de ExpressRoute, poderá usar a conexão de ExpressRoute mais rápida para o salto 2. (O salto 3 já é otimizado colocando o conector mais próximo ao aplicativo na sua cliente).

![Diagrama mostrando a otimização de salto 2, usando uma conexão de ExpressRoute](./media/application-proxy-network-topologies/application-proxy-expressroute-public.png)

### <a name="pattern-3-take-advantage-of-expressroute-with-private-peering"></a>Padrão 3: Aproveitar o ExpressRoute com o emparelhamento privado

Se você tiver uma configuração de VPN ou ExpressRoute dedicada com emparelhamento privado entre o Azure e sua rede corporativa, terá outra opção. Nessa configuração, a rede virtual no Azure é geralmente considerada uma extensão da rede corporativa. Portanto, você pode instalar o conector no data center do Azure e ainda atender aos requisitos de baixa latência da conexão entre o aplicativo e o conector para o salto 3.

A latência não é comprometida, pois o tráfego está fluindo por uma conexão dedicada. Você também obtém o benefício adicional de melhorar as características de latência do salto 2. Isso ocorre porque a conexão do conector de serviço de Proxy de Aplicativo agora é um salto mais curto. O conector é instalado em um local de datacenter do Azure perto de seu locatário do Azure AD (e, portanto, o Proxy de Aplicativo).

![Diagrama mostrando o conector instalado em um datacenter do Azure](./media/application-proxy-network-topologies/application-proxy-expressroute-private.png)

### <a name="other-approaches"></a>Outras abordagens

Embora o foco deste artigo é a instalação, você também pode alterar o posicionamento do aplicativo para obter os melhores características de latência.

As organizações estão cada vez mais migrando suas redes para ambientes hospedados. Isso permite colocar os aplicativos em um ambiente hospedado que também faz parte da rede corporativa, e ainda ser dentro do domínio. Nesse caso, os padrões discutidos nas seções anteriores podem ser aplicados para o novo local do aplicativo.

Considere usar grupos de conector para aplicativos de destino que estão em locais e redes diferentes. Se você estiver considerando essa opção, consulte [Serviços de Domínio do Azure AD](https://azure.microsoft.com/services/active-directory-ds).

## <a name="common-scenarios"></a>Cenários comuns

Nesta seção, percorreremos alguns casos de uso. Suponha que o locatário do Azure AD (e, portanto, ponto de extremidade de serviço de proxy) está localizado nos Estados Unidos (EUA). As considerações discutidas esses casos geralmente se aplicam também a outras regiões no mundo de uso.

### <a name="use-case-1"></a>Caso de uso 1

O aplicativo está na rede da organização nos EUA, com usuários na mesma região. Não há um ExpressRoute ou uma VPN entre o data center do Azure e a rede corporativa.

**Recomendação:** siga o padrão 1, explicado na seção anterior. Para melhorar a latência, considere o uso do ExpressRoute, se necessário.

Este é um padrão simples. Otimize o salto 3, colocando o conector perto do aplicativo. Essa também é uma opção natural, pois o conector normalmente é instalado com uma linha de visão para o aplicativo e com o data center para executar operações KCD.

![Diagrama mostrando a estrutura nos EUA e como os saltos estão organizados neste caso de uso](./media/application-proxy-network-topologies/application-proxy-pattern1.png)

### <a name="use-case-2"></a>Caso de uso 2

O aplicativo está na rede da organização nos EUA, com usuários distribuídos globalmente. Não há um ExpressRoute ou uma VPN entre o data center do Azure e a rede corporativa.

**Recomendação:** siga padrão 2, explicado na seção anterior. Para melhorar a latência, considere o uso do ExpressRoute, se necessário.

Novamente, o padrão mais comum é otimizar o salto 3, onde você coloca o conector perto do aplicativo. O salto 3 não costuma ser caro, se tudo estiver dentro da mesma região. No entanto, o salto 1 pode ser mais caro dependendo de onde o usuário estiver, pois todos os usuários acessarão a instância do Proxy de Aplicativo nos EUA. Vale a pena observar que qualquer solução de proxy tem características semelhantes sobre os usuários que estão sendo distribuídos globalmente.

![Diagrama mostrando a estrutura de continentes globais e como os saltos estão organizados neste caso de uso](./media/application-proxy-network-topologies/application-proxy-pattern2.png)

### <a name="use-case-3"></a>Caso de uso 3

O aplicativo está na rede da organização nos EUA. Há a ExpressRoute com emparelhamento público entre o Azure e a rede corporativa.

**Recomendação:** coloque o conector mais próximo possível do aplicativo. O sistema usará automaticamente o ExpressRoute para o salto 2. Isso segue o padrão 2, explicado na seção anterior.

Se o link de ExpressRoute estiver usando o emparelhamento público, o tráfego entre o proxy e o conector flui por esse link. O salto 2 tem latência otimizada.

![Diagrama mostrando a estrutura nos EUA e como os saltos estão organizados neste caso de uso](./media/application-proxy-network-topologies/application-proxy-pattern3.png)

### <a name="use-case-4"></a>Caso de uso 4

O aplicativo está na rede da organização nos EUA. Há a ExpressRoute com emparelhamento privado entre o Azure e a rede corporativa.

**Recomendação:** coloque o conector no data center do Azure que está conectado à rede corporativa por meio de emparelhamento privado de ExpressRoute. Isso segue o padrão 3, explicado na seção anterior.

O conector pode ser colocado no datacenter do Azure. Como o conector ainda tem uma linha de visão para o aplicativo e o data center por meio da rede privada, o salto 3 permanece otimizado. Além disso, o salto 2 é ainda mais otimizado.

![Diagrama mostrando a estrutura nos EUA e como os saltos estão organizados neste caso de uso](./media/application-proxy-network-topologies/application-proxy-pattern4.png)

### <a name="use-case-5"></a>Caso de uso 5

O aplicativo está na rede da organização da UE, com a maioria dos usuários nos Estados Unidos.

**Recomendação:** coloque o conector próximo ao aplicativo. Como os usuários dos Estados Unidos estão acessando uma instância do Proxy de Aplicativo que está na mesma região, o salto 1 não é muito caro. O salto 3 está otimizado. No entanto, o salto 2 é normalmente caro nesse caso de uso.

![Diagrama mostrando a estrutura de continentes globais e como os saltos estão organizados neste caso de uso](./media/application-proxy-network-topologies/application-proxy-pattern5a.png)

Considere o uso do ExpressRoute, conforme descrito nas seções anteriores sobre padrões 2 e 3.

![Diagrama mostrando a estrutura de continentes globais e como os saltos estão organizados neste caso de uso](./media/application-proxy-network-topologies/application-proxy-pattern5b.png)

Você também pode considerar o uso de uma outra variante nessa situação. Se a maioria dos usuários na organização estiver nos EUA, provavelmente sua rede se “estende” também para os EUA. Se esse for o caso, o conector poderá ser colocado nos EUA e poderá usar a linha de rede corporativa interna dedicada ao aplicativo na UE. Desta forma, os saltos 2 e 3 são otimizados.

![Diagrama mostrando a estrutura de continentes globais e como os saltos estão organizados neste caso de uso](./media/application-proxy-network-topologies/application-proxy-pattern5c.png)

## <a name="next-steps"></a>Próximas etapas
[Habilitar Proxy de aplicativo](active-directory-application-proxy-enable.md)<br>
[Habilitar o logon único](active-directory-application-proxy-sso-using-kcd.md)<br>
[Habilitar o acesso condicional](active-directory-application-proxy-conditional-access.md)<br>
[Solucionar problemas que surgirem com o Proxy de Aplicativo](active-directory-application-proxy-troubleshoot.md)

