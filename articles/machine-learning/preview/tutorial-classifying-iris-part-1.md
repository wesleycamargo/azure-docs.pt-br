---
title: "Preparar dados para o tutorial de classificação da Íris nos serviços do Azure Machine Learning (versão prévia) | Microsoft Docs"
description: "Este tutorial completo mostra como usar os serviços do Azure Machine Learning (versão prévia) de ponta a ponta. Essa é a primeira parte e ela aborda a preparação dos dados."
services: machine-learning
author: hning86
ms.author: haining, j-martens
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: tutorial
ms.date: 02/28/2018
ms.openlocfilehash: 0bef557ee1394e3c786fd2c54e821b5dea28fabf
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2018
---
# <a name="tutorial-classify-iris-part-1---preparing-the-data"></a>Tutorial: Classificar Íris pare 1 - Preparar os dados

Os serviços do Azure Machine Learning (versão prévia) são uma solução integrada de análise avançada e de ciência de dados de ponta a ponta para cientistas de dados profissionais prepararem dados, desenvolverem testes e implantarem modelos em escala de nuvem.

Este tutorial é a parte um de uma série de três partes. Neste tutorial, percorremos as noções básicas dos serviços do Azure Machine Learning (versão prévia). Você aprenderá como:

> [!div class="checklist"]
> * Criar um projeto no Azure Machine Learning Workbench
> * Criar um pacote de preparação de dados
> * Gerar código Python/PySpark para invocar um pacote de preparação de dados

