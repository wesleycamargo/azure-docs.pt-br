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
ms.openlocfilehash: 36e24da50386d1abc441e2beb09f570a9612a346
ms.sourcegitcommit: 828cd4b47fbd7d7d620fbb93a592559256f9d234
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/18/2018
---
# <a name="load-data-into-azure-sql-data-warehouse-using-azure-data-factory"></a>Carregar dados no SQL Data Warehouse usando o Azure Data Factory

O [Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) é um banco de dados baseado em nuvem e expansível com capacidade de processar volumes imensos de dados, relacionais e não relacionais.  Criado em arquitetura MPP (processamento paralelo maciço), o SQL Data Warehouse é otimizado para cargas de trabalho do data warehouse corporativas.  Ele oferece a elasticidade da nuvem com a flexibilidade de dimensionar o armazenamento e a computação de modo independente.

A introdução ao Azure SQL Data Warehouse agora é mais fácil do que nunca, usando o **Azure Data Factory**.  O Azure Data Factory é um serviço de integração de dados baseado em nuvem totalmente gerenciado, que pode ser usado para popular um SQL Data Warehouse com os dados de seu sistema existente e economizar tempo valioso ao avaliar o SQL Data Warehouse e criar suas soluções de análise. Aqui estão os principais benefícios de carregar dados no Azure SQL Data Warehouse usando o Azure Data Factory:

