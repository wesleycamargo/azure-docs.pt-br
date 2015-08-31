<properties
	pageTitle="Importar dados no Estúdio de Aprendizado de Máquina | Microsoft Azure"
	description="Como importar os dados de treinamento para o Estúdio de Aprendizado de Máquina do Azure de diferentes fontes de dados. Saiba quais tipos e formatos de dados têm suporte."
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/14/2015"
	ms.author="garye" />


# Importar os dados de treinamento para o Estúdio de Aprendizado de Máquina do Azure de diferentes fontes de dados

Ao desenvolver uma solução de análise preditiva no Estúdio de Aprendizado de Máquina do Azure, você treina seu modelo usando dados que representam seu espaço de problema. Este tutorial mostra como importar dados de várias fontes de dados para treinamento de seu modelo no Estúdio de Aprendizado de Máquina. Você também aprenderá quais formatos de dados têm suporte.

Há diversos exemplos de conjuntos de dados disponíveis no Estúdio de Aprendizado de Máquina que você pode usar para essa finalidade (consulte [Usar exemplos de conjuntos de dados no Estúdio de Aprendizado de Máquina do Azure](machine-learning-use-sample-datasets.md)). Mas você também pode importar seus próprios dados para o Estúdio de Aprendizado de Máquina para uso em seus experimentos.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Para usar seus próprios dados no Estúdio de Aprendizado de Máquina, você pode antes carregar antecipadamente um arquivo de dados do disco rígido local para criar um módulo de conjunto de dados em seu espaço de trabalho. Ou pode acessar dados de uma das várias fontes de dados online enquanto seu experimento é executado usando o módulo [Leitor][reader]\:

- Armazenamento BLOB, tabela ou Banco de Dados SQL do Azure
- Hadoop usando HiveQL
- Uma URL da Web usando HTTP
- Um provedor de feed de dados

O Estúdio de Aprendizado de Máquina foi criado para trabalhar com dados tabulares ou retangulares, como dados de texto delimitados ou dados estruturados de um banco de dados, embora em algumas circunstâncias seja possível usar dados não retangulares.

É melhor se os dados estiverem relativamente limpos. Ou seja, você deve cuidar de problemas como cadeias de caracteres sem aspas antes de carregar os dados para seu experimento.

No entanto, há módulos disponíveis no Estúdio de Aprendizado de Máquina que permitem fazer alguma manipulação de dados dentro do experimento. Dependendo dos algoritmos de aprendizado de máquina que for usar, talvez seja necessário decidir como você lidará com problemas estruturais dos dados, como valores ausentes e dados esparsos, e há módulos que podem ajudar com isso. Veja a seção **Transformação de Dados** da paleta de módulos para ver os módulos que executam essas funções.

A qualquer momento do experimento, você pode exibir ou baixar os dados que são produzidos por um módulo clicando com o botão direito na porta de saída. Dependendo do módulo, pode haver opções de download diferentes disponíveis, ou você poderá exibir os dados no seu navegador da Web no Estúdio de Aprendizado de Máquina.


## Formatos de dados

Você pode importar vários tipos de dados para seu experimento, dependendo de qual mecanismo usar para importar os dados e de onde eles estão vindo:

