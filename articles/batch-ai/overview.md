---
title: Serviço de IA do Lote do Azure – treinamento de IA | Microsoft Docs
description: Aprenda a usar o serviço IA do Lote do Azure gerenciado para treinar IA (inteligência artificial) e outros modelos de aprendizado de máquina em clusters de GPUs e CPUs.
services: batch-ai
documentationcenter: ''
author: alexsuttonms
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch-ai
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 08/01/2018
ms.author: danlep
ms.openlocfilehash: 98497812e75d07fc153e0e351331c05484164fdd
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44052692"
---
# <a name="what-is-azure-batch-ai"></a>O que é a IA do Lote do Azure?

A IA do Lote do Azure é um serviço gerenciado para ajudar cientistas de dados e pesquisadores de IA a treinar e testar o aprendizado de máquina e modelos de inteligência artificial em escala no Azure – sem precisar gerenciar uma infraestrutura complexa. Descreva os recursos de computação, os trabalhos que você deseja executar e onde armazenar as entradas e saídas do modelo, e a IA do Lote cuidará do resto.

Você pode usar a IA do Lote tanto de forma autônoma quanto para executar o treinamento do modelo como parte de um fluxo de trabalho de desenvolvimento maior:

* Use a IA do Lote sozinha para treinar, testar e pontuar em lote modelos de aprendizado de máquina e de IA em clusters de [GPUs](../virtual-machines/linux/sizes-gpu.md) ou CPUs. 

* Vise um cluster da IA do Lote em um fluxo de trabalho com o [Azure Machine Learning](../machine-learning/service/overview-what-is-azure-ml.md) ou outras [ferramentas da Plataforma de IA do Azure](https://azure.microsoft.com/overview/ai-platform/). O Azure ML oferece uma rica experiência de preparação de dados, experimentação e histórico de trabalhos. O Azure ML também pode empacotar um modelo treinado em um contêiner e implantar um modelo para inferência ou em dispositivos IoT.  

## <a name="train-machine-learning-and-ai-models"></a>Treinar modelos de IA e de aprendizado de máquina

Use a IA do Lote para treinar modelos de aprendizado de máquina, além de redes neurais profundas (aprendizado profundo) e outras abordagens de inteligência artificial. A IA do Lote tem suporte interno para bibliotecas populares de IA de software livre, incluindo [TensorFlow](https://github.com/tensorflow/tensorflow), [PyTorch](https://github.com/pytorch/pytorch), [Chainer](https://github.com/chainer/chainer) e [CNTK (Microsoft Cognitive Toolkit)](https://github.com/Microsoft/CNTK).

Depois de identificar sua área problemática e preparar os dados, trabalhe interativamente com a IA do Lote para testar ideias de modelo. Em seguida, quando você estiver pronto para fazer experiências em grande escala, inicie trabalhos em várias GPUs com MPI ou outras bibliotecas de comunicação e execute mais experimentos em paralelo.

A IA do Lote ajuda você a treinar modelos em grande escala de várias maneiras. Por exemplo:  

|  |  |
|---------|---------|
| **Distribuir o treinamento**<br/>![Treinamento distribuído](./media/overview/distributed-training.png)  | Escale o treinamento de um único trabalho verticalmente entre várias GPUs conectadas à rede para treinar redes maiores com grandes volumes de dados.|
| **Experimento**<br/>![Experimentação](./media/overview/experimentation.png) | Escale horizontalmente o treinamento com vários trabalhos. Execute limpezas de parâmetros para testar novas ideias ou ajuste os hiperparâmetros para otimizar o desempenho e a precisão. |
| **Executar em paralelo**![Execução paralela](./media/overview/parallel-execution.png) | Treine ou pontue vários modelos de cada vez, executados em paralelo em uma frota de servidores para que o trabalho seja feito com mais rapidez.|

Quando terminar de treinar um modelo, use a IA do Lote para testá-lo, se isso não fez parte do seu script de treinamento, ou execute a pontuação em lote.

## <a name="how-it-works"></a>Como ele funciona

Use os SDKs da IA do Lote, os scripts de linha de comando ou o portal do Azure para gerenciar recursos de computação e agendar trabalhos de treinamento e teste de IA: 

* **Provisionar e dimensionar clusters de VMs** – escolha o número de nós (VMs) e selecione uma VM habilitada para GPU ou outro tamanho de VM que atenda às necessidades de treinamento. Aumente ou reduza o número de nós automaticamente ou manualmente para que você use recursos somente quando necessário. 

* **Gerenciar dependências e contêineres** – por padrão, os clusters da IA do Lote executam imagens de VM do Linux que têm dependências previamente instaladas para executar estruturas de treinamento baseadas em contêiner em GPUs ou CPUs. Para uma configuração adicional, use imagens personalizadas ou execute scripts de inicialização.

* **Distribuir dados** – escolha uma ou várias opções de armazenamento para gerenciar scripts e dados de entrada e saídas de trabalho: Arquivos do Azure, Armazenamento de Blobs do Azure ou um servidor NFS gerenciado. A IA do Lote também dá suporte a soluções de armazenamento personalizado, incluindo sistemas de arquivos paralelos de alto desempenho. Monte sistemas de arquivos de armazenamento nos nós de cluster e nos contêineres de trabalho usando arquivos de configuração simples.

* **Agendar trabalhos** – envie trabalhos para uma fila de trabalhos baseada em prioridades, a fim de compartilhar recursos de cluster e tirar proveito das VMs de baixa prioridade e das instâncias reservadas.

* **Lidar com falhas** – monitore o status do trabalho e reinicie trabalhos em caso de falhas de VM durante trabalhos potencialmente longos.

* **Coletar resultados** – acesse facilmente logs de saída, Stdout, Stderr e modelos treinados. Configure os trabalhos da IA do Lote para efetuar push da saída diretamente para o armazenamento montado.

Como um serviço do Azure, a IA do Lote dá suporte a ferramentas comuns, como o RBAC (controle de acesso baseado em função) e as redes virtuais do Azure, para oferecer maior segurança.  

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre os [recursos da IA do Lote](resource-concepts.md) para treinar um modelo de aprendizado de máquina ou de IA.

* Comece a [treinar um modelo de aprendizado profundo de exemplo](quickstart-tensorflow-training-cli.md) com a IA do Lote.

* Confira as [receitas de treinamento](https://github.com/Azure/BatchAI/blob/master/recipes) de exemplo para estruturas populares de IA.
