---
title: Como copiar dados usando a ferramenta Copy Data do Azure | Microsoft Docs
description: Crie um Azure Data Factory e, em seguida, use a ferramenta Copy Data para copiar uma pasta no armazenamento de Blobs do Azure para outra pasta.
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.topic: hero-article
ms.date: 01/16/2018
ms.author: jingwang
ms.openlocfilehash: c17e738e3db18503f7cdda24a5f01ceb78e4e6a3
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/18/2018
---
# <a name="use-copy-data-tool-to-copy-data"></a>Use a ferramenta Copy Data para copiar dados 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 – já disponível](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Versão 2 – Versão prévia](quickstart-create-data-factory-copy-data-tool.md)

Neste guia de início rápido, você usa o Portal do Azure para criar um data factory. Em seguida, você usa a ferramenta Copy Data para criar um pipeline que copia dados de uma pasta no armazenamento de Blobs do Azure para outra pasta. 

> [!NOTE]
> Se você for novo no Azure Data Factory, confira a [Introdução ao Azure Data Factory](data-factory-introduction.md) antes de fazer este guia de início rápido. 
>
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do serviço Data Factory, que já está disponível (GA), confira [introdução ao Data Factory versão 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

## <a name="create-a-data-factory"></a>Criar uma data factory

1. Clique em **Novo** no menu à esquerda, clique em **Dados + Análise** e clique em **Data Factory**. 
   
   ![Novo -> DataFactory](./media/quickstart-create-data-factory-copy-data-tool/new-azure-data-factory-menu.png)
2. Na página **Novo data factory**, insira **ADFTutorialDataFactory** no campo **nome**. 
      
     ![Página de novo data factory](./media/quickstart-create-data-factory-copy-data-tool/new-azure-data-factory.png)
 
   O nome do Azure Data Factory deve ser **globalmente exclusivo**. Se a seguinte mensagem de erro for exibida para o campo nome, altere o nome do data factory (por exemplo, yournameADFTutorialDataFactory) e tente criar novamente. Confira o artigo [Data Factory - regras de nomenclatura](naming-rules.md) para ver as regras de nomenclatura para artefatos do Data Factory.
  
     ![Erro - nome não disponível](./media/quickstart-create-data-factory-portal/name-not-available-error.png)
3. Selecione a **assinatura** do Azure na qual você deseja criar o data factory. 
4. Para o **Grupo de Recursos**, execute uma das seguintes etapas:
     
      - Selecione **Usar existente**e selecione um grupo de recursos existente na lista suspensa. 
      - Selecione **Criar novo**e insira o nome de um grupo de recursos.   
         
      Para saber mais sobre grupos de recursos, consulte [Usando grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/resource-group-overview.md).  
4. Selecione **V2 (Versão Prévia)** para a **versão**.
5. Selecione o **local** do data factory. Somente os locais com suporte são exibidos na lista suspensa. Os armazenamentos de dados (Armazenamento do Azure, Banco de Dados SQL do Azure, etc.) e serviços de computação (HDInsight, etc.) usados pelo data factory podem estar em outros locais/regiões.
6. Selecione **Fixar no painel**.     
7. Clique em **Criar**.
8. No painel, você vê o seguinte bloco com status: **Implantando data factory**. 

    ![implantando bloco data factory](media/quickstart-create-data-factory-copy-data-tool/deploying-data-factory.png)
9. Após a criação, a página do **Data Factory** será exibida conforme mostrado na imagem.
   
   ![Página inicial da data factory](./media/quickstart-create-data-factory-copy-data-tool/data-factory-home-page.png)
10. Clique no bloco **Autor & Monitor** para iniciar a interface de usuário (IU) do Azure Data Factory em uma guia separada. 

## <a name="launch-copy-data-tool"></a>Iniciar a ferramenta Copy Data

1. Na página de Introdução, clique no bloco **Copiar dados** para iniciar a ferramenta Copy Data. 

   ![Bloco Ferramenta Copy Data](./media/quickstart-create-data-factory-copy-data-tool/copy-data-tool-tile.png)
2. Na página **Propriedades** da ferramenta Copy Data, clique em **Avançar**. Você pode especificar um nome e sua descrição para o pipeline nesta página. 

    ![Ferramenta Copy Data - página de propriedades](./media/quickstart-create-data-factory-copy-data-tool/copy-data-tool-properties-page.png)
3. Na página **Armazenamento de dados de origem**, selecione **Armazenamento de Blobs do Azure** e clique em **Avançar**.

    ![Página Armazenamento de dados de origem](./media/quickstart-create-data-factory-copy-data-tool/source-data-store-page.png)
4. Na página **Especificar a conta de armazenamento de Blobs do Azure**, escolha o **nome da conta de armazenamento** na lista suspensa e clique em Avançar. 

    ![Especifique a conta de armazenamento de Blobs](./media/quickstart-create-data-factory-copy-data-tool/specify-blob-storage-account.png)
5. Na página **Escolha o arquivo de entrada ou a pasta**, execute as seguintes etapas:

    1. Navegue até a pasta **adftutorial/input**. 
    2. Escolha o arquivo **emp.txt**.
    3. Clique em **Escolher**. Clique duas vezes em **emp.txt** para ignorar esta etapa. 
    4. Clique em **Próximo**. 

    ![Escolha a pasta ou arquivo de entrada](./media/quickstart-create-data-factory-copy-data-tool/choose-input-file-folder.png)
6. Na página **Configurações de formato de arquivo**, observe que a ferramenta detecta automaticamente os delimitadores de linha e coluna e clique em **Avançar**. Você também pode visualizar os dados e exibir o esquema dos dados de entrada nesta página. 

    ![Página Configurações de formato de arquivo](./media/quickstart-create-data-factory-copy-data-tool/file-format-settings-page.png)
7. Na página **Armazenamento de dados de destino**, selecione **Armazenamento de Blobs do Azure** e clique em **Avançar**. 

    ![Página Armazenamento de dados de destino](./media/quickstart-create-data-factory-copy-data-tool/destination-data-store-page.png)    
8. Na página **Especificar a conta de armazenamento de Blobs do Azure**, escolha a sua conta de armazenamento de Blobs do Azure e clique em **Avançar**. 

    ![Especifique a página de armazenamento de dados do coletor](./media/quickstart-create-data-factory-copy-data-tool/specify-sink-blob-storage-account.png)
9. Na página **Escolha o arquivo de saída ou a pasta**, execute as seguintes etapas: 

    1. Digite **adftutorial/output** para o **caminho da pasta**.
    2. Digite **emp.txt** para o **nome do arquivo**. 
    3. Clique em **Próximo**. 

    ![Escolha o arquivo ou a pasta de saída](./media/quickstart-create-data-factory-copy-data-tool/choose-output-file-folder.png) 
10. Na página **Configurações de formato de arquivo**, clique em **Avançar**. 

    ![Página Configurações de formato de arquivo](./media/quickstart-create-data-factory-copy-data-tool/file-format-settings-output-page.png)
11. Clique em **próximo** na página **Configurações**. 

    ![Página Configurações avançadas](./media/quickstart-create-data-factory-copy-data-tool/advanced-settings-page.png)
12. Revise todas as configurações na página **Resumo** e clique em Avançar. 

    ![Página Resumo](./media/quickstart-create-data-factory-copy-data-tool/summary-page.png)
13. Na página **Implantação concluída**, clique em **Monitor** para monitorar o pipeline que você criou. 

    ![Página Implantação](./media/quickstart-create-data-factory-copy-data-tool/deployment-page.png)
14. O aplicativo alterna para a guia **Monitor**. Confira o status do pipeline nesta página. Clique em **Atualizar** para atualizar a lista. 
    
    ![Página Monitorar execuções de pipeline](./media/quickstart-create-data-factory-copy-data-tool/monitor-pipeline-runs-page.png)
15. Clique no link **Exibir atividades em execução** na coluna de ações. O pipeline tem apenas uma atividade do tipo **Cópia**. 

    ![Página Atividades em execução](./media/quickstart-create-data-factory-copy-data-tool/activity-runs.png)
16. Para exibir detalhes sobre a operação de cópia, clique em **Detalhes** (imagem de óculos) na coluna **Ações**. Para obter detalhes sobre as propriedades, confira [Visão geral da atividade de cópia](copy-activity-overview.md). 

    ![Obter detalhes da operação](./media/quickstart-create-data-factory-copy-data-tool/copy-operation-details.png)
17. Verifique que o arquivo **emp.txt** foi criado na pasta de **saída** do contêiner **adftutorial**. O serviço Data Factory cria a pasta de saída automaticamente caso não exista. 
18. Acesse a guia **Editar** para editar serviços vinculados, conjuntos de dados e pipelines. Para saber mais sobre editá-los na IU do Data Factory, confira em [Criar um Data Factory usando o portal do Azure](quickstart-create-data-factory-portal.md).

    ![Guia Editar](./media/quickstart-create-data-factory-copy-data-tool/edit-tab.png)

## <a name="next-steps"></a>Próximas etapas
O pipeline nessa amostra copia dados de uma localização para outra em um Armazenamento de Blobs do Azure. Percorra os [tutoriais](tutorial-copy-data-portal.md) para saber mais sobre o uso do Data Factory em mais cenários. 