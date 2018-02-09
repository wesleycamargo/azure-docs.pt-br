---
title: "Visão geral de pré-requisitos para usar o Serviço de Migração de Banco de Dados do Azure | Microsoft Docs"
description: "Saiba mais sobre uma visão geral dos pré-requisitos para usar o Serviço de Migração de Banco de Dados do Azure para executar migrações de banco de dados."
services: database-migration
author: HJToland3
ms.author: jtoland
manager: 
ms.reviewer: 
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 01/25/2018
ms.openlocfilehash: a103bb4802bc4a20b6d82f0c6bb427133d9e5643
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/25/2018
---
# <a name="overview-of-prerequisites-for-using-the-azure-database-migration-service"></a>Visão geral de pré-requisitos para usar o Serviço de Migração de Banco de Dados do Azure
Existem vários pré-requisitos necessários para garantir que o Serviço de Migração de Banco de Dados do Azure funcione sem problemas ao executar migrações de bancos de dados. Alguns dos pré-requisitos se aplicam em todos os cenários (pares de origem e destino) com suporte do serviço, enquanto outros pré-requisitos são exclusivos para um cenário específico.

Os pré-requisitos associados a usar o Serviço de Migração de Banco de Dados do Azure estão listados nas seções a seguir.

## <a name="prerequisites-common-across-migration-scenarios"></a>Pré-requisitos comuns em cenários de migração
Os pré-requisitos do Serviço de Migração de Banco de Dados do Azure que são comuns a todos os cenários de migração compatíveis incluem a necessidade de:
- Criar um VNET para o Serviço de Migração de Banco de Dados do Azure usando o modelo de implantação do Azure Resource Manager, que fornece conectividade site a site aos servidores de origem locais usando o [ExpressRoute](https://docs.microsoft.com/en-us/azure/expressroute/expressroute-introduction) ou a [VPN](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Verifique se as regras do Grupo de Segurança de Rede da VNET (Rede Virtual) do Azure não bloqueiam as portas de comunicação 443, 53, 9354, 445 e 12000. Para obter mais detalhes sobre a filtragem de tráfego do NSG da Rede Virtual do Azure, consulte o artigo [Filtrar o tráfego de rede com grupos de segurança de rede](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-nsg).
- Ao usar um dispositivo de firewall na frente de seus bancos de dados de origem, talvez seja necessário adicionar regras de firewall para permitir que o Serviço de Migração de Banco de Dados do Azure acesse os bancos de dados de origem para migração.

## <a name="prerequisites-for-migrating-sql-server-to-azure-sql-database"></a>Pré-requisitos para migração do SQL Server para o Banco de Dados do SQL do Azure 
Além dos pré-requisitos do Serviço de Migração de Banco de Dados do Azure que são comuns a todos os cenários de migração, também há pré-requisitos que se aplicam especificamente para um cenário ou outro.

Ao usar o Serviço de Migração de Banco de Dados do Azure para executar migrações do SQL Server para Banco de Dados SQL, além dos pré-requisitos que são comuns a todos os cenários de migração, certifique-se de atender aos seguintes pré-requisitos adicionais:
- Configurar o [Firewall do Windows para acesso ao mecanismo de banco de dados](https://docs.microsoft.com/en-us/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Habilitar o protocolo TCP/IP, que está desabilitado por padrão durante a instalação do SQL Server Express, seguindo as instruções no artigo [Habilitar ou desabilitar um protocolo de rede de servidor](https://docs.microsoft.com/en-us/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
- Criar uma instância do Banco de Dados SQL do Azure seguindo os detalhes no artigo [Criar um banco de dados SQL do Azure no portal do Azure](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-get-started-portal).
- Fazer download e instalar o [Assistente de migração de dados](https://www.microsoft.com/en-us/download/details.aspx?id=53595) v3.3 ou posterior.
- Abrir o firewall do Windows para permitir que o Serviço de Migração de Banco de Dados do Azure acesse os bancos de dados de origem.
- Crie uma [regra de firewall](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-firewall-configure) no nível de servidor para o servidor do Banco de Dados SQL do Azure para permitir o acesso do Serviço de Migração de Banco de Dados do Azure aos bancos de dados de destino. Forneça o intervalo de sub-redes da VNET usado para o Serviço de Migração de Banco de Dados do Azure.
- Garantir que as credenciais usadas para se conectar à instância de origem do SQL Server tenham as permissões [CONTROL SERVER](https://docs.microsoft.com/en-us/sql/t-sql/statements/grant-server-permissions-transact-sql).
- Garantir que as credenciais usadas para se conectar para direcionar a instância do Banco de Dados SQL do Azure tenham a permissão CONTROL DATABASE nos bancos de dados de destino do SQL do Azure.

   > [!NOTE]
   > Para obter uma lista completa de pré-requisitos necessários para usar o Serviço de Migração de Banco de Dados do Azure para executar migrações do SQL Server para o Banco de Dados do SQL Azure, consulte o tutorial [Migrar o SQL Server para o Banco de Dados SQL do Azure](https://docs.microsoft.com/en-us/azure/dms/tutorial-sql-server-to-azure-sql).
   > 

## <a name="next-steps"></a>Próximas etapas
Para obter uma visão geral do Serviço de Migração de Banco de Dados do Azure e da disponibilidade regional durante a Visualização Pública, consulte o artigo [O que é a Visualização do Serviço de Migração de Banco de Dados do Azure](dms-overview.md). 