---
title: Instância Gerenciada do Banco de Dados SQL do Azure determina o tamanho da sub-rede/VNet | Microsoft Docs
description: Este tópico descreve como calcular o tamanho da sub-rede na qual as Instâncias Gerenciadas do Banco de Dados SQL do Azure serão implantadas.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: bonova, carlrab
manager: craigg
ms.date: 12/12/2018
ms.openlocfilehash: a588f8579d45f6230c80a62f8cd3abc155d75700
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/13/2018
ms.locfileid: "53344822"
---
# <a name="determine-vnet-subnet-size-for-azure-sql-database-managed-instance"></a>Determinar o tamanho da sub-rede da VNet para o banco de dados da Instância Gerenciada do Banco de Dados SQL do Azure

A Instância Gerenciada do Banco de Dados SQL do Azure deve ser implantada em uma [VNet (rede virtual)](../virtual-network/virtual-networks-overview.md) do Azure.

O número de Instâncias Gerenciadas que podem ser implantadas na sub-rede da VNet depende do tamanho da sub-rede (intervalo de sub-rede).

Quando você cria uma Instância Gerenciada, o Azure aloca uma série de máquinas virtuais, dependendo da camada selecionada durante o provisionamento. Como essas máquinas virtuais estão associadas à sua sub-rede, elas exigem endereços IP. Para garantir alta disponibilidade durante operações regulares e manutenção do serviço, o Azure pode alocar máquinas virtuais adicionais. Como resultado, o número de endereços IP necessários em uma sub-rede é maior que o número de Instâncias Gerenciadas nessa sub-rede. 

Por design, uma Instância Gerenciada precisa de um mínimo de 16 endereços IP em uma sub-rede e pode usar até 256 endereços IP. Como resultado, é possível usar máscaras de sub-rede /28 a /24 ao definir os intervalos de IP da sub-rede. 

> [!IMPORTANT]
> O tamanho da sub-rede com 16 endereços IP é o mínimo com potencial limitado para uma expansão futura da Instância Gerenciada. É altamente recomendável escolher uma sub-rede com o prefixo /27 ou abaixo. 

## <a name="determine-subnet-size"></a>Determinar o tamanho da sub-rede

Se você planeja implantar múltiplas instâncias gerenciadas dentro da sub-rede e precisa otimizar o tamanho da sub-rede, use estes parâmetros para formar um cálculo: 

- O Azure usa cinco endereços IP na sub-rede para necessidades próprias 
- Cada instância de Uso Geral precisa de dois endereços 
- Cada instância Comercialmente Crítica precisa de quatro endereços

**Exemplo**: você planeja ter três instâncias de Uso Geral e duas Gerenciadas Comercialmente Críticas. Isso significa que você precisa de 5 + 3 * 2 + 2 * 4 = 19 endereços IP. Como os intervalos de IP são definidos em potência de 2, você precisa do intervalo de IP de 32 (2^5) endereços IP. Portanto, é necessário reservar a sub-rede com a máscara de sub-rede de /27. 

> [!IMPORTANT]
> O cálculo exibido acima se tornará obsoleto com as melhorias futuras. 

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral, consulte [O que é uma Instância Gerenciada](sql-database-managed-instance.md).
- Saiba mais sobre [Arquitetura de conectividade para a Instância Gerenciada](sql-database-managed-instance-connectivity-architecture.md). 
- Veja como [criar uma VNet na qual você implantará Instâncias Gerenciadas](sql-database-managed-instance-create-vnet-subnet.md)
- 
- Para problemas de DNS, consulte [Configurar um DNS personalizado](sql-database-managed-instance-custom-dns.md)
