---
title: 'Rede Neural multiclasse: Referência de módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como usar o módulo de rede Neural Multiclasse no serviço Azure Machine Learning para criar um modelo de rede neural que pode ser usado para prever um destino que tenha vários valores.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: a7bef225c001ebd9bbb9a45c8bcc301cfb49edb6
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029379"
---
# <a name="multiclass-neural-network-module"></a>Módulo de rede Neural multiclasse

Este artigo descreve um módulo da interface visual (visualização) para o serviço de Azure Machine Learning.

Use este módulo para criar um modelo de rede neural que pode ser usado para prever um destino que tenha vários valores. 

Por exemplo, redes neurais desse tipo podem ser usadas nas tarefas de visão do computador complexas, como reconhecimento de letra ou dígito, classificação de documento e reconhecimento de padrões.

Classificação usando redes neurais é um método de aprendizado supervisionado e, portanto, requer uma *marcados dataset* que inclui uma coluna de rótulo.

Você pode treinar o modelo, fornecendo o modelo e o conjunto de dados marcado como uma entrada para [treinar modelo](./train-model.md). O modelo treinado, em seguida, pode ser usado para prever valores para os novos exemplos de entrada.  

## <a name="about-neural-networks"></a>Sobre redes neurais

Uma rede neural é um conjunto de camadas interconectadas. As entradas são a primeira camada e são conectadas a uma camada de saída por um grafo acíclico compostos por nós e bordas ponderadas.

Entre as camadas de entrada e saídas, você pode inserir várias camadas ocultas. Mais tarefas de previsão podem ser realizadas com facilidade com apenas uma ou algumas camadas ocultas. No entanto, uma pesquisa recente tenha mostrado que as redes neurais profundas (DNN) com várias camadas podem ser eficazes em tarefas complexas, como imagem ou reconhecimento de fala. As camadas sucessivas são usadas para modelar níveis crescentes de profundidade semântica.

A relação entre as entradas e saídas é aprendida de treinamento da rede neural nos dados de entrada. A direção do gráfico continua a partir de entradas por meio da camada oculta e para a camada de saída. Todos os nós em uma camada são conectados por bordas ponderadas a nós na próxima camada.

Para calcular a saída da rede para uma entrada específica, um valor é calculado em cada nó nas camadas ocultas e na camada de saída. O valor é definido Calculando a soma ponderada dos valores de nós da camada anterior. Uma função de ativação é então aplicada a essa soma ponderada.

## <a name="configure-multiclass-neural-network"></a>Configurar a rede Neural Multiclasse

1. Adicione a **rede Neural Multiclasse** módulo à sua experiência na interface. Você pode encontrar esse módulo sob **Machine Learning**, **inicializar**, no **classificação** categoria.

2. **Criar modo de aprendizagem**: Use esta opção para especificar como deseja que o modelo ser treinado:

    - **Um único parâmetro**: Escolha esta opção se você já souber como deseja configurar o modelo.

    

3. **Oculta a especificação da camada**: Selecione o tipo de arquitetura de rede para criar.

    - **Totalmente conectada caso**: Selecione esta opção para criar um modelo usando a arquitetura de rede neural padrão. Para modelos de rede neural multiclasse, os padrões são:

        - Uma camada oculta
        - A camada de saída é totalmente conectada à camada oculta.
        - A camada oculta é totalmente conectada à camada de entrada.
        - O número de nós na camada de entrada é determinado pelo número de recursos nos dados de treinamento.
        - O número de nós na camada oculta pode ser definido pelo usuário. O padrão é 100.
        - O número de nós na camada de saída depende do número de classes.
  
   

5. **Número de nós ocultos**: Essa opção lhe permite personalizar o número de nós ocultos na arquitetura padrão. Digite o número de nós ocultos. O padrão é uma camada oculta com 100 nós.

6. **A taxa de aprendizagem**: Defina o tamanho da etapa executada em cada iteração, antes da correção. Um valor maior para a taxa de aprendizagem pode fazer com que o modelo convergir mais rapidamente, mas com o risco mínimas locais.

7. **Número de iterações de aprendizado**: Especifique o número máximo de vezes que o algoritmo deve processar os casos de treinamento.

8. **O aprendizado inicial pondera o diâmetro**: Especifique os pesos de nó no início do processo de aprendizado.

9. **A dinâmica**: Especifica um peso a ser aplicado durante o aprendizado a nós de iterações anteriores.
  
11. **Exemplos de ordem aleatória**: Selecione esta opção para casos em ordem aleatória entre as iterações.

    Se você desmarcar essa opção, casos são processados em exatamente na mesma ordem toda vez que executar o teste.

12. **Número de semente aleatório**: Digite um valor para usar como a semente, se você quiser garantir a capacidade de repetição em execuções do mesmo experimento.

14. Conectar-se de um conjunto de dados de treinamento e uma da [módulos de treinamento](module-reference.md): 

    - Se você definir **criar modo de treino** à **único parâmetro**, use [modelo de treinamento](train-model.md).  
  

## <a name="results"></a>Resultados

Após a conclusão do treinamento:

- Para ver um resumo dos parâmetros do modelo, juntamente com o recurso de pesos que aprendi com treinamento e outros parâmetros de rede neural, clique com botão direito a saída de [modelo de treinamento](./train-model.md) e selecione **visualizar**.  

- Para salvar um instantâneo do modelo treinado, clique com botão direito do **modelo treinados** de saída e selecione **Salvar como modelo treinado**. Esse modelo não é atualizado em execuções sucessivas do mesmo experimento.


## <a name="next-steps"></a>Próximas etapas

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço de Azure Machine Learning. 