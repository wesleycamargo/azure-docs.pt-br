---
title: DNS personalizado da Instância Gerenciada do Banco de Dados SQL do Azure | Microsoft Docs
description: Este tópico descreve opções de configuração para um DNS personalizado com uma Instância Gerenciada do Banco de Dados SQL do Azure.
services: sql-database
author: srdan-bozovic-msft
manager: craigg
ms.service: sql-database
ms.custom: managed instance
ms.topic: conceptual
ms.date: 09/23/2018
ms.author: srbozovi
ms.reviewer: bonova, carlrab
ms.openlocfilehash: 2d1bb7e8522da32dd33933261ea41b578f8afac1
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46949478"
---
# <a name="configuring-a-custom-dns-for-azure-sql-database-managed-instance"></a>Configurar DNS personalizado para Instância Gerenciada do Banco de Dados SQL do Azure

Uma Instância Gerenciada do Banco de Dados SQL do Azure deve ser implantada em uma [VNet (rede virtual)](../virtual-network/virtual-networks-overview.md) do Azure. Há alguns cenários (ou seja, servidores vinculados a outras instâncias do SQL em seu ambiente de nuvem ou híbrido) que exigem nomes de hosts privados para serem resolvidos da Instância Gerenciada. Nesse caso, você precisa configurar um DNS personalizado dentro do Azure. Uma vez que a Instância Gerenciada usa o mesmo DNS para seu funcionamento interno, a configuração de DNS da rede virtual deverá ser compatível com a Instância Gerenciada. 

Para tornar uma configuração personalizada do DNS compatível com a Instância Gerenciada, é necessário: 
- Configurar o servidor DNS personalizado para que ele seja capaz de resolver nomes de domínio público 
- Colocar o endereço IP de DNS 168.63.129.16 do resolvedor recursivo do Azure no final da lista DNS de rede virtual 
 
## <a name="setting-up-custom-dns-servers-configuration"></a>Definir uma configuração de servidores DNS personalizada

1. No portal do Azure, encontre opção de DNS personalizado para sua rede virtual.

   ![opção de DNS personalizado](./media/sql-database-managed-instance-custom-dns/custom-dns-option.png) 

2. Alterne para Personalizado e insira o endereço IP do servidor DNS personalizado, bem como o endereço IP de resolvedores recursivos do Azure, 168.63.129.16. 

   ![opção de DNS personalizado](./media/sql-database-managed-instance-custom-dns/custom-dns-server-ip-address.png) 

   > [!IMPORTANT]
   > Não configurar o resolvedor recursivo do Azure na lista do DNS faz com que a Instância Gerenciada entre em estado com falha. A recuperação desse estado pode exigir a criação de uma nova instância em uma VNet com as políticas de rede compatíveis, recriar dados em nível de instância e restaurar os bancos de dados. Consulte [Configuração de VNET](sql-database-managed-instance-vnet-configuration.md).

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral, consulte [O que é uma Instância Gerenciada](sql-database-managed-instance.md)
- Para obter um tutorial mostrando como criar uma nova Instância Gerenciada, consulte [Criando uma Instância Gerenciada](sql-database-managed-instance-get-started.md).
- Para obter informações sobre como configurar sua rede virtual à Instância Gerenciada, consulte [Configuração da rede virtual de Instâncias Gerenciadas](sql-database-managed-instance-vnet-configuration.md)
