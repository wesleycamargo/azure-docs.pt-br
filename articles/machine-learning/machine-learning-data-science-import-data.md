<properties
	pageTitle="Importar dados no Estúdio de Aprendizado de Máquina | Microsoft Azure"
	description="Como importar os dados para o Estúdio de Aprendizado de Máquina do Azure de diferentes fontes de dados. Saiba quais tipos e formatos de dados têm suporte."
	keywords="importar dados, formato de dados, tipos de dados, fontes de dados, dados de treinamento"
	services="machine-learning"
	documentationCenter=""
	authors="bradsev"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/19/2016"
	ms.author="garye;bradsev" />


# Importar os dados de treinamento para o Estúdio de Aprendizado de Máquina do Azure de diferentes fontes de dados

Para usar seus próprios dados no Estúdio de Aprendizado de Máquina para desenvolver e treinar uma solução de análise preditiva, você pode:

- carregar dados de um **arquivo local** antes do tempo do seu disco rígido para criar um módulo de conjunto de dados no espaço de trabalho.
- acessar dados de uma das várias **fontes de dados online** enquanto o teste é executado usando o módulo [Importar Dados][import-data].
- usar dados de outro teste de Aprendizado de Máquina do Azure salvo como um **conjunto de dados**.

[AZURE.INCLUDE [import-data-into-aml-studio-selector](../../includes/machine-learning-import-data-into-aml-studio.md)]

Cada uma dessas opções são descritas em um dos tópicos no menu acima. Este tópico mostra como importar dados de várias fontes de dados para usar no Machine Learning Studio.

> [AZURE.NOTE] Há diversos conjuntos de dados de exemplo disponíveis no Estúdio de Aprendizado de Máquina que você pode usar para essa finalidade. Para saber mais, consulte [Usar os conjuntos de dados de amostra no Estúdio de Aprendizado de Máquina do Azure](machine-learning-use-sample-datasets.md).

Este tópico introdutório também mostra como obter dados prontos para uso no Machine Learning Studio e descreve quais formatos de dados e tipos de dados são compatíveis.

> [AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


## Preparar dados para uso no Estúdio de Aprendizado de Máquina do Azure.
O Estúdio de Aprendizado de Máquina foi criado para trabalhar com dados tabulares ou retangulares, como dados de texto delimitados ou dados estruturados de um banco de dados, embora em algumas circunstâncias seja possível usar dados não retangulares.

É melhor se os dados estiverem relativamente limpos. Ou seja, você deve cuidar de problemas como cadeias de caracteres sem aspas antes de carregar os dados para seu experimento.

No entanto, há módulos disponíveis no Estúdio de Aprendizado de Máquina que permitem fazer alguma manipulação de dados dentro do teste. Dependendo dos algoritmos de aprendizado de máquina que for usar, talvez seja necessário decidir como você lidará com problemas estruturais dos dados, como valores ausentes e dados esparsos, e há módulos que podem ajudar com isso. Veja a seção **Transformação de Dados** da paleta de módulos para ver os módulos que executam essas funções.

A qualquer momento do experimento, você pode exibir ou baixar os dados que são produzidos por um módulo clicando com o botão direito na porta de saída. Dependendo do módulo, pode haver opções de download diferentes disponíveis, ou você poderá exibir os dados no seu navegador da Web no Estúdio de Aprendizado de Máquina.

## Formatos e tipos de dados compatíveis

Você pode importar vários tipos de dados para seu experimento, dependendo de qual mecanismo usar para importar os dados e de onde eles estão vindo:

- Texto sem formatação (.txt)
- Valores separados por vírgulas (CSV) com cabeçalho (.csv) ou sem (.nh.csv)
- Valores separados por tabulação (TSV) com cabeçalho (.tsv) ou sem (.nh.tsv)
- Arquivo do Excel
- Tabela do Azure
- Tabela do Hive
- Tabela de Banco de Dados SQL
- Valores de OData
- Dados SVMLight (.svmlight) (consulte a [definição de SVMLight](http://svmlight.joachims.org/) para obter informações sobre o formato)
- Attribute Relation File Format (ARFF) (.arff) (consulte a [definição de ARFF](http://weka.wikispaces.com/ARFF) para obter informações sobre o formato)
- Arquivo zip (.zip)
- Arquivo de espaço de trabalho ou objeto R (.RData)

Se você importar dados em um formato como ARFF, que inclui metadados, o Estúdio de Aprendizado de Máquina usa esses metadados para definir o cabeçalho e o tipo de dados de cada coluna.

Se você importar dados em um formato como TSV ou CSV, que não incluem esses metadados, o Estúdio de Aprendizado de Máquina infere o tipo de dados de cada coluna por amostragem dos dados. Se os dados também não tiverem os cabeçalhos das colunas, o Estúdio de Aprendizado de Máquina fornece nomes padrão.

Você pode especificar explicitamente ou alterar os cabeçalhos e tipos de dados das colunas usando [Editar Metadados][edit-metadata].

Os **tipos de dados** a seguir são reconhecidos pelo Estúdio de Aprendizado de Máquina:

- Cadeia de caracteres
- Número inteiro
- Duplo
- Booliano
- DateTime
- TimeSpan

O Estúdio de Aprendizado de Máquina usa um tipo de dados interno chamado ***Tabela de Dados*** para transmitir dados entre módulos. Você pode converter explicitamente os dados para o formato Data Table usando o módulo [Converter para conjunto de dados][convert-to-dataset].

Qualquer módulo que aceitar formatos diferentes do Data Table converterá os dados para Data Table silenciosamente antes de transmiti-los para o módulo seguinte.

Se necessário, você pode converter o formato Data Table de volta para o formato CSV, TSV, ARFF ou SVMLight usando outros módulos de conversão. Veja a seção **Conversões de Formato de Dados** da paleta de módulos para ver os módulos que executam essas funções.



<!-- Module References -->
[convert-to-dataset]: https://msdn.microsoft.com/library/azure/72bf58e0-fc87-4bb1-9704-f1805003b975/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

<!---HONumber=AcomDC_0921_2016-->