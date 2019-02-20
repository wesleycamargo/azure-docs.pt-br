---
title: Cópia delta do banco de dados com a tabela de controle com o Azure Data Factory | Microsoft Docs
description: Saiba como usar um modelo de solução para copiar linhas novas ou atualizadas de maneira incremental de um banco de dados apenas com o Azure Data Factory.
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
ms.date: 12/24/2018
ms.openlocfilehash: 23e1255013cd5e52166fe0e59a8931dd9ecd81a0
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/08/2019
ms.locfileid: "55966306"
---
# <a name="delta-copy-from-database-with-control-table"></a>Cópia delta do banco de dados com controle de tabela

Quando você deseja carregar de maneira incremental as alterações (linhas novas ou atualizadas) apenas de uma tabela em um banco de dados para o Azure com uma tabela externa de controle armazenando o valor de marca d'água alta.  O presente modelo foi desenvolvido para esse caso. 

Neste modelo, o esquema de banco de dados de origem DEVE conter a coluna de data e hora ou a chave de incrementação para identificar as linhas novas ou atualizadas.

Se você tiver uma coluna de data de hora no banco de dados de origem para identificar as linhas novas ou atualizadas, mas não desejar criar uma tabela de controle externa para permitir a cópia delta, poderá usar a ferramenta de Cópia Delta para obter um pipeline, que utiliza um tempo de disparo agendado como variável para ler as novas linhas apenas no banco de dados de origem.

## <a name="about-this-solution-template"></a>Sobre o modelo de solução

Este modelo sempre recupera o valor antigo da marca d'água primeiro e, então, compara-o ao valor atual da marca d'água. Depois disso, ele apenas copia as alterações do banco de dados de origem com base na comparação entre os dois valores da marca d'água.  Quando concluído, ele armazena o novo valor da marca d'água alta em uma tabela externa de controle para a próxima vez que ocorrer o carregamento de dados delta.

O modelo contém três atividades:
-   Uma atividade **Lookup** para recuperar o valor antigo da marca d'água alta armazenado em uma tabela externa de controle.
-   Uma atividade **Lookup** para recuperar o valor atual da marca d'água alta do banco de dados de origem.
-   Uma atividade **Copy** para copiar as alterações apenas do banco de dados de origem para o armazenamento de destino. A consulta usada para identificar as alterações no banco de dados de origem na atividade de cópia é semelhante a de 'SELECT * FROM Data_Source_Table WHERE TIMESTAMP_Column > “última marca-d'água alta” and TIMESTAMP_Column <= “última marca-d'água alta”'.
-   Uma atividade **SqlServerStoredProcedure** para gravar o valor atual da marca d'água alta em uma tabela externa de controle para a cópia delta na próxima vez.

O modelo define cinco parâmetros:
-   O parâmetro *Nome_Tabela_Fonte_Dados* é o nome da tabela do banco de dados de origem de onde você deseja carregar os dados.
-   O parâmetro *ColunaMarcaDÁgua_Fonte_Dados* é o nome da coluna na tabela de origem que pode ser usado para identificar as linhas novas ou atualizadas. Normalmente, o tipo dessa coluna pode ser datetime ou INT etc.
-   O parâmetro *Caminho_Pasta_Destino_Dados* ou *Nome_Tabela_Destino_Dados* é o lugar em que os dados são copiados para o armazenamento de destino.
-   O parâmetro *Nome_Tabela_Tabela_Controle* é o nome da tabela de controle externo para armazenar o valor da marca d'água alta.
-   O parâmetro *Nome_Coluna_Tabela_Controle* é o nome da coluna na tabela de controle externo para armazenar o valor da marca d'água alta.

## <a name="how-to-use-this-solution-template"></a>Como usar este modelo de solução

1. Explore a tabela de origem que você deseja carregar e defina a coluna de marca d'água alta que pode ser usada para dividir as linhas novas ou atualizadas. Normalmente, o tipo dessa coluna pode ser datetime ou INT etc e seus dados aumentam à medida que novas linhas são adicionadas.  Da tabela de origem no exemplo abaixo (nome da tabela: tabela_fonte_dados), é possível usar a coluna *LastModifytime* como a coluna da marca d'água alta.

    ```sql
            PersonID    Name    LastModifytime
            1   aaaa    2017-09-01 00:56:00.000
            2   bbbb    2017-09-02 05:23:00.000
            3   cccc    2017-09-03 02:36:00.000
            4   dddd    2017-09-04 03:21:00.000
            5   eeee    2017-09-05 08:06:00.000
            6   fffffff 2017-09-06 02:23:00.000
            7   gggg    2017-09-07 09:01:00.000
            8   hhhh    2017-09-08 09:01:00.000
            9   iiiiiiiii   2017-09-09 09:01:00.000
    ```
    