Este tutorial usa o [Conjunto de dados de flor de íris](https://en.wikipedia.org/wiki/Iris_flower_data_set) atemporal. As capturas de tela são específicos do Windows, mas a experiência no macOS é quase idêntica.

## <a name="prerequisites"></a>pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

Para concluir este tutorial, você deve ter:
- Uma conta de Experimentação do Azure Machine Learning
- O Azure Machine Learning Workbench instalado

Se você ainda não tiver esses, siga as etapas no artigo [Início Rápido: instalar e iniciar](quickstart-installation.md) para configurar essa conta e instalar o aplicativo Azure Machine Learning Workbench. 

## <a name="create-a-new-project-in-workbench"></a>Criar um novo projeto no Workbench

Se você seguiu as etapas no artigo [Início Rápido: instalar e iniciar](quickstart-installation.md), já deve ter este projeto e pode ir para a próxima seção.

1. Abra o aplicativo Azure Machine Learning Workbench e faça logon se necessário. 
   
   + No Windows, inicie-o usando o atalho do **Machine Learning Workbench** na área de trabalho. 
   + No macOS, selecione **Azure ML Workbench** na Launchpad.

1. Selecione o sinal de adição (+) no painel **PROJETOS**, e selecione **Novo Projeto**.  

   ![Novo espaço de trabalho](media/tutorial-classifying-iris/new_ws.png)

1. Preencha os campos de formulário e selecione o botão **Criar** para criar um novo projeto no Workbench.

   Campo|Valor sugerido para o tutorial|DESCRIÇÃO
   ---|---|---
   Nome do projeto | myIris |Insira um nome exclusivo que identifique a sua conta. Você pode usar seu próprio nome ou um nome do projeto ou departamental que melhor identifique o experimento. O nome deve ter entre 2 e 32 caracteres. Ele deve conter apenas caracteres alfanuméricos e traços (-). 
   Diretório do projeto | c:\Temp\ | Especifique o diretório no qual o projeto é criado.
   Descrição do projeto | _deixar em branco_ | Campo opcional útil para descrever os projetos.
   Visualstudio.com |_deixar em branco_ | Campo opcional. Como opção, um projeto pode ser associado a um repositório Git no Visual Studio Team Services para controle do código-fonte e colaboração. [Saiba como configurar](https://docs.microsoft.com/en-us/azure/machine-learning/preview/using-git-ml-project#step-3-set-up-a-machine-learning-project-and-git-repo). 
   Espaço de trabalho | IrisGarden (se houver) | Escolha um espaço de trabalho que você criou para sua conta de Experimentação no portal do Azure. <br/>Se você seguiu o Início Rápido, deve ter um espaço de trabalho com o nome IrisGarden. Caso contrário, selecione aquele criado quando você criou sua conta de Experimentação ou quaisquer outras que deseja usar.
   Modelo do projeto | Classificando a Íris | Os modelos contêm scripts e dados que você pode usar para explorar o produto. Este modelo contém os scripts e os dados necessários para este início rápido e outros tutoriais neste site de documentação. 

   ![Novo Projeto](media/tutorial-classifying-iris/new_project.png)
 
 Um novo projeto é criado e o seu painel é aberto com esse projeto. Neste ponto, você pode explorar a página inicial, as fontes de dados, as anotações e os arquivos de código-fonte do projeto. 

## <a name="create-a-data-preparation-package"></a>Criar um pacote de preparação de dados

Nesta parte do tutorial, você explora os dados e inicia o processo de preparação de dados. Quando você prepara os dados no Azure Machine Learning Workbench, uma representação JSON das transformações que você executa no Workbench é armazenada em um pacote de preparação de dados locais (arquivo *.dprep). Este pacote de preparação de dados é o contêiner primário para o trabalho de preparação de dados no Workbench.

Este pacote de preparação de dados pode ser entregue para a execução de um tempo de execução, como local-C#/CoreCLR, Scala/Spark, ou Scala/HDI. onde o código é gerado para o tempo de execução apropriado para execução. 

1. Selecione o ícone de pasta para abrir a exibição do arquivo e, em seguida, selecione **iris.csv** para abrir esse arquivo.  

   O arquivo é uma tabela com 5 colunas e 150 linhas. Ele tem quatro colunas de recurso numéricas e uma coluna de destino da cadeia de caracteres. Ele não tem cabeçalhos de coluna.

   ![iris.csv](media/tutorial-classifying-iris/show_iris_csv.png)

   >[!NOTE]
   > Não é recomendável incluir arquivos de dados na pasta do projeto, especialmente quando o tamanho do arquivo é grande. Incluímos **iris.csv** nesse modelo para fins de demonstração porque ele é pequeno. Para obter mais informações, consulte [Como ler e gravar arquivos de dados grandes](how-to-read-write-files.md).

2. Na **Exibição de Dados**, selecione o sinal de adição (**+**) para adicionar uma nova fonte de dados. A página **Adicionar fonte de dados** será aberta. 

   ![Exibição de dados](media/tutorial-classifying-iris/data_view.png)

3. Selecione **Arquivos de Texto (*.csv, .json, .txt)** e clique em **Avançar**.
   ![Fonte de Dados](media/tutorial-classifying-iris/data-source.png)
   

4. Navegue até o arquivo **iris.csv** e clique em **Avançar**.  
 
   ![Selecionar íris](media/tutorial-classifying-iris/select_iris_csv.png)

   >[!IMPORTANT]
   >Verifique se você selecionou o arquivo **iris.csv** de dentro do diretório do projeto atual para este exercício. Caso contrário, as etapas posteriores podem falhar.
   
5. Deixe os valores padrão e clique em **Concluir**.

6. Um novo arquivo chamado **iris-1.dsource** é criado. O arquivo é nomeado exclusivamente com "-1", pois o projeto de exemplo já é fornecido com um arquivo **iris.dsource** não numerado.  

   O arquivo é aberto e os dados são mostrados. Uma série de cabeçalhos de coluna, de **Coluna1** a **Coluna5**, são automaticamente adicionados a esse conjunto de dados. Role para baixo e observe que a última linha do conjunto de dados está vazia. A linha está vazia porque há uma quebra de linha extra no arquivo CSV.

   ![Exibição de dados de íris](media/tutorial-classifying-iris/iris_data_view.png)

7. Selecione o botão **Métricas**. Observe os histogramas. Um conjunto completo de estatísticas foi calculado para cada coluna. Você também pode selecionar o botão **Dados** para ver os dados novamente. 

   ![Exibição de dados de íris](media/tutorial-classifying-iris/iris_metrics_view.png)

8. Selecione o botão **Preparar**. A caixa de diálogo **Preparar** é aberta. 

   O projeto de exemplo é fornecido com um arquivo **iris.dprep**. Por padrão, ele solicitará que você crie um novo fluxo de dados no pacote de preparação de dados **iris.dprep** que já existe. 

   Selecione **+ Novo pacote de preparação de dados** no menu suspenso, insira um novo valor para o nome do pacote, use **íris 1**e, em seguida, selecione **OK**.

   ![Exibição de dados de íris](media/tutorial-classifying-iris/new_dprep.png)

   Um novo pacote de preparação de dados chamado **iris-1.dprep** é criado e aberto no editor de preparação de dados.

9. Agora, faremos um pouco de preparação de dados básicos. Selecione cada cabeçalho de coluna para tornar o texto do cabeçalho editável e renomeie cada coluna da seguinte maneira: 

   Digite **Comprimento da sépala**, **Largura da sépala**, **Comprimento da pétala**, **Largura da pétala**, e **Espécies** para as cinco colunas, respectivamente.

   ![Renomeie as colunas](media/tutorial-classifying-iris/rename_column.png)

10. Para contar valores distintos, selecione a coluna **Espécies** e clique com o botão direito do mouse para selecioná-la. Selecione **Contagens de valor** no menu suspenso. 

   Essa ação abre o painel **Inspetores** abaixo dos dados. Um histograma com quatro barras é exibido. A coluna de destino possui três valores distintos: **Iris_virginica**, **Iris_versicolor**, **Iris-setosa** e um valor **(nulo)**.

   ![Selecione Contagens de valor](media/tutorial-classifying-iris/value_count.png)

11. Para filtrar os valores nulos, selecione o rótulo "Nulo" e clique no sinal de subtração (**-**). Em seguida, a linha Nulo fica cinza para indicar que ele foi filtrado. 

   ![Histograma de contagem de valor](media/tutorial-classifying-iris/filter_out.png)

12. Observe as etapas individuais detalhadas no painel **ETAPAS**. Como as colunas foram renomeadas e as linhas de valor nulo foram filtradas, cada ação foi gravada como uma etapa de preparação de dados. Você pode editar as etapas individuais para ajustar as configurações, reorganizar as etapas e remover etapas.

   ![Etapas](media/tutorial-classifying-iris/steps.png)

13. Feche o editor de preparação dos dados. Selecione **Fechar** (x) na guia **iris-1** com o ícone de grafo. O trabalho é salvo automaticamente no arquivo **iris-1.dprep** mostrado no cabeçalho **Preparações de dados**.

## <a name="generate-pythonpyspark-code-to-invoke-a-data-preparation-package"></a>Gerar código Python/PySpark para invocar um pacote de preparação de dados

<!-- The output/results of a Package can be explored in Python or via a Jupyter Notebook. A Package can be executed across multiple runtimes including local Python, Spark (including in Docker), and HDInsight. A Package contains one or more Dataflows that are the steps and transforms applied to the data. A Package may use another Package as a Data Source (referred to as a Reference Data Flow). -->

1. Localize o arquivo **iris-1.dprep** na guia de Preparação de Dados.

1. Clique com o botão direito do mouse no arquivo **iris-1.dprep** e selecione **Gerar Arquivo de Código de Acesso a Dados** no menu de contexto. 

   ![Gerar código](media/tutorial-classifying-iris/generate_code.png)

   Um novo arquivo denominado **iris-1.py** é aberto com as linhas de código a seguir para invocar a lógica que você criou como um pacote de preparação de dados:

   ```python
   # Use the Azure Machine Learning data preparation package
   from azureml.dataprep import package

   # Use the Azure Machine Learning data collector to log various metrics
   from azureml.logging import get_azureml_logger
   logger = get_azureml_logger()

   # This call will load the referenced package and return a DataFrame.
   # If run in a PySpark environment, this call returns a
   # Spark DataFrame. If not, it will return a Pandas DataFrame.
   df = package.run('iris-1.dprep', dataflow_idx=0)

   # Remove this line and add code that uses the DataFrame
   df.head(10)
   ```

   Dependendo do contexto no qual esse código é executado, `df` representa os vários tipos de quadros de dados. Um [DataFrame Pandas](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html) é usado durante o tempo de execução do Python, ou um [DataFrame Spark](https://spark.apache.org/docs/latest/sql-programming-guide.html) é usado quando executado em um contexto Spark. 
   
   Para obter mais informações sobre como preparar dados no Azure Machine Learning Workbench, confira o guia de [Introdução à preparação de dados](data-prep-getting-started.md).

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você usou o Azure Machine Learning Workbench para:
> [!div class="checklist"]
> * Criar um novo projeto
> * Criar um pacote de preparação de dados
> * Gerar código Python/PySpark para invocar um pacote de preparação de dados

Você está pronto para passar para a próxima parte da série de tutoriais, onde você aprende a criar um modelo do Azure Machine Learning:
> [!div class="nextstepaction"]
> [Tutorial 2 - Criar modelos](tutorial-classifying-iris-part-2.md)
