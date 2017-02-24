---
title: "Considerações de topologia de rede ao usar o Proxy de Aplicativo do Azure AD | Microsoft Docs"
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
ms.sourcegitcommit: 1fdc00865f31352026a64c2769d9d65047bb2fa6
ms.openlocfilehash: 26ec9167f1f883c3c71947a55142020fb48e206a


---

# <a name="network-topology-considerations-when-using-azure-ad-application-proxy"></a>Considerações de topologia de rede ao usar o Proxy de Aplicativo do Azure AD
> [!NOTE]
> O Proxy de Aplicativo é um recurso que está disponível somente se você tiver atualizado para a edição Premium ou Básica do Active Directory do Azure. Para obter mais informações, consulte [Edições do Active Directory do Azure](active-directory-editions.md).
> 

Este artigo explica as considerações de topologia de rede ao usar o Proxy de Aplicativo do Azure AD para publicar e acessar seus aplicativos remotamente. 

## <a name="traffic-flow"></a>Fluxo de tráfego

Quando um aplicativo é publicado por meio do Proxy de Aplicativo do Azure AD, todo o tráfego dos usuários para os aplicativos back-end de destino flui por meio dos saltos a seguir:

* Salto 1: Usuário para o ponto de extremidade público do serviço Proxy de Aplicativo do Azure AD no Azure
* Salto 2: Serviço Proxy de Aplicativo para o conector
* Salto 3: Conector para o aplicativo de destino

 ![Fornecedores de várias nuvens de IaaS do AzureAD](./media/application-proxy-network-topologies/application-proxy-three-hops.png)

## <a name="tenant-location-and-app-proxy-service"></a>Localização do locatário e serviço Proxy de Aplicativo

Quando você se inscreve para um locatário do Azure AD, a região do seu locatário (EUA, EMEA, APAC etc.) é determinada com base no país especificado. Quando você habilita o Proxy de Aplicativo, as instâncias do serviço Proxy de Aplicativo de seu locatário são exibidas na mesma região que seu locatário do Azure AD, ou na região mais próxima a ele. 

Por exemplo, se a região do seu Locatário do Azure AD for UE (União Europeia), todos os seus conectores do Proxy de Aplicativo do Azure AD serão conectados com as instâncias do serviço Proxy de Aplicativo em data centers do Azure na UE. Isso também significa que todos os seus usuários passarão por instâncias do serviço Proxy de Aplicativo nesse local ao tentar acessar os aplicativos publicados.

## <a name="considerations-for-reducing-latency"></a>Considerações para redução da latência

Todas as soluções de proxy introduzirão a latência em sua conexão de rede. Não importa a solução de proxy ou VPN escolhida como sua solução de acesso remoto, ela sempre incluirá um conjunto de servidores, permitindo a conexão dentro de sua rede corporativa. 

As empresas normalmente incluem pontos de extremidade do servidor na DMZ (zona desmilitarizada) da rede. Mas com o Proxy de Aplicativo do Azure AD, nenhuma DMZ é necessária.  Isso porque, com o Proxy de Aplicativo, o tráfego flui por meio do serviço de proxy na nuvem, enquanto os conectores residem em sua rede corporativa.

### <a name="connector-placement"></a>Posicionamento do conector

O serviço Proxy de Aplicativo escolhe o local das instâncias para você, com base no local de seu locatário. Portanto, você pode decidir onde instalar o conector, permitindo que você defina as características de latência de ponta a ponta de seu tráfego de rede.

Ao configurar o serviço Proxy de Aplicativo, estas são algumas das perguntas que você deve fazer:

* Onde o aplicativo está localizado?
* Onde está localizada a maioria dos usuários que acessa o aplicativo?
* Onde a instância do Proxy de Aplicativo está localizada (isso tem base em seu Locatário)?
* Você já tem uma conexão de rede dedicada com os Data Centers do Azure (como Express Route ou uma VPN semelhante definida)?

O posicionamento do conector determinará a latência do salto nº 2 e do salto nº3. Ao avaliar o posicionamento do Conector, considere o seguinte:

* O conector precisa de uma linha de visão para um data center a fim de executar operações KCD (delegação restrita de Kerberos), quando você quiser SSO (logon único) para aplicativos de back-end.
* O conector é normalmente instalado mais perto do aplicativo, a fim de reduzir o tempo do conector até o aplicativo.

### <a name="general-approach-to-minimize-latency"></a>Abordagem geral para minimizar a latência

Você pode tentar minimizar a latência do tráfego de ponta a ponta otimizando cada um dos saltos de rede, para que o tráfego flua sobre eles.

Cada salto pode ser otimizado fazendo o seguinte:

* Reduzindo a distância entre as duas extremidades do salto.
* Escolhendo a rede certa para percorrer. Por exemplo, percorrer uma rede privada, em vez da Internet pública, pode ser mais rápido devido aos links dedicados.
 
Se você tiver um link dedicado de VPN/Express Route entre o Azure e sua rede corporativa, convém aproveitá-lo.

