---
title: Azure Machine Learning e FPGAs
description: Saiba como acelerar modelos e redes neurais avançadas com FPGAs.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: tedway
author: tedway
ms.date: 05/07/2018
ms.openlocfilehash: 493b3f8de617146af534349e18ed49b83c46dee8
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
---
# <a name="what-is-a-field-programmable-gate-array-fpga"></a>O que é FPGA (matriz de portas programáveis em campo)?

FPGAs (matrizes de porta programável em campo) são circuitos integrados que podem ser reconfigurados conforme o necessário. Você pode alterar um FPGA conforme o necessário para implementar uma lógica nova. Os Modelos Acelerados de Hardware do Azure Machine Learning permitem que você implante modelos treinados no FPGAs na nuvem do Azure.

Essa funcionalidade é capacitada pelo Projeto Brainwave, que manipula a conversão de DNN (redes neurais avançadas) em um programa FPGA. 

## <a name="why-use-an-fpga"></a>Por que usar um FPGA?

FPGAs fornecem latência extremamente baixa e são particularmente eficazes para classificar dados em lote de tamanho um (não há necessidade de lotes grandes).  Elas são econômicas e disponíveis no Azure.  O Projeto Brainwave pode paralelizar DNNs previamente treinados entre essas FPGAs para escalar horizontalmente seu serviço.

## <a name="next-steps"></a>Próximas etapas

[Implantar um modelo como um serviço Web em uma FPGA](how-to-deploy-fpga-web-service.md)

[Saiba como instalar o SDK da FPGA](reference-fpga-package-overview.md)