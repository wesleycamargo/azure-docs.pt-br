---
title: Topologias de rede para migrações da Instância Gerenciada do Banco de Dados SQL do Azure usando o Serviço de Migração de Banco de Dados do Azure | Microsoft Docs
description: Saiba mais sobre as configurações de origem e destino do Serviço de Migração de Banco de Dados.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: ''
ms.reviewer: ''
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 06/21/2018
ms.openlocfilehash: 9fcee103854209016d73e29b598c9f33d35c4b6c
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/22/2018
ms.locfileid: "36316860"
---
# <a name="network-topologies-for-azure-sql-db-managed-instance-migrations-using-the-azure-database-migration-service"></a>Topologias de rede para migrações da Instância Gerenciada do BD SQL do Azure usando o Serviço de Migração de Banco de Dados do Azure
Este artigo discute as diversas topologias de rede com as quais o Serviço de Migração de Banco de Dados do Azure pode trabalhar para fornecer uma experiência de migração abrangente de SQL Servers locais para Instância Gerenciada do Banco de Dados SQL do Azure.

## <a name="azure-sql-database-managed-instance-configured-for-hybrid-workloads"></a>Instância Gerenciada do Banco de Dados SQL do Azure configurada para cargas de trabalho Híbridas 
Use essa topologia se a Instância Gerenciada do Banco de Dados SQL do Azure está conectada à rede local. Essa abordagem fornece o roteamento de rede mais simplificado e gera a taxa máxima de transferência de dados durante a migração.

![Topologia de rede para cargas de trabalho híbridas](media\resource-network-topologies\hybrid-workloads.png)

**Requisitos**
- Neste cenário, a Instância Gerenciada do Banco de Dados SQL do Azure e a instância do Serviço de Migração de Banco de Dados do Azure são criadas na mesma VNET do Azure, mas usam sub-redes diferentes.  
- A VNET usada neste cenário também é conectada à rede local com o [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).

## <a name="azure-sql-database-managed-instance-isolated-from-the-on-premises-network"></a>Instância Gerenciada do Banco de Dados SQL do Azure isolada da rede local
Use essa topologia de rede se o ambiente exigir um ou mais dos seguintes cenários:
- A Instância Gerenciada do Banco de Dados SQL do Azure está isolada da conectividade local, mas a instância do Serviço de Migração de Banco de Dados do Azure está conectada à rede local.
- Se políticas de RBAC (controle de acesso baseado em função) estiverem em vigor e você precisar limitar o acesso dos usuários à mesma assinatura que hospeda a Instância Gerenciada do Banco de Dados SQL do Azure.
- As VNETs usadas para a Instância Gerenciada do Banco de Dados SQL do Azure e o Serviço de Migração de Banco de Dados do Azure estão em assinaturas diferentes.

![Topologia de rede da Instância Gerenciada isolada da rede local](media\resource-network-topologies\mi-isolated-workload.png)

**Requisitos**
- A VNET usada pelo Serviço de Migração de Banco de Dados do Azure para este cenário também deve estar conectada à rede local por meio do uso de (https://docs.microsoft.com/azure/expressroute/expressroute-introduction) ou [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Configure um [emparelhamento de rede da VNET](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) entre a VNET usada para a Instância Gerenciada do Banco de Dados SQL do Azure e o Serviço de Migração de Banco de Dados do Azure.


## <a name="cloud-to-cloud-migrations-shared-vnet"></a>Migrações de nuvem para nuvem: VNET compartilhada

Use essa topologia se o SQL Server de origem estiver hospedado em uma VM do Azure e compartilhar a mesma VNET com a Instância Gerenciada do Banco de Dados SQL do Azure e o Serviço de Migração de Banco de Dados.

![Topologia de rede para migrações de nuvem para nuvem com uma vnet compartilhada](media\resource-network-topologies\cloud-to-cloud.png)

**Requisitos**
- Nenhum requisito adicional.

## <a name="cloud-to-cloud-migrations-isolated-vnet"></a>Migrações de nuvem para nuvem: VNET isolada

Use essa topologia de rede se o ambiente exigir um ou mais dos seguintes cenários:
- A Instância Gerenciada do Banco de Dados SQL do Azure é provisionada em uma VNET isolada.
- Se políticas de RBAC (controle de acesso baseado em função) estiverem em vigor e você precisar limitar o acesso dos usuários à mesma assinatura que hospeda a Instância Gerenciada do Banco de Dados SQL do Azure.
- As VNETs usadas para a Instância Gerenciada do Banco de Dados SQL do Azure e o Serviço de Migração de Banco de Dados do Azure estão em assinaturas diferentes.

![Topologia de rede para migrações de nuvem para nuvem com uma vnet isolada](media\resource-network-topologies\cloud-to-cloud-isolated.png)

**Requisitos**
- Configure um [emparelhamento de rede da VNET](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) entre a VNET usada para a Instância Gerenciada do Banco de Dados SQL do Azure e o Serviço de Migração de Banco de Dados do Azure.


## <a name="see-also"></a>Veja também
- [Migrar o SQL Server para a Instância Gerenciada do Banco de Dados SQL do Azure](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance)
- [Visão geral de pré-requisitos para o uso do Serviço de Migração de Banco de Dados do Azure](https://docs.microsoft.com/azure/dms/pre-reqs)
- [Criar uma rede virtual usando o portal do Azure](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

## <a name="next-steps"></a>Próximas etapas
Para obter uma visão geral do Serviço de Migração de Banco de Dados do Azure e da disponibilidade regional durante a Visualização Pública, consulte o artigo [O que é a Visualização do Serviço de Migração de Banco de Dados do Azure](dms-overview.md). 