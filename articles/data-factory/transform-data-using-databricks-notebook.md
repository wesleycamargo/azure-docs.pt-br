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
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/12/2018
ms.author: abnarain
ms.reviewer: douglasl
ms.openlocfilehash: 438aeda39c6ba9246f75f96214bb0ffa1d4d31e9
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37047801"
---
# <a name="run-a-databricks-notebook-with-the-databricks-notebook-activity-in-azure-data-factory"></a>Executar um Databricks Notebook com a atividade Databricks Notebook no Azure Data Factory

Neste tutorial, você usa o portal do Azure para criar um pipeline do Azure Data Factory que executa um Databricks Notebook em cluster de trabalhos Databricks. Ele também passa parâmetros do Azure Data Factory para o Databricks Notebook durante a execução.

Neste tutorial, você realizará os seguintes procedimentos:

  - Criar um data factory.

  - Criar um pipeline que usa a atividade Databricks Notebook.

  - Dispare uma execução de pipeline.

  - Monitorar a execução de pipeline.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

Para ver uma introdução de 11 minutos e uma demonstração desse recurso, assista ao seguinte vídeo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/ingest-prepare-and-transform-using-azure-databricks-and-data-factory/player]

## <a name="prerequisites"></a>pré-requisitos

  - **Espaço de trabalho do Azure Databricks**. [Criar um espaço de trabalho no Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) ou usar um existente. Você pode criar um notebook do Python no espaço de trabalho do Azure Databricks. Em seguida, execute o notebook e passe parâmetros para ele usando o Azure Data Factory.

## <a name="create-a-data-factory"></a>Criar uma data factory

1.  Iniciar o navegador da Web **Microsoft Edge** ou **Google Chrome**. Atualmente, a interface do usuário do Data Factory tem suporte apenas nos navegadores da Web Microsoft Edge e Google Chrome.

2.  Selecione **Novo** no menu à esquerda, selecione **Dados + Análise**e, em seguida, selecione **Data Factory**.

    ![Cria um novo data factory](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image1.png)

