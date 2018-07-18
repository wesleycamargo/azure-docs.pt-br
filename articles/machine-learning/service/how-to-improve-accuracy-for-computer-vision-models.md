---
title: Melhor precisão dos modelos de classificação e pesquisa visual computacional no Azure Machine Learning
description: Saiba como melhorar a precisão da classificação de imagens de pesquisa visual computacional, da detecção de objeto e de modelos de similaridade de imagem usando o Pacote do Azure Machine Learning para Pesquisa Visual Computacional.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: netahw
author: nhaiby
ms.date: 04/23/2018
ms.openlocfilehash: e134e1e544c51d6756d5021fef8c049fe7d8afb0
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
ms.locfileid: "33783575"
---
# <a name="improve-the-accuracy-of-computer-vision-models"></a>Melhorar a precisão dos modelos de pesquisa visual computacional

Com o **Pacote do Azure Machine Learning para Pesquisa Visual Computacional**, você pode criar e implantar classificação de imagem, detecção de objetos e modelos de similaridade da imagem. Saiba mais sobre o pacote e como instalá-lo.

Neste artigo, você aprenderá a ajustar esses modelos para aumentar a precisão dos dados. 

## <a name="accuracy-of-image-classification-models"></a>Precisão de modelos de classificação de imagem

O Pacote para Pesquisa Visual Computacional oferece comprovadamente bons resultados para uma ampla variedade de conjuntos de dados. No entanto, assim como ocorre na maioria dos projetos de aprendizado de máquina, a obtenção dos melhores resultados possíveis para um novo conjunto de dados exige um ajuste cuidadoso de parâmetros, bem como a avaliação de diferentes decisões de design. As seções a seguir orientam sobre como melhorar a precisão em determinado conjunto de dados, ou seja, quais parâmetros são mais interessantes de se otimizar primeiro, os valores desses parâmetros que devem ser experimentados e as armadilhas a serem evitadas.

Em termos gerais, o treinamento de modelos de aprendizado profundo vem com uma compensação entre o tempo de treinamento e a precisão do modelo. O Pacote para Pesquisa Visual Computacional tem parâmetros padrão predefinidos (confira a primeira linha na tabela a seguir) com foco em um treinamento rápido ao mesmo tempo que produz modelos com alta precisão. Essa precisão pode ser ainda mais aprimorada com o uso de, por exemplo, resoluções de imagem mais altas ou modelos mais profundos; no entanto, o custo pode ser o aumento do tempo de treinamento em 10x ou mais.

É recomendável primeiro trabalhar com os parâmetros padrão, treinar um modelo, verificar os resultados, corrigir as anotações de valor de referência conforme a necessidade e experimentar apenas parâmetros que diminuam o tempo de treinamento (veja na tabela abaixo os valores de parâmetro sugeridos). Recomenda-se compreender esses parâmetros, mesmo que isso não seja tecnicamente necessário.


### <a name="best-practices-and-tips"></a>Melhores práticas e dicas

* Qualidade de dados: o treinamento e os conjuntos de teste devem ser de alta qualidade. Ou seja, as imagens são anotadas corretamente, as imagens ambíguas são removidas (por exemplo, quando não está claro para o olho humano se a imagem mostra uma bola de tênis ou um limão) e os atributos são mutuamente exclusivos (ou seja, cada imagem pertence a um atributo).

* Antes de refinar a DNN, um classificador SVM deve ser treinado usando uma DNN previamente treinada e fixa como transformador de recurso. O Pacote para Pesquisa Visual Computacional dá suporte a isso e não exige muito tempo de treinamento, já que a DNN em si não é modificada. Mesmo essa abordagem simples alcança bons resultados de precisão e, portanto, representa uma linha de base forte. A próxima etapa é, em seguida, refinar a DNN que deve fornecer maior precisão.

* Se o objeto de interesse está pequeno na imagem, é sabido que as abordagens de Classificação de imagens não funcionam bem. Nesses casos, considere usar uma abordagem de detecção de objeto como o Faster R-CNN baseado em Tensorflow do Pacote para Pesquisa Visual Computacional.