- Texto sem formatação (.txt)
- Valores separados por vírgulas (CSV) com cabeçalho (.csv) ou sem (.nh.csv)
- Valores separados por tabulação (TSV) com cabeçalho (.tsv) ou sem (.nh.tsv)
- Tabela do Hive
- Tabela de Banco de Dados SQL
- Valores de OData
- Dados SVMLight (.svmlight) (consulte a [definição de SVMLight](http://svmlight.joachims.org/) para obter informações sobre o formato)
- Attribute Relation File Format (ARFF) (.arff) (consulte a [definição de ARFF](http://weka.wikispaces.com/ARFF) para obter informações sobre o formato)
- Arquivo zip (.zip)
- Arquivo de espaço de trabalho ou objeto R (.RData)

Se você importar dados em um formato como ARFF, que inclui metadados, o Estúdio de Aprendizado de Máquina usa esses metadados para definir o cabeçalho e o tipo de dados de cada coluna. Se você importar dados em um formato como TSV ou CSV, que não incluem esses metadados, o Estúdio de Aprendizado de Máquina infere o tipo de dados de cada coluna por amostragem dos dados. Se os dados também não tiverem os cabeçalhos das colunas, o Estúdio de Aprendizado de Máquina fornece nomes padrão. Você pode especificar explicitamente ou alterar os cabeçalhos e tipos de dados das colunas usando o [Editor de Metadados][metadata-editor].

Os tipos de dados a seguir são reconhecidos pelo Estúdio de Aprendizado de Máquina:

- Cadeia de caracteres
- Número inteiro
- Duplo
- Booliano
- DateTime
- TimeSpan

O Estúdio de Aprendizado de Máquina usa um tipo de dados interno chamado *Data Table* para transmitir dados entre módulos. Você pode converter explicitamente os dados para o formato Data Table usando o módulo [Converter para conjunto de dados][convert-to-dataset]. Qualquer módulo que aceitar formatos diferentes do Data Table converterá os dados para Data Table silenciosamente antes de transmiti-los para o módulo seguinte. Se necessário, você pode converter o formato Data Table de volta para o formato CSV, TSV, ARFF ou SVMLight usando outros módulos de conversão. Veja a seção **Conversões de Formato de Dados** da paleta de módulos para ver os módulos que executam essas funções.


## Importar dados de um arquivo local

Você pode importar dados de um disco rígido local fazendo o seguinte:

1. clique em **+NOVO** na parte inferior da janela do Estúdio de Aprendizado de Máquina.
2. Selecione **CONJUNTO DE DADOS** e **DO ARQUIVO LOCAL**.
3. Na caixa de diálogo **Carregar um novo conjunto de dados**, navegue até o arquivo que deseja carregar
4. Digite um nome, identifique o tipo de dados e, opcionalmente, insira uma descrição. Uma descrição é recomendada - ela permite que você registre quaisquer características sobre os dados que você desejará lembrar ao usar os dados no futuro.
5. A caixa de seleção **Esta é a nova versão de um conjunto de dados existente** permite que você atualize um conjunto de dados existente com novos dados. Basta clicar nesta caixa de seleção e digitar o nome de um conjunto de dados existente.

Durante o carregamento, você verá uma mensagem de que seu arquivo está sendo carregado. O tempo de carregamento depende do tamanho de seus dados e da velocidade da sua conexão com o serviço. Se souber que o arquivo levará muito tempo, você pode fazer outras coisas dentro do Estúdio de Aprendizado de Máquina enquanto espera. No entanto, fechar o navegador fará com que o carregamento de dados falhe.

Uma vez carregados, seus dados são armazenados em um módulo de conjunto de dados e ficam disponíveis para qualquer experimento em seu espaço de trabalho. Você pode encontrar o conjunto de dados, juntamente com todos os conjuntos de dados de exemplo pré-carregados, na lista **Conjuntos de Dados Salvos** na paleta de módulos quando estiver editando um experimento.


## Acessar dados online com o módulo Leitor

Você pode acessar dados de diversas fontes online enquanto seu experimento é executado usando o módulo [Leitor][reader] no experimento. Como esses dados de treinamento são acessados enquanto seu experimento está em execução, eles ficam disponíveis apenas em um experimento (em vez de módulos de conjuntos de dados que ficam disponíveis para qualquer experimento de seu espaço de trabalho).

Depois de adicionar o módulo [Leitor][reader] ao seu experimento, você seleciona a **Fonte de dados** e fornece informações de acesso usando os parâmetros do módulo. Por exemplo, se selecionar **URL da Web via HTTP**, você fornece a URL e o formato de dados de origem. Se estiver acessando seus dados de treinamento no armazenamento do Azure ou HDInsight (usando uma consulta de Hive), você fornece as informações de conta apropriadas e o local dos dados.

> [AZURE.NOTE]Este artigo fornece informações gerais sobre o módulo [Leitor][reader]. Para saber mais sobre os tipos de dados que você pode acessar, formatos, parâmetros e respostas para perguntas comuns, consulte o tópico de referência do módulo [Leitor][reader].


### Obter dados do Azure

Você pode importar dados de três fontes do Azure:

- **Armazenamento Blob do Azure** - se você usar o formato ARFF para armazenamento, as colunas são mapeadas usando os metadados de cabeçalho. Se você usar os formatos TSV ou CSV, os mapeamentos são inferidos por meio de amostras de dados da coluna.
- **Armazenamento de Tabela do Azure** - o módulo [Leitor][reader] faz uma varredura dos seus dados para identificar os tipos de dados de coluna. Se os dados forem relativamente homogêneos e previsíveis, você pode limitar o número de linhas que são analisadas.
- **Banco de Dados SQL do Azure** - o módulo [Leitor][reader] utiliza a API de cliente SQL Azure Transact para importar dados usando uma consulta de banco de dados que você fornecer.

Para o armazenamento BLOB e em tabela, você fornece um URI de SAS (Assinatura de Acesso Compartilhado) ou informações de conta de armazenamento do Azure para fornecer acesso aos dados. Para um Banco de Dados SQL do Azure, você fornece suas informações de banco de dados e conta, além de uma consulta do banco de dados que identifica os dados que você deseja importar.

### Obter dados da Web

Você pode usar o módulo [Leitor][reader] para ler dados de treinamento de um site da Web ou FTP. Você precisa fornecer:

- O endereço de URL HTTP completo de um arquivo
- O formato de dados do arquivo (CSV, TSV, ARFF ou SvmLight)
- Para arquivos CSV ou TSV, indique se a primeira linha do arquivo é um cabeçalho

### Obter dados do Hadoop

Você pode usar o módulo [Leitor][reader] para ler dados de treinamento de um armazenamento distribuído usando a linguagem de consulta HiveQL. Você precisará especificar a consulta do banco de dados Hive e fornecer informações de acesso do usuário no servidor HCatalog. Você também precisa especificar se os dados são armazenados em um HDFS (sistema de arquivos distribuído Hadoop) ou no Azure e, se estiverem no Azure, as informações de conta do Azure

### Obter dados de um provedor de feed de dados

Ao especificar uma URL de OData, você pode ler diretamente de um provedor de feed de dados. Você precisará fornecer a URL de origem e o tipo do conteúdo de dados.


## Salvar dados do seu experimento


Haverá momentos em que você vai querer pegar o resultado intermediário de um experimento e usá-lo como parte de outro experimento. Para fazer isso:

1. Clique com o botão direito na saída do módulo que você deseja salvar como um conjunto de dados.

2. Clique em **Salvar como Conjunto de Dados**.

3. Quando solicitado, insira um nome e uma descrição que permitiriam identificar o conjunto de dados facilmente.

4. Clique na marca de seleção **OK**.

Quando o salvamento for concluído, o conjunto de dados ficará disponível para uso em qualquer experimento do seu espaço de trabalho. Você pode encontrá-lo na lista **Conjuntos de Dados Salvos** na paleta de módulos.


<!-- Module References -->
[convert-to-dataset]: https://msdn.microsoft.com/library/azure/72bf58e0-fc87-4bb1-9704-f1805003b975/
[metadata-editor]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

<!---HONumber=August15_HO8-->