---
title: Aplicativo de conexão da Instância Gerenciada do Banco de Dados SQL do Azure | Microsoft Docs
description: Este artigo descreve como conectar seu aplicativo à Instância Gerenciada do Banco de Dados SQL do Azure.
ms.service: sql-database
author: srdjan-bozovic
manager: cguyer
ms.custom: managed instance
ms.topic: article
ms.date: 03/07/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: 243dfd09df5eaac05608f57a0b2abf6e757f00e8
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2018
---
# <a name="connect-your-application-to-azure-sql-database-managed-instance"></a>Conectar seu aplicativo à Instância Gerenciada do Banco de Dados SQL do Azure

Atualmente, você tem várias opções ao decidir como e onde hospedar seu aplicativo. 
 
Você pode optar por hospedar o aplicativo na nuvem usando o Serviço de Aplicativo do Azure ou algumas das opções integradas de VNET (rede virtual) do Azure como o Ambiente do Serviço de Aplicativo do Azure, a Máquina Virtual e o Conjunto de Dimensionamento de Máquinas Virtuais. Você também pode adotar uma abordagem de nuvem híbrida e manter seus aplicativos localmente. 
 
Seja qual for sua escolha, você poderá conectá-la a uma Instância Gerenciada (versão prévia).  

![alta disponibilidade](./media/sql-database-managed-instance/application-deployment-topologies.png)  

## <a name="connect-an-application-inside-the-same-vnet"></a>Conectar um aplicativo dentro da mesma VNET 

Este cenário é o mais simples. As máquinas virtuais dentro da VNET podem se conectar entre si diretamente mesmo se estão em sub-redes diferentes. Isso significa que tudo o que você precisa para conectar o aplicativo em um Ambiente de Aplicativo do Azure ou uma Máquina Virtual é definir a cadeia de conexão corretamente.  
 
Caso você não possa estabelecer a conexão, verifique se você tem um Grupo de Segurança de Rede definido na sub-rede do aplicativo. Nesse caso, você precisa abrir a conexão de saída na porta 1433 do SQL, bem como o intervalo 11000-12000 de portas para o redirecionamento. 

## <a name="connect-an-application-inside-a-different-vnet"></a>Conectar um aplicativo em uma VNET diferente 

Este cenário é um pouco mais complexo porque a Instância Gerenciada tem o endereço IP privado em sua própria VNET. Para se conectar, um aplicativo precisa ter acesso à VNET na qual a Instância Gerenciada foi implantada. Portanto, primeiro você precisa fazer uma conexão entre o aplicativo e a VNET da Instância Gerenciada. As VNETs não precisam estar na mesma assinatura para que este cenário funcione. 
 
Há duas opções de conexão de VNETs: 
- [Emparelhamento de Rede Virtual do Azure](../virtual-network/virtual-network-peering-overview.md) 
- Gateway de VPN VNET a VNET ([portal do Azure](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md), [PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md), [CLI do Azure](../vpn-gateway/vpn-gateway-howto-vnet-vnet-cli.md)) 
 
A opção de emparelhamento é a preferencial porque o emparelhamento usa a rede de backbone da Microsoft. Portanto, da perspectiva de conectividade, não há nenhuma diferença perceptível na latência entre as máquinas virtuais na VNET emparelhada e na mesma VNET. O emparelhamento VNET é limitado às redes na mesma região, embora o emparelhamento entre regiões esteja habilitado em algumas regiões como uma versão prévia.  
 
> [!IMPORTANT]
> Os emparelhamentos de VNET criados entre regiões podem não ter o mesmo nível de disponibilidade e confiabilidade encontrado em emparelhamentos em uma versão de disponibilidade geral. Os emparelhamentos de VNET podem ter funcionalidades restringidas e podem não estar disponíveis em todas as regiões do Azure. Para obter as notificações mais atualizadas sobre a disponibilidade e o status desse recurso, confira a página de atualizações  [Rede Virtual do Azure](https://azure.microsoft.com/updates/?product=virtual-network). 

## <a name="connect-an-on-premises-application"></a>Conectar um aplicativo local 

A Instância Gerenciada pode ser acessada somente por meio de um endereço IP privado. Para acessá-la localmente, você precisa fazer uma conexão Site a Site entre o aplicativo e a VNET da Instância Gerenciada. 
 
Há duas opções de conexão do local à VNET do Azure: 
- Conexão VPN Site a Site ([portal do Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), [PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md), [CLI do Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)) 
- Conexão do [ExpressRoute](../expressroute/expressroute-introduction.md)  
 
Se você estabeleceu com êxito a conexão do local ao Azure e não pode estabelecer a conexão à Instância Gerenciada, verifique se o firewall tem a conexão de saída aberta na porta 1433 do SQL, bem como no intervalo 11000-12000 de portas para o redirecionamento. 

## <a name="connect-an-azure-app-service-hosted-application"></a>Conectar um aplicativo hospedado no Serviço de Aplicativo do Azure 

A Instância Gerenciada pode ser acessada somente por meio de um endereço IP privado. Portanto, para acessá-la no Serviço de Aplicativo do Azure, primeiro você precisa fazer uma conexão entre o aplicativo e a VNET da Instância Gerenciada. Consulte [Integrar seu aplicativo a uma Rede Virtual do Azure](../app-service/web-sites-integrate-with-vnet.md).  
 
Para obter a solução de problemas, consulte [Solução de problemas de VNETs e aplicativos](../app-service/web-sites-integrate-with-vnet.md#troubleshooting). Se uma conexão não puder ser estabelecida, tente [sincronizar a configuração de rede](sql-database-managed-instance-sync-network-configuration.md). 
 
Um caso especial de conexão do Serviço de Aplicativo do Azure à Instância Gerenciada é quando você integrou o Serviço de Aplicativo do Azure a uma rede emparelhada com a VNET da Instância Gerenciada. Esse caso exige a definição da seguinte configuração: 

- A VNET da Instância Gerenciada NÃO deve ter um gateway  
- A VNET da Instância Gerenciada deve ter definida a opção Usar gateways remotos 
- A VNET emparelhada deve ter definida a opção Permitir trânsito de gateway 
 
Este cenário é ilustrado no seguinte diagrama:

![emparelhamento de aplicativo integrado](./media/sql-database-managed-instance/integrated-app-peering.png)
 
## <a name="connect-an-application-on-the-developers-box"></a>Conectar um aplicativo na caixa de desenvolvedores 

A Instância Gerenciada pode ser acessada somente por meio de um endereço IP privado. Portanto, para acessá-la na caixa de desenvolvedor, primeiro você precisa fazer uma conexão entre a caixa de desenvolvedor e a VNET da Instância Gerenciada.  
 
Configure uma conexão Ponto a Site com uma VNET usando a autenticação nativa de certificado do Azure. Os artigos ([portal do Azure](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md), [PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md), [CLI do Azure](../vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal.md)) mostram em detalhes como isso pode ser feito.  

## <a name="next-steps"></a>Próximas etapas

- Para obter informações sobre a Instância Gerenciada, consulte [O que é uma Instância Gerenciada](sql-database-managed-instance.md).
- Para obter um tutorial, consulte [Criar uma Instância Gerenciada](sql-database-managed-instance-tutorial-portal.md).
