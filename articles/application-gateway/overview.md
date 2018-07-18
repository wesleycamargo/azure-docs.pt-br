---
title: O que é o Gateway de Aplicativo do Azure
description: Saiba como você pode usar um gateway de aplicativo do Azure para gerenciar o tráfego da Web para seu aplicativo.
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.topic: overview
ms.custom: mvc
ms.workload: infrastructure-services
ms.date: 5/15/2018
ms.author: victorh
ms.openlocfilehash: 045443637c06745472458dd9e33670875a33352b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34193060"
---
# <a name="what-is-azure-application-gateway"></a>O que é o Gateway de Aplicativo do Azure?

O Gateway de Aplicativo do Azure é um balanceador de carga do tráfego da Web que permite que você gerencie o tráfego para seus aplicativos Web. 

Os balanceadores de carga tradicionais operam na camada de transporte (camada OSI 4 – TCP e UDP) e encaminham o tráfego com base no endereço IP de origem e na porta para um endereço IP de destino e uma porta. Porém, com o Gateway de Aplicativo, você pode ser ainda mais específico. Por exemplo, você pode encaminhar o tráfego com base na URL de entrada. Portanto, se `/images` estiver na URL de entrada, você poderá encaminhar o tráfego para um conjunto específico de servidores (conhecido como um pool) configurado para as imagens. Se `/video` estiver na URL, esse tráfego será encaminhado para outro pool otimizado para vídeos.

![imageURLroute](./media/application-gateway-url-route-overview/figure1-720.png)

Esse tipo de roteamento é conhecido como balanceamento de carga da camada de aplicativo (camada OSI 7). O Gateway de Aplicativo do Azure pode fazer o roteamento baseado em URL e muito mais. 

Os seguintes recursos estão incluídos no Gateway de Aplicativo do Azure: 

## <a name="secure-sockets-layer-ssl-termination"></a>Encerramento do protocolo SSL

O gateway de aplicativo dá suporte a terminação SSL no gateway, pelo qual o tráfego flui geralmente descriptografado até os servidores de back-end. Esse recurso permite que os servidores Web fiquem livres da sobrecarga da criptografia e descriptografia dispendiosa. No entanto, às vezes, a comunicação não criptografada com os servidores não é uma opção aceitável. Isso pode ocorrer devido a requisitos de segurança, requisitos de conformidade ou o aplicativo pode aceitar apenas uma conexão segura. Para tais aplicativos, o gateway de aplicativo dá suporte à criptografia SSL de ponta a ponta.

## <a name="web-application-firewall"></a>Firewall do aplicativo Web

