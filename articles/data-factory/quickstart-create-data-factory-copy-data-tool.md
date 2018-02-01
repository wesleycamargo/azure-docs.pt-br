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
ms.openlocfilehash: aa9cdba4f4e891d5321eb8af6349d8b141faee03
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/25/2018
---
# <a name="use-the-copy-data-tool-to-copy-data"></a>Use a ferramenta Copy Data para copiar dados 
> [!div class="op_single_selector" title1="Select the version of Data Factory service that you are using:"]
> * [Versão 1 – já disponível](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Versão 2 – Versão prévia](quickstart-create-data-factory-copy-data-tool.md)

Neste guia de início rápido, você usa o Portal do Azure para criar um data factory. Em seguida, você usa a ferramenta Copy Data para criar um pipeline que copia dados de uma pasta no armazenamento de Blobs do Azure para outra pasta. 

> [!NOTE]
> Se você for novo no Azure Data Factory, confira a [Introdução ao Azure Data Factory](data-factory-introduction.md) antes de fazer este guia de início rápido. 
>
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do serviço, que já está disponível (GA), consulte [introdução ao Data Factory versão 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

## <a name="create-a-data-factory"></a>Criar uma data factory

1. Selecione **Novo** no menu à esquerda, selecione **Dados + Análise**e, em seguida, selecione **Data Factory**. 
   
   ![Seleção de Data Factory no painel "Novo"](./media/quickstart-create-data-factory-copy-data-tool/new-azure-data-factory-menu.png)
2. Na página **Novo data factory**, insira **ADFTutorialDataFactory** no campo **Nome**. 
      
   ![Página de “Novo data factory”](./media/quickstart-create-data-factory-copy-data-tool/new-azure-data-factory.png)
 
   O nome do Azure Data Factory deve ser *globalmente exclusivo*. Se você visualizar o seguinte erro, altere o nome de data factory (por exemplo, **&lt;yourname&gt;ADFTutorialDataFactory**) e tente criar novamente. Para ver as regras de nomenclatura para artefatos do Data Factory consulte o artigo [Data Factory - regras de nomenclatura](naming-rules.md).
  
   ![Erro quando um nome não está disponível](./media/quickstart-create-data-factory-portal/name-not-available-error.png)
3. Para **Assinatura**, selecione a assinatura do Azure na qual você deseja criar o data factory. 
4. Para o **Grupo de Recursos**, use uma das seguintes etapas:
     
   - Selecione **Usar existente** e selecione um grupo de recursos existente na lista. 
   - Selecione **Criar novo**e insira o nome de um grupo de recursos.   
         
   Para saber mais sobre grupos de recursos, consulte [Usando grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/resource-group-overview.md).  
4. Para **Versão**, selecione **V2 (Versão prévia)**.
5. Em **Local**, selecione uma localização para o data factory. 

   A lista mostra somente os locais com suporte. Os armazenamentos de dados (como o Armazenamento do Azure e Banco de Dados SQL do Azure) e serviços de computação (como o Azure HDInsight) usados pelo Data Factory podem estar em outros locais/regiões.

6. Selecione **Fixar no painel**.     
7. Selecione **Criar**.
8. No painel, o bloco com o seguinte status é exibido: **Implantando o Data Factory**: 

    ![Bloco “Implantando Data Factory”](media/quickstart-create-data-factory-copy-data-tool/deploying-data-factory.png)
9. Após a criação, a página do **Data Factory** será exibida. Clique no bloco **Criar e Monitorar** para iniciar o aplicativo de interface do usuário (IU) do Azure Data Factory em uma guia separada.
   
   ![Página inicial do Data Factory, com o bloco “Criar e Monitorar"](./media/quickstart-create-data-factory-copy-data-tool/data-factory-home-page.png)

## <a name="start-the-copy-data-tool"></a>Iniciar a ferramenta Copy Data

1. Na página de **Introdução**, selecione o bloco **Copy Data** para iniciar a ferramenta Copy Data. 

   ![Bloco “Copy Data”](./media/quickstart-create-data-factory-copy-data-tool/copy-data-tool-tile.png)
2. Na página **Propriedades** da ferramenta Copy Data, selecione **Avançar**. Você pode especificar um nome e sua descrição para o pipeline nesta página. 

   ![Página “Propriedades”](./media/quickstart-create-data-factory-copy-data-tool/copy-data-tool-properties-page.png)
3. Na página **Armazenamento de dados de origem**, selecione **Armazenamento de Blobs do Azure** e clique em **Avançar**.

   ![Página “Armazenamento de dados de origem”](./media/quickstart-create-data-factory-copy-data-tool/source-data-store-page.png)
4. Na página **Especificar a conta de armazenamento de Blobs do Azure**, selecione a conta de armazenamento a partir da lista **nome da conta de armazenamento** e selecione **Avançar**. 

   ![Página “Especificar a conta de armazenamento de Blobs do Azure”](./media/quickstart-create-data-factory-copy-data-tool/specify-blob-storage-account.png)
5. Na página **Escolha o arquivo de entrada ou a pasta**, execute as seguintes etapas:

   a. Navegue até a pasta **adftutorial/input**.

   b. Selecione o arquivo **emp.txt**.

   c. Selecione **Escolher**. Clique duas vezes em **emp.txt** para ignorar esta etapa.

   d. Selecione **Avançar**. 

   ![Na página “Escolher o arquivo de entrada ou a pasta”](./media/quickstart-create-data-factory-copy-data-tool/choose-input-file-folder.png)
6. Na página **Configurações de formato de arquivo**, observe que a ferramenta detecta automaticamente os delimitadores de linha e coluna e selecione **Avançar**. Você também pode visualizar os dados e exibir os esquemas dos dados de entrada nesta página. 

   ![Página “Configurações de formato de arquivo”](./media/quickstart-create-data-factory-copy-data-tool/file-format-settings-page.png)
7. Na página **Armazenamento de dados de destino**, selecione **Armazenamento de Blobs do Azure** e selecione **Avançar**. 

   ![Página “Armazenamento de dados de destino”](./media/quickstart-create-data-factory-copy-data-tool/destination-data-store-page.png)    
8. Na página **Especificar a conta de armazenamento de Blobs do Azure**, escolha a sua conta de armazenamento de Blobs do Azure e selecione **Avançar**. 

   ![Página “Especificar a conta de armazenamento de Blobs do Azure”](./media/quickstart-create-data-factory-copy-data-tool/specify-sink-blob-storage-account.png)
9. Na página **Escolha o arquivo de saída ou a pasta**, execute as seguintes etapas: 

   a. Digite **adftutorial/output** para o caminho da pasta.

   b. Digite **emp.txt** para o nome do arquivo.

   c. Selecione **Avançar**. 

   ![Página “Escolha o arquivo ou a pasta de saída”](./media/quickstart-create-data-factory-copy-data-tool/choose-output-file-folder.png) 
10. Na página **Configurações de formato de arquivo**, selecione **Avançar**. 

    ![Página “Configurações de formato de arquivo”](./media/quickstart-create-data-factory-copy-data-tool/file-format-settings-output-page.png)
11. Na página **Configurações**, selecione **Avançar**. 

    ![Página “Configurações”](./media/quickstart-create-data-factory-copy-data-tool/advanced-settings-page.png)
12. Revise todas as configurações na página **Resumo** e selecione **Avançar**. 

    ![Página “Resumo”](./media/quickstart-create-data-factory-copy-data-tool/summary-page.png)
13. Na página **Implantação concluída**, selecione **Monitor** para monitorar o pipeline que você criou. 

    ![Página “Implantação concluída”](./media/quickstart-create-data-factory-copy-data-tool/deployment-page.png)
14. O aplicativo alterna para a guia **Monitor**. Confira o status do pipeline nesta guia. Selecione **Atualizar** para atualizar a lista. 
    
    ![Guia para monitorar as execuções do pipeline, com o botão “Atualizar”](./media/quickstart-create-data-factory-copy-data-tool/monitor-pipeline-runs-page.png)
15. Selecione o link **Exibir execuções de atividade** na coluna **Ações**. O pipeline tem apenas uma atividade do tipo **Cópia**. 

    ![Lista de execuções de atividade](./media/quickstart-create-data-factory-copy-data-tool/activity-runs.png)
16. Para exibir detalhes sobre a operação de cópia, selecione **Detalhes** (imagem de óculos) na coluna **Ações**. Para obter detalhes sobre as propriedades, confira [Visão geral da atividade de cópia](copy-activity-overview.md). 

    ![Obter detalhes da operação](./media/quickstart-create-data-factory-copy-data-tool/copy-operation-details.png)
17. Verifique que o arquivo **emp.txt** foi criado na pasta de **saída** do contêiner **adftutorial**. O serviço Data Factory cria a pasta de saída automaticamente caso não exista. 
18. Acesse a guia **Editar** para que você possa editar serviços vinculados, conjuntos de dados e pipelines. Para saber mais sobre editá-los na IU do Data Factory, consulte [Criar um Data Factory usando o portal do Azure](quickstart-create-data-factory-portal.md).

    ![Guia Editar](./media/quickstart-create-data-factory-copy-data-tool/edit-tab.png)

## <a name="next-steps"></a>Próximas etapas
O pipeline neste exemplo copia dados de uma localização para outra em um Armazenamento de Blobs do Azure. Percorra os [tutoriais](tutorial-copy-data-portal.md) para saber mais sobre o uso do Data Factory em mais cenários. 