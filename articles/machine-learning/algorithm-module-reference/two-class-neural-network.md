---
title: 'Rede Neural de duas classes: Referência de módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como usar o módulo de rede Neural de duas classes no serviço de Azure Machine Learning para criar um modelo de rede neural que pode ser usado para prever um destino que tenha somente dois valores.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 7ea852fcd312c6f7b1b716278ed538b7accde5bd
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029214"
---
# <a name="two-class-neural-network-module"></a>Módulo de rede Neural de duas classes

Este artigo descreve um módulo da interface visual (visualização) para o serviço de Azure Machine Learning.

Use este módulo para criar um modelo de rede neural que pode ser usado para prever um destino que tenha somente dois valores.

Classificação usando redes neurais é um método de aprendizado supervisionado e, portanto, requer uma *marcados dataset*, que inclui uma coluna de rótulo. Por exemplo, você pode usar esse modelo de rede neural para prever resultados binários, como se um paciente tem certa doença ou se uma máquina é a probabilidade de falhar dentro de uma janela de tempo especificada.  

Depois de definir o modelo, treiná-lo, fornecendo um conjunto de dados marcado e o modelo como uma entrada para [treinar modelo](./train-model.md). O modelo treinado, em seguida, pode ser usado para prever valores para novas entradas.

### <a name="more-about-neural-networks"></a>Mais informações sobre redes neurais

Uma rede neural é um conjunto de camadas interconectadas. As entradas são a primeira camada e são conectadas a uma camada de saída por um grafo acíclico compostos por nós e bordas ponderadas.

Entre as camadas de entrada e saídas, você pode inserir várias camadas ocultas. Mais tarefas de previsão podem ser realizadas com facilidade com apenas uma ou algumas camadas ocultas. No entanto, uma pesquisa recente tenha mostrado que as redes neurais profundas (DNN) com várias camadas podem ser eficazes em tarefas complexas, como imagem ou reconhecimento de fala. As camadas sucessivas são usadas para modelar níveis crescentes de profundidade semântica.

A relação entre as entradas e saídas é aprendida de treinamento da rede neural nos dados de entrada. A direção do gráfico continua a partir de entradas por meio da camada oculta e para a camada de saída. Todos os nós em uma camada são conectados por bordas ponderadas a nós na próxima camada.

Para calcular a saída da rede para uma entrada específica, um valor é calculado em cada nó nas camadas ocultas e na camada de saída. O valor é definido Calculando a soma ponderada dos valores de nós da camada anterior. Uma função de ativação é então aplicada a essa soma ponderada.
  
## <a name="how-to-configure"></a>Como configurar

1.  Adicione a **rede Neural de duas classes** módulo ao seu experimento. Você pode encontrar esse módulo sob **Machine Learning**, **inicializar**, no **classificação** categoria.  
  
2.  Especifique como deseja que o modelo ser treinado, definindo o **criar modo de treino** opção.  
  
    -   **Um único parâmetro**: Escolha esta opção se você já souber como deseja configurar o modelo.  

3.  Para **oculta a especificação da camada**, selecione o tipo de arquitetura de rede para criar.  
  
    -   **Totalmente conectada caso**: Usa a arquitetura de rede neural padrão, definida para redes neurais de duas classes da seguinte maneira:
  
        -   Tem uma camada oculta.
  
        -   A camada de saída é totalmente conectada à camada oculta e a camada oculta é totalmente conectada à camada de entrada.
  
        -   O número de nós na camada de entrada é igual ao número de recursos nos dados de treinamento.
  
        -   O número de nós na camada oculta é definido pelo usuário. O valor padrão é 100.
  
        -   O número de nós é igual ao número de classes. Para uma rede neural de duas classes, isso significa que todas as entradas devem mapear para um dos dois nós na camada de saída.

5.  Para **taxa de aprendizagem**, defina o tamanho da etapa executada em cada iteração, antes da correção. Um valor maior para a taxa de aprendizagem pode fazer com que o modelo convergir mais rapidamente, mas com o risco mínimas locais.

6.  Para **número de iterações de aprendizado**, especifique o número máximo de vezes que o algoritmo deve processar os casos de treinamento.

7.  Para **o aprendizado inicial pondera o diâmetro**, especifique os pesos de nó no início do processo de aprendizado.

8.  Para **a dinâmica**, especificar um peso a ser aplicado durante o aprendizado a nós de iterações anteriores  

10. Selecione o **exemplos de ordem aleatória** opção para casos em ordem aleatória entre as iterações. Se você desmarcar essa opção, casos são processados em exatamente na mesma ordem toda vez que executar o teste.
  
11. Para **propagação de número aleatório**, digite um valor para usar como a semente.
  
     Especificar uma semente de valor é útil quando você deseja garantir a capacidade de repetição em execuções do mesmo experimento.  Caso contrário, um valor de relógio do sistema é usado como a semente, o que pode causar resultados ligeiramente diferentes sempre que você executar o teste.
  
13. Adicionar um conjunto de dados marcado para o experimento e conecte-se um dos [módulos de treinamento](module-reference.md).  
  
    -   Se você definir **criar modo de treino** à **único parâmetro**, use o [modelo de treinamento](train-model.md) módulo.  
  
14. Execute o experimento.

## <a name="results"></a>Resultados

Após a conclusão do treinamento:

+ Para ver um resumo dos parâmetros do modelo, juntamente com o recurso de pesos que aprendi com treinamento e outros parâmetros de rede neural, clique com botão direito a saída de [modelo de treinamento](./train-model.md)e selecione **visualizar**.  

+ Para salvar um instantâneo do modelo treinado, clique com botão direito do **modelo treinados** de saída e selecione **Salvar como modelo treinado**. Esse modelo não é atualizado em execuções sucessivas do mesmo experimento.


## <a name="next-steps"></a>Próximas etapas

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço de Azure Machine Learning. 