O Firewall do aplicativo Web (WAF) é um recurso do Gateway de Aplicativo que fornece proteção centralizada de seus aplicativos Web de vulnerabilidades e explorações comuns. O WAF é baseado em regras dos [conjuntos de regras de núcleo do OWASP (Open Web Application Security Project)](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 ou 2.2.9. 

Os aplicativos Web cada vez mais são alvos de ataques mal-intencionados que exploram vulnerabilidades conhecidas comuns. Os ataques de injeção de SQL, os ataques de scripts entre sites, entre outros, são comuns entre essas explorações. Pode ser difícil impedir esses ataques no código do aplicativo e isso pode exigir manutenção, aplicação de patches e monitoramento rigorosos em muitas camadas da topologia do aplicativo. Um firewall de aplicativo Web centralizado ajuda a simplificar bastante o gerenciamento de segurança e oferece mais garantia ao administrador do aplicativo contra ameaças ou invasões. Uma solução WAF também pode reagir a uma ameaça de segurança mais rapidamente ao aplicar um patch contra uma vulnerabilidade conhecida em um local central do que a proteção de cada um dos aplicativos Web individuais. Os gateways de aplicativos existentes podem ser facilmente convertidos em um gateway de aplicativo com firewall de aplicativo Web.

## <a name="url-based-routing"></a>Roteamento baseado em URL

O Roteamento Baseado em Caminho de URL permite rotear o tráfego para pools do servidor de back-end com base nos Caminhos de URL da solicitação. Um dos cenários possíveis é encaminhar as solicitações de diferentes tipos de conteúdo para diferentes pools.

Por exemplo, as solicitações de `http://contoso.com/video/*` são encaminhadas para VideoServerPool, e as de `http://contoso.com/images/*` são encaminhadas para ImageServerPool. O DefaultServerPool será selecionado se nenhum dos padrões de caminho forem compatíveis.

## <a name="multiple-site-hosting"></a>Hospedagem de vários sites

A hospedagem de vários sites permite que você configure mais de um site na mesma instância de gateway de aplicativo. Esse recurso permite que você configure uma topologia mais eficiente para suas implantações adicionando até 20 sites a um gateway de aplicativo. Cada site pode ser direcionado para seu próprio pool. Por exemplo, o gateway de aplicativo pode fornecer o tráfego para `contoso.com` e `fabrikam.com` de dois pools de servidores chamados ContosoServerPool e FabrikamServerPool.

As solicitações de `http://contoso.com` são encaminhadas para ContosoServerPool, e as de `http://fabrikam.com` são encaminhadas para FabrikamServerPool.

Da mesma forma, dois subdomínios do mesmo domínio pai podem ser hospedados na mesma implantação de gateway de aplicativo. Exemplos de uso de subdomínios podem incluir `http://blog.contoso.com` e `http://app.contoso.com` hospedados em uma implantação de gateway de aplicativo único.

## <a name="redirection"></a>Redirecionamento

Um cenário comum para muitos aplicativos Web é dar suporte ao redirecionamento automático de HTTP para HTTPS para garantir que toda a comunicação entre o aplicativo e seus usuários ocorra em um caminho criptografado. 

No passado, talvez você tenha usado técnicas como a criação de um pool dedicado cujo único propósito é redirecionar as solicitações recebidas em HTTP para HTTPS. O gateway de aplicativo dá suporte à capacidade de redirecionar o tráfego no Gateway de Aplicativo. Isso simplifica a configuração do aplicativo, otimiza o uso de recursos e dá suporte a novos cenários de redirecionamento, incluindo redirecionamento global e baseado no caminho. O suporte ao redirecionamento do Gateway de Aplicativo não está limitado apenas ao redirecionamento de HTTP para HTTPS. Esse é um mecanismo de redirecionamento genérico; portanto, você pode redirecionar bidirecionalmente em qualquer porta definida usando regras. Ele também dá suporte a redirecionamento para um site externo.

O suporte a redirecionamento do Gateway de Aplicativo oferece os seguintes recursos:

- Redirecionamento global de uma porta para outra porta no Gateway. Isso habilita o redirecionamento de HTTP para HTTPS em um site.
- Redirecionamento baseado em caminho. Esse tipo de redirecionamento permite o redirecionamento de HTTP para HTTPS apenas em uma área específica do site, por exemplo, uma área de carrinho de compras indicada por `/cart/*`.
- Redirecionamento para um site externo.



## <a name="session-affinity"></a>Afinidade de sessão

O recurso de afinidade de sessão baseada em cookies é útil quando você deseja manter uma sessão de usuário no mesmo servidor. Usando cookies gerenciados pelo gateway, o Gateway de Aplicativo pode direcionar o tráfego seguinte de uma sessão de usuário para o mesmo servidor para processamento. Isso é importante em casos em que o estado de sessão é salvo localmente no servidor para uma sessão de usuário.




## <a name="websocket-and-http2-traffic"></a>Tráfego do WebSocket e HTTP/2

O Gateway de Aplicativo fornece suporte nativo para os protocolos WebSocket e HTTP/2. Não há nenhuma configuração configurável pelo usuário para habilitar ou desabilitar seletivamente o suporte ao WebSocket. O suporte a HTTP/2 pode ser habilitado por meio do Azure PowerShell.
 
Os protocolos WebSocket e HTTP/2 permitem uma comunicação full duplex entre um servidor e um cliente em uma conexão TCP de execução longa. Isso permite uma comunicação mais interativa entre o servidor Web e o cliente, que pode ser bidirecional, sem a necessidade de sondagem, necessária nas implementações baseadas em HTTP. Esses protocolos têm baixa sobrecarga, ao contrário do HTTP, e podem reutilizar a mesma conexão TCP para várias solicitações/respostas, resultando em uma utilização mais eficiente de recursos. Esses protocolos foram projetados para funcionar em portas HTTP tradicionais de 80 e 443.



## <a name="next-steps"></a>Próximas etapas

Dependendo de seus requisitos e do ambiente, você pode criar um Gateway de Aplicativo de teste usando o portal do Azure, o Azure PowerShell ou a CLI do Azure:

- [Início Rápido: Direcionar o tráfego da Web com o Gateway de Aplicativo do Azure – portal do Azure](quick-create-portal.md).
- [Início Rápido: Direcionar o tráfego da Web com o Gateway de Aplicativo do Azure – Azure PowerShell](quick-create-powershell.md)
- [Início Rápido: Direcionar o tráfego da Web com o Gateway de Aplicativo do Azure – CLI do Azure](quick-create-cli.md)
