---
title: O que é o FPGA e o Projeto Brainwave? – Serviço do Azure Machine Learning
description: Saiba como acelerar modelos e redes neurais profundas com FPGAs no Azure. Este artigo fornece uma introdução ao FPGA (matrizes de portas programáveis em campo) e como o Serviço do Azure Machine Learning fornece IA (inteligência artificial) em tempo real quando você implanta seu modelo em um FPGA do Azure.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: tedway
author: tedway
ms.reviewer: jmartens
ms.date: 9/24/2018
ms.openlocfilehash: 411beacd65915c30338ab415b095acc1a0c8cbe6
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/03/2018
ms.locfileid: "48238848"
---
# <a name="what-is-fpga-and-project-brainwave"></a>O que é FPGA e Project Brainwave?

Este artigo fornece uma introdução ao FPGA (matrizes de portas programáveis em campo) e como o serviço do Azure Machine Learning fornece IA (inteligência artificial) em tempo real quando você implanta seu modelo em um FPGA do Azure.

OS FPGAs contêm uma matriz de blocos lógicos programáveis e uma hierarquia de interconexões reconfiguráveis. As interconexões permitem que esses blocos sejam configurados de várias maneiras na pós-fabricação. Os FPGAs fornecem uma combinação de programação e desempenho comparado a outros chips.

## <a name="fpgas-vs-cpu-gpu-and-asic"></a>FPGAs vs. CPU, GPU e ASIC

![Comparação de FPGA do serviço do Azure Machine Learning](./media/concept-accelerate-with-fpgas/azure-machine-learning-fpga-comparison.png)

|Processador||DESCRIÇÃO|
|---|:-------:|------|
|Circuitos integrados específicos do aplicativo|ASICs|Circuitos personalizados, como as Unidades de Processador (TPU) do TensorFlow da Google, fornecem a maior eficiência. Eles não podem ser reconfigurados conforme suas necessidades mudam.|
|Matrizes de portas programáveis em campo|FPGAs|Os FPGAs, como aqueles disponíveis no Azure, fornecem desempenho semelhante ao oferecido pelos ASICs, mas são flexíveis e reconfiguráveis ao longo do tempo para implementar uma nova lógica.|
|Unidades de processamento gráfico|GPUs|Uma opção popular para cálculos de IA que oferece funcionalidades de processamento paralelo, tornando-os mais rápidos na renderização de imagens comparado às CPUs.|
|Unidades de processamento central|CPUs|Processadores de uso geral cujo desempenho não é ideal para processamento de gráfico e vídeo.|

## <a name="project-brainwave-on-azure"></a>Project Brainwave no Azure

O [Projeto Brainwave](https://www.microsoft.com/research/project/project-brainwave/) é a arquitetura de hardware econômica da Microsoft, baseada em dispositivos FPGA da Intel, usada pelos cientistas de dados e desenvolvedores para acelerar os cálculos de IA em tempo real.  Essa arquitetura habilitada para FPGA oferece **desempenho**, **flexibilidade** e **escala** e está disponível no Azure.

**Os FPGAs possibilitam alcançar baixa latência para solicitações de inferência em tempo real.** O envio em lote significa a coleta de uma grande quantidade de dados e seu feed em um processador para melhoria da utilização de hardware. O envio em lote pode causar latência porque mais dados precisam ser processados, mas pode melhorar a taxa de transferência. As implementações do Projeto Brainwave de unidades de processamento neural não exigem o envio em lote; portanto, a latência pode ser muitas vezes menor comparado à CPU e à GPU.

### <a name="reconfigurable-power"></a>Potência reconfigurável
**Os FPGAs podem ser reconfigurados para diferentes tipos de modelos de machine learning.** Essa flexibilidade torna mais fácil acelerar os aplicativos baseados na precisão numérica mais ideal e o modelo de memória que está sendo usado.

Novas técnicas de aprendizado de máquina estão sendo desenvolvidas regularmente, e o design de hardware do Project Brainwave também está evoluindo rapidamente. Como as FPGAs podem ser reconfiguradas, é possível ficar atualizado com os requisitos dos algoritmos de IA que mudam rapidamente.

### <a name="whats-supported-on-azure"></a>O que tem suporte no Azure
**O Microsoft Azure é o maior investimento de nuvem do mundo em FPGAs.** Você pode executar o Projeto Brainwave na infraestrutura de escala do Azure.

Hoje, o Projeto Brainwave dá suporte a:
+ Cenários de classificação e reconhecimento de imagens
+ Implantação do TensorFlow
+ DNNs: ResNet 50, ResNet 152, VGG-16, SSD-VGG e DenseNet-121
+ Hardware de FPGA da Intel 

Usando essa arquitetura de hardware habilitado para FPGA, as redes neurais treinadas são executadas rapidamente e com uma latência mais baixa. O Projeto Brainwave pode paralelizar DNNs (redes neurais profundas) pré-treinadas entre os FPGAs para expandir o serviço. As DNNs podem ser pré-treinadas, como um transformador de recursos profundo para transferência de aprendizado, ou ajustadas com pesos atualizados.

### <a name="scenarios-and-applications"></a>Cenários e aplicativos

O Projeto Brainwave é integrado ao Azure Machine Learning. A Microsoft usa FPGAs para avaliação de DNN, classificação da Pesquisa do Bing e aceleração de SDN (rede definida por software) para reduzir a latência durante a liberação de CPUs para outras tarefas.

Os cenários a seguir usam a FPGA na arquitetura do Project Brainwave:
+ [Sistema automatizado de inspeção ótica](https://blogs.microsoft.com/ai/build-2018-project-brainwave/)

+ [Mapeamento de cobertura de terra](https://blogs.technet.microsoft.com/machinelearning/2018/05/29/how-to-use-fpgas-for-deep-learning-inference-to-perform-land-cover-mapping-on-terabytes-of-aerial-images/)

## <a name="deploy-to-fpgas-on-azure"></a>Realizar a implantação em FPGAs no Azure

Este é o fluxo de trabalho para criação de um serviço de reconhecimento de imagens no Azure usando DNNs compatíveis como um transformador de recursos para implantação em FPGAs do Azure:

1. Use o SDK do Azure Machine Learning para Python para criar uma definição de serviço, que é um arquivo que descreve um pipeline de grafos (entrada, transformador de recursos e classificador) baseado no TensorFlow. O comando de implantação compactará automaticamente a definição e os grafos em um arquivo zip e fará upload o zip no Armazenamento de Blobs do Azure.  A DNN já está implantada no Projeto Brainwave para ser executada no FPGA.

1. Registre o modelo usando o SDK com o arquivo zip no Armazenamento de Blobs do Azure.

1. Implante o serviço com o modelo registrado usando o SDK.

Comece implantando os modelos DNN treinados nos FPGAs na nuvem do Azure com este artigo **"[Implantar um modelo como um serviço Web em um FPGA](how-to-deploy-fpga-web-service.md)"**.


## <a name="next-steps"></a>Próximas etapas

Confira estes vídeos e blogs:

+ [Hardware de hiperescala: ML em escala em cima do Azure + FPGA: build 2018 (vídeo)](https://www.youtube.com/watch?v=BMgQAHIx2eY)

+ [Por dentro da nuvem configurável baseada em FPGA da Microsoft (vídeo)](https://channel9.msdn.com/Events/Build/2017/B8063)

+ [Projeto Brainwave para IA em tempo real: home page do projeto](https://www.microsoft.com/research/project/project-brainwave/)
