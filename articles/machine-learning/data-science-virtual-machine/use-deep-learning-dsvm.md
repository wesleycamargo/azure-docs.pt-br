---
title: Ciência de dados com a máquina virtual de ciência de dados de Deep Learning no Azure | Microsoft Docs
description: Como executar várias tarefas comuns da ciência de dados com a VM de ciência de dados de Deep Learning.
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: 34ef0b10-9270-474f-8800-eecb183bbce4
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: 2053ed8cc420183d493097eeb2cd2ad93c82c70c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2018
---
# <a name="using-the-deep-learning-virtual-machine"></a>Usando a máquina virtual de Deep Learning

Após provisionar a DLVM (máquina virtual de Deep Learning), você poderá começar a construir modelos de rede neural profunda para compilar aplicativos de IA em domínios como pesquisa visual computacional e reconhecimento vocal. 

Há várias ferramentas fornecidas na VM de Deep Learning para AI. A [página de estruturas de Deep Learning e IA](dsvm-deep-learning-ai-frameworks.md) contém detalhes sobre como usar essas ferramentas. 

## <a name="deep-learning-tutorials-and-walkthroughs"></a>Tutoriais e passo a passo sobre Deep Learning

Além dos exemplos baseados em framework, também é fornecido um conjunto abrangente de orientações passo a passo que foram validadas no DLVM. Essas orientações passo a passo ajudam a iniciar o desenvolvimento de aplicativos de aprendizagem profunda em domínios como reconhecimento vocal/texto e imagem. Mais tutoriais de ponta a ponta em diferentes domínios e tecnologias continuarão a ser adicionados.   


- [Executando redes neurais em estruturas diferentes](https://github.com/ilkarman/DeepLearningFrameworks): um passo a passo completo que mostra como migrar o código de uma estrutura para outra. Ele também demonstra como comparar o desempenho do modelo e do tempo de execução entre estruturas. 

- [Um guia de instruções para criar uma solução de ponta a ponta para detectar produtos em imagens](https://github.com/Azure/cortana-intelligence-product-detection-from-images): a detecção de imagem é uma técnica que pode localizar e classificar objetos em imagens. Essa tecnologia tem o potencial de trazer grande recompensa em vários domínios de negócios da vida real. Por exemplo, os varejistas podem usar essa técnica para determinar qual produto um cliente retirou da prateleira. Por sua vez, essas informações ajudam as lojas a gerenciar o estoque do produtos. 

- [Extração de entidade nomeada de resumos de PubMed](https://docs.microsoft.com/azure/machine-learning/preview/scenario-tdsp-biomedical-recognition) Este tutorial mostra como extrair entidades nomeadas, como nomes de droga ou doença, de texto não estruturado. Ele treina uma palavra personalizada inserindo em um corpo de texto de 18 milhões resumos de PubMed, usa esse modelo para criar um modelo de rede neural recorrente de memória de longo curto prazo (LSTM) para extração de entidade e mostra que o modelo que insere a palavra específica de domínio pode ter um desempenho melhor que inserir uma palavra genérica para extração de entidade.

- [Deep learning para áudio](https://blogs.technet.microsoft.com/machinelearning/2018/01/30/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure/) Este tutorial mostra como treinar um modelo de deep learning para detecção de eventos de áudio no [conjunto de dados de sons urbanos](https://serv.cusp.nyu.edu/projects/urbansounddataset/urbansound8k.html) e fornece uma visão geral de como trabalhar com dados de áudio.

- [Classificação de documentos de texto](https://github.com/anargyri/lstm_han): esse passo a passo demonstra como compilar e treinar duas arquiteturas de redes neurais diferentes: Rede de Atenção Hierárquica e rede LSTM (Memória Longa de Curto Prazo). Essas redes neurais usam a API Keras para aprendizagem profunda para classificar documentos de texto. Keras é um front-end para três dentre as estruturas mais populares de aprendizagem profunda: Microsoft Cognitive Toolkit, TensorFlow e Theano.

## <a name="next-steps"></a>Próximas etapas

A [página de exemplos](dsvm-samples-and-walkthroughs.md) fornece indicadores para os exemplos de código previamente carregados na VM para cada uma das estruturas, a fim de ajudá-lo a começar rapidamente. 