* Quanto mais dados de treinamento, melhor. Como regra geral, é ideal ter pelo menos cem exemplos para cada classe, ou seja, cem imagens de "cachorro", cem imagens de "gato", etc. O treinamento de um modelo com menos imagens é possível, mas pode não produzir bons resultados.

* As imagens de treinamento precisam residir localmente no computador com a GPU e estar em uma unidade SSD (não em um HDD). Caso contrário, a latência de leitura da imagem poderá reduzir consideravelmente a velocidade de treinamento (pelo mesmo fator de 100x).


### <a name="parameters-to-optimize"></a>Parâmetros a serem otimizados

É importante localizar os valores ideais para esses parâmetros, pois isso pode melhorar significativamente a precisão:
* Taxa de aprendizado (`lr_per_mb`): indiscutivelmente, o parâmetro mais importante a se acertar. Se a precisão no conjunto de treinamento após refinamento da DNN fica acima de ~5%, a taxa de aprendizagem provavelmente está muito alta ou o número de épocas de treinamento está muito baixo. Especialmente no caso de conjuntos de treinamento pequenos, a DNN tende a se ajustar aos dados de treinamento em excesso; no entanto, isso costuma gerar bons modelos no conjunto de teste, na prática. Normalmente, usamos 15 épocas, em que a taxa de aprendizagem inicial é reduzida duas vezes; em alguns casos, o uso de mais épocas de treinamento pode melhorar o desempenho.

* Resolução de entrada (`image_dims`): a resolução de imagem padrão é de 224 x 224 pixels. O uso de resolução de imagem mais alta de, por exemplo, 500 x 500 pixels ou 1000 x 1000 pixels, pode melhorar significativamente a precisão, mas atrasa o ajuste da DNN. O Pacote para Pesquisa Visual Computacional espera que a resolução de entrada seja uma tupla de (canais de cor, largura da imagem, altura da imagem), por exemplo, (3, 224, 224), em que o número de canais de cor deve ser definido como 3 (as faixas de vermelho-verde-azul).

* Arquitetura de modelo (`base_model_name`): tente usar DNNs mais profundas, como ResNet-34 ou ResNet-50 em vez do ResNet-18 padrão. O modelo Resnet-50 não é apenas mais profundo, mas a saída da penúltima camada tem o tamanho de 2.048 flutuações (vs. 512 flutuações dos modelos ResNet-18 e ResNet-34). Esse aumento de dimensionalidade pode ser especialmente útil na hora de manter a DNN fixa e treinar um classificador SVM.

* Tamanho de minilote (`mb_size`): grandes tamanhos de minilote geram tempos de treinamento mais rápidos; no entanto, isso aumenta o consumo de memória da DNN. Portanto, ao selecionar modelos mais profundos (por exemplo, ResNet-50 em vez de ResNet-18) e/ou resoluções de imagem mais altas (500\*500 pixels em vez de 224\*224 pixels), normalmente será preciso reduzir o tamanho do minilote para evitar erros de falta de memória. Ao alterar o tamanho do minilote, muitas vezes a taxa de aprendizado precisa ser ajustada como mostra a tabela a seguir.
* Taxa de abandono (`dropout_rate`) e L2-regularizer (`l2_reg_weight`): o ajuste excessivo de DNN pode ser reduzido usando uma taxa de abandono de 0,5 (o padrão é 0,5 no Pacote para Pesquisa Visual Computacional) ou mais e aumentando o peso do regularizador (o padrão é 0,0005 no Pacote para Pesquisa Visual Computacional). No entanto, observe que, especialmente em pequenos conjuntos de dados, o sobreajuste da DNN é complicado e, geralmente, impossível de evitar.


### <a name="parameter-definitions"></a>Definições de parâmetro

