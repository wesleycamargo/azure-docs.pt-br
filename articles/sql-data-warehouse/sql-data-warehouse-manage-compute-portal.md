---
title: "Gerenciar potência de computação no SQL Data Warehouse do Azure (portal do Azure) | Microsoft Docs"
description: "Tarefas do portal do Azure para gerenciar o poder de computação. Dimensionar recursos de computação ajustando as DWUs. Ou, para economizar custos, pause e retome os recursos de computação."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: 233b0da5-4abd-4d1d-9586-4ccc5f50b071
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: c1c23ab46d9b4e43154a62080cb8865b246489f9
ms.openlocfilehash: 2e11486203f04d671548b6132e61f804acb7f90a


---
# <a name="manage-compute-power-in-azure-sql-data-warehouse-azure-portal"></a>Gerenciar poder de computação no SQL Data Warehouse do Azure (portal do Azure)
> [!div class="op_single_selector"]
> * [Visão geral](sql-data-warehouse-manage-compute-overview.md)
> * [Portal](sql-data-warehouse-manage-compute-portal.md)
> * [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
> * [REST](sql-data-warehouse-manage-compute-rest-api.md)
> * [TSQL](sql-data-warehouse-manage-compute-tsql.md)
>
>

Dimensionar o desempenho escalando horizontalmente recursos de computação e memória para atender às demandas de mudança de sua carga de trabalho. Economizar custos reduzindo recursos durante horários que não sejam de pico ou pausando a computação.

Esta coleção de tarefas usa o portal do Azure para:

* Computação de escala
* Pausar computação
* Retomar a computação

Para obter mais informações, consulte [Visão geral sobre gerenciar computação][Visão geral sobre gerenciar computação].

## <a name="scale-compute-power"></a>Dimensionar poder de computação
[!INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Para alterar recursos de computação:

1. Abra o [portal do Azure][portal do Azure], abra o banco de dados e clique em **Escala**.

    ![Clique em Escala.][1]
2. Na folha Escala, mova o controle deslizante para a esquerda ou direita para alterar a configuração de DWU.

    ![Mover o controle deslizante][2]
3. Clique em **Salvar**. Será exibida uma mensagem de confirmação. Clique em **sim** para confirmar ou em **não** para cancelar.

    ![Clique em Salvar][3]

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>Pausar computação
[!INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

Para pausar um banco de dados:

1. Abra o [portal do Azure][portal do Azure] e abra seu banco de dados. Observe que o Status é **Online**.

    ![Status online][6]
2. Para suspender os recursos de computação e de memória, clique em **Pausar**e uma mensagem de confirmação será exibida. Clique em **sim** para confirmar ou em **não** para cancelar.

    ![Confirmar pausar][7]
3. Enquanto o SQL Data Warehouse estiver iniciando o banco de dados, o status será **Pausando**.
4. Quando o status for **Em pausa**, a operação de pausa terá sido concluída e você deixará de ser cobrado pelas DWUs.

    ![Status em pausa][4]

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>Retomar a computação
[!INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]

Para retomar um banco de dados:

1. Abra o [portal do Azure][portal do Azure] e abra seu banco de dados. Observe que o Status é **Em pausa**.

    ![Pausar banco de dados][4]
2. Para retomar o banco de dados, clique em **Iniciar**e uma mensagem de confirmação será exibida. Clique em **sim** para confirmar ou em **não** para cancelar.

    ![Confirmar retomar][5]
3. Enquanto o SQL Data Warehouse estiver iniciando o banco de dados, o status será “Continuando”.
4. Quando o status for **online**, o banco de dados estará pronto.

    ![Status online][6]

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações, consulte [Visão geral de gerenciamento][Visão geral de gerenciamento].

<!--Image references-->
[1]: ./media/sql-data-warehouse-manage-compute-portal/click-scale.png
[2]: ./media/sql-data-warehouse-manage-compute-portal/move-slider.png
[3]: ./media/sql-data-warehouse-manage-compute-portal/click-save.png
[4]: ./media/sql-data-warehouse-manage-compute-portal/resume-database.png
[5]: ./media/sql-data-warehouse-manage-compute-portal/resume-confirm.png
[6]: ./media/sql-data-warehouse-manage-compute-portal/pause-database.png
[7]: ./media/sql-data-warehouse-manage-compute-portal/pause-confirm.png

<!--Article references-->
[Visão geral de gerenciamento]: ./sql-data-warehouse-overview-manage.md
[Visão geral sobre gerenciar computação]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->


<!--Other Web references-->

[portal do Azure]: http://portal.azure.com/



<!--HONumber=Nov16_HO3-->


