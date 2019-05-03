---
title: 'Regressão de rede neural: Referência de módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como usar o módulo de regressão de rede Neural no serviço de Azure Machine Learning para criar um modelo de regressão usando um algoritmo de rede neural personalizável...
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: bc6a7505ab09e929e5add61eea687f871aedf242
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029304"
---
# <a name="neural-network-regression-module"></a>Módulo de regressão de rede neural

*Cria um modelo de regressão usando um algoritmo de rede neural*  
  
 Categoria: Machine Learning / inicializar modelo / regressão
  
## <a name="module-overview"></a>Visão geral do módulo  

Este artigo descreve um módulo da interface visual (visualização) para o serviço de Azure Machine Learning.

Use este módulo para criar um modelo de regressão usando um algoritmo de rede neural personalizável.
  
 Embora as redes neurais sejam amplamente conhecidas para uso em problemas complexos, como reconhecimento de imagem de modelagem e de aprendizado profundo, eles são facilmente adaptados para problemas de regressão. Qualquer classe de modelos estatísticos pode ser chamada de rede neural se eles usam pesos adaptáveis e podem aproximar funções não lineares de suas entradas. Assim, a regressão de rede neural é adequada para onde um modelo de regressão mais tradicional não tiver uma solução de problemas.
  
 Regressão de rede neural é um método de aprendizado supervisionado e, portanto, requer uma *marcados dataset*, que inclui uma coluna de rótulo. Porque um modelo de regressão prevê um valor numérico, a coluna de rótulo deve ser um tipo de dados numéricos.  
  
 Você pode treinar o modelo, fornecendo o modelo e o conjunto de dados marcado como uma entrada para [treinar modelo](./train-model.md). O modelo treinado, em seguida, pode ser usado para prever valores para os novos exemplos de entrada.  
  
## <a name="configure-neural-network-regression"></a>Configurar a regressão de rede Neural 

Redes neurais podem ser amplamente personalizadas. Esta seção descreve como criar um modelo usando dois métodos:
  
+ [Criar um modelo de rede neural usando a arquitetura padrão](#bkmk_DefaultArchitecture)  
  
    Se você aceitar a arquitetura de rede neural padrão, use o **propriedades** painel para definir os parâmetros que controlam o comportamento da rede neural, como o número de nós na camada oculta, taxa de aprendizagem e normalização.

    Comece aqui se estiver familiarizado com redes neurais. O módulo dá suporte a muitas personalizações, bem como ajuste de modelos, sem conhecimento profundo de redes neurais. 

+ Definir uma arquitetura personalizada para uma rede neural 

    Use esta opção se você quiser adicionar camadas ocultas extra ou personalizar totalmente as funções de ativação, suas conexões e arquitetura de rede.
    
    Essa opção é recomendada se você já estiver familiarizado com redes neurais. Você pode usar a linguagem Net # para definir a arquitetura de rede.  

##  <a name="bkmk_DefaultArchitecture"></a> Criar um modelo de rede neural usando a arquitetura padrão
  
1.  Adicione a **regressão de rede Neural** módulo à sua experiência na interface. Você pode encontrar esse módulo sob **Machine Learning**, **inicializar**, no **regressão** categoria. 
  
2. Indique como deseja que o modelo ser treinado, definindo o **criar modo de treino** opção.  
  
    -   **Um único parâmetro**: Escolha esta opção se você já souber como deseja configurar o modelo.  

3.  Na **oculta a especificação de camada**, selecione **totalmente conectada caso**. Essa opção cria um modelo usando a arquitetura de rede neural padrão, que, para um modelo de regressão de rede neural tem os seguintes atributos:  
  
    + A rede tem exatamente uma camada oculta.
    + A camada de saída é totalmente conectada à camada oculta e a camada oculta é totalmente conectada à camada de entrada.
    + O número de nós na camada oculta pode ser definido pelo usuário (o valor padrão é 100).  
  
    Como o número de nós na camada de entrada é determinado pelo número de recursos nos dados de treinamento, em um modelo de regressão pode haver apenas um nó na camada de saída.  
  
4. Para **número de nós ocultos**, digite o número de nós ocultos. O padrão é uma camada oculta com 100 nós. (Essa opção não estará disponível se você definir uma arquitetura personalizada usando Net #.)
  
5.  Para **taxa de aprendizagem**, digite um valor que define a etapa executada em cada iteração, antes da correção. Um valor maior para a taxa de aprendizagem pode fazer com que o modelo convergir mais rapidamente, mas com o risco mínimas locais.

6.  Para **número de iterações de aprendizado**, especifique o número máximo de vezes que o algoritmo processa os casos de treinamento.

7.  Para * * o aprendizado inicial pondera o diâmetro, digite um valor que determina os pesos de nó no início do processo de aprendizado.

8.  Para **a dinâmica**, digite um valor a ser aplicado durante o aprendizado de um peso em nós de iterações anteriores.

10. Selecione a opção **exemplos de ordem aleatória**, para alterar a ordem dos casos entre as iterações. Se você desmarcar essa opção, casos são processados em exatamente na mesma ordem toda vez que executar o teste.
  
11. Para **propagação de número aleatório**, opcionalmente, você pode digitar um valor para usar como a semente. Especificar uma semente de valor é útil quando você deseja garantir a capacidade de repetição em execuções do mesmo experimento.
  
13. Conectar-se de um conjunto de dados de treinamento e uma da [módulos de treinamento](module-reference.md): 
  
    -   Se você definir **criar modo de treino** à **único parâmetro**, use [modelo de treinamento](./train-model.md).  
  
   
14. Execute o experimento.  

## <a name="results"></a>Resultados

Após a conclusão do treinamento:

+ Para ver um resumo dos parâmetros do modelo, juntamente com o recurso de pesos que aprendi com treinamento e outros parâmetros de rede neural, clique com botão direito a saída de [modelo de treinamento](./train-model.md)e selecione **visualizar**.  

+ Para salvar um instantâneo do modelo treinado, clique com botão direito do **modelo treinados** de saída e selecione **Salvar como modelo treinado**. Esse modelo não é atualizado em execuções sucessivas do mesmo experimento.


## <a name="next-steps"></a>Próximas etapas

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço de Azure Machine Learning. 