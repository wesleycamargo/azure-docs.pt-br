<properties
   pageTitle="Gerenciar poder de computação no SQL Data Warehouse do Azure (portal do Azure) | Microsoft Azure"
   description="Tarefas do portal do Azure para gerenciar o poder de computação. Dimensionar recursos de computação ajustando as DWUs. Ou, para economizar custos, pause e retome os recursos de computação."
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
   ms.date="05/06/2016"
   ms.author="barbkess;sonyama"/>

# Gerenciar poder de computação no SQL Data Warehouse do Azure (portal do Azure)

> [AZURE.SELECTOR]
- [Visão geral](sql-data-warehouse-overview-manage-compute.md)
- [Portal](sql-data-warehouse-manage-compute-portal.md)
- [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
- [REST](sql-data-warehouse-manage-compute-rest-api.md)
- [TSQL](sql-data-warehouse-manage-compute-tsql.md)


Dimensionar o desempenho escalando horizontalmente recursos de computação e memória para atender às demandas de mudança de sua carga de trabalho. Economizar custos reduzindo recursos durante horários que não sejam de pico ou pausando a computação.

Esta coleção de tarefas usa o portal do Azure para:

- Computação de escala
- Pausar computação
- Retomar a computação

Para saber mais sobre isso, consulte [Visão geral sobre gerenciar poder de computação][].

<a name="scale-performance-bk"></a> <a name="scale-compute-bk"></a>

## Dimensionar poder de computação

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
2. Quando o status for **Em pausa**, a operação de pausa terá sido concluída e não estará mais sendo cobrados pelas DWUs.

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
[1]: ./media/sql-data-warehouse-manage-compute-portal/click-scale.png
[2]: ./media/sql-data-warehouse-manage-compute-portal/move-slider.png
[3]: ./media/sql-data-warehouse-manage-compute-portal/click-save.png
[4]: ./media/sql-data-warehouse-manage-compute-portal/resume-database.png
[5]: ./media/sql-data-warehouse-manage-compute-portal/resume-confirm.png
[6]: ./media/sql-data-warehouse-manage-compute-portal/pause-database.png
[7]: ./media/sql-data-warehouse-manage-compute-portal/pause-confirm.png

<!--Article references-->
[Visão geral de gerenciamento]: ./sql-data-warehouse-overview-manage.md
[Visão geral sobre gerenciar poder de computação]: ./sql-data-warehouse-overview-manage-compute.md

<!--MSDN references-->


<!--Other Web references-->

[portal do Azure]: http://portal.azure.com/

<!---HONumber=AcomDC_0518_2016-->