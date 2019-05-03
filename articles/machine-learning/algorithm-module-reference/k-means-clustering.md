---
title: 'Clustering de K-Means: Referência de módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como usar o módulo de clustering K-means no serviço do Azure Machine Learning para treinar modelos de clustering.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 29aeb460581655190b7c3f4256647059f4642d3e
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029694"
---
# <a name="k-means-clustering"></a>Clustering de K-Means

Este artigo descreve como usar o **Clustering K-Means** módulo no Azure Machine Learning Studio para criar um modelo de clustering K-means não treinado. 
 
K-means é a mais simples e mais conhecido *sem supervisão* algoritmos de aprendizado e pode ser usado para uma variedade de tarefas de aprendizado de máquina, como [detectando dados anormais](https://msdn.microsoft.com/magazine/jj891054.aspx), clustering de texto documentos e análise de um conjunto de dados antes de usar outros métodos de classificação ou regressão. Para criar um modelo de clustering, adicionar esse módulo à sua experiência, conecte-se um conjunto de dados e definir parâmetros, como o número de clusters de se esperar, a métrica de distância para usar na criação de clusters e assim por diante. 
  
Depois de configurar os hiperparâmetros de módulo, o modelo não treinado para conectar-se a [treinar modelo de Clustering](train-clustering-model.md) porque o algoritmo K-means é um método de aprendizado sem supervisão, uma coluna de rótulo é opcional. 

+ Se seus dados incluem um rótulo, você pode usar os valores de rótulo para guiar a seleção dos clusters e otimizar o modelo. 

+ Se seus dados não tem nenhum rótulo, o algoritmo cria clusters que representa as categorias possíveis, com base apenas nos dados.  
  

  
##  <a name="understanding-k-means-clustering"></a>Noções básicas sobre clustering de k-means
 
Em geral, o clustering usa técnicas iterativas para agrupar casos em um conjunto de dados em clusters que contenham características semelhantes. Esses agrupamentos são úteis para explorar dados, identificando anomalias nos dados e, eventualmente, para fazer previsões. Modelos de clustering também podem ajudar você a identificar relações em um conjunto de dados que você pode derivar sem lógica pela observação de navegação ou simple. Por esses motivos, clustering é frequentemente usado nas fases iniciais de tarefas, aprendizado de máquina para explorar os dados e descobrir correlações inesperadas.  
  
 Quando você configura um modelo de clustering usando o método k-means, você deve especificar um número de destino *k* indicando o número de *centroides* desejado no modelo. O centroide é um ponto que é o representante de cada cluster. O algoritmo K-means atribui cada ponto de dados de entrada a um dos clusters, minimizando a soma dos quadrados dentro do cluster. 
 
Ao processar os dados de treinamento, o algoritmo K-means começa com um conjunto inicial de randomnly escolhido centroides, que servem como ponto de partida para cada cluster e aplicam o algoritmo de Lloyd para refinar de forma iterativa os locais de centroides. O algoritmo K-means para de criar e refinar os clusters quando ele atende a uma ou mais das seguintes condições:  
  
-   Os centroides estabilizam, significando que não mais alterar as atribuições de cluster para pontos individuais e o algoritmo convergiu para uma solução.  
  
-   O algoritmo é concluído executando o número especificado de iterações.  
  
 Depois de concluir a fase de treinamento, você deve usar o [atribuir dados aos Clusters](assign-data-to-clusters.md) módulo atribuir novos casos a um dos clusters que foi encontrado pelo algoritmo k-means. Atribuição de cluster é executada ao calcular a distância entre o novo caso e o centroide de cada cluster. Cada novo caso é atribuído ao cluster com o centroide mais próximo.  

## <a name="how-to-configure-k-means-clustering"></a>Como configurar o Clustering K-Means
  
1.  Adicione a **Clustering K-Means** módulo ao seu experimento.  
  
2.  Especifique como deseja que o modelo ser treinado, definindo o **criar modo de treino** opção.  
  
    -   **Um único parâmetro**: Se você souber os parâmetros exatos que você deseja usar no modelo de clustering, você pode fornecer um conjunto específico de valores como argumentos.  
  
   
  
3.  Para **número de Centroides**, digite o número de clusters que você quiser que o algoritmo para começar.  
  
     O modelo não é garantido para produzir exatamente esse número de clusters. O algorithn começa com esse número de pontos de dados e itera para localizar a configuração ideal.  
  
     
  
4.  As propriedades **inicialização** é usado para especificar o algoritmo que é usado para definir a configuração de cluster inicial.  
  
    -   **N primeiro**: Um número inicial de pontos de dados é escolhido no conjunto de dados e usado como o meio inicial.  
  
         Também chamado de *método Forgy*.  
  
    -   **Aleatório**: O algoritmo coloca aleatoriamente um ponto de dados em um cluster e, em seguida, calcula a média inicial para ser o centroide do cluster aleatoriamente atribuída pontos.  
  
         Também chamado de *partição aleatória* método.  
  
    -   **K-Means++**: Isso é o método padrão para a inicialização de clusters.  
  
         O **K-means + +** algoritmo foi proposto em 2007 por David Arthur e Sergei Vassilvitskii para evitar o clustering ruim pelo padrão k-means algoritmo. **K-means + +** aprimora padrão K-means, usando um método diferente para escolher os centros de cluster inicial.  
  
    
5.  Para **propagação de número aleatório**, opcionalmente, digite um valor a ser usado como a semente para a inicialização do cluster. Esse valor pode ter um impacto significativo sobre a seleção de cluster.  
  
    
  
6.  Para **métrica**, escolha a função a ser usada para medir a distância entre os vetores de cluster ou entre novos pontos de dados e o centroide escolhido aleatoriamente. O Azure Machine Learning suporta as seguintes métricas de distância do cluster:  
  
    -   **Euclidiana**: A distância Euclidiana normalmente é usada como uma medida de dispersão de cluster para clustering de K-means. Essa métrica é preferencial porque minimiza a distância média entre pontos e os centroides.
  

  
7.  Para **iterações**, digite o número de vezes que o algoritmo deve iterar os dados de treinamento antes de finalizar a seleção de centroides.  
  
     Você pode ajustar esse parâmetro de precisão saldo vs. tempo de treinamento.  
  
8.  Para **modo de rótulo de atribuir**, escolha uma opção que especifica como uma coluna de rótulo, se estiverem presentes no conjunto de dados, deve ser tratada.  
  
     Como o clustering K-means é uma método de aprendizado de máquina não supervisionado, os rótulos são opcionais. No entanto, se seu conjunto de dados já tem uma coluna de rótulo, você pode usar esses valores para guiar a seleção dos clusters, ou você pode especificar que os valores de ser ignorada.  
  
    -   **Ignorar a coluna de rótulo**: Os valores na coluna rótulo são ignorados e não são usados na criação do modelo.
  
    -   **Preencher valores ausentes**: Os valores de coluna de rótulo são usados como recursos para ajudar a criar os clusters. Se todas as linhas não têm um rótulo, o valor é inserir usando outros recursos.  
  
    -   **Substituição de mais próximo ao centro**: Os valores de coluna de rótulo são substituídos por valores de rótulo previsto, usando o rótulo do ponto mais próximo de centroide atual.  

8.  Selecione a opção **normalizar recursos**, se você deseja normalizar recursos antes do treinamento.
  
     Se você aplicar a normalização, antes do treinamento, os pontos de dados são normalizados para `[0,1]` por MinMaxNormalizer.

10. Treine o modelo.  
  
    -   Se você definir **criar modo de treino** à **único parâmetro**, adicione um conjunto de dados marcado e treinar o modelo usando o [treinar o modelo de Clustering](train-clustering-model.md) módulo.  
  

### <a name="results"></a>Resultados

Depois de concluir a configuração e treinamento do modelo, você tem um modelo que você pode usar para gerar pontuações. No entanto, há várias maneiras para treinar o modelo e várias maneiras de exibir e usar os resultados: 

#### <a name="capture-a-snapshot-of-the-model-in-your-workspace"></a>Capturar um instantâneo do modelo no seu espaço de trabalho

+ Se você tiver usado o [treinar modelo de Clustering](train-clustering-model.md) módulo
    1. Clique com botão direito do [treinar modelo de Clustering](train-clustering-model.md) módulo.
    2. Selecione **modelo treinados** e, em seguida, clique em **Salvar como modelo treinado**.


O modelo salvo representará os dados de treinamento no momento em que você salvou o modelo. Se você atualizar os dados de treinamento usados no teste, ele não atualizará o modelo salvo. 



#### <a name="see-the-clustering-result-dataset"></a>Consulte o conjunto de dados de resultado clustering 

+ Se você tiver usado o [treinar modelo de Clustering](train-clustering-model.md) módulo
    1. Clique com botão direito do [treinar modelo de Clustering](train-clustering-model.md) módulo.
    2. Selecione **conjunto de dados de resultados** e, em seguida, clique em **visualizar**.


### <a name="tips-for-generating-the-best-clustering-model"></a>Dicas para gerar o melhor modelo de clustering  

É comum que o **propagação** processo usado durante o clustering pode afetar significativamente o modelo. A propagação significa que o posicionamento inicial de pontos em potental de centroides.
 
Por exemplo, se o conjunto de dados contiver muitas exceções, e uma exceção é escolhida para propagar os clusters, nenhum outro ponto de dados se ajusta bem com o cluster e o cluster pode ser um singleton: ou seja, um cluster com apenas um ponto.  
  
Há várias maneiras de evitar esse problema:  
  
-   Alterar o número de centroides e tente vários valores de semente.  
  
-   Crie vários modelos, variando a métrica ou iterar muito mais.  
  
  
Em geral, com modelos de clustering, é possível que uma determinada configuração resultará em um conjunto localmente otimizado de clusters. Em outras palavras, o conjunto de clusters retornados pelo modelo adequado para somente os pontos de dados atual e não é generalizável com outros dados. Se você usou uma configuração inicial diferente, o método K-means pode encontrar uma configuração diferente, talvez superior. 