## <a name="focus-your-optimizing-strategy"></a>Concentre sua estratégia de otimização

Uma vez que os usuários podem acessar aplicativos remotamente pela Internet, concentre-se sempre na otimização dos saltos 2 e 3. Veja abaixo alguns dos padrões comuns que você pode incorporar.

### <a name="pattern-1-optimize-hop-3"></a>Padrão nº 1: Otimizar o salto nº 3:

Para otimizar o salto 3, o conector é colocado perto do aplicativo de destino na rede do cliente. A vantagem de fazer isso é que provavelmente o conector precisa de uma linha de visão para o Controlador de domínio, conforme mencionado acima. Essa abordagem costuma ser suficiente para a maioria dos clientes e cenários. A maioria de nossos clientes segue esse padrão.

 ![Fornecedores de várias nuvens de IaaS do AzureAD](./media/application-proxy-network-topologies/application-proxy-hop3.png)


> [!NOTE]
Há alguns cenários nos quais você precisará otimizar os saltos 2 e 3 a fim de obter as características de latência desejadas. Por exemplo, se você tiver uma configuração de VPN ou ExpressRoute entre sua rede e o datacenter do Azure, este cenário permitirá a otimização do salto 2, além do salto 3.
>

### <a name="pattern-2-take-advantage-of-expressroute-with-public-peering"></a>Padrão de nº 2: Aproveitar a ExpressRoute com o emparelhamento público

Se você tiver uma configuração de ExpressRoute com emparelhamento público, aproveitaremos a conexão de ExpressRoute mais rápida para o salto nº 2. O salto nº 3 já é otimizado colocando o conector mais próximo ao aplicativo na rede do cliente.

![Fornecedores de várias nuvens de IaaS do AzureAD](./media/application-proxy-network-topologies/application-proxy-expressroute-public.png)

### <a name="pattern-3-taking-advantage-expressroute-with-private-peering"></a>Padrão nº 3: Aproveitando a ExpressRoute com o emparelhamento privado

Se você tiver uma configuração de VPN ou ExpressRoute dedicada com emparelhamento privado entre o Azure e sua rede corporativa no qual o aplicativo está instalado, terá outra opção. Nessa configuração, a rede virtual no Azure é geralmente considerada uma extensão da rede corporativa. Portanto, você pode instalar o conector no datacenter do Azure e ainda atender aos requisitos de baixa latência da conexão entre o aplicativo e o conector para o salto 3. 

A latência não é comprometida, pois o tráfego está fluindo por uma conexão dedicada. No entanto, você tem o benefício adicional de melhorar as características de latência do salto 2. Isso ocorre porque a conexão entre o conector e o serviço Proxy (salto 2) é um salto mais curto, pois o conector está instalado em um datacenter do Azure próximo ao seu locatário do AAD (e, portanto, do Proxy de Aplicativo).

![Fornecedores de várias nuvens de IaaS do AzureAD](./media/application-proxy-network-topologies/application-proxy-expressroute-private.png)

### <a name="other-approaches"></a>Outras abordagens

O foco deste artigo até agora foi no posicionamento do conector. No entanto, se você tiver a opção de mover o aplicativo (por exemplo, para o Azure ou outro ambiente hospedado), o posicionamento do aplicativo poderá ser alterado para conseguir as melhores características de latência. 

As organizações estão cada vez mais migrando suas redes para ambientes hospedados. Isso permite colocar os aplicativos no ambiente hospedado que também faz parte da rede corporativa, e ainda ser dentro do domínio. Nesse caso, os padrões acima podem ser aplicados ao novo local do aplicativo.

