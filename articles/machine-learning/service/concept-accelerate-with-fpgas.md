---
title: O que é uma FPGA? – Project Brainwave – Azure Machine Learning
description: Saiba como acelerar modelos e redes neurais avançadas com FPGAs.
services: machine-learning
ms.service: machine-learning
ms.component: service
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 05/31/2018
ms.openlocfilehash: 5ed94c3b750c927ec48959c12388bd22de3d3df4
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35261943"
---
# <a name="what-is-fpga-and-project-brainwave"></a>O que é FPGA e Project Brainwave?

Este artigo fornece uma introdução à FPGA (matriz de portões programáveis em campo) e à maneira como o FPGA integra-se ao aprendizado de máquina do Azure para fornecer IA em tempo real.

## <a name="fpgas-vs-cpu-gpu-and-asic"></a>FPGAs vs. CPU, GPU e ASIC

As FPGAs contêm uma matriz de blocos lógicos programáveis e uma hierarquia de interconexões reconfiguráveis que permitem que os blocos sejam configurados de maneiras diferentes após a fabricação.

As FPGAs fornecem uma combinação de possibilidade de programação e de desempenho em comparação com outros chips:

![Comparação da FPGA do Azure Machine Learning](./media/concept-accelerate-with-fpgas/azure-machine-learning-fpga-comparison.png)

- **CPUs (unidades de processamento central)** são processadores de uso geral. O desempenho da CPU não é ideal para processamento de gráficos e vídeos.
- **GPUs (unidades de processamento de gráficos)** oferecem processamento paralelo e são uma opção popular para cálculos de IA. O processamento paralelo com GPUs resulta em uma renderização mais rápida de imagens do que CPUs.
- **ASIC (Circuitos integrados específicos para aplicativos)**, como Unidades de Processador TensorFlow do Google, são circuitos personalizados. Embora esses chips ofereçam a mais alta eficiência, os ASICs são inflexíveis.
- **FPGAs**, como os disponíveis no Azure, fornecem desempenho próximo ao do ASIC, mas oferecem a flexibilidade para serem reconfigurados mais tarde.

Agora as FPGAs estão em todo servidor novo do Azure. A Microsoft já está usando FPGAs para classificação de pesquisa do Bing, avaliação de DNN (rede neural profunda) e aceleração SDN (rede definida por software). Essas implementações de FPGA reduzem a latência durante a liberação de CPUs para outras tarefas.

## <a name="project-brainwave-on-azure"></a>Project Brainwave no Azure

O Project Brainwave é uma arquitetura de hardware criada com base em dispositivos FPGA da Intel e usada para acelerar os cálculos de IA em tempo real. Com essa arquitetura econômica habilitada para FPGA, uma rede neural treinada pode ser executada o mais rápido possível e com latência inferior. O Projeto Brainwave pode paralelizar DNNs previamente treinados entre as FPGAs para escalar horizontalmente seu serviço.

- Desempenho

    As FPGAs possibilitam alcançar baixa latência para solicitações de inferência em tempo real. O envio em lote significa dividir uma solicitação em partes menores e alimentá-las para que um processador melhore a utilização do hardware. O envio em lote não é eficiente e pode causar latência. O Brainwave não requer envio em lote; portanto, a latência é dez vezes menor comparada com CPU e GPU.

- Flexibilidade

    As FPGAs podem ser reconfiguradas para diferentes tipos de modelos de aprendizado de máquina. Essa flexibilidade torna mais fácil acelerar os aplicativos baseados na precisão numérica mais ideal e o modelo de memória que está sendo usado.

    Novas técnicas de aprendizado de máquina estão sendo desenvolvidas regularmente, e o design de hardware do Project Brainwave também está evoluindo rapidamente. Como as FPGAs podem ser reconfiguradas, é possível ficar atualizado com os requisitos dos algoritmos de IA que mudam rapidamente.

- Escala

    O Microsoft Azure é o maior investimento de nuvem do mundo em FPGAs. É possível executar o Brainwave na infraestrutura de escala do Azure.

Uma versão prévia do Project Brainwave integrada ao Azure Machine Learning está disponível no momento. E uma versão prévia também está disponível para aprimorar o Project Brainwave para que você possa aproveitar essa velocidade de computação em suas próprias empresas e instalações.

Na versão prévia atual, o Brainwave está limitado à implantação do TensorFlow e às redes neurais baseadas em ResNet50 no hardware Intel FPGA para classificação e reconhecimento de imagem. Há planos para dar suporte a mais modelos de galeria e a outras estruturas.

Os cenários a seguir usam a FPGA na arquitetura do Project Brainwave:

- Sistema automatizado de inspeção ótica. Consulte [Real-time AI: Microsoft announces preview of Project Brainwave](https://blogs.microsoft.com/ai/build-2018-project-brainwave/) (IA em tempo real: a Microsoft anuncia a versão prévia do Project Brainwave).
- Mapeamento de cobertura de terra. Consulte [How to Use FPGAs for Deep Learning Inference to Perform Land Cover Mapping on Terabytes of Aerial Images](https://blogs.technet.microsoft.com/machinelearning/2018/05/29/how-to-use-fpgas-for-deep-learning-inference-to-perform-land-cover-mapping-on-terabytes-of-aerial-images/) (Como usar FPGAs para inferência de aprendizado profundo para realizar o mapeamento de cobertura de terra em terabytes de imagens aéreas).

## <a name="how-to-deploy-a-web-service-to-an-fpga"></a>Como implantar um serviço Web em uma FPGA?

O fluxo de alto nível para criar um serviço de reconhecimento de imagem no Azure usando o ResNet50 como um transformador de recurso é o seguinte:

1. O ResNet50 já está implantado no Brainwave. É possível criar outros grafos (entrada de data, classificação e assim por diante) com TensorFlow e definir um pipeline (entrada -> transformador de recurso -> classificar) usando um arquivo json de definição de serviços. Compacte a definição e os grafos em um arquivo zip e carregue o arquivo zip para o Azure Blob Storage.
2. Registre o modelo usando a API de Gerenciamento de Modelos do ML do Azure com o arquivo zip no Armazenamento de blobs.
3. Implante o serviço com o modelo registrado usando a API de Gerenciamento de Modelos do ML do Azure.

Saiba mais sobre esse processo no artigo, [Implantar um modelo como um serviço Web em uma FPGA com o Azure Machine Learning](how-to-deploy-fpga-web-service.md).


## <a name="start-using-fpga"></a>Iniciar usando a FPGA

Os Modelos de Aceleração de Hardware do Azure Machine Learning permitem que você implante modelos DNN treinados em FPGAs na nuvem do Azure. Para começar. confira:

- [Implantar um modelo como um serviço Web em uma FPGA](how-to-deploy-fpga-web-service.md)
- [Modelos de Aceleração de Hardware do Microsoft Azure Machine Learning desenvolvidos pelo Project Brainwave](https://github.com/azure/aml-real-time-ai).

## <a name="next-steps"></a>Próximas etapas

[Implantar um modelo como um serviço Web em uma FPGA](how-to-deploy-fpga-web-service.md)

[Saiba como instalar o SDK da FPGA](reference-fpga-package-overview.md)

[Hardware de hiperescala: ML em escala em cima do Azure + FPGA: build 2018 (vídeo)](https://www.youtube.com/watch?v=BMgQAHIx2eY)

[Por dentro da nuvem configurável baseada em FPGA da Microsoft (vídeo)](https://channel9.msdn.com/Events/Build/2017/B8063)

[Microsoft lança o Project Brainwave para IA em tempo real](https://www.microsoft.com/research/blog/microsoft-unveils-project-brainwave/)