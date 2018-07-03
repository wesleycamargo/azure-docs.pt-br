---
title: Como copiar dados usando a ferramenta Copy Data do Azure | Microsoft Docs
description: Crie um Azure Data Factory e, em seguida, use a ferramenta Copy Data para copiar um local no armazenamento de Blobs do Azure para outro local.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: hero-article
ms.date: 06/20/2018
ms.author: jingwang
ms.openlocfilehash: 4df392ec7e100ef0efcbb3876079710a6b9ca4fb
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37048019"
---
# <a name="use-the-copy-data-tool-to-copy-data"></a>Use a ferramenta Copy Data para copiar dados 
> [!div class="op_single_selector" title1="Select the version of Data Factory service that you are using:"]
> * [Versão 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Versão atual](quickstart-create-data-factory-copy-data-tool.md)

Neste guia de início rápido, você usa o Portal do Azure para criar um data factory. Em seguida, você usa a ferramenta Copy Data para criar um pipeline que copia dados de uma pasta no armazenamento de Blobs do Azure para outra pasta. 

> [!NOTE]
> Se você for novo no Azure Data Factory, confira a [Introdução ao Azure Data Factory](data-factory-introduction.md) antes de fazer este guia de início rápido. 

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
4. Para **Versão**, selecione **V2**.
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

2. Na página **Propriedades** da ferramenta de Cópia de dados, você pode especificar um nome para o pipeline e sua descrição e, depois, selecionar **Avançar**. 

   ![Página “Propriedades”](./media/quickstart-create-data-factory-copy-data-tool/copy-data-tool-properties-page.png)
3. Na página **Configurações do armazenamento de dados**, conclua as seguintes etapas:

    a. Clique em **+ Criar nova conexão** para adicionar uma conexão.

    ![Página “Armazenamento de dados de origem”](./media/quickstart-create-data-factory-copy-data-tool/new-source-linked-service.png)

    b. Selecione **Armazenamento de Blobs do Azure** da galeria e, em seguida, selecione **Avançar**.

    ![Selecione o armazenamento de blob da galeria](./media/quickstart-create-data-factory-copy-data-tool/select-blob-source.png)

    c. Na página **Especificar a conta de armazenamento de Blobs do Azure**, selecione a conta de armazenamento a partir da lista **nome da conta de armazenamento** e selecione **Concluir**. 

   ![Configurar a conta de armazenamento de Blobs do Azure](./media/quickstart-create-data-factory-copy-data-tool/configure-blob-storage.png)

   d. Selecione o serviço vinculado recém-criado como fonte, depois clique **Avançar**.

   ![Selecione o serviço de origem vinculado](./media/quickstart-create-data-factory-copy-data-tool/select-source-linked-service.png)


4. Na página **Escolha o arquivo de entrada ou a pasta**, execute as seguintes etapas:

   a. Clique em **Navegar** para navegar até a pasta **adftutorial/input**, selecione o arquivo **emp.txt**, depois clique em **Escolher**. 

   ![Na página “Escolher o arquivo de entrada ou a pasta”](./media/quickstart-create-data-factory-copy-data-tool/configure-source-path.png)

   d. Marque a opção **Cópia binária** para copiar o arquivo no estado em que se encontra, depois selecione **Avançar**. 

   ![Na página “Escolher o arquivo de entrada ou a pasta”](./media/quickstart-create-data-factory-copy-data-tool/select-binary-copy.png)


5. Na página **Armazenamento de dados de destino**, selecione o serviço vinculado do **Armazenamento de Blobs do Azure** que você acabou de criar e selecione **Avançar**. 

   ![Página “Armazenamento de dados de destino”](./media/quickstart-create-data-factory-copy-data-tool/select-sink-linked-service.png)

6. Na página **Escolher o arquivo ou a pasta de saída**, insira **adftutorial/output** no caminho de pasta, depois selecione **Avançar**. 

   ![Página “Escolha o arquivo ou a pasta de saída”](./media/quickstart-create-data-factory-copy-data-tool/configure-sink-path.png) 

7. Na página **Configurações**, selecione **Avançar** para usar as configurações padrão. 

8. Na página **Resumo**, revise todas as configurações e selecione **Avançar**. 

    ![Página “Resumo”](./media/quickstart-create-data-factory-copy-data-tool/summary-page.png)

9. Na página **Implantação concluída**, selecione **Monitor** para monitorar o pipeline que você criou. 

    ![Página “Implantação concluída”](./media/quickstart-create-data-factory-copy-data-tool/deployment-page.png)

10. O aplicativo alterna para a guia **Monitor**. Confira o status do pipeline nesta guia. Selecione **Atualizar** para atualizar a lista. 
    
    ![Monitorar execução do pipeline](./media/quickstart-create-data-factory-copy-data-tool/pipeline-monitoring.png)

11. Selecione o link **Exibir execuções de atividade** na coluna **Ações**. O pipeline tem apenas uma atividade do tipo **Cópia**. 

    ![Monitorar a execução de atividade](./media/quickstart-create-data-factory-copy-data-tool/activity-monitoring.png)
    
12. Para exibir detalhes sobre a operação de cópia, selecione **Detalhes** (imagem de óculos) na coluna **Ações**. Para obter detalhes sobre as propriedades, confira [Visão geral da atividade de cópia](copy-activity-overview.md).

    ![Obter detalhes da operação](./media/quickstart-create-data-factory-copy-data-tool/activity-execution-details.png)

13. Verifique que o arquivo **emp.txt** foi criado na pasta de **saída** do contêiner **adftutorial**. O serviço Data Factory cria a pasta de saída automaticamente caso não exista. 

14. Alterne para a guia **Autor** acima da guia **Monitor** no painel esquerdo para que você possa editar serviços vinculados, conjuntos de dados e pipelines. Para saber mais sobre editá-los na IU do Data Factory, consulte [Criar um Data Factory usando o portal do Azure](quickstart-create-data-factory-portal.md).

## <a name="next-steps"></a>Próximas etapas
O pipeline neste exemplo copia dados de uma localização para outra em um Armazenamento de Blobs do Azure. Percorra os [tutoriais](tutorial-copy-data-portal.md) para saber mais sobre o uso do Data Factory em mais cenários. 