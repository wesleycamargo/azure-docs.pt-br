---
title: Executar um Databricks Notebook com a atividade Databricks Notebook no Azure Data Factory
description: Saiba como você pode usar a atividade do Databricks Notebook em um data factory do Azure para executar um Databricks Notebook no cluster de trabalhos databricks.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/12/2018
ms.author: abnarain
ms.reviewer: douglasl
ms.openlocfilehash: 7035035823e00fb0c12de3f4eeae11d8b3e1d54d
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54016901"
---
# <a name="run-a-databricks-notebook-with-the-databricks-notebook-activity-in-azure-data-factory"></a>Executar um Databricks Notebook com a atividade Databricks Notebook no Azure Data Factory

Neste tutorial, você usa o portal do Azure para criar um pipeline do Azure Data Factory que executa um Databricks Notebook em cluster de trabalhos Databricks. Ele também passa parâmetros do Azure Data Factory para o Databricks Notebook durante a execução.

Neste tutorial, você realizará os seguintes procedimentos:

  - Criar um data factory.

  - Criar um pipeline que usa a atividade Databricks Notebook.

  - Dispare uma execução de pipeline.

  - Monitorar a execução de pipeline.

Se você não tiver uma assinatura do Azure, crie uma  [conta gratuita](https://azure.microsoft.com/free/)  antes de começar.

Para ver uma introdução de 11 minutos e uma demonstração desse recurso, assista ao seguinte vídeo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/ingest-prepare-and-transform-using-azure-databricks-and-data-factory/player]

## <a name="prerequisites"></a>Pré-requisitos

  - **Workspace do Azure Databricks**. [Criar um workspace no Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) ou usar um existente. Você pode criar um notebook do Python no workspace do Azure Databricks. Em seguida, execute o notebook e passe parâmetros para ele usando o Azure Data Factory.

## <a name="create-a-data-factory"></a>Criar uma data factory

1.  Inicie o navegador da Web **Microsoft Edge** ou o **Google Chrome** . Atualmente, a interface do usuário do Data Factory tem suporte apenas nos navegadores da Web Microsoft Edge e Google Chrome.

1.  Selecione **Criar um recurso** no menu à esquerda, selecione **Análise**e, em seguida, **Data Factory**.

    ![Cria um novo data factory](media/transform-data-using-databricks-notebook/new-azure-data-factory-menu.png)