2. Crie uma tabela de controle em um servidor SQL ou SQL Azure para armazenar o valor da marca d'água alta para o carregamento de dados delta. No exemplo abaixo, podemos ver que o nome da tabela de controle é *watermarktable*. Nela, o nome da coluna em que será armazenado o valor da marca d'água alta é *WatermarkValue* e seu tipo é *datetime*.

    ```sql
            create table watermarktable
            (
            WatermarkValue datetime,
            );
            INSERT INTO watermarktable
            VALUES ('1/1/2010 12:00:00 AM')
    ```
    
3. Crie um procedimento de armazenado no mesmo SQL server ou no SQL Azure usado para criar a tabela de controle. O procedimento armazenado é usado para gravar o novo valor da marca d'água alta na tabela externa de controle para a próxima vez que ocorrer o carregamento de dados delta.

    ```sql
            CREATE PROCEDURE update_watermark @LastModifiedtime datetime
            AS

            BEGIN

                UPDATE watermarktable
                SET [WatermarkValue] = @LastModifiedtime 

            END
    ```
    
4. Vá para o modelo **Cópia Delta do banco de dados** e crie uma **nova conexão** para seu banco de dados de origem de onde os dados serão copiados.

    ![Criar uma nova conexão para a tabela de origem](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable4.png)

5. Crie uma **nova conexão** para seu armazenamento de dados de destino, para onde os dados são copiados.

    ![Criar uma nova conexão para a tabela de destino](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable5.png)

6. Crie uma **nova conexão** para a sua tabela externa de controle e para o procedimento armazenado.  Ele está se conectando ao banco de dados em que você criou a tabela de controle e o procedimento armazenado nas etapas 2 e 3.

    ![Criar uma nova conexão para o armazenamento de dados na tabela de controle](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable6.png)

7. Clique em **Usar este modelo**.

     ![Usar este modelo](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable7.png)
    
8. Você verá o pipeline disponível no painel, conforme mostrado no seguinte exemplo:

     ![Examine o pipeline](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable8.png)

9. Clique em Atividade de Procedimento Armazenado, selecione **nome do Procedimento Armazenado**, clique em **parâmetro de importação** e em **adicionar conteúdo dinâmico**.  

     ![Definir a atividade do procedimento armazenado](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable9.png) 

10. Escreva o conteúdo **@{activity('LookupCurrentWaterMark').output.firstRow.NewWatermarkValue}** e clique em **Concluir**.  

     ![Escrever o conteúdo do parâmetro do Procedimento Armazenado](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable10.png)      
     
11. Clique em **Depurar**, insira os parâmetros e clique em **Concluir**.

    ![Clique em Depurar](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

12. Você verá o resultado disponível no painel, conforme mostrado no seguinte exemplo:

    ![Revisar o resultado](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable12.png)

13. É possível criar novas linhas na tabela de origem.  O sql de exemplo para criar novas linhas pode ser da seguinte maneira:

    ```sql
            INSERT INTO data_source_table
            VALUES (10, 'newdata','9/10/2017 2:23:00 AM')

            INSERT INTO data_source_table
            VALUES (11, 'newdata','9/11/2017 9:01:00 AM')
    ```
13. Execute o pipeline novamente clicando em **Depurar**, insira os parâmetros e clique em **Concluir**.

    ![Clique em Depurar](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

14. Você verá que somente as novas linhas foram copiadas para o destino.

15. (Opcional) Se você selecionar o SQL Data Warehouse como destino dos dados, também será necessário inserir a conexão de um armazenamento de blobs do Azure como uma preparação, o que é exigido pelo Polybase do SQL Data Warehouse.  Verifique se o contêiner no armazenamento de blobs já foi criado.  
    
    ![Configurar o Polybase](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable15.png)
    
## <a name="next-steps"></a>Próximas etapas

- [Introdução ao Azure Data Factory](introduction.md)