---
title: Criar e gerenciar regras e pontos de extremidade de serviço de VNet do Banco de Dados do Azure para PostgreSQL usando o portal do Azure
description: Criar e gerenciar regras e pontos de extremidade de serviço de VNet do Banco de Dados do Azure para PostgreSQL usando o portal do Azure
author: mbolz
ms.author: mbolz
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/23/2018
ms.openlocfilehash: bf34b99ca1d328a2de47aade0dcdff5b584ff07c
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2019
ms.locfileid: "59006364"
---
# <a name="create-and-manage-azure-database-for-postgresql-vnet-service-endpoints-and-vnet-rules-by-using-the-azure-portal"></a>Criar e gerenciar regras de VNet e pontos de extremidade de serviço de VNet do Banco de Dados do Azure para PostgreSQL usando o portal do Azure
As regras e pontos de extremidade de serviços de VNet (rede virtual) estendem o espaço de endereço privado de uma rede virtual para seu servidor do Banco de Dados do Azure para PostgreSQL. Para obter uma visão geral dos pontos de extremidade de serviço de VNet do Banco de Dados do Azure para PostgreSQL, confira [Pontos de extremidade de serviço de VNet do servidor do Banco de Dados do Azure para PostgreSQL](concepts-data-access-and-security-vnet.md). Os terminais de serviços da VNet estão disponíveis em todas as regiões suportadas para o Banco de Dados do Azure para PostgreSQL.

> [!NOTE]
> O suporte para ponto de extremidade de serviço de VNet é apenas para servidores de Uso Geral e Otimizados para Memória.
> No caso de emparelhamento de VNet, se o tráfego estiver fluindo em um Gateway VNet comum com pontos de extremidade de serviço e deve fluir para o par, crie uma regra ACL/VNet para permitir que Máquinas Virtuais do Azure na VNet do Gateway acessem o servidor de Banco de Dados do Azure para PostgreSQL.

## <a name="create-a-vnet-rule-and-enable-service-endpoints-in-the-azure-portal"></a>Criar uma regra de VNet e habilitar pontos de extremidade de serviço no portal do Azure

1. Na página do servidor PostgreSQL, no título Configurações, clique em **Segurança de Conexão** para abrir o painel Segurança de Conexão para o Banco de Dados do Azure para PostgreSQL. 

2. Verifique se a permitir acesso ao controle de serviços do Azure é definido como **OFF**.

> [!Important]
> Se você deixar o controle definido como ON, o servidor de banco de dados do Azure PostgreSQL aceita comunicação de qualquer sub-rede. Deixar o controle definido como ON pode ocasionar acesso excessivo de um ponto de vista de segurança. O recurso de ponto de extremidade de serviço de rede Virtual do Microsoft Azure, em conjunto com o recurso de regra de rede virtual do banco de dados do Azure para PostgreSQL, pode reduzir a área da superfície de segurança.

3. Em seguida, clique em **+ Adicionar rede virtual existente**. Se você não tiver uma VNet existente, clique em **+ Criar nova rede virtual** para criar uma. Confira [Início Rápido: Crie uma rede virtual usando o portal do Azure](../virtual-network/quick-create-portal.md)

   ![Portal do Azure - clique em Segurança de Conexão](./media/howto-manage-vnet-using-portal/1-connection-security.png)

4. Insira um nome de regra de VNet, selecione a assinatura, a rede virtual e o nome da sub-rede. Depois, clique em **Habilitar**. Dessa forma, os pontos de extremidade de serviço da VNet serão habilitados na sub-rede usando a marcação de serviço **Microsoft.SQL**.

   ![Portal do Azure – configurar a VNet](./media/howto-manage-vnet-using-portal/2-configure-vnet.png)

    A conta deve ter as permissões necessárias para criar uma rede virtual e um ponto de extremidade de serviço.

    Pontos de extremidade de serviço podem ser configurados em redes virtuais de forma independente por um usuário com acesso de gravação à rede virtual.
    
    Para proteger os recursos de serviço do Azure em uma VNet, o usuário deve ter permissão para "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/" das sub-redes que estão sendo adicionadas. Essa permissão está incluída nas funções de administrador de serviço internas por padrão e pode ser modificada com a criação de funções personalizadas.
    
    Saiba mais sobre [funções internas](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) e como atribuir permissões específicas a [funções personalizadas](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles).
    
    As VNets e os recursos de serviço do Azure podem estar na mesma assinatura ou em assinaturas diferentes. Se os recursos de serviço VNet e Azure estiverem em assinaturas diferentes, os recursos deverão estar no mesmo locatário do Active Directory (AD).

   > [!IMPORTANT]
   > É altamente recomendável ler este artigo sobre considerações e configurações de ponto de extremidade de serviço antes de configurá-los. **Ponto de extremidade de serviço de Rede Virtual:** Um [ponto de extremidade de serviço de Rede Virtual](../virtual-network/virtual-network-service-endpoints-overview.md) é uma sub-rede cujos valores de propriedade incluem um ou mais nomes formais de tipo de serviço do Azure. Os pontos de extremidade de serviços de VNet usam o nome de tipo de serviço **Microsoft.Sql**, que se refere ao serviço do Azure chamado Banco de Dados SQL. Essa marcação de serviço também é aplicável aos serviços Banco de Dados SQL do Azure, Banco de Dados do Azure para PostgreSQL e MySQL. É importante observar que, ao aplicar a marcação de serviço **Microsoft.Sql** a um ponto de extremidade de serviço de VNet, ela configura o tráfego do ponto de extremidade de serviço para todos os serviços de Banco de Dados do Azure, incluindo servidores do Banco de Dados SQL do Azure, do Banco de Dados do Azure para PostgreSQL e do Banco de Dados do Azure para MySQL na sub-rede. 
   > 

5. Depois de habilitá-lo, clique em **OK**. Você verá que os pontos de extremidade de serviço da VNet estão habilitados junto com uma regra de VNet.

   ![Pontos de extremidade de serviço de VNet habilitados e a regra de VNet criada](./media/howto-manage-vnet-using-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png)

## <a name="next-steps"></a>Próximas etapas
- Da mesma forma, é possível criar scripts para [Habilitar pontos de extremidade de serviço VNet e criar um regra VNET para o Banco de Dados do Azure para PostgreSQL usando a CLI do Azure](howto-manage-vnet-using-cli.md).
- Para obter ajuda com a conexão com um Banco de Dados para servidor PostgreSQL, veja [Bibliotecas de conexão para o Banco de Dados do Azure para PostgreSQL](./concepts-connection-libraries.md)
