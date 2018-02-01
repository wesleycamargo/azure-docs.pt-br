---
title: "Preparar dados para o tutorial de classificação da Íris nos serviços do Azure Machine Learning (versão prévia) | Microsoft Docs"
description: "Este tutorial completo mostra como usar os serviços do Azure Machine Learning (versão prévia) de ponta a ponta. Essa é a primeira parte e ela aborda a preparação dos dados."
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: tutorial
ms.date: 09/28/2017
ms.openlocfilehash: 4e558518a5a1fb7b4cd0a58fe2453fd4c083b46a
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2018
---
# <a name="classify-iris-part-1-prepare-the-data"></a>Parte 1 de Classificação da Íris: Preparar dados
Os serviços do Azure Machine Learning (versão prévia) são uma solução integrada de análise avançada e de ciência de dados de ponta a ponta para cientistas de dados profissionais prepararem dados, desenvolverem testes e implantarem modelos em escala de nuvem.

Este tutorial é a parte um de uma série de três partes. Neste tutorial, percorremos as noções básicas dos serviços do Azure Machine Learning (versão prévia). Você aprenderá como:
> [!div class="checklist"]
> * Criar um projeto no Azure Machine Learning Workbench.
> * Criar um pacote de preparação de dados.
> * Gerar código Python/PySpark para invocar um pacote de preparação de dados.

Este tutorial usa o [Conjunto de dados de flor de íris](https://en.wikipedia.org/wiki/Iris_flower_data_set) atemporal. As capturas de tela são específicas do Windows, mas a experiência no macOS é quase idêntica.

## <a name="prerequisites"></a>pré-requisitos
- Criar uma conta de Experimentação do Azure Machine Learning.
- Instalar o Azure Machine Learning Workbench.

Você pode seguir as instruções no artigo [Guia de início rápido para instalação e criação](quickstart-installation.md) para instalar o aplicativo Azure Machine Learning Workbench. A instalação também inclui a ferramenta de linha de comando de plataforma cruzada do Azure ou a CLI do Azure.

## <a name="create-a-new-project-in-azure-machine-learning-workbench"></a>Criar um novo projeto no Azure Machine Learning Workbench
1. Abra o aplicativo Azure Machine Learning Workbench e faça logon se necessário. No painel **PROJETOS**, clique no sinal de adição (**+**) para criar um **Novo Projeto**.

   ![Novo espaço de trabalho](media/tutorial-classifying-iris/new_ws.png)

2. Preencha os detalhes de **Criar Novo Projeto**: 

   ![Novo Projeto](media/tutorial-classifying-iris/new_project.png)

   - Preencha a caixa **Nome do projeto** com um nome para o projeto. Por exemplo, use o valor **myIris**.
   - Selecione o **Diretório do projeto** no qual o projeto é criado. Por exemplo, use o valor `C:\Temp\`. 
   - Insira a **Descrição do projeto**, que é opcional. 
   - O campo **Repositório Git** também é opcional e pode ser deixado em branco. Você pode fornecer um repositório Git vazio (um repositório sem ramificação mestre) existente no Visual Studio Team Services. Se você usar um repositório Git que já existe, você pode habilitar os cenários de roaming e compartilhamento mais tarde. Para obter mais informações, consulte [Usar o repositório Git](using-git-ml-project.md). 
   - Selecione um **Espaço de trabalho**, por exemplo, este tutorial usa **IrisGarden**. 
   - Selecione o modelo **Classificando a Íris** da lista de modelos de projeto. 

3. Selecione o botão **Criar**. O projeto está agora criado e aberto para você.

## <a name="create-a-data-preparation-package"></a>Criar um pacote de preparação de dados
1. Abra o arquivo **iris.csv** na **Exibição do Arquivo**. O arquivo é uma tabela com 5 colunas e 150 linhas. Ele tem quatro colunas de recurso numéricas e uma coluna de destino da cadeia de caracteres. Ele não tem cabeçalhos de coluna.

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

9. Agora, faremos um pouco de preparação de dados básicos. Renomeie os nomes das colunas. Selecione cada cabeçalho de coluna para tornar o texto do cabeçalho editável. 

   Digite **comprimento da sépala**, **largura da sépala**, **comprimento da pétala**, **largura da pétala**, e **espécies** para o cinco colunas, respectivamente.

   ![Renomeie as colunas](media/tutorial-classifying-iris/rename_column.png)

10. Para contar valores distintos, selecione a coluna **Espécies** e clique com o botão direito do mouse para selecioná-la. Selecione **Contagens de valor** no menu suspenso. 

   ![Selecione Contagens de valor](media/tutorial-classifying-iris/value_count.png)

   Essa ação abre o painel **Inspetores** e exibe um histograma com quatro barras. A coluna de destino possui três valores distintos: **Iris_virginica**, **Iris_versicolor**, **Iris-setosa** e um valor **(nulo)**.

11. Para filtrar os valores nulos, selecione a barra do grafo que representa o valor nulo. Há uma linha com um valor **(nulo)**. Para remover essa linha, selecione o sinal de subtração (**-**).

   ![Histograma de contagem de valor](media/tutorial-classifying-iris/filter_out.png)

12. Observe as etapas individuais detalhadas no painel **ETAPAS**. Como as colunas foram renomeadas e as linhas de valor nulo foram filtradas, cada ação foi gravada como uma etapa de preparação de dados. Você pode editar as etapas individuais para ajustar as configurações, reorganizar as etapas e remover etapas.

   ![Etapas](media/tutorial-classifying-iris/steps.png)

13. Feche o editor de preparação dos dados. Selecione **Fechar** (x) na guia **iris-1** com o ícone de grafo. O trabalho é salvo automaticamente no arquivo **iris-1.dprep** mostrado no cabeçalho **Preparações de dados**.

## <a name="generate-pythonpyspark-code-to-invoke-a-data-preparation-package"></a>Gerar código Python/PySpark para invocar um pacote de preparação de dados

1. Clique com o botão direito do mouse no arquivo **iris-1.dprep** para acessar o menu de contexto e escolha **Gerar arquivo de código de acesso a dados**. 

   ![Gerar código](media/tutorial-classifying-iris/generate_code.png)

2. Um novo arquivo denominado **iris-1.py** abre com as linhas de código a seguir:

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

   Esse trecho de código invoca a lógica que você criou como um pacote de preparação de dados. Dependendo do contexto no qual esse código é executado, `df` representa os vários tipos de quadros de dados. Um [DataFrame Pandas](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html) é usado durante o tempo de execução do Python, ou um [DataFrame Spark](https://spark.apache.org/docs/latest/sql-programming-guide.html) é usado quando executado em um contexto Spark. 

   Para obter mais informações sobre como preparar dados no Azure Machine Learning Workbench, consulte o guia de [Introdução à preparação de dados](data-prep-getting-started.md).

## <a name="next-steps"></a>Próximas etapas
Nessa primeira parte da série de tutoriais de três partes, você aprendeu a usar o Azure Machine Learning Workbench para:
> [!div class="checklist"]
> * Criar um novo projeto. 
> * Criar um pacote de preparação de dados.
> * Gerar código Python/PySpark para invocar um pacote de preparação de dados.

Você está pronto para passar para a próxima parte da série, onde você aprende como criar um modelo do Azure Machine Learning:
> [!div class="nextstepaction"]
> [Criar um modelo](tutorial-classifying-iris-part-2.md)
