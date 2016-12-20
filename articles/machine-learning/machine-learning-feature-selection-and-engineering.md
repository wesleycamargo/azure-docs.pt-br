---
title: "Engenharia e seleção de recursos no Azure Machine Learning | Microsoft Docs"
description: "Explica a finalidade da seleção de recursos e da engenharia de recursos e fornece exemplos de sua função no processo de aperfeiçoamento de dados do aprendizado de máquina."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 9ceb524d-842e-4f77-9eae-a18e599442d6
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2016
ms.author: zhangya;bradsev
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 1d29f416fe7d5ef3f74cf64122a84acc4a4535da


---
# <a name="feature-engineering-and-selection-in-azure-machine-learning"></a>Engenharia e seleção de recursos no Aprendizado de Máquina do Azure
Este tópico explica a finalidade da engenharia e da seleção de recursos no processo de aprimoramento de dados de aprendizado de máquina. Ele ilustra o que esses processos envolvem usando exemplos fornecidos pelo Azure Machine Learning Studio.

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Os dados de treinamento usados no aprendizado de máquina frequentemente podem ser aprimorados pela seleção ou extração de recursos dos dados brutos coletados. Um exemplo de recurso de engenharia no contexto de aprender a classificar imagens de caracteres manuscritos é um mapa de densidade de bits construído por meio dos dados brutos de distribuição de bits. Este mapa pode ajudar a localizar as bordas dos caracteres da forma mais eficiente que a distribuição bruta.

Os recursos de engenharia e selecionados aumentam a eficiência do processo de treinamento, que tenta extrair as informações importantes contidas nos dados. Eles também melhoram a capacidade desses modelos de classificar os dados de entrada com precisão e prever resultados de interesse com mais robustez. Também é possível combinar seleção e engenharia de recursos para que o aprendizado seja mais tratável por computação. Ele faz isso aperfeiçoando e, em seguida, reduzindo o número de recursos necessários para calibrar ou treinar um modelo. Matematicamente, os recursos selecionados para treinar o modelo são um conjunto mínimo de variáveis independentes que explicam os padrões dos dados e preveem os resultados com êxito.

A engenharia e a seleção de recursos é parte de um processo maior, que geralmente consiste de quatro etapas:

* Coleta de dados
* Aperfeiçoamento de dados
* Construção de modelo
* Pós-processamento

A engenharia e a seleção são a etapa de aperfeiçoamento de dados do aprendizado de máquina. Três aspectos desse processo podem ser diferenciados para nossos propósitos:

* **Pré-processamento de dados**: este processo tenta assegurar que os dados coletados estejam limpos e consistentes. Ele inclui tarefas como a integração de vários conjuntos de dados, manipulação de dados que estão faltando, manipulação de dados inconsistentes e conversão de tipos de dados.
* **Engenharia de recursos**: esse processo tenta criar outros recursos relevantes por meio dos recursos brutos existentes nos dados e aumentar a capacidade de previsão do algoritmo de aprendizado.
* **Seleção de recursos**: este processo seleciona o subconjunto principal de recursos de dados originais para tentar reduzir a dimensionalidade do problema de treinamento.

