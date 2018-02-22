---
title: Carregar dados no SQL Data Warehouse usando o Azure Data Factory | Microsoft Docs
description: Usar o Azure Data Factory para copiar dados para o Azure SQL Data Warehouse
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.date: 01/17/2018
ms.author: jingwang
ms.openlocfilehash: eec6eeb3419c5f5f4c8d22398051f7cf057ac980
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2018
---
# <a name="load-data-into-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Carregar dados no SQL Data Warehouse usando o Azure Data Factory

O [Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) é um banco de dados baseado em nuvem e expansível com capacidade de processar volumes imensos de dados, relacionais e não relacionais. O SQL Data Warehouse é criado em arquitetura de processamento paralelo maciço (MPP) que é otimizado para cargas de trabalho do data warehouse. Ele oferece a elasticidade da nuvem com a flexibilidade de dimensionar o armazenamento e a computação de modo independente.

A introdução ao Azure SQL Data Warehouse agora é mais fácil do que nunca quando você usar o Azure Data Factory. O Azure Data Factory é um serviço de integração de dados baseado em nuvem completamente gerenciado. Você pode usar o serviço para preencher o SQL Data Warehouse com dados do seu sistema existente e economizar tempo ao construir suas soluções analíticas.

O Azure Data Factory oferece os seguintes benefícios para carregar dados no Azure Data SQL Data Warehouse:

* **Fácil de configurar**: assistente intuitivo de 5 etapas sem nenhum script necessário.
* **Suporte de armazenamento de dados avançados**: suporte interno para um conjunto avançado de armazenamentos de dados locais e baseados em nuvem. Para obter uma lista detalhada, consulte a tabela de [Suporte para repositórios de dados](copy-activity-overview.md#supported-data-stores-and-formats).
* **Seguro e compatível**: os dados são transferidos por HTTPS, ou ExpressRoute. A presença do serviço global garante que os dados nunca saiam do limite geográfico.
* **Desempenho incomparável usando PolyBase**: usar o Polybase é a maneira mais eficiente para mover dados ao Azure SQL Data Warehouse. Use o recurso de objeto binário em etapas para obter velocidades de alta carga de todos os tipos de armazenamentos de dados, incluindo armazenamento Azure Blob e Data Lake Store. (Polybase dá suporte ao armazenamento de Azure Blob e Azure Data Lake Store por padrão) Para obter detalhes, consulte [Desempenho da atividade de cópia](copy-activity-performance.md).

Este artigo mostra como usar a ferramenta de cópia de dados do Data Factory para _carregar dados do Banco de Dados SQL no Azure SQL Data Warehouse_. Você pode seguir as etapas semelhantes para copiar dados de outros tipos de armazenamentos de dados.

> [!NOTE]
> Para obter mais informações, consulte [Copiar dados para ou da Azure SQL Data Warehouse usando o Azure Data Factory](connector-azure-sql-data-warehouse.md).
>
> Este artigo aplica-se à versão 2 do Azure Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do serviço Data Factory, que está disponibilidade geral (GA), consulte a [atividade de cópia no Azure Data Factore versão 1](v1/data-factory-data-movement-activities.md).

## <a name="prerequisites"></a>pré-requisitos

* Assinatura do Azure: Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Azure SQL Data Warehouse: o data warehouse contém os dados copiados do Banco de Dados SQL. Se você não tiver um Azure SQL Data Warehouse, veja as instruções em [Criar um SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md).
* Azure SQL Database: este tutorial copia os dados do banco de dados do Azure SQL com os dados modelo do Adventure Works LT. Você pode criar um banco de dados seguindo as instruções em [Criar um banco de dados Azure SQL](../sql-database/sql-database-get-started-portal.md). 
* Conta de armazenamento do Azure: armazenamento do Azure é usado como _blob_ de processo de reparo na operação de cópia em massa. Se você não tiver uma conta de armazenamento do Azure, confira as instruções em [Criar uma conta de armazenamento](../storage/common/storage-create-storage-account.md#create-a-storage-account).

## <a name="create-a-data-factory"></a>Criar uma data factory

1. No menu à esquerda, selecione **Novo** > **Dados + Análise** > **Data Factory**: 
   
   ![Cria um novo data factory](./media/load-azure-sql-data-warehouse/new-azure-data-factory-menu.png)
2. Na página **Novo data factory**, forneça os valores para os campos que estão mostrados na imagem a seguir:
      
   ![Página de novo data factory](./media/load-azure-sql-data-warehouse/new-azure-data-factory.png)
 
    * **Nome**: insira um nome exclusivo para o seu Azure data factory. Se você receber o erro "Nome do Data factory \"LoadSQLDWDemo\" não está disponível," digite um nome diferente para o data factory. Por exemplo, use o nome _**seunome**_**ADFTutorialDataFactory**. Tente criar o data factory novamente. Para ver as regras de nomenclatura de artefatos do Data Factory, confira [Regras de nomenclatura do Data Factory](naming-rules.md).
    * **Assinatura**: selecione a assinatura do Azure na qual você deseja criar o data factory. 
    * **Grupo de Recursos**: Selecione um grupo de recursos existente na lista suspensa ou selecione a opção **Criar novo** e digite o nome de um grupo de recursos. Para saber mais sobre grupos de recursos, consulte [Usando grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/resource-group-overview.md).  
    * **Versão**: selecione **V2 (Versão prévia)**.
    * **Local**: selecione um local para o data factory. Somente os locais com suporte são exibidos na lista suspensa. Os armazenamentos de dados que são usados pela data factory podem estar em outros locais e regiões. Esses armazenamentos de dados incluem Azure Data Lake Store, Azure Storage, Azure SQL Database e assim por diante.

3. Selecione **Criar**.
4. Após a conclusão da criação, vá para o seu data factory. Você verá a home page **Data Factory** conforme mostrado na imagem a seguir:
   
   ![Página inicial da data factory](./media/load-azure-sql-data-warehouse/data-factory-home-page.png)

   Selecione o bloco **Autor & Monitor** para iniciar o aplicativo de integração de dados em uma guia separada.

## <a name="load-data-into-azure-sql-data-warehouse"></a>Carregar dados no SQL Data Warehouse do Azure

1. Na página **Introdução**, selecione o bloco **Copy Data** para iniciar a ferramenta Copy Data:

   ![Bloco Ferramenta Copy Data](./media/load-azure-sql-data-warehouse/copy-data-tool-tile.png)
2. Na página **Propriedades**, especifique **CopyFromSQLToSQLDW** para o campo **Nome da tarefa** e clique em **Avançar**:

    ![Página Propriedades](./media/load-azure-sql-data-warehouse/copy-data-tool-properties-page.png)
3. Na página **Armazenamento de dados de origem**, selecione **Banco de Dados SQL do Azure** e clique em **Avançar**:

    ![Página Armazenamento de dados de origem](./media/load-azure-sql-data-warehouse/specify-source.png)
4. Na página **Especificar o banco de dados SQL do Azure**, execute as seguintes etapas: 
   1. Escolha seu SQL Server do Azure para o **Nome do servidor**.
   2. Escolha seu banco de dados SQL do Azure para o **Nome do banco de dados**.
   3. Especifique o nome do usuário para **Nome de usuário**.
   4. Especifique a senha do usuário para **Senha**.
   5. Selecione **Avançar**.
   
   ![Especifique o banco de dados SQL do Azure](./media/load-azure-sql-data-warehouse/specify-source-connection.png)
5. Em **Selecione as tabelas das quais copiar os dados ou use uma página de consulta personalizada**, insira **SalesLT** para filtrar as tabelas. Escolha a caixa **(Selecionar tudo)** para usar todas as tabelas para a cópia e, em seguida, selecione **Avançar**: 

    ![Selecionar tabelas de origem](./media/load-azure-sql-data-warehouse/select-source-tables.png)

6. Na página **Armazenamento de dados de destino**, selecione **SQL Data Warehouse do Azure** e selecione **Avançar**:

    ![Página Armazenamento de dados de destino](./media/load-azure-sql-data-warehouse/specify-sink.png)
7. Na página **Especificar o SQL Data Warehouse do Azure**, execute as seguintes etapas: 

   1. Escolha seu SQL Server do Azure para o **Nome do servidor**.
   2. Escolha seu SQL Data Warehouse do Azure para o **Nome do banco de dados**.
   3. Especifique o nome do usuário para **Nome de usuário**.
   4. Especifique a senha do usuário para **Senha**.
   5. Selecione **Avançar**.
   
   ![Especifique o SQL Data Warehouse do Azure](./media/load-azure-sql-data-warehouse/specify-sink-connection.png)
8. Na página **Mapeamento de tabela**, examine o conteúdo e selecione **Avançar**. Um mapeamento de tabela inteligente é exibido. As tabelas de origem são mapeadas para as tabelas de destino com base nos nomes de tabela. Se a tabela de origem não existir no destino, por padrão o Azure Data Factory cria uma tabela de destino com o mesmo nome por padrão. Você também pode mapear uma tabela de origem para uma tabela de destino existente. 

   > [!NOTE]
   > A criação de tabela automática para o coletor do SQL Data Warehouse aplica-se quando o SQL Server ou banco de dados do SQL Azure for a origem. Se você copiar seus dados de outro armazenamento de dados de origem, você precisa criar previamente o esquema no coletor SQL Data Warehouse do Azure antes de executar a cópia de dados.

   ![Página Mapeamento de tabela](./media/load-azure-sql-data-warehouse/specify-table-mapping.png)

9. Na página **Mapeamento de esquema**, examine o conteúdo e selecione **Avançar**. O mapeamento de tabela inteligente é baseado no nome da coluna. Se você deixar o Data Factory criar automaticamente as tabelas, a conversão do tipo de dados pode ocorrer quando houver incompatibilidades entre a origem e os armazenamentos de destino. Se houver uma conversão de tipo de dados sem suporte entre a coluna de origem e de destino, você verá uma mensagem de erro próximo à tabela correspondente.

    ![Página Mapeamento de esquema](./media/load-azure-sql-data-warehouse/specify-schema-mapping.png)

11. Na página **Configurações**, selecione uma conta de armazenamento do Azure na lista suspensa **Nome da conta de armazenamento**. A conta usada para preparar os dados antes de ele carregar no SQL Data Warehouse usando PolyBase. Depois que a cópia for concluída, os dados provisórios no Azure Storage serão limpos automaticamente. Em **Configurações avançadas**, desmarque a opção **Usar padrão de tipo**:

    ![Página Configurações](./media/load-azure-sql-data-warehouse/copy-settings.png)
12. Na página **Resumo**, examine as configurações e selecione **Avançar**:

    ![Página Resumo](./media/load-azure-sql-data-warehouse/summary-page.png)
13. Na **Página Implantação**, selecione **Monitor** para monitorar o pipeline (tarefa):

    ![Página Implantação](./media/load-azure-sql-data-warehouse/deployment-page.png)
14. Observe que a guia **Monitor** à esquerda é selecionada automaticamente. A coluna **Ações** inclui links para exibir detalhes da execução da atividade e executar o pipeline novamente: 

    ![Monitorar execuções de pipeline](./media/load-azure-sql-data-warehouse/monitor-pipeline-run.png)
15. Para exibir as execuções de atividade associadas com a execução do pipeline, selecione o link **Exibir as Execuções de Atividade** na coluna **Ações**. Há 10 atividades de cópia no pipeline, cada uma copia uma tabela de dados. Para voltar à exibição de execuções de pipeline, selecione o link **Pipelines** na parte superior. Selecione **Atualizar** para atualizar a lista. 

    ![Monitorar execuções de atividade](./media/load-azure-sql-data-warehouse/monitor-activity-run.png)

16. Para monitorar os detalhes de execução de cada atividade de cópia, selecione o link **Detalhes** em **Ações** no modo de exibição de monitoramento de atividades. Você pode monitorar detalhes como o volume de dados copiados da fonte para o coletor, taxa de transferência de dados, etapas de execução com duração correspondente e configurações usadas:

    ![Monitorar detalhes da execução da atividade](./media/load-azure-sql-data-warehouse/monitor-activity-run-details.png)

## <a name="next-steps"></a>Próximas etapas

Ir para o seguinte artigo para saber mais sobre o suporte do SQL Data Warehouse do Azure: 

> [!div class="nextstepaction"]
>[Conector do SQL Data Warehouse do Azure](connector-azure-sql-data-warehouse.md).