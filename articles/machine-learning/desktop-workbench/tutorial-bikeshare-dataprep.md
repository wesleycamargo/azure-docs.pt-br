---
title: Tutorial de compartilhamento de bicicleta – preparação de dados avançada com o Azure Machine Learning Workbench
description: Neste tutorial, você realizará uma tarefa de preparação de dados de ponta a ponta usando o Azure Machine Learning Workbench
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc
ms.topic: tutorial
ms.date: 09/21/2017
ms.openlocfilehash: 951ce8947d113eaad2ea0e3b5df5e9714aa33dd8
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38723180"
---
# <a name="tutorial-use-azure-machine-learning-workbench-for-advanced-data-preparation-bike-share-data"></a>Tutorial: usar o Azure Machine Learning Workbench para preparação avançada de dados (Dados de compartilhamento de bicicleta)
O Azure Machine Learning (versão prévia) é uma solução de análise avançada e integrada de ciência de dados de ponta a ponta para cientistas profissionais prepararem dados, desenvolverem experimentos e implantarem modelos em escala de nuvem.

Neste tutorial, você usará o Machine Learning (versão prévia) para aprender como:
> [!div class="checklist"]
> * Preparar dados interativamente com a ferramenta de preparação de dados do Machine Learning.
> * Importar, transformar e criar um conjunto de dados de teste.
> * Gerar um pacote de preparação de dados.
> * Executar o pacote de preparação de dados usando o Python.
> * Gerar um conjunto de dados de treinamento reutilizando o pacote de preparação de dados para arquivos de entrada adicionais.
> * Executar scripts em uma janela de CLI do Azure local.
> * Executar scripts em um ambiente do Azure HDInsight na nuvem.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>pré-requisitos