Este tópico abrange os aspectos da engenharia de recursos e da seleção de recursos do processo de aperfeiçoamento de dados. Para saber mais sobre a etapa de pré-processamento de dados, consulte [Pré-processamento de dados no Azure Machine Learning Studio](https://azure.microsoft.com/documentation/videos/preprocessing-data-in-azure-ml-studio/).

## <a name="creating-features-from-your-data--feature-engineering"></a>Criando recursos de seus dados - Engenharia de recursos
Os dados de treinamento consistem de uma matriz composta de exemplos (registros ou observações armazenadas em linhas), cada um deles dos quais tem um conjunto de recursos (variáveis ou campos armazenados em colunas). Espera-se que os recursos especificados no design experimental caracterizem os padrões nos dados. Embora muitos dos campos de dados brutos possam ser incluídos diretamente no conjunto de recursos selecionado para treinar um modelo, frequentemente é necessário que recursos adicionais de engenharia precisem ser construídos por meio dos recursos nos dados brutos para gerar um conjunto de dados de treinamento aperfeiçoado.

Que tipos de recursos devem ser criados para aperfeiçoar o conjunto de dados ao treinar um modelo? Recursos de engenharia que aprimoram o treinamento fornecem informações que diferenciam melhor os padrões nos dados. Esperamos que os novos recursos forneçam informações adicionais que não são capturadas claramente ou que não ficam aparentes com facilidade no conjunto de recursos original ou existente, entretanto, esse processo é como uma arte. Decisões sensatas e produtivas frequentemente exigem alguma experiência de domínio.

Ao começar a usar o Azure Machine Learning, é mais fácil entender esse processo concretamente usando exemplos fornecidos no Machine Learning Studio. Dois exemplos são apresentados aqui:

* Um exemplo de regressão ([previsão do número de locações de bicicletas](http://gallery.cortanaintelligence.com/Experiment/Regression-Demand-estimation-4)) em um experimento supervisionado em que os valores de destino são conhecidos
* Um exemplo de classificação de mineração de texto usando [Hash de recursos][feature-hashing]

### <a name="example-1-adding-temporal-features-for-a-regression-model"></a>Exemplo 1: adição de recursos temporais para um modelo de regressão
Vamos usar o experimento "Previsão de demanda de bicicletas" no Estúdio do Azure Machine Learning para demonstrar como fazer a engenharia de recursos em uma tarefa de regressão. O objetivo desse experimento é prever a demanda de bicicletas, ou seja, o número de locações de bicicletas dentro de um mês, dia ou hora específica. O conjunto de dados de **UCI de locação de bicicletas** é usado como os dados brutos de entrada.

Esse conjunto de dados baseia-se em dados reais da empresa Capital Bikeshare, que mantém uma rede de aluguel de bicicletas em Washington, D.C. nos Estados Unidos. O conjunto de dados representa o número de locações de bicicletas em uma hora específica de um dia de 2011 e 2012 e contém 17379 linhas e 17 colunas. O conjunto de recursos brutos contém condições climáticas (temperatura, umidade, velocidade do vento) e o tipo de dia (feriado ou dia da semana). O campo a ser previsto é **cnt**, uma contagem que representa os aluguéis de bicicleta em uma hora específica e que varia de 1 a 977.

Com o objetivo de construir recursos efetivos nos dados de treinamento, quatro modelos de regressão são criados usando o mesmo algoritmo, mas com quatro conjuntos de dados de treinamento diferentes. Os quatro conjuntos de dados representam os mesmos dados de entrada brutos, mas com um número crescente de conjunto de recursos. Os recursos são agrupados em quatro categorias:

1. A = características de clima + feriado + dia da semana + final de semana para o dia previsto
2. B = número de bicicletas que foram alugadas em cada uma das últimas 12 horas
3. C = número de bicicletas que foram alugadas em cada um dos últimos 12 dias, na mesma hora
4. D = número de bicicletas que foram alugadas em cada uma das últimas 12 semanas, na mesma hora e mesmo dia

Além do conjunto de recursos A, que já existe nos dados brutos originais, três outros conjuntos de recursos são criados por meio do processo de engenharia de recursos. O conjunto de recursos B captura a demanda recente pelas bicicletas. O conjunto de recursos C captura a demanda por bicicletas em uma hora específica. O conjunto de recursos D captura a demanda por bicicletas em uma hora específica de um dia específico da semana. Os quatro conjuntos de dados de treinamento incluem os conjuntos de recursos A, A + B, A + B + C e A + B + C + D, respectivamente.

No experimento do Azure Machine Learning, esses quatro conjuntos de dados de treinamento são formados por meio de quatro ramificações do conjunto de dados de entrada pré-processado. Exceto pela ramificação mais à esquerda, cada uma dessas ramificações contém um módulo [Executar Script R][execute-r-script], no qual um conjunto de recursos derivados (conjuntos de recursos B, C e D) é construído e acrescentado ao conjunto de dados importado. A figura a seguir demonstra o script R sendo usado para criar o conjunto de recursos B na segunda ramificação à esquerda.

![Criar um conjunto de recursos](./media/machine-learning-feature-selection-and-engineering/addFeature-Rscripts.png)

A tabela a seguir resume a comparação dos resultados de desempenho dos quatro modelos. Os melhores resultados são mostrados pelos recursos A + B + C. Observe que a taxa de erro diminui quando o conjunto de recursos adicional é incluído nos dados de treinamento. Isso confirma nossa suposição de que os conjuntos de recursos B e C fornecem informações adicionais relevantes para a tarefa de regressão. Adicionar o conjunto de recursos D não parece fornecer qualquer redução adicional da taxa de erro.

![Comparação dos resultados de desempenho](./media/machine-learning-feature-selection-and-engineering/result1.png)

### <a name="a-nameexample2a-example-2-creating-features-in-text-mining"></a><a name="example2"></a> Exemplo 2: criando recursos com mineração de texto
A engenharia de recursos é amplamente aplicada a tarefas relacionadas à mineração de texto, como classificação de documentos e análise de sentimento. Por exemplo, quando desejar classificar documentos em várias categorias, um pressuposto típico é que as palavras ou expressões incluídas em uma categoria de documentos têm menor probabilidade de ocorrer em outra categoria de documentos. Em outras palavras, a frequência da distribuição de palavras ou expressões é capaz de caracterizar diferentes categorias de documentos. Para aplicações associadas à mineração de texto, como trechos individuais do conteúdo de texto normalmente funcionam como dados de entrada, o processo de engenharia de recurso é necessário para criar os recursos que envolvem frequências de palavra ou expressão.

Para realizar essa tarefa, uma técnica chamada *hash de recursos* é aplicada para transformar de maneira eficiente recursos de texto arbitrários em índices. Em vez de associar cada recurso de texto (palavras ou expressões) a um índice em particular, este método funciona aplicando uma função de hash aos recursos e usando seus valores de hash como índices diretamente.

No Azure Machine Learning, há um módulo [Hash de Recursos][feature-hashing] que cria esses recursos de palavra ou expressão. A figura a seguir mostra um exemplo de uso deste módulo. O conjunto de dados de entrada contém duas colunas: a classificação do livro, que varia de 1 a 5, e o conteúdo real da resenha. O objetivo desse módulo de [Hash de Recursos][feature-hashing] é recuperar novos recursos que mostram a frequência de ocorrência das palavras ou expressões correspondentes dentro de uma resenha de livro. Para usar este módulo, é necessário concluir as seguintes etapas:

1. Selecione a coluna que contém o texto de entrada (**Col2** neste exemplo).
2. Defina *Bitsize de hashing* como 8, o que significa que 2^8 = 256 recursos serão criados. A palavra ou expressão no texto será colocada em hash em 256 índices. O parâmetro *Bitsize de hashing* varia de 1 a 31. Se o parâmetro for definido para um número maior, as palavras ou frases são menos prováveis de serem colocadas em hash no mesmo índice.
3. Defina o parâmetro *N-grams* como 2. Isso recupera a frequência de ocorrência de unigrams (um recurso para cada palavra) e bigrams (um recurso para cada par de palavras adjacentes) do texto de entrada. O parâmetro *N-grams* vai de 0 a 10, o que indica o número máximo de palavras sequenciais a serem incluídas em um recurso.  

![Módulo Hash de Recursos](./media/machine-learning-feature-selection-and-engineering/feature-Hashing1.png)

A figura a seguir mostra como ficariam esses novos recursos.

![Exemplo de Hash de Recursos](./media/machine-learning-feature-selection-and-engineering/feature-Hashing2.png)

## <a name="filtering-features-from-your-data--feature-selection"></a>Filtrando recursos de seus dados--Seleção de recursos
A *seleção de recursos* é um processo que geralmente é aplicado à criação de conjuntos de dados de treinamento para tarefas de modelagem de previsão, como tarefas de classificação ou regressão. O objetivo é selecionar um subconjunto de recursos no conjunto de dados original, reduzindo suas dimensões usando um conjunto mínimo de recursos para representar a quantidade máxima de variação nos dados. Esse subconjunto de recursos contém os únicos recursos a serem incluídos para treinar o modelo. A seleção de recursos atende a duas finalidades principais:

* A seleção de recursos frequentemente aumenta a precisão de classificação, eliminando recursos irrelevantes, redundantes ou altamente correlacionados.
* Ela reduz o número de recursos, o que torna o processo de treinamento de modelo mais eficiente. Isso é particularmente importante para tecnologias que sejam dispendiosas de treinar, como máquinas de vetor de suporte.

Embora a seleção de recursos busque reduzir o número de recursos no conjunto de dados usado para treinar o modelo, ela não costuma ser mencionada usando o termo *redução de dimensionalidade*. Métodos de seleção de recursos extraem um subconjunto dos recursos originais dos dados sem alterá-los.  Métodos de redução de dimensionalidade empregam recursos de engenharia que podem transformar os recursos originais e, assim, modificá-los. Exemplos de métodos de redução de dimensionalidade incluem a análise de componente principal, a análise de correlação canônica e a decomposição de valor singular.

Uma categoria amplamente aplicada de métodos de seleção de recursos em um contexto supervisionado é a seleção de recursos baseada em filtros. Ao avaliar a correlação entre cada recurso e o atributo de destino, esses métodos se aplicam a uma medida estatística para atribuir uma pontuação a cada recurso. Os recursos são classificados pela pontuação, o que pode ser usado para definir o limiar para manter ou eliminar um recurso específico. Correlação de Pearson, informações mútuas e o teste de quadrados de Chi são exemplos das medidas estatísticas usadas nesses métodos.

O Azure Machine Learning Studio fornece módulos para seleção de recursos. Conforme mostrado na figura a seguir, esses módulos incluem [Seleção de Recursos Baseada em Filtro][filter-based-feature-selection] e [Análise Discriminante Linear de Fisher][fisher-linear-discriminant-analysis].

![Exemplo de seleção de recursos](./media/machine-learning-feature-selection-and-engineering/feature-Selection.png)

Por exemplo, use o módulo [Seleção de Recursos baseada em Filtro][filter-based-feature-selection] com o exemplo de mineração de texto descrito anteriormente. Suponha que você deseja compilar um modelo de regressão após a criação de um conjunto de 256 recursos por meio do módulo [Hash de Recursos][feature-hashing], e que a variável de resposta é **Col1** e representa as resenhas de um livro que variam de 1 a 5. Defina **Método de pontuação de recursos** como **Correlação de Pearson**, a **Coluna de destino** como **Col1** e o **Número de recursos desejados** como **50**. O módulo [Seleção de Recursos Baseada em Filtros][filter-based-feature-selection] produz um conjunto de dados que contém 50 recursos, em conjunto com o atributo de destino **Col1**. A figura a seguir mostra o fluxo desse experimento e os parâmetros de entrada.

![Exemplo de seleção de recursos](./media/machine-learning-feature-selection-and-engineering/feature-Selection1.png)

A figura a seguir mostra os conjuntos de dados resultantes. Cada recurso é pontuado com base na Correlação de Pearson entre ele mesmo e o atributo de destino **Col1**. Os recursos com pontuação mais alta são mantidos.

![Conjuntos de dados da seleção de recursos baseada em filtro](./media/machine-learning-feature-selection-and-engineering/feature-Selection2.png)

A figura a seguir mostra as pontuações correspondentes dos recursos selecionados.

![Pontuações de recursos selecionados](./media/machine-learning-feature-selection-and-engineering/feature-Selection3.png)

Ao aplicar o módulo [Seleção de Recursos Baseada em Filtros][filter-based-feature-selection], 50 dos 256 recursos são selecionados porque eles têm o maior número de recursos correlacionados à variável de destino **Col1**, com base no método de pontuação **Correlação de Pearson**.

## <a name="conclusion"></a>Conclusão
A engenharia e a seleção de recursos são duas etapas realizadas com frequência para preparar os dados de treinamento ao criar um modelo de aprendizado de máquina. Normalmente, a engenharia de recursos é aplicada primeiro para gerar recursos adicionais e, em seguida, a etapa de seleção do recurso é feita para eliminar recursos redundantes, altamente correlacionados ou irrelevantes.

Nem sempre é necessário realizar a engenharia ou a seleção de recursos. Essa necessidade depende dos dados que você tem ou coleta, do algoritmo escolhido e do objetivo do experimento.

<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[fisher-linear-discriminant-analysis]: https://msdn.microsoft.com/library/azure/dcaab0b2-59ca-4bec-bb66-79fd23540080/



<!--HONumber=Nov16_HO3-->