* **Fácil de configurar**: assistente intuitivo de 5 etapas sem nenhum script necessário.
* **Suporte de armazenamento de dados avançados**: suporte interno para um conjunto avançado de armazenamentos de dados locais e baseados em nuvem, para ver lista detalhada na tabela [armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
* **Seguro e compatível**: os dados são transferidos por HTTPS ou ExpressRoute e a presença global do serviço garante que os dados nunca saiam do limite geográfico
* **Desempenho incomparável usando PolyBase**: usar o Polybase é a maneira mais eficiente para mover dados para o SQL Data Warehouse. Usando o recurso de blob de preparo, você pode obter velocidades de alta carga de todos os tipos de armazenamentos de dados além do Armazenamento de Blobs do Azure e Data Lake Store, ao qual o Polybase dá suporte por padrão. Obtenha detalhes de [desempenho da atividade de cópia](copy-activity-performance.md).

Este artigo mostra como usar a ferramenta de cópia de dados do Data Factory para **carregar dados do Banco de Dados SQL no Azure SQL Data Warehouse**. Você pode seguir as etapas semelhantes para copiar dados de outros tipos de armazenamentos de dados.

> [!NOTE]
>  Para obter informações gerais sobre as funcionalidades do Data Factory para copiar dados bidirecionalmente no SQL Data Warehouse do Azure, consulte o artigo [Copiar dados bidirecionalmente no SQL Data Warehouse do Azure usando o Azure Data Factory](connector-azure-sql-data-warehouse.md).
>
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do serviço Data Factory, que está com GA (disponibilidade geral), consulte a [Atividade de cópia na V1](v1/data-factory-data-movement-activities.md).

## <a name="prerequisites"></a>pré-requisitos

* **Assinatura do Azure**. Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.
* **SQL Data Warehouse do Azure**. Esse data warehouse contém os dados copiados do Banco de Dados SQL. Se você não tiver um SQL Data Warehouse do Azure, veja o artigo [Criar um SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md) para conhecer as etapas para criar um.
* **Banco de dados SQL do Azure**. Este tutorial copias dados de um Banco de Dados SQL do Azure contendo dados de exemplo do Adventure Works LT, seguindo o artigo [Criar um Banco de Dados SQL do Azure](../sql-database/sql-database-get-started-portal.md). 
* **Conta de Armazenamento do Azure**. O Armazenamento do Azure é usada como Blobs **de preparo** na operação de cópia em massa. Se você não tiver uma conta de Armazenamento do Azure, veja o artigo [Criar uma conta de armazenamento](../storage/common/storage-create-storage-account.md#create-a-storage-account) para conhecer as etapas para criar uma.

## <a name="create-a-data-factory"></a>Criar uma data factory

1. Clique em **NOVO** no menu à esquerda, clique em **Dados + Analytics** e clique em **Data Factory**. 
   
   ![Novo -> DataFactory](./media/load-azure-sql-data-warehouse/new-azure-data-factory-menu.png)
2. Na página **Novo data factory**, forneça os valores conforme mostrado na captura de tela a seguir:
      
     ![Página de novo data factory](./media/load-azure-sql-data-warehouse/new-azure-data-factory.png)
 
   * **Name.** Insira um nome global exclusivo para o armazenamento de dados. Se você receber o seguinte erro, altere o nome de data factory (por exemplo, yournameADFTutorialDataFactory) e tente criar novamente. Confira o artigo [Data Factory - regras de nomenclatura](naming-rules.md) para ver as regras de nomenclatura para artefatos do Data Factory.
  
            `Data factory name "LoadSQLDWDemo" is not available`

    * **Subscription.** Selecione a **assinatura** do Azure na qual você deseja criar o data factory. 
    * **Grupo de Recursos.** Selecione um grupo de recursos existente na lista suspensa ou selecione a opção **Criar novo** e digite o nome de um grupo de recursos. Para saber mais sobre grupos de recursos, consulte [Usando grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/resource-group-overview.md).  
    * **Versão.** Selecione **V2 (versão prévia)**.
    * **Local.** Selecione o local para o data factory. Somente os locais com suporte são exibidos na lista suspensa. Os armazenamentos de dados (Armazenamento do Azure, Banco de Dados SQL do Azure, etc.) usados pelo data factory podem estar em outros locais/regiões. 

3. Clique em **Criar**.
4. Após a criação, vá ao seu data factory, a página do **Data Factory** será exibida conforme mostrado na imagem. Clique no bloco **Autor & Monitor** para iniciar o aplicativo de integração de dados em uma guia separada.
   
   ![Página inicial da data factory](./media/load-azure-sql-data-warehouse/data-factory-home-page.png)

## <a name="load-data-into-azure-sql-data-warehouse"></a>Carregar dados no SQL Data Warehouse do Azure

1. Na página de Introdução, clique no bloco **Copiar dados** para iniciar a ferramenta Copy Data. 

   ![Bloco Ferramenta Copy Data](./media/load-azure-sql-data-warehouse/copy-data-tool-tile.png)
2. Na página **Propriedades** da ferramenta Copy Data, especifique **CopyFromSQLToSQLDW** para o **Nome da tarefa** e clique em **Avançar**. 

    ![Ferramenta Copy Data - página de propriedades](./media/load-azure-sql-data-warehouse/copy-data-tool-properties-page.png)
3. Na página **Armazenamento de dados de origem**, selecione **Banco de Dados SQL do Azure** e clique em **Avançar**.

    ![Página Armazenamento de dados de origem](./media/load-azure-sql-data-warehouse/specify-source.png)
4. Na página **Especificar o banco de dados SQL do Azure**, execute as seguintes etapas: 
    1. Escolha seu SQL Server do Azure para o **Nome do servidor**.
    2. Escolha seu banco de dados SQL do Azure para o **Nome do banco de dados**.
    3. Especifique o nome do usuário para **Nome de usuário**.
    4. Especifique a senha do usuário para **Senha**.
    5. Clique em **Próximo**. 

        ![Especifique o banco de dados SQL do Azure](./media/load-azure-sql-data-warehouse/specify-source-connection.png)
5. Na página **Selecionar tabelas da qual copiar os dados ou usar uma consulta personalizada**, filtre as tabelas especificando **SalesLT** na caixa de entrada, em seguida, marque a caixa de seleção **(Selecionar tudo)** para selecionar todas as tabelas e, em seguida, clique em **Avançar**. 

    ![Escolha a pasta ou arquivo de entrada](./media/load-azure-sql-data-warehouse/select-source-tables.png)

6. Na página **Armazenamento de dados de destino**, selecione **SQL Data Warehouse do Azure** e clique em **Avançar**. 

    ![Página Armazenamento de dados de destino](./media/load-azure-sql-data-warehouse/specify-sink.png)
7. Na página **Especificar o SQL Data Warehouse do Azure**, execute as seguintes etapas: 

    1. Escolha seu SQL Server do Azure para o **Nome do servidor**.
    2. Escolha seu SQL Data Warehouse do Azure para o **Nome do banco de dados**.
    3. Especifique o nome do usuário para **Nome de usuário**.
    4. Especifique a senha do usuário para **Senha**.
    5. Clique em **Próximo**. 

        ![Especifique o SQL Data Warehouse do Azure](./media/load-azure-sql-data-warehouse/specify-sink-connection.png)
8. Na página **Mapeamento de tabela**, revise e clique em **Avançar**. É exibido um mapeamento de tabela inteligente, que mapeia tabelas de origem para tabelas de destino com base em nomes de tabelas. Se a tabela não existir no destino, por padrão o Azure Data Factory cria uma com o mesmo nome. Também é possível mapear para uma tabela existente. 

    > [!NOTE]
    > A criação de tabela automática para o coletor do SQL Data Warehouse aplica-se quando o SQL Server ou banco de dados do SQL Azure é a origem. Se você copiar dados de outro armazenamento de dados de origem, você precisa criar previamente o esquema no coletor SQL Data Warehouse do Azure antes da cópia de dados.

    ![Página Mapeamento de tabela](./media/load-azure-sql-data-warehouse/specify-table-mapping.png)

9. Na página **Mapeamento de esquema**, revise e clique em **Avançar**. O mapeamento inteligente é baseado no nome da coluna. Se você escolher permitir que o Data Factory crie automaticamente as tabelas, a conversão apropriada de tipo de dados poderá ocorrer se for necessário corrigir a incompatibilidade entre os repositórios de origem e destino. Se houver uma conversão de tipo de dados sem suporte entre a coluna de origem e de destino, você verá uma mensagem de erro juntamente com a tabela correspondente.

    ![Página Mapeamento de esquema](./media/load-azure-sql-data-warehouse/specify-schema-mapping.png)

11. Na página **Configurações**, selecione um armazenamento do Azure na lista suspensa **Nome da conta de armazenamento**. É usado para preparar os dados antes de ele carregar no SQL Data Warehouse usando PolyBase. Depois que a cópia for concluída, os dados provisórios no armazenamento serão limpos automaticamente. 

    Em "Configurações avançadas", desmarque a opção "Usar padrão de tipo".

    ![Página Configurações](./media/load-azure-sql-data-warehouse/copy-settings.png)
12. Na página **Resumo** examine as configurações e clique em **Avançar**.

    ![Página Resumo](./media/load-azure-sql-data-warehouse/summary-page.png)
13. Na **página Implantação**, clique em **Monitor** para monitorar o pipeline (tarefa).

    ![Página Implantação](./media/load-azure-sql-data-warehouse/deployment-page.png)
14. Observe que a guia **Monitor** à esquerda é selecionada automaticamente. Confira os links para exibir detalhes de execução da atividade e executar novamente o pipeline da coluna **Ações**. 

    ![Monitorar execuções de pipeline](./media/load-azure-sql-data-warehouse/monitor-pipeline-run.png)
15. Para ver as atividades em execução associadas com o pipeline de execução, clique em **Exibir atividades em execução** na coluna **Ações**. Há 10 atividades de cópia no pipeline, cada uma copia uma tabela de dados. Para alternar novamente para a exibição de pipeline em execução, Clique em **Pipelines** na parte superior. Clique em **Atualizar** para atualizar a lista. 

    ![Monitorar execuções de atividade](./media/load-azure-sql-data-warehouse/monitor-activity-run.png)

16. Você pode monitorar ainda mais detalhes de execução de cada atividade de cópia, clicando no link **Detalhes** em **Ações** no modo de exibição de monitoramento de atividades. Ele mostra as informações, incluindo o volume de dados copiados da fonte para o coletor, a taxa de transferência, as etapas realizadas com a duração correspondente e as configurações usadas.

    ![Monitorar detalhes da execução da atividade](./media/load-azure-sql-data-warehouse/monitor-activity-run-details.png)

## <a name="next-steps"></a>Próximas etapas

Ir para o seguinte artigo para saber mais sobre o suporte do SQL Data Warehouse do Azure: 

> [!div class="nextstepaction"]
>[Conector do SQL Data Warehouse do Azure](connector-azure-sql-data-warehouse.md).