* Uma instalação local do Azure Machine Learning Workbench. Para obter mais informações, siga o [Guia de início rápido de instalação](../service/quickstart-installation.md).
* Caso não tenha a CLI do Azure instalada, siga as instruções para [instalar a versão mais recente da CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
* Um [cluster HDInsights Spark](how-to-create-dsvm-hdi.md#create-an-apache-spark-for-azure-hdinsight-cluster-in-azure-portal) criado no Azure.
* Uma conta de armazenamento do Azure.
* Familiaridade com o método de criação de um novo projeto no Workbench.
* Embora não seja necessário, é útil ter o [Gerenciador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) instalado, de modo que seja possível carregar, baixar e exibir os blobs na conta de armazenamento.

## <a name="data-acquisition"></a>Aquisição de dados
Este tutorial usa o [conjunto de dados Boston Hubway](https://s3.amazonaws.com/hubway-data/index.html) e os dados de clima de Boston da [NOAA](http://www.noaa.gov/).

1. Baixe os arquivos de dados nos links a seguir para o seu ambiente de desenvolvimento local:

   * [Dados de clima de Boston](https://azuremluxcdnprod001.blob.core.windows.net/docs/azureml/bikeshare/BostonWeather.csv)

   * Dados de trajeto do Hubway do site do Hubway:

      - [201501-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201501-hubway-tripdata.zip)
      - [201504-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201504-hubway-tripdata.zip)
      - [201510-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201510-hubway-tripdata.zip)
      - [201601-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201601-hubway-tripdata.zip)
      - [201604-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201604-hubway-tripdata.zip)
      - [201610-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201610-hubway-tripdata.zip)
      - [201701-hubway-tripdata.zip](https://s3.amazonaws.com/hubway-data/201701-hubway-tripdata.zip)

2. Descompacte cada arquivo .zip após o download.

## <a name="upload-data-files-to-azure-blob-storage"></a>Carregar arquivos de dados no armazenamento do Blob do Azure
É possível usar o armazenamento de Blobs do Azure para hospedar os arquivos de dados.

1. Use a mesma conta de armazenamento usada para o cluster HDInsight que você usa.

    ![Contas de armazenamento do cluster HDInsight](media/tutorial-bikeshare-dataprep/hdinsightstorageaccount.png)

2. Crie um novo contêiner chamado **data-files** para armazenar os arquivos de dados do **BikeShare**.

3. Carregue os arquivos de dados. Carregue `BostonWeather.csv` em uma pasta chamada `weather`. Carregue os arquivos de dados de viagem em uma pasta chamada `tripdata`.

    ![Carregar arquivos de dados](media/tutorial-bikeshare-dataprep/azurestoragedatafile.png)

> [!TIP]
> Também é possível usar o Gerenciador de Armazenamento para carregar blobs. Também use essa ferramenta quando desejar exibir o conteúdo de qualquer um dos arquivos gerados no tutorial.

## <a name="learn-about-the-datasets"></a>Saiba mais sobre conjuntos de dados
1. O arquivo __Clima de Boston__ contém os seguintes campos relacionados ao clima, relatados de hora em hora:

   * **DATE**

   * **REPORTTPYE**

   * **HOURLYDRYBULBTEMPF**

   * **HOURLYRelativeHumidity**

   * **HOURLYWindSpeed**

2. Os dados do __Hubway__ são organizados em arquivos por ano e por mês. Por exemplo, o arquivo chamado `201501-hubway-tripdata.zip` contém um arquivo .csv que contém dados de janeiro de 2015. Os dados contêm os campos a seguir, com cada linha representando um trajeto de bicicleta:

   * **Duração do trajeto (em segundos)**

   * **Data e hora de início**

   * **Data e hora de parada**

   * **Nome e ID da estação inicial**

   * **Nome e ID da estação final**

   * **ID da bicicleta**

   * **Tipo de usuário (Casual = Usuário com permissão de 24 horas ou 72 horas; Membro = Membro anual ou mensal)**

   * **CEP (se o usuário for membro)**

   * **Sexo (declarado pelo próprio membro)**

## <a name="create-a-new-project"></a>Criar um novo projeto
1. Inicie o **Machine Learning Workbench** no menu Iniciar ou usando o iniciador.

2. Crie um novo projeto do Machine Learning. Selecione o botão **+** na página **Projetos** ou selecione **Arquivo** > **Novo**.

   * Use o modelo **Bike Share**.

   * Nomeie o projeto **BikeShare**. 

## <a id="newdatasource"></a>Criar uma nova fonte de dados

1. Crie uma nova fonte de dados. Selecione o botão **Dados** (ícone de cilindro) na barra de ferramentas à esquerda para ver o modo de exibição de **Dados**.

   ![Guia Exibição de dados](media/tutorial-bikeshare-dataprep/navigatetodatatab.png)

2. Adicione uma fonte de dados. Selecione o ícone **+** e depois **Adicionar Fonte de Dados**.

   ![Opção Adicionar Fonte de Dados](media/tutorial-bikeshare-dataprep/newdatasource.png)

## <a name="add-weather-data"></a>Adicionar dados de clima

1. **Armazenamento de Dados**: selecione o armazenamento de dados que contém os dados. Como você está usando arquivos, selecione **Arquivo(s)/Diretório**. Selecione **Avançar** para continuar.

   ![Entrada Arquivo(s)/Diretório](media/tutorial-bikeshare-dataprep/datasources.png)

2. **Seleção de Arquivo**: adicione os dados de clima. Procure e selecione o arquivo `BostonWeather.csv` que você carregou anteriormente no Armazenamento de Blobs. Selecione **Avançar**.

   ![Seleção de arquivo com o BostonWeather.csv selecionado](media/tutorial-bikeshare-dataprep/azureblobpickweatherdatafile.png)

3. **Detalhes do Arquivo**: verifique o esquema de arquivo detectado. O Machine Learning Workbench analisa os dados no arquivo e deduz o esquema a ser usado.

   ![Verificar detalhes do arquivo](media/tutorial-bikeshare-dataprep/fileparameters.png)

   > [!IMPORTANT]
   > O Workbench pode não detectar o esquema correto em alguns casos. Sempre verifique se os parâmetros estão corretos para o seu conjunto de dados. Para os dados de clima, verifique se eles estão configurados com os seguintes valores:
   >
   > * __Tipo de Arquivo__: arquivo delimitado (csv, tsv, txt, etc.)
   > * __Separador__: vírgula [,]
   > * __Caractere de Linha de Comentário__: nenhum valor definido.
   > * __Modo Ignorar Linhas__: não ignorar
   > * __Codificação de Arquivo__: UTF-8
   > * __Modo Promover Cabeçalhos__: usar cabeçalhos do primeiro arquivo

   A visualização dos dados deve exibir as seguintes colunas:

   * **Caminho**

   * **DATE**

   * **REPORTTYPE**

   * **HOURLYDRYBULBTEMPF**
   
   * **HOURLYRelativeHumidity**

   * **HOURLYWindSpeed**

   Para continuar, selecione **Avançar**.

4. **Tipos de Dados**: examine os tipos de dados que são detectados automaticamente. O Machine Learning Workbench analisa os dados no arquivo e deduz os tipos de dados a serem usados.

   a. Para esses dados, mude o **TIPO DE DADOS** de todas as colunas para **Cadeia de caracteres**.

   > [!NOTE]
   > A cadeia de caracteres é usada para realçar os recursos do Workbench em partes posteriores deste tutorial. 

   ![Revisar tipos de dados](media/tutorial-bikeshare-dataprep/datatypedetection.png)

   b. Para continuar, selecione __Avançar__. 

5. **Amostragem**: para criar um esquema de amostragem, selecione **Editar**. Selecione a nova linha __10000 Principais__ que foi adicionada e, em seguida, selecione __Editar__. Defina a __Estratégia de Exemplo__ como **Arquivo Completo** e, em seguida, selecione **Aplicar**.

   ![Adicione uma nova estratégia de amostragem](media/tutorial-bikeshare-dataprep/weatherdatasamplingfullfile.png)

   Para usar a estratégia __Arquivo Completo__, selecione a entrada __Arquivo Completo__ e, em seguida, selecione __Definido como Ativo__. Uma estrela é exibida ao lado de __Arquivo Completo__ para indicar que essa é a estratégia ativa.

   ![Arquivo Completo definido como a estratégia ativa](media/tutorial-bikeshare-dataprep/fullfileactive.png)

   Para continuar, selecione **Avançar**.

6. **Coluna de Caminho**: use a seção __Coluna de Caminho__ para incluir o caminho de arquivo completo como uma coluna nos dados importados. Selecione __Não Incluir Coluna de Caminho__.

   > [!TIP]
   > Incluir o caminho como uma coluna será útil caso esteja importando uma pasta de muitos arquivos com nomes de arquivo diferentes. Também será útil se os nomes de arquivo contiverem informações que você deseja extrair mais tarde.

   ![Coluna de Caminho definida para não incluir](media/tutorial-bikeshare-dataprep/pathcolumn.png)

7. **Concluir**: para concluir a criação da fonte de dados, selecione **Concluir**.

    Uma nova guia de fonte de dados chamada __BostonWeather__ é aberta. Um exemplo de dados é exibido em uma exibição em grade. O exemplo é baseado no esquema de amostragem ativo especificado anteriormente.

    Observe que o painel **Etapas** no lado direito da tela exibe as ações individuais tomadas durante a criação dessa fonte de dados.

   ![Exibição da fonte de dados, do exemplo e das etapas](media/tutorial-bikeshare-dataprep/weatherdataloaded.png)

### <a name="view-data-source-metrics"></a>Exibir métricas de fonte de dados

Selecione __Métricas__ no canto superior esquerdo do modo de exibição em grade da guia. Essa exibição mostra a distribuição e outras estatísticas agregadas dos dados de amostra.

![Exibição das métricas](media/tutorial-bikeshare-dataprep/weathermetrics.png)

> [!NOTE]
> Para configurar a visibilidade das estatísticas, use a lista suspensa **Escolher Métrica**. Selecione e limpe as métricas nesse local para alterar o modo de exibição em grade.

Para retornar ao modo de exibição de __Dados__, selecione __Dados__ no canto superior esquerdo da página.

## <a name="add-a-data-source-to-the-data-preparation-package"></a>Adicionar uma fonte de dados ao pacote de preparação de dados

1. Selecione __Preparar__ para começar a preparar os dados. 

2. Quando solicitado, insira um nome para o pacote de preparação de dados, como **BikeShare Data Prep**. 

3. Selecione __OK__ para continuar.

   ![Caixa de diálogo Preparar](media/tutorial-bikeshare-dataprep/dataprepdialog.png)

4. Um novo pacote chamado **BikeShare Data Prep** aparece na seção __Preparação de Dados__ da guia __Dados__. 

   Para exibir o pacote, selecione essa entrada. 

5. Selecione o botão **>>** para expandir o __Fluxos de Dados__ e exibir os fluxos de dados contidos no pacote. Neste exemplo, __BostonWeather__ é o único fluxo de dados.

   > [!IMPORTANT]
   > Um pacote pode conter vários fluxos de dados.

   ![Fluxos de dados no pacote](media/tutorial-bikeshare-dataprep/weatherdataloadedingrid.png)

## <a name="filter-data-by-value"></a>Filtrar dados por valor
1. Para filtrar os dados, clique com o botão direito do mouse em uma célula com um determinado valor e selecione __Filtrar__. Em seguida, escolha o tipo de filtro.

2. Para este tutorial, selecione uma célula que contenha o valor `FM-15`. Em seguida, defina o filtro como **é igual a**.  Agora os dados estão filtrados para retornar apenas as linhas em que __REPORTTYPE__ é `FM-15`.

   ![Caixa de diálogo Filtro](media/tutorial-bikeshare-dataprep/weatherfilterinfm15.png)

   > [!NOTE]
   > FM-15 é um tipo de relatório de clima de Informe Meteorológico Regular de Aeródromo (METAR). Os relatórios FM-15 são considerados empiricamente os mais completos, com poucos dados ausentes.

## <a name="remove-a-column"></a>Remover uma coluna

Você não precisa mais da coluna __REPORTTYPE__. Clique com botão direito do mouse no cabeçalho da coluna e selecione **Remover Coluna**.

   ![Opção Remover Coluna](media/tutorial-bikeshare-dataprep/weatherremovereporttype.png)

## <a name="change-datatypes-and-remove-errors"></a>Alterar tipos de dados e remover erros
1. Selecione Ctrl (comando ⌘ no Mac) enquanto seleciona os cabeçalhos de coluna para selecionar várias colunas ao mesmo tempo. Use essa técnica para selecionar os seguintes cabeçalhos de coluna:

   * **HOURLYDRYBULBTEMPF**

   * **HOURLYRelativeHumidity**

   * **HOURLYWindSpeed**

2. Clique com botão direito do mouse em um dos cabeçalhos de coluna selecionados e selecione **Converter o Tipo de Campo em Numérico**. Essa opção converte o tipo de dados das colunas para numérico.

   ![Converter várias colunas para numérico](media/tutorial-bikeshare-dataprep/weatherconverttonumeric.png)

3. Filtre os valores de erro. Algumas colunas têm problemas de conversão de tipo de dados. Esse problema é indicado pela cor vermelha na __Barra de Qualidade de Dados__ da coluna.

   Para remover as linhas que apresentam erros, clique com o botão direito do mouse no título de coluna **HOURLYDRYBULBTEMPF**. Selecione **Filtrar Coluna**. Use o **Eu Quero** padrão como **Manter Linhas**. Altere a lista suspensa **Condições** para selecionar **não é erro**. Selecione **OK** para aplicar o filtro.

   ![Filtrar valores de erro](media/tutorial-bikeshare-dataprep/filtererrorvalues.png)

4. Para eliminar as linhas de erro restantes nas outras colunas, repita esse processo de filtro para as colunas **HOURLYRelativeHumidity** e **HOURLYWindSpeed**.

## <a name="use-by-example-transformations"></a>Usar transformações pelo exemplo

Para usar os dados em uma previsão de blocos de tempo de duas horas, você deve calcular as condições médias de clima em períodos de duas horas. Execute as seguintes ações:

* Divida a coluna **DATE** nas colunas **Data** e **Hora** separadas. Consulte a seção a seguir para obter as etapas detalhadas.

* Derive uma coluna **Intervalo_de_Hora** da coluna **Hora**. Consulte a seção a seguir para obter as etapas detalhadas.

* Derive uma coluna **Intervalo\_de_Data\_e_Hora** das colunas **DATE** e **Intervalo_de_Hora**. Consulte a seção a seguir para obter as etapas detalhadas.

### <a name="split-column-by-example"></a>Dividir colunas por exemplo

1. Divida a coluna **DATE** nas colunas **Data** e **Hora** separadas. Clique com botão direito do mouse no cabeçalho da coluna **DATE** e selecione **Dividir Coluna por Exemplo**.

   ![Entrada Dividir Colunas por Exemplo](media/tutorial-bikeshare-dataprep/weathersplitcolumnbyexample.png)

2. O Machine Learning Workbench identifica automaticamente um delimitador significativo e cria duas colunas, dividindo os dados em valores de data e hora. 

3. Selecione __OK__ para aceitar os resultados da operação de divisão.

   ![Divisão de colunas DATE_1 e DATE_2](media/tutorial-bikeshare-dataprep/weatherdatesplitted.png)

### <a name="derive-column-by-example"></a>Derivar colunas por exemplo

1. Para derivar um intervalo de duas horas, clique com o botão direito do mouse no cabeçalho da coluna __DATE\_2__ e selecione **Derivar Coluna por Exemplo**.

   ![Entrada Derivar Colunas por Exemplo](media/tutorial-bikeshare-dataprep/weatherdate2range.png)

   Uma nova coluna vazia é adicionada com valores nulos.

2. Selecione a primeira célula vazia na nova coluna. Para fornecer um exemplo do intervalo de tempo desejado, digite **12AM-2AM** na nova coluna e pressione Enter.

   ![Nova coluna com o valor 12AM-2AM](media/tutorial-bikeshare-dataprep/weathertimerangeexample.png)

   > [!NOTE]
   > O Machine Learning Workbench sintetiza um programa com base nos exemplos fornecidos por você e aplica o mesmo programa às linhas restantes. Todas as outras linhas são preenchidas automaticamente com base no exemplo fornecido. O Workbench também analisa os dados e tenta identificar casos extremos. 

   > [!IMPORTANT]
   > A identificação de casos extremos pode não funcionar no Mac com a versão atual do Workbench. Ignore as etapas 3 e 4 a seguir no Mac. Em vez disso, selecione __OK__ depois que todas as linhas tiverem sido preenchidas com os valores derivados.
   
3. O texto **Analisando Dados** acima da grade indica que o Workbench está tentando detectar os casos extremos. Ao concluir, o status será alterado para **Examinar a próxima linha sugerida** ou **Nenhuma sugestão**. Neste exemplo, é retornado **Examinar a próxima linha sugerida**.

4. Para examinar as alterações sugeridas, selecione **Revisar a próxima linha sugerida**. A célula que você deve examinar e corrigir, se necessário, está realçada na exibição.

   ![Examinar próxima linha sugerida](media/tutorial-bikeshare-dataprep/weatherreviewnextsuggested.png)

    Selecione __OK__ para aceitar a transformação.
 
5. Você retorna para a exibição em grade dos dados do __BostonWeather__. Agora, a grade contém as três colunas adicionadas anteriormente.

   ![Modo de exibição em grade com linhas adicionadas](media/tutorial-bikeshare-dataprep/timerangecomputed.png)

   > [!TIP]
   > Todas as alterações feitas são preservadas no painel **Etapas**. Vá para a etapa que você criou no painel **Etapas**, selecione a seta para baixo e depois **Editar**. A janela avançada de **Derivar Coluna pelo Exemplo** é exibida. Todos os seus exemplos são preservados aqui. Também é possível adicionar exemplos manualmente clicando duas vezes em uma linha na grade a seguir. Selecione **Cancelar** para retornar à grade principal sem aplicar alterações. Também é possível acessar esse modo de exibição selecionando **Modo Avançado** durante a execução de uma transformação de **Derivar Coluna por Exemplo**.

6. Para renomear a coluna, clique duas vezes no cabeçalho da coluna e digite **Intervalo de Hora**. Selecione Enter para salvar a alteração.

   ![Renomear a coluna](media/tutorial-bikeshare-dataprep/weatherhourrangecolumnrename.png)

7. Para derivar o intervalo de data e hora, faça a seleção múltipla das colunas **Date\_1** e **Intervalo de Hora**, clique com o botão direito do mouse e selecione **Derivar Coluna por Exemplo**.

   ![Derivar Colunas por Exemplo](media/tutorial-bikeshare-dataprep/weatherderivedatehourrange.png)

   Digite **Jan 01, 2015 12AM-2AM**, conforme o exemplo, na primeira linha e pressione Enter.

   O Workbench determina a transformação com base no exemplo que você fornece. Neste exemplo, o resultado é que o formato de data é alterado e concatenado com o período de duas horas.

   ![O exemplo Jan 01, 2015 12AM-2AM](media/tutorial-bikeshare-dataprep/wetherdatehourrangeexample.png)

   > [!IMPORTANT]
   > No Mac, siga a etapa seguir em vez da etapa 8:
   >
   > * Vá para a primeira célula que contém **Feb 01, 2015 12AM-2AM**. Deve ser a linha 15. Corrija o valor para **Jan 02, 2015 12AM-2AM** e pressione Enter. 
   

8. Espere até que o status seja alterado de **Analisando Dados** para **Examinar a próxima linha sugerida**. Essa alteração pode levar alguns segundos. Selecione o link de status para ir até a linha sugerida. 

   ![Linha sugerida para examinar](media/tutorial-bikeshare-dataprep/wetherdatehourrangedisambiguate.png)

   A linha tem um valor nulo porque o valor da data de origem pode ser dd/mm/aaaa ou mm/dd/aaaa. Digite o valor correto de **Jan 13, 2015 2AM-4AM** e pressione Enter. O Workbench usa os dois exemplos para melhorar a derivação das linhas restantes.

   ![Dados formatados corretamente](media/tutorial-bikeshare-dataprep/wetherdatehourrangedisambiguated.png)

9. Selecione **OK** para aceitar a transformação.

   ![Grade com a transformação concluída](media/tutorial-bikeshare-dataprep/weatherdatehourrangecomputed.png)

   > [!TIP]
   > Para usar o **Modo avançado** de **Derivar coluna por exemplo** para essa etapa, selecione a seta para baixo no painel **Etapas**. Na grade de dados, há caixas de seleção ao lado das colunas **DATE\_1** e **Intervalo de Hora**. Limpe a caixa de seleção ao lado da coluna **Intervalo de Hora** para ver como a saída é alterada. Na ausência da coluna **Intervalo de Hora** como entrada, **12AM-2AM** será tratado como uma constante e será anexado aos valores derivados. Selecione **Cancelar** para retornar à grade principal sem aplicar as alterações.
   ![Modo Avançado](media/tutorial-bikeshare-dataprep/derivedcolumnadvancededitdeselectcolumn.png)

10. Para renomear a coluna, clique duas vezes no cabeçalho. Altere o nome para **Intervalo de Data e Hora** e pressione Enter.

11. Faça a seleção múltipla das colunas **DATE**, **DATE\_1**, **DATE\_2** e **Intervalo de Hora**. Clique com o botão direito do mouse e, em seguida, selecione **Remover coluna**.

## <a name="summarize-data-mean"></a>Resumir dados (média)

A próxima etapa é resumir as condições de clima obtendo a média dos valores, agrupados por intervalo de hora.

1. Selecione a coluna **Intervalo de Data e Hora** e depois, no menu **Transformações**, selecione **Resumir**.

    ![Menu Transformações](media/tutorial-bikeshare-dataprep/weathersummarizemenu.png)

2. Para resumir os dados, arraste colunas da grade na parte inferior da página para os painéis esquerdo e direito na parte superior. O painel esquerdo contém o texto **Arrastar colunas aqui para agrupar dados**. O painel direito contém o texto **Arrastar colunas aqui para resumir dados**. 

    a. Arraste a coluna **Intervalo de Data e Hora** da grade na parte inferior do painel esquerdo. Arraste **HOURLYDRYBULBTEMPF**, **HOURLYRelativeHumidity** e **HOURLYWindSpeed** para o painel direito. 

    b. No painel direito, selecione **Média** como a medida **Agregação** para cada coluna. Selecione **OK** para concluir o resumo.

    ![Tela de dados resumidos](media/tutorial-bikeshare-dataprep/weathersummarize.png)

## <a name="transform-dataflow-by-using-script"></a>Transformar o fluxo de dados usando script

Alterar os dados nas colunas numéricas para um intervalo de 0 a 1 permite que alguns modelos sejam convergidos rapidamente. Atualmente, não há nenhuma transformação integrada para fazer essa transformação genericamente. Use um script do Python para realizar essa operação.

1. No menu **Transformação**, selecione **Transformar Fluxo de Dados (Script)**.

2. Insira o código a seguir na caixa de texto que aparece. Caso já tenha usado os nomes de coluna, o código deverá funcionar sem modificação. Você está escrevendo uma lógica de normalização de mín./máx. simples em Python.

    > [!WARNING]
    > O script espera os nomes de coluna usados anteriormente neste tutorial. Se você tiver nomes de coluna diferentes, será possível alterar os nomes no script.

   ```python
   maxVal = max(df["HOURLYDRYBULBTEMPF_Mean"])
   minVal = min(df["HOURLYDRYBULBTEMPF_Mean"])
   df["HOURLYDRYBULBTEMPF_Mean"] = (df["HOURLYDRYBULBTEMPF_Mean"]-minVal)/(maxVal-minVal)
   df.rename(columns={"HOURLYDRYBULBTEMPF_Mean":"N_DryBulbTemp"},inplace=True)

   maxVal = max(df["HOURLYRelativeHumidity_Mean"])
   minVal = min(df["HOURLYRelativeHumidity_Mean"])
   df["HOURLYRelativeHumidity_Mean"] = (df["HOURLYRelativeHumidity_Mean"]-minVal)/(maxVal-minVal)
   df.rename(columns={"HOURLYRelativeHumidity_Mean":"N_RelativeHumidity"},inplace=True)

   maxVal = max(df["HOURLYWindSpeed_Mean"])
   minVal = min(df["HOURLYWindSpeed_Mean"])
   df["HOURLYWindSpeed_Mean"] = (df["HOURLYWindSpeed_Mean"]-minVal)/(maxVal-minVal)
   df.rename(columns={"HOURLYWindSpeed_Mean":"N_WindSpeed"},inplace=True)

   df
   ```

    > [!TIP]
    > O script Python deverá retornar `df` no final. Esse valor é usado para popular a grade.
    
   ![Caixa de diálogo Transformar fluxo de dados (script)](media/tutorial-bikeshare-dataprep/transformdataflowscript.png)

3. Selecione __OK__ para usar o script. Agora, as colunas numéricas na grade contêm valores no intervalo de 0 a 1.

    ![Grade que contém os valores entre 0 e 1](media/tutorial-bikeshare-dataprep/datagridwithdecimals.png)

Você concluiu a preparação dos dados de clima. Em seguida, prepare os dados de percurso.

## <a name="load-trip-data"></a>Carregar dados de percurso

1. Para importar o arquivo `201701-hubway-tripdata.csv`, use as etapas da seção [Criar uma nova fonte de dados](#newdatasource). Use as opções a seguir durante o processo de importação:

    * __Seleção de Arquivos__: selecione **Blob do Azure** ao navegar para selecionar o arquivo.

    * __Esquema de Amostragem__: selecione o esquema de amostragem **Arquivo Completo** e ative o exemplo.

    * __Tipo de Dados__: aceite os padrões.

2. Depois de importar os dados, selecione __Preparar__ para começar a preparar os dados. Selecione o pacote **BikeShare Data Prep.dprep** existente e, em seguida, selecione __OK__.

    Esse processo adiciona um **Fluxo de Dados** no arquivo **Preparação de Dados** existente em vez de criar um novo.

    ![Selecionar o pacote existente](media/tutorial-bikeshare-dataprep/addjandatatodprep.png)

3. Depois que a grade for carregada, expanda __DATAFLOWS__. Agora, há dois fluxos de dados: **BostonWeather** e **201701-hubway-tripdata**. Selecione a entrada **201701-hubway-tripdata**.

    ![Entrada 201701-hubway-tripdata](media/tutorial-bikeshare-dataprep/twodfsindprep.png)

## <a name="use-the-map-inspector"></a>Use o inspetor de mapa

Para a preparação de dados, visualizações úteis chamadas de inspetores estão disponíveis para dados numéricos, geográficos e de cadeia de caracteres. Eles ajudam a entender melhor os dados e a identificar exceções. Siga estas etapas para usar o inspetor de mapa.

1. Faça uma seleção múltipla das colunas **latitude da estação inicial** e **longitude da estação inicial**. Clique com o botão direito do mouse em uma das colunas e selecione **Mapa**.

    > [!TIP]
    > Para habilitar a seleção múltipla, mantenha pressionada a tecla Ctrl (comando ⌘ no Mac) e selecione o cabeçalho de cada coluna.

    ![Visualização do mapa](media/tutorial-bikeshare-dataprep/launchMapInspector.png)

2. Para maximizar a visualização do mapa, selecione o ícone **Maximizar**. Para ajustar o mapa à janela, selecione o ícone **E** no canto superior esquerdo da visualização.

    ![Imagem maximizada](media/tutorial-bikeshare-dataprep/maximizedmap.png)

3. Selecione o botão **Minimizar** para retornar ao modo de exibição em grade.

## <a name="use-the-column-statistics-inspector"></a>Usar o inspetor de estatística de coluna

Para usar o inspetor de estatística de coluna, clique com o botão direito do mouse na coluna __tripduration__ e selecione __Estatística da Coluna__.

![Entrada de estatística da coluna](media/tutorial-bikeshare-dataprep/tripdurationcolstats.png)

Esse processo adiciona uma nova visualização intitulada __Estatística de tripduration__ no painel __INSPECTORS__.

![O inspetor de estatística de tripduration](media/tutorial-bikeshare-dataprep/tripdurationcolstatsinwell.png)

> [!IMPORTANT]
> O valor máximo da duração da viagem é de 961.814 minutos, o que dá cerca de dois anos. Parece que há algumas exceções no conjunto de dados.

## <a name="use-the-histogram-inspector"></a>Usar o inspetor de histograma

Para tentar identificar exceções, clique com o botão direito do mouse na coluna __tripduration__ e selecione __Histograma__.

![Inspetor de histograma](media/tutorial-bikeshare-dataprep/tripdurationhistogram.png)

O histograma não é útil porque as exceções distorcem o gráfico.

## <a name="add-a-column-by-using-script"></a>Adicionar uma coluna usando o script

1. Clique com botão direito do mouse na coluna **tripduration** e selecione **Adicionar Coluna (Script)**.

    ![Menu Adicionar Coluna (Script)](media/tutorial-bikeshare-dataprep/computecolscript.png)

2. Na caixa de diálogo __Adicionar Coluna (Script)__, use os seguintes valores:

    * __Nome da Nova Coluna__: logtripduration

    * __Inserir essa Nova Coluna Após__: tripduration

    * __Novo Código de Coluna__: `math.log(row.tripduration)`

    * __Tipo de Bloco de Código__: expressão

   ![Caixa de diálogo Adicionar Coluna (Script)](media/tutorial-bikeshare-dataprep/computecolscriptdialog.png)

3. Selecione __OK__ para adicionar a coluna **logtripduration**.

4. Clique com o botão direito do mouse na coluna e selecione **Histograma**.

    ![Histograma da coluna logtripduration](media/tutorial-bikeshare-dataprep/logtriphistogram.png)

    Visualmente, esse histograma aparenta ser uma distribuição normal com um final anormal.

## <a name="use-an-advanced-filter"></a>Usar um filtro avançado

Usar um filtro nos dados atualiza os inspetores com a nova distribuição. 

1. Clique com botão direito do mouse na coluna **logtripduration** e selecione **Filtrar Coluna**. 

2. Na caixa de diálogo __Editar__, use os seguintes valores:

    * __Filtrar esta Coluna de Número__: logtripduration

    * __Eu Quero__: manter linhas

    * __Quando__: qualquer uma das condições abaixo for True (OR lógico)

    * __Se esta Coluna__: for menor que

    * __O valor__: 9

    ![Opções de filtro](media/tutorial-bikeshare-dataprep/loftripfilter.png)

3. Selecione __OK__ para aplicar o filtro.

    ![Histogramas atualizadas depois que o filtro foi aplicado](media/tutorial-bikeshare-dataprep/loftripfilteredinspector.png)

### <a name="the-halo-effect"></a>O efeito de halo

1. Maximize o histograma **logtripduration**. Um histograma azul está sobreposto em um histograma cinza. Essa exibição é chamada de **Efeito Halo**:

    * O histograma cinza representa a distribuição antes da operação (nesse caso, a operação de filtragem).

    * O histograma azul representa o histograma após a operação. 

   O efeito de halo ajuda a visualizar o efeito de uma operação nos dados.

   ![Efeito de halo](media/tutorial-bikeshare-dataprep/loftripfilteredinspectormaximized.png)

    > [!NOTE]
    > O histograma azul aparece menor em comparação ao anterior. Essa diferença se deve à nova segmentação automática dos dados no novo intervalo.

2. Para remover o halo, selecione __Editar__ e limpe __Mostrar halo__.

    ![Opções do histograma](media/tutorial-bikeshare-dataprep/uncheckhalo.png)

3. Selecione **OK** para desabilitar o efeito de halo. Depois minimize o histograma.

### <a name="remove-columns"></a>Remover colunas

Nos dados de trajeto, cada linha representa um evento de retirada de bicicleta. Para este tutorial, só são necessárias as colunas **starttime** e **start station id**. Para remover as outras colunas, faça uma seleção múltipla dessas duas colunas, clicando com o botão direito do mouse no cabeçalho da coluna e selecionando **Manter Coluna**. As outras colunas foram removidas.

![Opção Manter Coluna](media/tutorial-bikeshare-dataprep/tripdatakeepcolumn.png)

### <a name="summarize-data-count"></a>Resumir dados (contagem)

Para resumir a demanda de bicicleta por um período de duas horas, use colunas derivadas.

1. Clique com o botão direito do mouse na coluna **starttime** e selecione **Derivar Coluna por Exemplo**.

    ![Opção Derivar Colunas por Exemplo](media/tutorial-bikeshare-dataprep/tripdataderivebyexample.png)

2. Por exemplo, insira o valor **Jan 01, 2017 12AM-2AM** para a primeira linha.

    > [!IMPORTANT]
    > No exemplo de derivação de colunas anterior, você usou várias etapas para derivar uma coluna que continha o período de data e hora. Neste exemplo, você pode ver que essa operação pode ser executada como uma única etapa, fornecendo um exemplo de saída final.

    > [!NOTE]
    > Você pode fornecer um exemplo em relação a qualquer uma das linhas. Neste exemplo, o valor **Jan 01, 2017 12AM-2AM** é válido para a primeira linha de dados.

    ![Dados de exemplo](media/tutorial-bikeshare-dataprep/tripdataderivebyexamplefirstexample.png)

   > [!IMPORTANT]
   > Em um Mac, siga a etapa a seguir em vez da etapa 3:
   >
   > * Vá para a primeira célula que contém **Jan 01, 2017 1AM-2AM**. Deve ser a linha 14. Corrija o valor para **Jan 01, 2017 12AM-2AM** e pressione Enter. 

3. Espere até que o aplicativo calcule os valores em todas as linhas. O processo pode levar alguns segundos. Depois que a análise for concluída, use o link __Examinar próxima linha sugerida__ para examinar os dados.

   ![Análise concluída com link de revisão](media/tutorial-bikeshare-dataprep/tripdatabyexanalysiscomplete.png)

    Verifique se os valores calculados estão corretos. Caso contrário, atualize o valor com o valor esperado e pressione Enter. Depois espere até que a análise seja concluída. Conclua o processo **Examinar próxima linha sugerida** até que aparece **Nenhuma sugestão**. **Nenhuma sugestão** significa que o aplicativo analisou os casos extremos e está satisfeito com o programa sintetizado. Uma prática recomendada é realizar uma inspeção visual dos dados transformados antes de aceitar a transformação. 

4. Selecione **OK** para aceitar a transformação. Renomeie a coluna recém-criada para **Intervalo de Data e Hora**.

    ![Colunas renomeadas](media/tutorial-bikeshare-dataprep/tripdatasummarize.png)

5. Clique com botão direito do mouse no cabeçalho da coluna **starttime** e selecione **Remover coluna**.

6. Para resumir os dados, no menu __Transformação__, selecione __Resumir__. Para criar a transformação, siga as seguintes etapas:

    * Arraste __Intervalo de Data e Hora__ e __ID da estação inicial__ para o painel **Agrupar Por** à esquerda.

    * Arraste __ID da estação inicial__ para o painel **resumir dados** à direita.

   ![Opções de resumo](media/tutorial-bikeshare-dataprep/tripdatacount.png)

7. Selecione **OK** para aceitar o resultado do resumo.

## <a name="join-dataflows"></a>Unir fluxos de dados

Para unir os dados de clima aos dados de viagem, use as seguintes etapas:

1. No menu __Transformações__, selecione __Unir__.

2. __Tabelas__: selecione **BostonWeather** como o fluxo de dados à **Esquerda** e **201701-hubway-tripdata** como o fluxo de dados à **Direita**. Para continuar, selecione **Avançar**.

    ![Seleções de tabelas](media/tutorial-bikeshare-dataprep/jointableselection.png)

3. __Colunas Principais__: selecione a coluna **Intervalo de Data e Hora** nas duas tabelas e, em seguida, selecione __Avançar__.

    ![Seleções de colunas da chave](media/tutorial-bikeshare-dataprep/joinkeyselection.png)

4. __Tipo de Junção__: selecione __Linhas correspondentes__ como o tipo de junção e, em seguida, selecione __Concluir__.

    ![Tipo de junção de linhas correspondentes](media/tutorial-bikeshare-dataprep/joinscreen.png)

    Esse processo cria um novo fluxo de dados denominado __Resultado da Junção__.

## <a name="create-additional-features"></a>Criar recursos adicionais

1. Para criar uma coluna que contenha o dia da semana, clique com o botão direito do mouse na coluna **Intervalo de Data e Hora** e selecione **Derivar Coluna por Exemplo**. Use o valor __Dom__ para uma data ocorrida em um domingo. Um exemplo é **Jan 01, 2017 12AM-2AM**. Pressione Enter e selecione **OK**. Renomeie esta coluna como __Dia da semana__.

    ![Criar nova coluna para o dia da semana](media/tutorial-bikeshare-dataprep/featureweekday.png)

2. Para criar uma coluna que contenha o período de tempo para uma linha, clique com o botão direito do mouse na coluna **Intervalo de Data e Hora** e selecione **Derivar Coluna por Exemplo**. Use um valor de **12AM-2AM** para a linha que contenha **Jan 01, 2017 12AM-2AM**. Pressione Enter e selecione **OK**. Renomeie essa coluna para **Período**.

    ![Coluna de período](media/tutorial-bikeshare-dataprep/featurehourrange.png)

3. Para remover as colunas **Intervalo de Data e Hora** e **r_intervalo de Data e Hora**, selecione Ctrl (comando ⌘ no Mac) e, em seguida, selecione cada cabeçalho de coluna. Clique com botão direito do mouse e selecione **Remover Coluna**.

## <a name="read-data-from-python"></a>Ler dados do Python

Você pode executar um pacote de preparação de dados do Python ou do PySpark e recuperar o resultado como um **Quadro de Dados**.

Para gerar um script Python de exemplo, clique com o botão direito do mouse em __BikeShare Data Prep__ e selecione __Gerar Arquivo de Código de Acesso a Dados__. O arquivo Python de exemplo é criado na **Pasta do Projeto** e também é carregado em uma guia no Workbench. O script Python a seguir é um exemplo do código gerado:

```python
# Use the Azure Machine Learning data preparation package
from azureml.dataprep import package

# Use the Azure Machine Learning data collector to log various metrics
from azureml.logging import get_azureml_logger
logger = get_azureml_logger()

# This call will load the referenced package and return a DataFrame.
# If run in a PySpark environment, this call returns a
# Spark DataFrame. If not, it will return a Pandas DataFrame.
df = package.run('BikeShare Data Prep.dprep', dataflow_idx=0)

# Remove this line and add code that uses the DataFrame
df.head(10)
```

Para este tutorial, o nome do arquivo é `BikeShare Data Prep.py`. Esse arquivo será usado mais tarde no tutorial.

## <a name="save-test-data-as-a-csv-file"></a>Salvar os dados de teste como um arquivo CSV

Para salvar o fluxo de dados **Resultado da Junção** em um arquivo .csv, você deve alterar o script `BikeShare Data Prep.py`. 

1. Abra o projeto para edição no Visual Studio Code.

    ![Abrir o projeto no Visual Studio Code](media/tutorial-bikeshare-dataprep/openprojectinvscode.png)

2. Atualize o script Python no arquivo `BikeShare Data Prep.py` usando o seguinte código:

    ```python
    import pyspark

    from azureml.dataprep.package import run
    from pyspark.sql.functions import *

    # start Spark session
    spark = pyspark.sql.SparkSession.builder.appName('BikeShare').getOrCreate()

    # dataflow_idx=2 sets the dataflow to the 3rd dataflow (the index starts at 0), the Join Result.
    df = run('BikeShare Data Prep.dprep', dataflow_idx=2)
    df.show(n=10)
    row_count_first = df.count()

    # Example file name: 'wasb://data-files@bikesharestorage.blob.core.windows.net/testata'
    # 'wasb://<your container name>@<your azure storage name>.blob.core.windows.net/<csv folder name>
    blobfolder = 'Your Azure Storage blob path'

    df.write.csv(blobfolder, mode='overwrite') 

    # retrieve csv file parts into one data frame
    csvfiles = "<Your Azure Storage blob path>/*.csv"
    df = spark.read.option("header", "false").csv(csvfiles)
    row_count_result = df.count()
    print(row_count_result)
    if (row_count_first == row_count_result):
        print('counts match')
    else:
        print('counts do not match')
    print('done')
    ```

3. Substitua `Your Azure Storage blob path` pelo caminho para o arquivo de saída a ser criado. Substitua para as variáveis `blobfolder` e `csvfiles`.

## <a name="create-an-hdinsight-run-configuration"></a>Criar uma configuração de execução HDInsight

1. No Machine Learning Workbench, abra a janela de linha de comando, selecione o menu **Arquivo** e, em seguida, selecione **Abrir o Prompt de Comando**. O seu prompt de comando inicia na pasta do projeto com o prompt `C:\Projects\BikeShare>`.

    ![Abrir prompt de comando](media/tutorial-bikeshare-dataprep/opencommandprompt.png)

   >[!IMPORTANT]
   >Você deve usar a janela de linha de comando (aberta por meio do Workbench) para realizar as etapas a seguir.

2. Use o prompt de comando para entrar no Azure. 

   O aplicativo Workbench e a CLI usam caches de credenciais independentes ao autenticar em recursos do Azure. Só é preciso fazer isso uma vez, até que o token em cache expire. O comando `az account list` retorna a lista de assinaturas disponíveis para seu logon. Se houver mais de um, use o valor da ID da assinatura desejada. Defina essa assinatura como a conta padrão para usar com o comando `az account set -s` e, em seguida, forneça o valor da ID da assinatura. Em seguida, confirme a configuração usando o comando `show` da conta.

   ```azurecli
   REM login by using the aka.ms/devicelogin site
   az login
   
   REM lists all Azure subscriptions you have access to 
   az account list -o table
   
   REM sets the current Azure subscription to the one you want to use
   az account set -s <subscriptionId>
   
   REM verifies that your current subscription is set correctly
   az account show
   ```

3. Crie a configuração de execução HDInsight. É preciso o nome do cluster e da senha `sshuser`.

    ```azurecli
    az ml computetarget attach cluster --name hdinsight --address <yourclustername>.azurehdinsight.net --username sshuser --password <your password>
    az ml experiment prepare -c hdinsight
    ```
> [!NOTE]
> Quando um projeto em branco é criado, as configurações de execução padrão são **local** e **docker**. Essa etapa cria uma nova configuração de execução disponível no Workbench quando você executa os scripts. 

## <a name="run-in-an-hdinsight-cluster"></a>Executar um cluster HDInsight

Retorne ao aplicativo Machine Learning Workbench para executar o script no cluster HDInsight.

1. Retorne à tela inicial do projeto selecionando o ícone **Página inicial** à esquerda.

2. Selecione **hdinsight** na lista suspensa para executar o script no cluster HDInsight.

3. Selecione **Executar**. O script é enviado como um trabalho. O status do trabalho muda para __Concluído__ depois de o arquivo ser gravado no local especificado no seu contêiner de armazenamento.

    ![Script de execução do HDInsight](media/tutorial-bikeshare-dataprep/hdinsightrunscript.png)


## <a name="substitute-data-sources"></a>Substituir as fontes de dados

Nas etapas anteriores, você usou as fontes de dados `201701-hubway-tripdata.csv` e `BostonWeather.csv` para preparar os dados de teste. Para usar o pacote com os outros arquivos de dados de trajeto, use as seguintes etapas:

1. Crie uma nova fonte de dados usando as etapas descritas anteriormente, com as seguintes alterações no processo:

    * __Seleção de Arquivo__: ao selecionar um arquivo, faça a seleção múltipla dos seis arquivos .csv de dados de trajeto restantes.

    ![Carregar seis arquivos restantes](media/tutorial-bikeshare-dataprep/browseazurestoragefortripdatafiles.png)

    > [!NOTE]
     > A entrada __+ 5__ indica que há cinco arquivos adicionais além do que está listado.

    * __Detalhes do Arquivo__: defina __Modo para Promover Cabeçalhos__ como **Todos os Arquivos Têm os Mesmos Cabeçalhos**. Esse valor indica que cada um dos arquivos contêm o mesmo cabeçalho.

    ![Seleção de detalhes do arquivo](media/tutorial-bikeshare-dataprep/headerfromeachfile.png) 

   Salve o nome dessa fonte de dados, pois ela será usada em etapas posteriores.

2. Selecione o ícone de pasta para exibir os arquivos no projeto. Expanda o diretório __aml\_config__ e, em seguida, selecione o arquivo `hdinsight.runconfig`.

    ![Local do hdinsight.runconfig](media/tutorial-bikeshare-dataprep/hdinsightsubstitutedatasources.png) 

3. Selecione o botão **Editar** para abrir o arquivo no Visual Studio Code.

4. Adicione as seguintes linhas ao final do arquivo `hdinsight.runconfig` e selecione o ícone de disco para salvar o arquivo.

    ```yaml
    DataSourceSubstitutions:
      201701-hubway-tripdata.dsource: 201501-hubway-tripdata.dsource
    ```

    Essa alteração substitui a fonte de dados original pela que contém os seis arquivos de dados de trajeto.

## <a name="save-training-data-as-a-csv-file"></a>Salvar dados de treinamento como um arquivo CSV

1. Navegue até o arquivo `BikeShare Data Prep.py` do Python que você editou anteriormente. Forneça um caminho de arquivo diferente para salvar os dados de treinamento.

    ```python
    import pyspark

    from azureml.dataprep.package import run
    from pyspark.sql.functions import *

    # start Spark session
    spark = pyspark.sql.SparkSession.builder.appName('BikeShare').getOrCreate()

    # dataflow_idx=2 sets the dataflow to the 3rd dataflow (the index starts at 0), the Join Result.
    df = run('BikeShare Data Prep.dprep', dataflow_idx=2)
    df.show(n=10)
    row_count_first = df.count()

    # Example file name: 'wasb://data-files@bikesharestorage.blob.core.windows.net/traindata'
    # 'wasb://<your container name>@<your azure storage name>.blob.core.windows.net/<csv folder name>
    blobfolder = 'Your Azure Storage blob path'

    df.write.csv(blobfolder, mode='overwrite') 

    # retrieve csv file parts into one data frame
    csvfiles = "<Your Azure Storage blob path>/*.csv"
    df = spark.read.option("header", "false").csv(csvfiles)
    row_count_result = df.count()
    print(row_count_result)
    if (row_count_first == row_count_result):
        print('counts match')
    else:
        print('counts do not match')
    print('done')
    ```

2. Use a pasta chamada `traindata` para a saída de dados de treinamento.

3. Para enviar um novo trabalho, selecione **Executar**. Certifique-se de **hdinsight** esteja selecionado. Um trabalho é enviado com a nova configuração. A saída desse trabalho são os dados de treinamento. Esses dados são criados usando as mesmas etapas de preparação de dados que você criou anteriormente. O trabalho pode levar alguns minutos para ser concluído.


## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Próximas etapas
Você concluiu o tutorial de preparação de dados de compartilhamento de bicicleta. Neste tutorial, você usou o Machine Learning (versão prévia) para aprender como:
> [!div class="checklist"]
> * Preparar dados interativamente com a ferramenta de preparação de dados do Machine Learning.
> * Importar, transformar e criar um conjunto de dados de teste.
> * Gerar um pacote de preparação de dados.
> * Executar o pacote de preparação de dados usando o Python.
> * Gerar um conjunto de dados de treinamento reutilizando o pacote de preparação de dados para arquivos de entrada adicionais.

Em seguida, saiba mais sobre a preparação de dados:
> [!div class="nextstepaction"]
> [Guia do usuário de preparação de dados](data-prep-user-guide.md)
