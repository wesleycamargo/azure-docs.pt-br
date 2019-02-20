---
title: Cópia em massa do banco de dados com a tabela de controle usando o Azure Data Factory | Microsoft Docs
description: Saiba como usar um modelo de solução para copiar completamente os dados em massa de um banco de dados usando uma tabela de controle externa, para armazenar uma lista de partições das tabelas de origem com o Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/14/2018
ms.openlocfilehash: b267da18f2537e462ecda0ac265eac07a069c293
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/08/2019
ms.locfileid: "55966328"
---
# <a name="bulk-copy-from-database-with-control-table"></a>Cópia em massa do banco de dados com tabela de controle

Quando você quer copiar dados do seu data warehouse, como o servidor Oracle, servidor Netezza, servidor Teradata ou do SQL Server para o Azure, é preciso carregar uma enorme quantidade de dados de várias tabelas em fontes de dados. Na maioria dos casos, os dados precisam ser ainda mais particionados em cada tabela para que seja possível carregar linhas com vários threads paralelamente de uma única tabela. O presente modelo foi desenvolvido para esse caso. 

Se quiser copiar dados de um pequeno número de tabelas com pequeno volume de dados, é mais eficiente usar a ferramenta "copiar dados" para ter uma única atividade de cópia ou a atividade foreach + a atividade de cópia em seu pipeline. Esse modelo é o mais adequado para esse caso de uso simples.

## <a name="about-this-solution-template"></a>Sobre o modelo de solução

Esse modelo recupera a lista de partições do banco de dados de origem de uma tabela de controle externa, que precisa ser copiada para o repositório de destino e, em seguida, itera em cada partição no banco de dados de origem e executa a operação de cópia de dados.

O modelo contém três atividades:
-   Uma atividade **Lookup** para recuperar a lista de partições do banco de dados de origem de uma tabela de controle externa.
-   Uma atividade **ForEach** para obter a lista de partições da atividade de pesquisa e, em seguida, iterar cada uma delas para a atividade de cópia.
-   Uma atividade **Copy** para copiar cada partição do armazenamento de dados de origem para o armazenamento de destino.

O modelo define cinco parâmetros:
-   O parâmetro *Control_Table_Name* é o nome da tabela de controle externa. A tabela de controle é usada para armazenar a lista de partições do banco de dados de origem.
-   O parâmetro *Control_Table_Schema_PartitionID* é o nome da coluna em sua tabela de controle externa para armazenar cada ID da Partição. Verifique se a ID da Partição é exclusiva para cada partição no banco de dados de origem.
-   O parâmetro *Control_Table_Schema_SourceTableName* é o nome da coluna em sua tabela de controle externa para armazenar cada nome de tabela do banco de dados de origem.
-   O parâmetro *Control_Table_Schema_FilterQuery* é o nome da coluna em sua tabela de controle externa para armazenar a consulta de filtro a fim de obter os dados de cada partição no banco de dados de origem. Por exemplo, se você particionou os dados por ano, a consulta armazenada em cada linha pode ser semelhante a 'select * from datasource where LastModifytime >= ''2015-01-01 00:00:00'' e LastModifytime <= ''2015-12-31 23:59:59.999'''
-   O parâmetro *Data_Destination_Folder_Path* é o caminho da pasta em que os dados são copiados no seu armazenamento de destino.  Esse parâmetro fica visível somente quando o destino escolhido é um repositório de armazenamento baseado em arquivo.  Se você escolher o SQL Data Warehouse como o armazenamento de destino, não há parâmetros obrigatórios a inserir aqui. Porém, os nomes de tabela e o esquema no SQL Data Warehouse devem ser os mesmos que os do banco de dados de origem.

## <a name="how-to-use-this-solution-template"></a>Como usar este modelo de solução

1. Crie uma tabela de controle em um SQL Server ou SQL Azure para armazenar a lista de partições do banco de dados de origem para a cópia em massa.  No exemplo abaixo, você pode ver que há cinco partições no seu banco de dados de origem; três partições são para uma tabela (*datasource_table*) e duas partições são para outra tabela (*project_table*). A coluna *LastModifytime* é usada para particionar os dados na tabela *datasource_table* do banco de dados de origem. A consulta usada para ler a primeira partição é 'select * from datasource_table where LastModifytime >= ''2015-01-01 00:00:00'' and LastModifytime <= ''2015-12-31 23:59:59.999'''.  Você também pode ver a consulta semelhante para ler dados de outras partições. 

     ```sql
            Create table ControlTableForTemplate
            (
            PartitionID int,
            SourceTableName  varchar(255),
            FilterQuery varchar(255)
            );

            INSERT INTO ControlTableForTemplate
            (PartitionID, SourceTableName, FilterQuery)
            VALUES
            (1, 'datasource_table','select * from datasource_table where LastModifytime >= ''2015-01-01 00:00:00'' and LastModifytime <= ''2015-12-31 23:59:59.999'''),
            (2, 'datasource_table','select * from datasource_table where LastModifytime >= ''2016-01-01 00:00:00'' and LastModifytime <= ''2016-12-31 23:59:59.999'''),
            (3, 'datasource_table','select * from datasource_table where LastModifytime >= ''2017-01-01 00:00:00'' and LastModifytime <= ''2017-12-31 23:59:59.999'''),
            (4, 'project_table','select * from project_table where ID >= 0 and ID < 1000'),
            (5, 'project_table','select * from project_table where ID >= 1000 and ID < 2000');
    ```

2. Vá para o modelo **Cópia em massa do Banco de Dados** e crie uma **nova conexão** com a sua tabela de controle externa.  Essa conexão é estabelecida com o banco de dados em que você criou a tabela de controle na etapa 1.

    ![Criar uma nova conexão com a tabela de controle](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable2.png)

3. Crie uma **nova conexão** com o seu banco de dados de origem, de onde os dados são copiados.

     ![Criar uma nova conexão com o banco de dados de origem](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable3.png)
    
4. Crie uma **nova conexão** para seu armazenamento de dados de destino, para onde os dados são copiados.

    ![Criar uma nova conexão com o armazenamento de destino](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable4.png)

5. Clique em **Usar este modelo**.

    ![Usar este modelo](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable5.png)
    
6. Você verá o pipeline disponível no painel, conforme mostrado no seguinte exemplo:

    ![Revisar o pipeline](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable6.png)

7. Clique em **Depurar**, insira os parâmetros e clique em **Concluir**

    ![Clicar em Depurar](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable7.png)

8. Você verá o resultado disponível no painel, conforme mostrado no seguinte exemplo:

    ![Revisar o resultado](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable8.png)

9. (Opcional) Se você selecionar o SQL Data Warehouse como destino dos dados, também será necessário inserir a conexão de um armazenamento de blobs do Azure como uma preparação, o que é exigido pelo Polybase do SQL Data Warehouse.  Verifique se o contêiner no armazenamento de blobs já foi criado.  
    
    ![Configuração do Polybase](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable9.png)
       
## <a name="next-steps"></a>Próximas etapas

- [Introdução ao Azure Data Factory](introduction.md)