1.  No painel **Novo data factory**, digite **ADFTutorialDataFactory** em **Nome**.

    O nome do Azure data factory precisa ser *global exclusivo*. Se você vir o erro a seguir, altere o nome do data factory. (Por exemplo, use **\<seunome\>ADFTutorialDataFactory**). Para obter as regras de nomenclatura para artefatos do Data Factory, confira o artigo [Data Factory – regras de nomenclatura](https://docs.microsoft.com/azure/data-factory/naming-rules) .

    ![Fornecer um nome para o novo data factory](media/transform-data-using-databricks-notebook/new-azure-data-factory.png)

1.  Em **Assinatura**, selecione a assinatura do Azure na qual deseja criar o data factory.

1.  Em **Grupo de Recursos**, use uma das seguintes etapas:
    
    - Selecione **Usar existente** e selecione um grupo de recursos existente na lista suspensa.
    
    - Selecione **Criar** e insira o nome de um grupo de recursos.

    Algumas das etapas deste início rápido pressupõem que você esteja usando o nome **ADFTutorialResourceGroup** para o grupo de recursos. Para saber mais sobre grupos de recursos, confira [Usando grupos de recursos para gerenciar recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

1.  Em **Versão**, selecione **V2**.

1.  Em **Local**, selecione o local para o data factory.

    Para obter uma lista de regiões do Azure no qual o Data Factory está disponível no momento, selecione as regiões que relevantes para você na página a seguir e, em seguida, expanda **Análise** para localizar **Data Factory**: [Produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/). Os armazenamentos de dados (como o Armazenamento do Azure e Banco de Dados SQL do Azure) e serviços de computação (como o HDInsight) usados pelo Data Factory podem estar em outras regiões.
1.  Selecione **Criar**.


1.  Após a conclusão da criação, a página do **Data factory** será exibida. Selecione o bloco **Criar e Monitorar** para iniciar o aplicativo de interface do usuário do Data Factory em uma guia separada.

    ![Iniciar o aplicativo de interface do usuário do data factory](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image4.png)

## <a name="create-linked-services"></a>Criar serviços vinculados

Nesta seção, você cria um serviço vinculado Databricks. Esse serviço vinculado contém as informações de conexão para o cluster Databricks:

### <a name="create-an-azure-databricks-linked-service"></a>Criar um serviço vinculado do Azure Databricks

1.  Na página **Introdução**, alterne para a guia **Editar** no painel esquerdo.

    ![Editar o novo serviço vinculado](media/transform-data-using-databricks-notebook/get-started-page.png)

1.  Selecione **Conexões** na parte inferior da janela e, em seguida, **+ Nova**.
    
    ![Crie uma nova conexão](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image6.png)

1.  Na janela **Novo Serviço Vinculado**, selecione **Computação** \> **Azure Databricks** e, em seguida, **Continuar**.
    
    ![Especifique um serviço vinculado do Databricks](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image7.png)

1.  Na janela **Novo Serviço Vinculado**, conclua as seguintes etapas:
    
    1.  Em **Nome**, insira ***AzureDatabricks\_LinkedService***
    
    1.  Selecionar o **workspace do Databricks** adequado em que você executará o notebook

    1.  Para **Selecionar cluster**, selecione **Novo cluster de trabalho**
    
    1.  Para **Domínio/Região**, as informações devem ser populadas automaticamente

    1.  Em **Token de Acesso**, gere-o no espaço de trabalho do Azure Databricks. Você pode encontrar as etapas [aqui](https://docs.databricks.com/api/latest/authentication.html#generate-token).

    1.  Para **Versão do cluster**, selecione **4.0** (com o Apache Spark 2.3.0, Scala 2.11)

    1.  Para o **Tipo de nó de cluster**, selecione **Standard\_D3\_v2** na categoria **Uso Geral (HD)** para este tutorial. 
    
    1.  Para **Trabalhos**, insira **2**.
    
    1.  Selecione **Concluir**

        ![Concluir a criação do serviço vinculado](media/transform-data-using-databricks-notebook/new-databricks-linkedservice.png)

## <a name="create-a-pipeline"></a>Criar um pipeline

1.  Selecione o botão **+** (adição) e, em seguida, **Pipeline** no menu.

    ![Botões para criar um novo pipeline](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image9.png)

1.  Crie um **parâmetro** a ser usado no **Pipeline**. Mais tarde, você passará esse parâmetro para a atividade do Databricks Notebook. No pipeline vazio, clique na guia **parâmetros**, em **Novo** e chame-a de '**name**'.

    ![Criar um novo parâmetro](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image10.png)

    ![Criar o parâmetro name](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image11.png)

1.  Na caixa de ferramentas **Atividades**, expanda **Databricks**. Arraste a atividade **Notebook** da caixa de ferramentas **Atividades** para a superfície do designer de pipeline.

    ![Arrastar o notebook à superfície do designer](media/transform-data-using-databricks-notebook/new-adf-pipeline.png)

1.  Nas propriedades da janela da atividade **Notebook** do **Databricks**, na parte inferior, conclua as seguintes etapas:

     a. Alterne para a guia **Azure Databricks**.

    b. Selecione **AzureDatabricks\_LinkedService** (criado no procedimento anterior).

    c. Alternar para a guia **Configurações**

    c. Navegue para selecionar um **caminho de Notebook** do Databricks. Vamos criar um notebook e especificar o caminho aqui. Siga as próximas etapas para obter o Caminho do Notebook.

       1. Iniciar o workspace do Azure Databricks

       1. Crie uma **Nova Pasta** no local de trabalho e chame-a de **adftutorial**.

          ![Criar uma nova pasta](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image13.png)

       1. [Crie um novo Notebook](https://docs.databricks.com/user-guide/notebooks/index.html#creating-a-notebook) (Python), vamos chamá-lo de **mynotebook** na pasta **adftutorial****,** e clique em **Criar.**

          ![Criar um novo notebook](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image14.png)

          ![Definir as propriedades do novo notebook](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image15.png)

       1. No notebook recém-criado, “mynotebook”, adicione o seguinte código:

           ```
           # Creating widgets for leveraging parameters, and printing the parameters

           dbutils.widgets.text("input", "","")
           dbutils.widgets.get("input")
           y = getArgument("input")
           print "Param -\'input':"
           print y
           ```

           ![Criar widgets para parâmetros](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image16.png)

       1. O **Caminho do Notebook** nesse caso é **/adftutorial/mynotebook**

1.  Volte para a **ferramenta de criação de IU do Data Factory**. Navegue a guia **Configurações** na **Atividade Notebook1**. 
    
     a.  **Adicionar parâmetro** à atividade de Notebook. Use o mesmo parâmetro que você adicionou anteriormente ao **Pipeline**.

       ![Adicionar um parâmetro](media/transform-data-using-databricks-notebook/new-adf-parameters.png)

    b.  Nomear o parâmetro como **input** e fornecer o valor da expressão  **@pipeline().parameters.name**.

1.  Para validar o pipeline, selecione o botão **Validar** na barra de ferramentas. Para fechar a janela de validação, selecione o botão **\>\>** (seta para a direita).

    ![Validar o pipeline](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image18.png)

1.  Selecione **Publicar Tudo**. A IU de Data Factory publica entidades (serviços vinculados e pipeline) para o serviço Azure Data Factory.

    ![Publicar novas entidades do data factory](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image19.png)

## <a name="trigger-a-pipeline-run"></a>Disparar uma execução de pipeline

Selecione **Gatilho** na barra de ferramentas e, em seguida, **Disparar Agora**.

![Selecionar o comando Disparar Agora](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image20.png)

A caixa de diálogo **Execução de Pipeline** solicita o parâmetro **name**. Use **/path/filename** como o parâmetro aqui. Clique em **Concluir.**

![Forneça um valor para os parâmetros de nome](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image21.png)

## <a name="monitor-the-pipeline-run"></a>Monitorar a execução de pipeline

1.  Alterne para a guia **Monitorar**. Verifique se o pipeline está sendo executado. Leva aproximadamente 5 a 8 minutos para criar um cluster de trabalho do Databricks onde o notebook é executado.

    ![Monitorar o Pipeline](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image22.png)

1.  Selecione **Atualizar** periodicamente para verificar o status da execução de pipeline.

1.  Para ver as execuções de atividade associadas à execução de pipeline, selecione **Exibir Execuções de Atividade** na coluna **Ações**.

    ![Exibir as execuções de atividade](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image23.png)

Alterne novamente para a exibição das execuções de pipeline selecionando o link **Pipelines** na parte superior.

## <a name="verify-the-output"></a>Verificar a saída

Você pode fazer logon no **workspace do Azure Databricks**; vá para **Clusters** e veja o status do **trabalho** como *execução pendente, em execução ou concluído*.

![Exibir o cluster de trabalho e o trabalho](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image24.png)

Você pode clicar no **nome do trabalho** e navegar para ver mais detalhes. Ao executar com êxito, você pode validar os parâmetros passados e a saída do Notebook Python.

![Exibir os detalhes de execução e a saída](media/transform-data-using-databricks-notebook/databricks-output.png)

## <a name="next-steps"></a>Próximas etapas

O pipeline neste exemplo dispara uma atividade do Databricks Notebook e passa um parâmetro para ele. Você aprendeu como:

  - Criar um data factory.

  - Criar um pipeline que usa uma atividade do Databricks Notebook.

  - Dispare uma execução de pipeline.

  - Monitorar a execução de pipeline.