- **Taxa de aprendizado**: tamanho de etapa usado durante o aprendizado descendente do gradiente. Se o número for muito baixo, o modelo terá muitas épocas de treinamento; se o número for muito alto, o modelo não se tornará uma boa solução. Normalmente, uma agenda é usada quando a taxa de aprendizado é reduzida após determinado número de épocas. E. Por exemplo, o agendamento de taxa de aprendizado `[0.05]*7 + [0.005]*7 + [0.0005]` corresponde ao uso de uma taxa de aprendizado inicial de 0,05 para as primeiras sete épocas, seguido por uma taxa de aprendizado reduzida 10x, de 0,005, para outras sete épocas e, por fim, o ajuste do modelo para uma única época com uma taxa de aprendizado reduzida 100x, de 0,0005.

- **Tamanho de minilote**: as GPUs podem processar várias imagens em paralelo para acelerar o cálculo. Essas imagens processadas paralelamente também são conhecidas como minilotes. Quanto maior o tamanho do minilote, mais rápido será o treinamento, mas às custas de um maior consumo de memória da DNN.

### <a name="recommended-parameter-values"></a>Valores de parâmetro recomendados

A tabela a seguir fornece conjuntos de parâmetros diferentes que produziram modelos de alta precisão em uma ampla variedade de tarefas de classificação de imagem. Os parâmetros ideais dependem do conjunto de dados específico e da GPU exata usada e, portanto, a tabela deve ser considerada apenas como uma diretriz. Depois de experimentar esses parâmetros, considere também as resoluções de imagem com mais de 500 x 500 pixels ou modelos mais profundos, como Resnet-101 ou Resnet-152.

A primeira linha da tabela corresponde aos parâmetros padrão que estão definidos no Pacote para Pesquisa Visual Computacional. Todas as outras linhas levam mais tempo para treinar (indicado na primeira coluna); no entanto, elas têm a vantagem de uma maior precisão (veja a precisão média ao longo de três conjuntos internos na segunda coluna). Por exemplo, os parâmetros na última linha demoram 5 a 15 vezes mais a serem treinados, mas resultaram em um aumento de precisão (média), em três conjuntos de dados internos, de 82,6% a 92,8%.

Modelos mais profundos e resoluções mais altas ocupam mais memória de DNN e, portanto, o tamanho do minilote precisa ser reduzido de acordo com o aumento da complexidade do modelo para evitar erros de falta de memória. Como pode ser visto na tabela a seguir, é vantajoso diminuir a taxa de aprendizagem pela metade sempre que diminuir o tamanho do minilote por esse mesmo fator. O tamanho do minilote talvez precise ser reduzido ainda mais no caso de GPUs com pequenas quantidades de memória.

| Tempo de treinamento (estimativa) | Precisão do exemplo | Tamanho do minilote (*mb_size*) | Taxa de aprendizado (*lr_per_mb*) | Resolução de imagem (*image_dims*) | Arquitetura DNN (*base_model_name*) |
|------------- |:-------------:|:-------------:|:-----:|:-----:|:---:|
| 1x (referência) | 82,6% | 32 | [0,05] \*7 + [0,005]\*7 + [0,0005]  | (3, 224, 224) | ResNet18_ImageNet_CNTK |
| 2 a 5x    | 90,2% | 16 | [0,025]\*7 + [0,0025]\*7 + [0,00025] | (3, 500, 500) | ResNet18_ImageNet_CNTK |
| 2 a 5x    | 87,5% | 16 | [0,025]\*7 + [0,0025]\*7 + [0,00025] | (3, 224, 224) | ResNet50_ImageNet_CNTK |
| 5 a 15 vezes        | 92,8% |  8 | [0,01] \*7 + [0,001]\*7 + [0,0001]  | (3, 500, 500) | ResNet50_ImageNet_CNTK |


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o Pacote do Azure Machine Learning para Pesquisa Visual Computacional:
+ Conferir a documentação de referência

+ Saiba mais sobre [outros pacotes do Python para o Azure Machine Learning](reference-python-package-overview.md)