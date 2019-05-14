---
title: Proteger pontos de extremidade públicos a instância gerenciada - banco de dados SQL a instância gerenciada | Microsoft Docs
description: Usar com segurança os pontos de extremidade públicos no Azure com uma instância gerenciada
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 05/08/2019
ms.openlocfilehash: f06677b66c8f6586fec8cc5dfe97b1515b741e9c
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65470293"
---
# <a name="use-an-azure-sql-database-managed-instance-securely-with-public-endpoints"></a>Usar uma instância gerenciada do banco de dados SQL com segurança com pontos de extremidade públicos

Banco de dados SQL do Azure instâncias gerenciadas podem fornecer conectividade de usuário pela [pontos de extremidade públicos](../virtual-network/virtual-network-service-endpoints-overview.md). Este artigo explica como fazer essa configuração mais segura.

## <a name="scenarios"></a>Cenários

Uma instância gerenciada do banco de dados SQL fornece um ponto de extremidade privado para permitir a conectividade de dentro de sua rede virtual. A opção padrão é fornecer isolamento máximo. No entanto, há cenários em que você precisa fornecer uma conexão de ponto de extremidade público:

- A instância gerenciada deve se integrar com o multi-tenant plataforma como serviço (PaaS) as ofertas de apenas.
- Você precisa de maior taxa de transferência de troca de dados que é possível quando você estiver usando uma VPN.
- As políticas da empresa proíbem PaaS dentro de redes corporativas.

## <a name="deploy-a-managed-instance-for-public-endpoint-access"></a>Implantar uma instância gerenciada para acesso ao ponto de extremidade público

Embora não seja obrigatório, o modelo de implantação comuns para uma instância gerenciada com acesso de ponto de extremidade público é criar a instância em uma rede virtual isolada dedicada. Nessa configuração, a rede virtual é usada apenas para o isolamento de cluster virtual. Não importa se o espaço de endereço IP da instância gerenciada se sobrepuser ao espaço de endereço IP da rede corporativa.

## <a name="secure-data-in-motion"></a>Proteger dados em movimento

Tráfego de dados de instância gerenciada sempre será criptografado se o driver cliente dá suporte à criptografia. Dados enviados entre a instância gerenciada e outras máquinas virtuais do Azure ou serviços do Azure nunca deixam o backbone do Azure. Se houver uma conexão entre a instância gerenciada e uma rede local, é recomendável que usar o Azure ExpressRoute com emparelhamento da Microsoft. ExpressRoute ajuda a evitar a movimentação de dados pela internet pública. Para conectividade privada de instância gerenciada, o emparelhamento privado só pode ser usado.

## <a name="lock-down-inbound-and-outbound-connectivity"></a>Bloquear a conectividade de entrada e saída

O diagrama a seguir mostra as configurações de segurança recomendadas:

![Configurações de segurança para bloquear a conectividade de entrada e saída](media/sql-database-managed-instance-public-endpoint-securely/managed-instance-vnet.png)

Uma instância gerenciada tem um [endereço do ponto de extremidade público dedicado](sql-database-managed-instance-find-management-endpoint-ip-address.md). No firewall de saída do lado do cliente e as regras de grupo de segurança de rede, defina esse endereço IP do ponto de extremidade público para limitar a conectividade de saída.

Para garantir que o tráfego para a instância gerenciada é proveniente de fontes confiáveis, é recomendável conectar-se de fontes com endereços IP bem conhecidos. Use um grupo de segurança de rede para limitar o acesso para o ponto de extremidade público de instância gerenciada na porta 3342.

Quando os clientes precisam iniciar uma conexão de uma rede local, verifique se que o endereço de origem é convertido para um conjunto conhecido de endereços IP. Se você não pode fazer isso (por exemplo, uma força de trabalho móvel, sendo um cenário típico), é recomendável que você use [conexões VPN ponto a site e um ponto de extremidade privado](sql-database-managed-instance-configure-p2s.md).

Se as conexões forem iniciadas do Azure, é recomendável que o tráfego proveniente de um bem conhecidos atribuídos [endereço IP virtual](../virtual-network/virtual-networks-reserved-public-ip.md) (por exemplo, uma máquina virtual). Para facilitar o gerenciamento de endereços IP virtuais (VIP) mais fácil, você talvez queira usar [prefixos de endereço IP públicos](../virtual-network/public-ip-address-prefix.md).

## <a name="next-steps"></a>Próximas etapas

- Saiba como configurar o ponto de extremidade público para gerenciar instâncias: [Configurar o ponto de extremidade público](sql-database-managed-instance-public-endpoint-configure.md)
