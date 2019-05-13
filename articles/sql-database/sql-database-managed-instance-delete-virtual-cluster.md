---
title: Excluir rede virtual depois de excluir o banco de dados SQL de instância gerenciada | Microsoft Docs
description: Saiba como excluir a rede virtual depois de excluir o banco de dados SQL de instância gerenciada.
services: sql-database
ms.service: sql-database
ms.subservice: management
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: douglas, carlrab, sstein
manager: craigg
ms.date: 05/07/2019
ms.openlocfilehash: 95d1681c9ff9981990d873a58a2d01833d690e0f
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65411980"
---
# <a name="delete-subnet-after-deleting-azure-sql-database-managed-instance"></a>Excluir sub-rede depois de excluir o banco de dados SQL de instância gerenciada

Este artigo fornece diretrizes sobre como excluir manualmente a sub-rede depois de excluir o último banco de dados SQL a instância gerenciada do que residem nele.

O [cluster virtual](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture) que continha o excluído a instância gerenciada será mantida por 12 horas contra a exclusão da instância. O cluster virtual é mantido ativo por design para habilitar agiliza a criação de instâncias gerenciadas na mesma sub-rede. Durante esse período, a sub-rede associada ao cluster virtual não pode ser excluída.

Versão imediata da sub-rede usada por um cluster virtual vazio é possível por meio de exclusão manual do cluster virtual. Exclusão do cluster virtual pode ser obtido por meio do portal do Azure ou API de clusters virtuais.

> [!NOTE]
> O cluster virtual não deve conter nenhuma instância gerenciada para a exclusão seja bem-sucedida.

## <a name="delete-virtual-cluster-from-azure-portal"></a>Excluir o cluster virtual do portal do Azure

Para excluir um cluster virtual usando o portal do Azure, pesquise os recursos de cluster virtual usando a pesquisa interna.

![Pesquisar cluster virtual.](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-search.png)

Depois que você localize o cluster virtual que deseja excluir, selecione esse recurso e selecione a opção de exclusão. Você será solicitado a confirmar a exclusão de cluster virtual.

![Exclua o cluster virtual.](./media/sql-database-managed-instance-delete-virtual-cluster/virtual-clusters-delete.png)

Confirmação de que o cluster virtual foi excluído é fornecida em notificações do portal do Azure. Exclusão bem-sucedida de cluster virtual libera imediatamente a sub-rede para uso posterior.

## <a name="delete-virtual-cluster-using-api"></a>Excluir o cluster virtual usando a API

Para excluir uma máquina virtual de cluster por meio do uso da API os parâmetros do URI especificados na [método de exclusão de clusters virtuais](https://docs.microsoft.com/rest/api/sql/virtualclusters/delete).

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral, confira [O que é uma Instância Gerenciada?](sql-database-managed-instance.md).
- Saiba mais sobre [arquitetura de conectividade na Instância Gerenciada](sql-database-managed-instance-connectivity-architecture.md).
- Saiba como [modificar uma rede virtual existente para a Instância Gerenciada](sql-database-managed-instance-configure-vnet-subnet.md).
- Para um tutorial que mostra como criar uma rede virtual, criar uma Instância Gerenciada e restaurar um banco de dados com base em um backup de banco de dados, confira [Criar uma Instância Gerenciada do Banco de Dados SQL do Azure](sql-database-managed-instance-get-started.md).
- Para problemas de DNS, confira [Configurar um DNS personalizado](sql-database-managed-instance-custom-dns.md).
