---
Título: Importar dados para o Machine Learning Studio – titleSuffix: Descrição do Azure Machine Learning Studio: Como importar os dados para o Azure Machine Learning Studio de diferentes fontes de dados. Saiba quais tipos e formatos de dados têm suporte.
services: machine-learning ms.service: machine-learning ms.subservice: studio ms.topic: article

autor: ericlicoding ms.author: amlstudiodocs ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro ms.date: 29/11/2017
---
# <a name="import-your-training-data-into-azure-machine-learning-studio-from-various-data-sources"></a>Importar os dados de treinamento para o Azure Machine Learning Studio de diferentes fontes de dados

Para usar seus próprios dados no Machine Learning Studio para desenvolver e treinar uma solução de análise preditiva, você pode usar dados de: 

* Um [**arquivo local**](import-data-from-local-file.md) - Carregue dados locais antecipadamente do seu disco rígido para criar um módulo de conjunto de dados em sua área de trabalho
* [**Fontes de dados on-line**](import-data-from-online-sources.md): use o módulo [Importar dados][import-data] para acessar dados de uma das várias fontes on-line enquanto sua experiência está em exibição
* [**Experiência do Machine Learning Studio**](import-data-from-an-experiment.md): use dados que foram salvos como um conjunto de dados no Machine Learning Studio
* [**Banco de dados do SQL Server local**](use-data-from-an-on-premises-sql-server.md) - Use dados de um banco de dados do SQL Server local sem ter que copiar dados manualmente

> [!NOTE]
> Há diversos conjuntos de dados de exemplo disponíveis no Machine Learning Studio que você pode usar para treinamento de dados. Para obter informações, consulte [Usar os conjuntos de dados de amostra no Azure Machine Learning Studio](use-sample-datasets.md).
> 
> 

Este artigo introdutório também discute como obter dados prontos para uso no Machine Learning Studio e descreve quais formatos de dados e tipos de dados são suportados.

## <a name="get-data-ready-for-use-in-azure-machine-learning-studio"></a>Preparar dados para uso no Azure Machine Learning Studio.
O Machine Learning Studio foi criado para trabalhar com dados tabulares ou retangulares, como dados de texto delimitados ou dados estruturados de um banco de dados, embora em algumas circunstâncias seja possível usar dados não retangulares.

É melhor se os dados estiverem relativamente limpos. Ou seja, você deve cuidar de problemas como cadeias de caracteres sem aspas antes de carregar os dados para seu experimento.

No entanto, há módulos disponíveis no Machine Learning Studio que permitem fazer alguma manipulação de dados dentro do teste. Dependendo dos algoritmos de aprendizado de máquina que for usar, talvez seja necessário decidir como você lidará com problemas estruturais dos dados, como valores ausentes e dados esparsos, e há módulos que podem ajudar com isso. Veja a seção **Transformação de Dados** da paleta de módulos para ver os módulos que executam essas funções.

A qualquer momento do teste, é possível exibir ou baixar os dados produzidos por um módulo clicando na porta de saída. Dependendo do módulo, pode haver opções de download diferentes disponíveis ou a possibilidade de visualizar os dados no seu navegador da Web no Machine Learning Studio.

## <a name="data-formats-and-data-types-supported"></a>Formatos e tipos de dados compatíveis
Você pode importar vários tipos de dados para seu experimento, dependendo de qual mecanismo usar para importar os dados e de onde eles estão vindo:

* Texto sem formatação (.txt)
* Valores separados por vírgulas (CSV) com cabeçalho (.csv) ou sem (.nh.csv)
* Valores separados por tabulação (TSV) com cabeçalho (.tsv) ou sem (.nh.tsv)
* Arquivo do Excel
* Tabela do Azure
* Tabela do Hive
* Tabela de Banco de Dados SQL
* Valores de OData
* Dados SVMLight (.svmlight) (consulte a [definição de SVMLight](http://svmlight.joachims.org/) para obter informações sobre o formato)
* Attribute Relation File Format (ARFF) (.arff) (consulte a [definição de ARFF](http://weka.wikispaces.com/ARFF) para obter informações sobre o formato)
* Arquivo zip (.zip)
* Arquivo de workspace ou objeto R (.RData)

Se você importar dados em um formato como ARFF, que inclui metadados, o Machine Learning Studio usa esses metadados para definir o cabeçalho e o tipo de dados de cada coluna.

Se você importar dados em um formato como TSV ou CSV, que não incluem esses metadados, o Machine Learning Studio infere o tipo de dados de cada coluna por amostragem dos dados. Se os dados também não tiverem os cabeçalhos das colunas, o Machine Learning Studio fornece nomes padrão.

É possível especificar explicitamente ou alterar os cabeçalhos e tipos de dados das colunas usando [Editar Metadados][edit-metadata].

Os **tipos de dados** a seguir são reconhecidos pelo Machine Learning Studio:

* Cadeia de caracteres
* Número inteiro
* Double
* BOOLEAN
* DateTime
* TimeSpan

O Machine Learning Studio usa um tipo de dados interno chamado ***Tabela de Dados*** para transmitir dados entre módulos. É possível converter explicitamente os dados para o formato de tabela de dados usando o módulo [Converter para Conjunto de Dados][convert-to-dataset].

Qualquer módulo que aceitar formatos diferentes do Data Table converterá os dados para Data Table silenciosamente antes de transmiti-los para o módulo seguinte.

Se necessário, você pode converter o formato Data Table de volta para o formato CSV, TSV, ARFF ou SVMLight usando outros módulos de conversão.
Veja a seção **Conversões de Formato de Dados** da paleta de módulos para ver os módulos que executam essas funções.

<!-- Module References -->
[convert-to-dataset]: https://msdn.microsoft.com/library/azure/72bf58e0-fc87-4bb1-9704-f1805003b975/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
