<properties
   pageTitle="Gerenciar tarefas de escalabilidade do SQL Data Warehouse do Azure (portal do Azure) | Microsoft Azure"
   description="Tarefas do portal do Azure para escalar horizontalmente o desempenho do Azure SQL Data Warehouse. Altere os recursos de computação ajustando as DWUs. Ou, para economizar custos, pause e retome os recursos de computação."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="04/28/2016"
   ms.author="barbkess;sonyama"/>

# Gerenciar tarefas de escalabilidade do SQL Data Warehouse do Azure (portal do Azure)

> [AZURE.SELECTOR]
- [Visão geral](sql-data-warehouse-overview-scalability.md)
- [Portal](sql-data-warehouse-manage-scale-out-tasks.md)
- [PowerShell](sql-data-warehouse-manage-scale-out-tasks-powershell.md)
- [REST](sql-data-warehouse-manage-scale-out-tasks-rest-api.md)
- [TSQL](sql-data-warehouse-manage-scale-out-tasks-tsql.md)


Escale horizontalmente de forma elástica os recursos de computação e memória para atender às demandas em transformação de sua carga de trabalho e economizar custos dimensionando os recursos de volta durante os horários fora de pico.

Esta coleção de tarefas usa o portal do Azure para:

- Dimensionar o desempenho ajustando DWUs
- Pausar recursos de computação
- Retomar recursos de computação

Para saber mais sobre isso, consulte [Visão geral de escalabilidade de desempenho][].

<a name="scale-performance-bk"></a>

## Dimensionar o desempenho

[AZURE.INCLUDE [Descrição de DWUs de escala do SQL Data Warehouse](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Para alterar recursos de computação:

1. Abra o [portal do Azure][], abra o banco de dados e clique em **Escala**.

    ![Clique em Escala.][1]

1. Na folha Escala, mova o controle deslizante para a esquerda ou direita para alterar a configuração de DWU.

    ![Mover o controle deslizante][2]

1. Clique em **Salvar**. Será exibida uma mensagem de confirmação. Clique em **sim** para confirmar ou em **não** para cancelar.

    ![Clique em Salvar][3]

<a name="pause-compute-bk"></a>

## Pausar computação

[AZURE.INCLUDE [Descrição de pausa do SQL Data Warehouse](../../includes/sql-data-warehouse-pause-description.md)]

Para pausar um banco de dados:

1. Abra o [portal do Azure][] e abra seu banco de dados. Observe que o Status é **Online**. 

    ![Status online][6]

1. Para suspender os recursos de computação e de memória, clique em **Pausar** e uma mensagem de confirmação será exibida. Clique em **sim** para confirmar ou em **não** para cancelar.

    ![Confirmar pausar][7]

1. Enquanto o SQL Data Warehouse estiver iniciando o banco de dados, o status será **Pausando".
2. Quando o status for **Em pausa**, a operação de pausa terá sido concluída e não estará mais sendo cobrados pelos DWUs.

    ![Status em pausa][4]

<a name="resume-compute-bk"></a>

## Retomar a computação

[AZURE.INCLUDE [Descrição de retomada do SQL Data Warehouse](../../includes/sql-data-warehouse-resume-description.md)]Para retomar um banco de dados:

1. Abra o [portal do Azure][] e abra seu banco de dados. Observe que o Status é **Em pausa**. 

    ![Pausar banco de dados][4]

1. Para retomar do banco de dados, clique em **Iniciar** e uma mensagem de confirmação será exibida. Clique em **sim** para confirmar ou em **não** para cancelar.

    ![Confirmar retomar][5]

1. Enquanto o SQL Data Warehouse estiver iniciando o banco de dados, o status será "Retomando".
2. Quando o status for **online**, o banco de dados estará pronto.

    ![Status online][6]

<a name="next-steps-bk"></a>

## Próximas etapas
Para obter mais informações, consulte [Visão geral de gerenciamento][].

<!--Image references-->
[1]: ./media/sql-data-warehouse-manage-scale-out-tasks/click-scale.png
[2]: ./media/sql-data-warehouse-manage-scale-out-tasks/move-slider.png
[3]: ./media/sql-data-warehouse-manage-scale-out-tasks/click-save.png
[4]: ./media/sql-data-warehouse-manage-scale-out-tasks/resume-database.png
[5]: ./media/sql-data-warehouse-manage-scale-out-tasks/resume-confirm.png
[6]: ./media/sql-data-warehouse-manage-scale-out-tasks/pause-database.png
[7]: ./media/sql-data-warehouse-manage-scale-out-tasks/pause-confirm.png

<!--Article references-->
[Visão geral de gerenciamento]: ./sql-data-warehouse-overview-manage.md
[Visão geral de escalabilidade de desempenho]: ./sql-data-warehouse-overview-scalability.md

<!--MSDN references-->


<!--Other Web references-->

[portal do Azure]: http://portal.azure.com/

<!---HONumber=AcomDC_0504_2016-->