3.  No painel **Novo data factory**, insira **ADFTutorialDataFactory** no campo **Nome**.

    O nome do Azure Data Factory deve ser *globalmente exclusivo*. Se você vir o erro a seguir, altere o nome do data factory. (Por exemplo, use **\<seunome\>ADFTutorialDataFactory**). Para ver as regras de nomenclatura para artefatos do Data Factory consulte o artigo [Data Factory - regras de nomenclatura](https://docs.microsoft.com/azure/data-factory/naming-rules).

    ![Fornecer um nome para o novo data factory](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image2.png)

4.  Para **Assinatura**, selecione a assinatura do Azure na qual você deseja criar o data factory.

5.  Em **Grupo de Recursos**, use uma das seguintes etapas:
    
    - Selecione **Usar existente**e selecione um grupo de recursos existente na lista suspensa.
    
    - Selecione **Criar novo**e insira o nome de um grupo de recursos.

    Algumas das etapas neste guia de início rápido supõem que você usa o nome **ADFTutorialResourceGroup** para o grupo de recursos. Para saber mais sobre grupos de recursos, consulte [Usando grupos de recursos para gerenciar recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

1.  Para **Versão**, selecione **V2**.

2.  Em **Local**, selecione uma localização para o data factory.

    Atualmente, o Data Factory V2 permite que você crie os data factories somente nas regiões Leste dos EUA, Leste dos EUA 2 e Europa Ocidental. Os armazenamentos de dados (como o Armazenamento do Azure e Banco de Dados SQL do Azure) e serviços de computação (como o HDInsight) usados pelo Data Factory podem estar em outras regiões.

3.  Selecione **Fixar no painel**.

4.  Selecione **Criar**.

5.  No painel, o bloco com o seguinte status é exibido: **Implantando o Data Factory**:

    ![](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image3.png)

6.  Após a criação, a página do **Data Factory** será exibida. Selecione o bloco **Criar e Monitorar** para iniciar o aplicativo de interface do usuário do Data Factory em uma guia separada.

    ![Iniciar o aplicativo de interface do usuário do data factory](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image4.png)

## <a name="create-linked-services"></a>Criar serviços vinculados

Nesta seção, você cria um serviço vinculado Databricks. Esse serviço vinculado contém as informações de conexão para o cluster Databricks:

### <a name="create-an-azure-databricks-linked-service"></a>Criar um serviço vinculado do Azure Databricks

1.  Na página **Introdução**, acesse a guia **Editar** no painel esquerdo.

    ![Editar o novo serviço vinculado](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image5.png)

2.  Selecione **Conexões** na parte inferior da janela e, depois, selecione **+ Novo**.
    
    ![Crie uma nova conexão](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image6.png)

3.  Na janela **Novo Serviço Vinculado**, selecione **Armazenamento de Dados**\>**Azure Databricks** e **Continuar**.
    
    ![Especifique um serviço vinculado do Databricks](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image7.png)

4.  Na janela **Novo Serviço Vinculado**, execute as seguintes etapas:
    
    1.  Em **Nome,** insira ***AzureDatabricks\_LinkedService***
    
    2.  Em **Cluster**, selecione um **Novo Cluster**
    
    3.  Em **Domínio/Região**, selecione a região onde seu espaço de trabalho do Azure Databricks está localizado.
    
    4.  Em **Tipo de nó de cluster**, selecione **Standard\_D3\_v2** para este tutorial.
    
    5.  Em **Token de Acesso**, gere-o no espaço de trabalho do Azure Databricks. Você pode encontrar as etapas [aqui](https://docs.databricks.com/api/latest/authentication.html#generate-token).
    
    6.  Em **Versão de cluster,** selecione **4.0 Beta** (versão mais recente)
    
    7.  Em **Número de nós de trabalho**, insira **2**.
    
    8.  Selecione **Concluir**

        ![Concluir a criação do serviço vinculado](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image8.png)

## <a name="create-a-pipeline"></a>Criar um pipeline

1.  Selecione o botão **+** (adição) e, em seguida, selecione **Pipeline** no menu.

    ![Botões para criar um novo pipeline](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image9.png)

2.  Crie um **parâmetro** a ser usado no **Pipeline**. Mais tarde, você passará esse parâmetro para a atividade do Databricks Notebook. No pipeline vazio, clique na guia **parâmetros**, em **Novo** e chame-a de '**name**'.

    ![Criar um novo parâmetro](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image10.png)

    ![Criar o parâmetro name](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image11.png)

3.  Na caixa de ferramentas **Atividades**, expanda **Databricks**. Arraste a atividade **Notebook** da caixa de ferramentas **Atividades** para a superfície do designer do pipeline.

    ![Arrastar o notebook à superfície do designer](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image12.png)

4.  Nas propriedades da atividade **Notebook** do **Databricks**, na parte inferior, execute as seguintes etapas:

    a. Alterne para a guia **Configurações** .

    b. Selecione **myAzureDatabricks\_LinkedService** (criado no procedimento anterior).

    c. Selecione um **caminho de Notebook** do Databricks. Vamos criar um notebook e especificar o caminho aqui. Siga as próximas etapas para obter o Caminho do Notebook.

       1. Iniciar o espaço de trabalho do Azure Databricks

       2. Crie uma **Nova Pasta** no local de trabalho e chame-a de **adftutorial**.

          ![Criar uma nova pasta](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image13.png)

       3. [Crie um novo Notebook](https://docs.databricks.com/user-guide/notebooks/index.html#creating-a-notebook) (Python), vamos chamá-lo de **mynotebook** na pasta **adftutorial****,** e clique em **Criar.**

          ![Criar um novo notebook](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image14.png)

          ![Definir as propriedades do novo notebook](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image15.png)

       4. No notebook recém-criado, “mynotebook”, adicione o seguinte código:

           ```
           # Creating widgets for leveraging parameters, and printing the parameters

           dbutils.widgets.text("input", "","")
           dbutils.widgets.get("input")
           y = getArgument("input")
           print "Param -\'input':"
           print y
           ```

           ![Criar widgets para parâmetros](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image16.png)

       5. O **Caminho do Notebook** nesse caso é **/adftutorial/mynotebook**

5.  Volte para a **ferramenta de criação de IU do Data Factory**. Navegue a guia **Configurações** na **Atividade Notebook1**. 
    
    a.  **Adicionar parâmetro** à atividade de Notebook. Use o mesmo parâmetro que você adicionou anteriormente ao **Pipeline**.

       ![Adicionar um parâmetro](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image17.png)

    b.  Nomear o parâmetro como **input** e fornecer o valor da expressão  **@pipeline().parameters.name**.

6.  Para validar o pipeline, selecione o botão **Validar** na barra de ferramentas. Para fechar a janela de validação, selecione o botão **\>\>** (seta para a direita).

    ![Validar o pipeline](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image18.png)

7.  Selecione **Publicar Tudo**. A IU de Data Factory publica entidades (serviços vinculados e pipeline) para o serviço Azure Data Factory.

    ![Publicar novas entidades do data factory](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image19.png)

## <a name="trigger-a-pipeline-run"></a>Disparar uma execução de pipeline

Selecione **Gatilho** na barra de ferramentas e selecione **Disparar Agora**.

![Selecionar o comando Disparar Agora](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image20.png)

A caixa de diálogo **Execução de Pipeline** solicita o parâmetro **name**. Use **/path/filename** como o parâmetro aqui. Clique em **Concluir.**

![Forneça um valor para os parâmetros de nome](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image21.png)

## <a name="monitor-the-pipeline-run"></a>Monitorar a execução de pipeline

1.  Alterne para a guia **Monitorar**. Verifique se o pipeline está sendo executado. Leva aproximadamente 5 a 8 minutos para criar um cluster de trabalho do Databricks onde o notebook é executado.

    ![Monitorar o Pipeline](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image22.png)

2.  Selecione **Atualizar** periodicamente para verificar o status da execução do pipeline.

3.  Para ver as execuções de atividade associadas com a execução de pipeline, selecione **Exibir as Execuções de Atividade** na coluna **Ações**.

    ![Exibir as execuções de atividade](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image23.png)

Você pode alternar novamente para o modo de execução do pipeline selecionando o link **Pipelines** na parte superior.

## <a name="verify-the-output"></a>Verificar a saída

Você pode fazer logon no **espaço de trabalho do Azure Databricks**; vá para **Clusters** e veja o status do **trabalho** como *execução pendente, em execução ou concluído*.

![Exibir o cluster de trabalho e o trabalho](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image24.png)

Você pode clicar no **nome do trabalho** e navegar para ver mais detalhes. Ao executar com êxito, você pode validar os parâmetros passados e a saída do Notebook Python.

![Exibir os detalhes de execução e a saída](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image25.png)

## <a name="next-steps"></a>Próximas etapas

O pipeline neste exemplo dispara uma atividade do Databricks Notebook e passa um parâmetro para ele. Você aprendeu como:

  - Criar um data factory.

  - Criar um pipeline que usa uma atividade do Databricks Notebook.

  - Dispare uma execução de pipeline.

  - Monitorar a execução de pipeline.
