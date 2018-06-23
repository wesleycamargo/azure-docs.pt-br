---
title: Criar e gerenciar regras e pontos de extremidade de serviço de VNet do Banco de Dados do Azure para MySQL usando o portal do Azure | Microsoft Docs
description: Criar e gerenciar regras e pontos de extremidade de serviço de VNet do Banco de Dados do Azure para MySQL usando o portal do Azure
services: mysql
author: mbolz
ms.author: mbolz
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 06/01/2018
ms.openlocfilehash: 7520868fd6bd349043ad2c53e62de5db978db8b1
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35267213"
---
# <a name="create-and-manage-azure-database-for-mysql-vnet-service-endpoints-and-vnet-rules-by-using-the-azure-portal"></a>Criar e gerenciar regras e pontos de extremidade de serviço de VNet do Banco de Dados do Azure para MySQL e regras de VNet usando o portal do Azure
As regras e pontos de extremidade de serviços de VNet (rede virtual) estendem o espaço de endereço privado de uma rede virtual para seu servidor do Banco de Dados do Azure para MySQL. Para obter uma visão geral dos pontos de extremidade de serviço de VNet do Banco de Dados do Azure para MySQL, confira [Pontos de extremidade de serviço de VNet do servidor do Banco de Dados do Azure para MySQL](concepts-data-access-and-security-vnet.md). Os pontos de extremidade de serviços VNet estão disponíveis na versão prévia pública em todas as regiões compatíveis com o Banco de Dados do Azure para MySQL.

> [!NOTE]
> O suporte para ponto de extremidade de serviço de VNet é apenas para servidores de Uso Geral e Otimizados para Memória.

## <a name="create-a-vnet-rule-and-enable-service-endpoints-in-the-azure-portal"></a>Criar uma regra de VNet e habilitar pontos de extremidade de serviço no portal do Azure

1. Na página do Servidor MySQL, na seção Configurações, clique em **Segurança da Conexão** para abrir o painel Segurança da Conexão do Banco de Dados do Azure para MySQL. Em seguida, clique em **+ Adicionar rede virtual existente**. Se você não tiver uma VNet existente, clique em **+ Criar nova rede virtual** para criar uma. Confira [Início Rápido: criar uma rede virtual usando o portal do Azure](../virtual-network/quick-create-portal.md)

   ![Portal do Azure - clique em Segurança de Conexão](./media/howto-manage-vnet-using-portal/1-connection-security.png)

2. Insira um nome de regra de VNet, selecione a assinatura, a rede virtual e o nome da sub-rede. Depois, clique em **Habilitar**. Dessa forma, os pontos de extremidade de serviço da VNet serão habilitados na sub-rede usando a marcação de serviço **Microsoft.SQL**.

   ![Portal do Azure – configurar a VNet](./media/howto-manage-vnet-using-portal/2-configure-vnet.png)

   > [!IMPORTANT]
   > É altamente recomendável ler este artigo sobre considerações e configurações de ponto de extremidade de serviço antes de configurá-los. **Ponto de extremidade de serviço de rede virtual:** um [ponto de extremidade de serviço de rede virtual](../virtual-network/virtual-network-service-endpoints-overview.md) é uma sub-rede cujos valores de propriedade incluem um ou mais nomes de tipo formais de serviço do Azure. Os pontos de extremidade de serviços de VNet usam o nome de tipo de serviço **Microsoft.Sql**, que se refere ao serviço do Azure chamado Banco de Dados SQL. Essa marcação de serviço também é aplicável aos serviços Banco de Dados SQL do Azure, Banco de Dados do Azure para PostgreSQL e MySQL. É importante observar que, ao aplicar a marcação de serviço **Microsoft.Sql** a um ponto de extremidade de serviço de VNet, ela configura o tráfego do ponto de extremidade de serviço para todos os serviços de Banco de Dados do Azure, incluindo servidores do Banco de Dados SQL do Azure, do Banco de Dados do Azure para PostgreSQL e do Banco de Dados do Azure para MySQL na sub-rede. 
   > 

3. Depois de habilitá-lo, clique em **OK**. Você verá que os pontos de extremidade de serviço da VNet estão habilitados junto com uma regra de VNet.

   ![Pontos de extremidade de serviço de VNet habilitados e a regra de VNet criada](./media/howto-manage-vnet-using-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png)

## <a name="next-steps"></a>Próximas etapas
- Da mesma forma, é possível criar scripts para [Habilitar pontos de extremidade de serviço da VNet e criar uma regra de VNET para o Banco de Dados do Azure para MySQL usando a CLI do Azure](howto-manage-vnet-using-cli.md).
- Para obter ajuda com a conexão com um Banco de Dados para servidor MySQL, veja [Bibliotecas de conexão para o Banco de Dados para MySQL](./concepts-connection-libraries.md)