Considere usar grupos de conector para aplicativos de destino que estão em locais e redes diferentes. Se você estiver considerando essa opção, consulte [Serviços de Domínio do Azure AD](https://azure.microsoft.com/en-us/services/active-directory-ds). 

## <a name="common-scenarios"></a>Cenários comuns

Nesta seção, percorreremos alguns casos de uso. Para todos os casos de uso abaixo, suponha que o locatário do Azure AD (e, portanto, o ponto de extremidade do serviço de proxy) esteja nos EUA. Em outras regiões do mundo, as mesmas considerações geralmente serão aplicada.

### <a name="use-case-1"></a>Caso de uso 1

O aplicativo está na rede do cliente nos EUA, com usuários na mesma região. Não há uma ExpressRoute ou VPN entre e Azure DC e a rede corporativa.

**Recomendação:** siga o caso de uso 1 acima. Para melhorar a latência, considere a ExpressRoute, se for necessário <veja os casos de uso 3 e 4>.

Este é um padrão simples. O padrão mais comum é otimizar o salto 3, no qual o conector é colocado perto do aplicativo. Essa também é uma opção natural, pois o conector normalmente é instalado com uma linha de visão para o aplicativo e com o controlador de domínio para executar operações KCD.
Esse caso de uso segue o padrão 1 abaixo.

![Fornecedores de várias nuvens de IaaS do AzureAD](./media/application-proxy-network-topologies/application-proxy-pattern1.png)

### <a name="use-case-2"></a>Caso de uso 2

O aplicativo está na rede do cliente nos EUA, com usuários espalhados no mundo todo. Não há uma ExpressRoute ou VPN entre e Azure DC e a rede corporativa.

**Recomendação:** siga o caso de uso 2 acima. Para melhorar a latência, considere a ExpressRoute, se for necessário <veja os casos de uso 3 e 4>.

Novamente, o padrão mais comum é otimizar o salto 3, no qual o conector é colocado perto do aplicativo pelos motivos abordados acima. O salto 3 não costuma ser caro, se tudo estiver dentro da mesma região. No entanto, o salto 1 pode ser mais caro dependendo de onde o usuário estiver, pois todos os usuários acessarão a instância do Proxy de Aplicativo nos EUA. Vale a pena observar que qualquer solução de proxy terá características semelhantes com relação aos usuários distribuídos globalmente.

Esse caso de uso segue o padrão 2 abaixo.

![Fornecedores de várias nuvens de IaaS do AzureAD](./media/application-proxy-network-topologies/application-proxy-pattern2.png)

### <a name="use-case-3"></a>Caso de uso 3

O aplicativo está em rede do cliente nos EUA. Há a ExpressRoute com emparelhamento público entre o Azure e a rede corporativa.

**Recomendação:** coloque o conector mais próximo possível do aplicativo. O sistema usará automaticamente a ExpressRoute para o salto 2. Isso segue o padrão 2 descrito acima.

Se o link de ExpressRoute estiver usando o emparelhamento público, o tráfego entre o proxy e o conector fluirá por esse link, e a latência do salto 2 será otimizada.

Esse caso de uso segue o padrão 3 abaixo.

![Fornecedores de várias nuvens de IaaS do AzureAD](./media/application-proxy-network-topologies/application-proxy-pattern3.png)

### <a name="use-case-4"></a>Caso de uso 4

O aplicativo está em rede do cliente nos EUA. Há a ExpressRoute com emparelhamento privado entre o Azure e a rede corporativa.

**Recomendação:** coloque o conector no Controlador de Domínio do Azure que está conectado à rede corporativa por meio de emparelhamento privado de ExpressRoute. Isso segue o padrão 3 descrito acima.

O conector pode ser colocado no Controlador de Domínio do Azure. Como ainda há uma linha de visão para o aplicativo e o controlador de domínio por meio da rede privada, salto 3 permanece otimizado. No entanto, essa configuração otimiza ainda mais o salto 2.

Esse caso de uso segue o padrão 4 abaixo.

![Fornecedores de várias nuvens de IaaS do AzureAD](./media/application-proxy-network-topologies/application-proxy-pattern4.png)

### <a name="use-case-5"></a>Caso de uso 5

O aplicativo está na rede do cliente na UE com a maioria dos usuários nos Estados Unidos.

**Recomendação:** coloque o conector próximo ao aplicativo. Pelas razões abordadas acima, essa é a melhor opção. Como os usuários dos Estados Unidos estão acessando uma instância do Proxy de Aplicativo que está na mesma região, o salto 1 não é muito caro. O salto 3 é otimizado. No entanto, o salto 2 é normalmente caro nesse caso de uso.

Esse caso de uso segue o padrão 5a abaixo.

![Fornecedores de várias nuvens de IaaS do AzureAD](./media/application-proxy-network-topologies/application-proxy-pattern5a.png)

Considere aproveitar a ExpressRoute, conforme destacado nos padrões 2 e 3 acima. Abaixo, mostro o padrão 2 aplicado.

Esse caso de uso segue o padrão 5b abaixo.

![Fornecedores de várias nuvens de IaaS do AzureAD](./media/application-proxy-network-topologies/application-proxy-pattern5b.png)

Veja abaixo outra variante para esse caso de uso que você pode considerar.

Se a maioria dos usuários na organização estiver nos EUA, provavelmente sua rede se “estende” também para os EUA. Se esse for o caso, o conector poderá ser colocado nos EUA e aproveitar a linha de rede corporativa interna dedicada ao aplicativo na UE. Dessa forma, os salto 2 e 3 são otimizados.

Esse caso de uso segue o padrão 5c abaixo.

![Fornecedores de várias nuvens de IaaS do AzureAD](./media/application-proxy-network-topologies/application-proxy-pattern5c.png)

## <a name="next-steps"></a>Próximas etapas
[Habilitar Proxy de aplicativo](active-directory-application-proxy-enable.md)<br>
[Habilitar o logon único](active-directory-application-proxy-sso-using-kcd.md)<br>
[Habilitar o acesso condicional](active-directory-application-proxy-conditional-access.md)<br>
[Solucionar problemas que surgirem com o Proxy de Aplicativo](active-directory-application-proxy-troubleshoot.md)





<!--HONumber=Feb17_HO1-->


