---
title: Cópia de delta de um banco de dados usando uma tabela de controle com o Azure Data Factory | Microsoft Docs
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
ms.openlocfilehash: c32592ce539eeb2dec71792e4a6eb31e7d904eff
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57771150"
---
# <a name="delta-copy-from-a-database-with-a-control-table"></a>Cópia de delta de um banco de dados com uma tabela de controle

Este artigo descreve um modelo que está disponível para carregar incrementalmente linhas novas ou atualizadas de uma tabela de banco de dados para o Azure usando uma tabela de controle externo que armazena um valor de marca d'água alta.

Este modelo requer que o esquema do banco de dados de origem contém uma chave de coluna ou incrementar de carimbo de hora para identificar linhas novas ou atualizadas.

>[!NOTE]
> Se você tiver uma coluna de carimbo de hora em seu banco de dados de origem para identificar linhas novas ou atualizadas, mas você não quiser criar uma tabela externa do controle a ser usado para cópia de delta, em vez disso, você pode usar o [ferramenta Copiar dados do Azure Data Factory](copy-data-tool.md) para obter um pipeline. Essa ferramenta usa um tempo de gatilho agendado como uma variável para ler as novas linhas do banco de dados de origem.

## <a name="about-this-solution-template"></a>Sobre o modelo de solução

Este modelo primeiro recupera o valor de marca d'água antiga e a compara com o valor atual de marca d'água. Depois disso, ele copia somente as alterações do banco de dados de origem, com base em uma comparação entre os valores de marca d' dois água. Por fim, ele armazena o novo valor de marca d'água alta para uma tabela externa do controle para o próximo tempo de carregamento de dados delta.

O modelo contém três atividades:
- **Pesquisa** recupera o valor de marca d'água alta antigo, que é armazenado em uma tabela externa do controle.
- Outra **pesquisa** atividade recupera o valor de marca d'água alta atual do banco de dados de origem.
- **Cópia** copia somente as alterações do banco de dados de origem para o armazenamento de destino. A consulta que identifica as alterações no banco de dados de origem é semelhante a ' Selecionar * de Data_Source_Table onde TIMESTAMP_Column > "último alta-marca d'água" e TIMESTAMP_Column < = "alta-marca d'água" '.
- **SqlServerStoredProcedure** grava o valor de marca d'água alta atual para uma tabela externa do controle para cópia de delta próxima vez.

O modelo define cinco parâmetros:
- *Data_Source_Table_Name* é a tabela de banco de dados de origem que você deseja carregar dados.
- *Data_Source_WaterMarkColumn* é o nome da coluna na tabela de origem que foi usado para identificar mensagens novas ou linhas atualizadas. O tipo desta coluna é normalmente *datetime*, *INT*, ou semelhantes.
- *Data_Destination_Folder_Path* ou *Data_Destination_Table_Name* é o lugar onde os dados são copiados no seu repositório de destino.
- *Control_Table_Table_Name* é a tabela de controle externo que armazena o valor de marca d'água alta.
- *Control_Table_Column_Name* é a coluna na tabela de controle externo que armazena o valor de marca d'água alta.

## <a name="how-to-use-this-solution-template"></a>Como usar este modelo de solução

1. Explorar a fonte de tabela você deseja carregar e, em seguida, definir a coluna de marca d'água alta pode ser usada para identificar linhas novas ou atualizadas. O tipo desta coluna pode ser *datetime*, *INT*, ou semelhantes. Valor dessa coluna aumenta à medida que novas linhas são adicionadas. Na tabela a seguir exemplo origem (data_source_table), podemos usar o *LastModifytime* coluna como a coluna de marca d'água alta.

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
    
2. Crie uma tabela de controle no SQL Server ou banco de dados SQL para armazenar o valor de marca d'água alta de carregamento de dados delta. No exemplo a seguir, é o nome da tabela de controle *watermarktable*. Nesta tabela, *WatermarkValue* é a coluna que armazena o valor de marca d'água alta e seu tipo for *datetime*.

    ```sql
            create table watermarktable
            (
            WatermarkValue datetime,
            );
            INSERT INTO watermarktable
            VALUES ('1/1/2010 12:00:00 AM')
    ```
    
3. Crie um procedimento armazenado na instância do SQL Server ou banco de dados SQL do Azure mesma que você usou para criar a tabela de controle. O procedimento armazenado é usado para gravar o novo valor de marca d'água alta para a tabela de controle externo para o próximo tempo de carregamento de dados delta.

    ```sql
            CREATE PROCEDURE update_watermark @LastModifiedtime datetime
            AS

            BEGIN

                UPDATE watermarktable
                SET [WatermarkValue] = @LastModifiedtime 

            END
    ```
    
4. Vá para o **cópia de Delta do banco de dados** modelo. Criar uma **New** conexão à fonte de dados que você deseja copiar dados do.

    ![Criar uma nova conexão para a tabela de origem](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable4.png)

5. Criar uma **New** conexão para o armazenamento de dados de destino que você deseja copiar os dados.

    ![Criar uma nova conexão para a tabela de destino](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable5.png)

6. Criar uma **New** conexão na tabela de controle externo e um procedimento armazenado que você criou nas etapas 2 e 3.

    ![Criar uma nova conexão para o armazenamento de dados na tabela de controle](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable6.png)

7. Selecione **Use este modelo**.

     ![Usar este modelo](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable7.png)
    
8. Você vê o pipeline disponível, conforme mostrado no exemplo a seguir:

     ![Revisar o pipeline](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable8.png)

9. Selecione **procedimento armazenado**. Para **nome do procedimento armazenado**, escolha **[update_watermark]**. Selecione **parâmetro de importação**e, em seguida, selecione **adicionar conteúdo dinâmico**.  

     ![Defina a atividade de procedimento armazenado](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable9.png) 

10. Escreva o conteúdo  **\@{activity('LookupCurrentWaterMark').output.firstRow.NewWatermarkValue}** e, em seguida, selecione **concluir**.  

     ![Escreva o conteúdo para os parâmetros do procedimento armazenado](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable10.png)      
     
11. Selecione **Debug**, insira o **parâmetros**e, em seguida, selecione **concluir**.

    ![Selecione * * * * de depuração](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

12. São exibidos resultados semelhantes ao exemplo a seguir:

    ![Revisar o resultado](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable12.png)

13. É possível criar novas linhas na tabela de origem. Aqui está a linguagem SQL de exemplo para criar novas linhas:

    ```sql
            INSERT INTO data_source_table
            VALUES (10, 'newdata','9/10/2017 2:23:00 AM')

            INSERT INTO data_source_table
            VALUES (11, 'newdata','9/11/2017 9:01:00 AM')
    ```
14. Para executar novamente o pipeline, selecione **Debug**, insira o **parâmetros**e, em seguida, selecione **concluir**.

    ![Selecione * * * * de depuração](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

    Veja de que apenas novas linhas foram copiadas para o destino.

15. (Opcional:) Se você selecionou o SQL Data Warehouse como o destino de dados, você também deve fornecer uma conexão para o armazenamento de BLOBs do Azure para preparação, o que é exigida pelo Polybase do SQL Data Warehouse. Certifique-se de que o contêiner já foi criado no armazenamento de BLOBs.
    
    ![Configurar o Polybase](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable15.png)
    
## <a name="next-steps"></a>Próximas etapas

- [Cópia em massa de um banco de dados usando uma tabela de controle com o Azure Data Factory](solution-template-bulk-copy-with-control-table.md)
- [Copiar arquivos de vários contêineres com o Azure Data Factory](solution-template-copy-files-multiple-containers.md)
