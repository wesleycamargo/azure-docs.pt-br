---
title: Mova bancos de dados entre servidores, entre as assinaturas e para dentro e para fora do Azure.
description: "Passos rápidos para copiar, mover e migrar dados e bancos de dados no Banco de Dados SQL."
services: sql-database
documentationcenter: 
author: v-shysun
manager: felixwu
editor: 
ms.assetid: dea01868-5757-41e2-826c-ec26361a339c
ms.service: sql-database
ms.custom: migrate and move
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2016
ms.author: v-shysun
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: bc88bb49c601f525c62c4dac4716a3f77a30172e


---
# <a name="move-databases-between-servers-between-subscriptions-and-in-and-out-of-azure"></a>Mover bancos de dados entre servidores, entre as assinaturas e para dentro e para fora do Azure
[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="to-move-a-database-to-a-different-server-in-the-same-subscription"></a>Para mover um banco de dados para um servidor diferente na mesma assinatura
* No [Portal do Azure](https://portal.azure.com), clique em **Bancos de Dados SQL**, selecione um banco de dados na lista e, em seguida, clique em **Copiar**. Para obter mais detalhes, consulte [Copiar um Banco de Dados SQL do Azure](sql-database-copy.md) .

## <a name="to-move-a-database-between-subscriptions"></a>Para mover um banco de dados entre assinaturas
* No [Portal do Azure](https://portal.azure.com), clique em **servidores SQL** e, em seguida, selecione o servidor que hospeda seu banco de dados da lista. Clique em **Mover**, então selecione os recursos a mover e a assinatura para a qual movê-los.

## <a name="to-migrate-a-sql-database-into-azure"></a>Para migrar um Banco de Dados SQL para o Azure
* Determine a compatibilidade do banco de dados e, em seguida, escolha o método de migração correto com base em suas necessidades. Siga as diretrizes e as opções em [Migração de banco de dados do SQL Server](sql-database-cloud-migrate.md).

## <a name="to-create-a-copy-of-a-database-for-use-outside-of-azure"></a>Para criar uma cópia de um banco de dados para uso fora do Azure
* [Exporte um arquivo BACPAC.](sql-database-export.md)




<!--HONumber=Nov16_HO3-->


