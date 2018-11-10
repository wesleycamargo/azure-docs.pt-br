---
title: Ambientes de desenvolvimento para Machine Learning do Azure | Microsoft Docs
description: Uma visão geral dos ambientes de desenvolvimento que você pode usar com o serviço de Azure Machine Learning. O Python 3 é o único requisito para o seu ambiente de desenvolvimento, mas é recomendável usar também os ambientes do Conda. Para o desenvolvimento de ferramentas, é recomendável Jupyter Notebooks, Azure Notebooks e editores IDE/código.
services: machine-learning
author: rastala
ms.author: roastala
manager: cgronlun
ms.service: machine-learning
ms.component: core
ms.reviewer: larryfr
ms.topic: conceptual
ms.date: 9/24/2018
ms.openlocfilehash: 4d25e147044053aa76afe2da482b71c24efc2325
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51242867"
---
# <a name="development-environment-for-azure-machine-learning"></a>Ambiente de desenvolvimento para a Machine Learning do Azure 

Saiba sobre os ambientes de desenvolvimento que você pode usar com o serviço de Azure Machine Learning. 

O serviço de Azure Machine Learning é independente de plataforma e não requer um ambiente de desenvolvimento específico. Requer __Python 3__, e é recomendável usar __Conda__ para criar ambientes isolados. __Se você já tiver um ambiente de desenvolvimento que atende a esses requisitos__, ignore este documento e vá para o documento [Configurar seu ambiente de desenvolvimento](how-to-configure-environment.md).

O restante deste documento aborda os ambientes de desenvolvimento que recomendamos:

* __Jupyter Notebooks__
* __Azure Notebooks__
* __Ambientes de desenvolvimento integrado (IDE) e editores de código__
* __Máquina Virtual de Ciência de Dados__

Uma comparação entre esses ambientes é difícil, pois os blocos de anotações e IDEs podem ser estendidos. Por exemplo, alguns IDEs podem ser usados como clientes para Jupyter Notebooks. Em termos gerais, __notebooks__ são projetados para __experimentação interativa__ e __visualização__. __Editores de código e IDEs__ fornecem ferramentas para __melhorar a qualidade do código__ e __integrar com sistemas externos__ como controle de versão.

> [!TIP]
> Usando um ambiente não o impede de usar o outro. Notebooks e IDEs são apenas ferramentas e podem ser combinados conforme necessário. Por exemplo, você pode começar a experimentar em um notebook e exportar para um script de python para edição e depuração em um IDE.
>
> É por isso que a Máquina Virtual de Ciência de Dados fornece os Jupyter Notebook e vários IDEs Python populares.

## <a name="jupyter-notebooks"></a>Notebooks Jupyter

Jupyter Notebooks fazem parte do [Projeto Jupyter](https://jupyter.org/). Se concentram no fornecimento de uma experiência interativa de codificação, onde você pode criar documentos que combinam código ao vivo com elementos gráficos e texto. Você pode instalar os Jupyter Notebooks em uma variedade de plataformas.

Ter sua própria instalação do Jupyter Notebook permite que você instale e configure o ambiente conforme necessário. No entanto, você é responsável por manter o sistema.

## <a name="azure-notebooks"></a>Azure Notebooks

[O Azure Notebooks](https://notebooks.azure.com) (versão prévia) é um ambiente de blocos de anotações na nuvem do Azure. Se baseia no Jupyter e fornece um ambiente que é pré-carregado com bibliotecas populares. O SDK de aprendizado Machine Learning do Azure já está instalado nos Azure Notebooks, para que você possa começar a experimentar com quase nenhuma configuração.

O Azure Notebooks também simplifica o processo de compartilhamento de seus blocos de anotações. Você pode compartilhar uma URL para os blocos de anotações, tornar sua biblioteca pública ou compartilhar em mídias sociais da interface do Azure Notebooks.

A desvantagem do Azure Notebooks é que você não tem controle completo sobre o ambiente e pode não ser capaz de instalar o software personalizado que você precisa. Também é um ambiente compartilhado, portanto, os notebooks podem funcionar mais lentos do que em uma instalação do Jupyter Notebook dedicada.

## <a name="ides-and-code-editors"></a>Editores de código e IDEs

Há muitos editores de código e IDEs que funcionarão com o Azure Machine Learning. O requisito de software somente é o SDK do Azure Machine Learning, que pode ser instalado usando o `pip` utilitário.

É recomendável o [Visual Studio Code](https://code.visualstudio.com/), pois fornece ferramentas para trabalhar com a linguagem Python e o Azure Machine Learning. Está disponível para plataformas Windows, macOS e Linux.

## <a name="data-science-virtual-machine"></a>Máquina Virtual de Ciência de Dados

A máquina Virtual de Ciência de Dados (DSVM) é uma combinação dos ambientes anteriores. É uma VM na plataforma do Azure que tem Jupyter Notebooks, Visual Studio Code e o SDK de aprendizado da Azure Machine Learning pré-instalada. Uma vez que o software necessário pré-instalado na imagem da VM, você pode começar a experimentar com o Azure Machine Learning rapidamente quando a VM tiver sido criada.

A DSVM permite que você selecione os recursos de computação, que você precisa, como memória, CPU e GPU. Também é instalada previamente com outros editores, como PyCharm, bem como software de aprendizado de máquina como TensorFlow, Keras e PyTorch. Se o software necessário não estiver instalado, você pode instalá-lo.

Para obter mais informações sobre o que é pré-instalado, consulte o documento [O que é Máquina Virtual de Ciência de Dados](../data-science-virtual-machine/overview.md).

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu sobre os ambientes de desenvolvimento, saiba [como configurar um ambiente de desenvolvimento](how-to-configure-environment.md).

