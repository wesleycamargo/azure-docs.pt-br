---
title: "Início Rápido: escalar horizontalmente a computação no SQL Data Warehouse do Azure – Portal do Azure | Microsoft Docs"
description: "Tarefas do portal do Azure para gerenciar o poder de computação. Dimensionar recursos de computação ajustando as DWUs. Ou, para economizar custos, pause e retome os recursos de computação."
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: jhubbard
editor: 
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 01/31/2018
ms.author: elbutter;barbkess
ms.openlocfilehash: 6b86042ed6b95ba49fa2089ba36b1dbe9a61cc40
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="quickstart-scale-compute-in-azure-sql-data-warehouse-in-the-azure-portal"></a>Início Rápido: dimensionar a computação no SQL Data Warehouse do Azure no Portal do Azure

Dimensionar a computação no SQL Data Warehouse do Azure no Portal do Azure. [Escale horizontalmente a computação](sql-data-warehouse-manage-compute-overview.md) para melhorar o desempenho ou reduza a escala da computação para economizar custos. 

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Entrar no Portal do Azure

Entre no [portal do Azure](https://portal.azure.com/).

## <a name="before-you-begin"></a>Antes de começar

Você pode dimensionar um data warehouse que você já tenha ou usar o [Início Rápido: Criar e conectar – portal](create-data-warehouse-portal.md) para criar um data warehouse com o nome **mySampleDataWarehouse**.  Este início rápido dimensiona o **mySampleDataWarehouse**.

## <a name="scale-compute"></a>Computação de escala

No SQL Data Warehouse, você pode aumentar ou diminuir os recursos de computação ao ajustar as unidades de data warehouse. O [Criar e conectar – portal](create-data-warehouse-portal.md) criou o **mySampleDataWarehouse** e o inicializou com 400 DWUs. As seguintes etapas ajustam as DWUs do **mySampleDataWarehouse**.

Para alterar unidades de data warehouse:

1. Clique em **Bancos de Dados SQL** na página esquerda do portal do Azure.
2. Selecione **mySampleDataWarehouse** da página **Bancos de Dados SQL**. O data warehouse é aberto.
3. Clique em **Escala**.

    ![Clique em Escala.](media/quickstart-scale-compute-portal/click-scale.png)

2. No painel Escala, mova o controle deslizante para a esquerda ou direita para alterar a configuração de DWU.

    ![Mover o controle deslizante](media/quickstart-scale-compute-portal/scale-dwu.png)

3. Clique em **Salvar**. Será exibida uma mensagem de confirmação. Clique em **sim** para confirmar ou em **não** para cancelar.

    ![Clique em Salvar](media/quickstart-scale-compute-portal/confirm-change.png)



## <a name="next-steps"></a>Próximas etapas
Agora, você aprendeu como dimensionar a computação para seu data warehouse. Para saber mais sobre o SQL Data Warehouse do Azure, prossiga para o tutorial de carregamento de dados.

> [!div class="nextstepaction"]
>[Carregar dados no SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md)
