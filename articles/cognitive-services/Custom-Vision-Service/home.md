---
title: Visão geral do aprendizado de máquina do Serviço de Visão Personalizada - Serviços Cognitivos do Azure | Microsoft Docs
description: O Serviço de Visão Personalizada é um Serviço Cognitivo da Microsoft que permite que você crie classificadores de imagem personalizada na plataforma do Azure.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: overview
ms.date: 05/02/2018
ms.author: anroth
ms.openlocfilehash: de45fc399470a806fb7456cbbe936cecf659ee7c
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2018
ms.locfileid: "37087486"
---
# <a name="what-is-the-custom-vision-service"></a>O que é o Serviço de Visão Personalizada?

O Serviço de Visão Personalizada é um Serviço Cognitivo da Microsoft que permite que você crie classificadores de imagem personalizada. Ele torna fácil e rápida a compilação, implantação e melhora um classificador de imagem. O Serviço de Visão Personalizada fornece uma API REST e uma interface Web para carregar suas imagens e treinar o classificador.

## <a name="what-does-custom-vision-service-do-well"></a>O que o Serviço de Visão Personalizada faz bem?

O Serviço de Visão Personalizada funciona melhor quando o item que você está tentando classificar é destacado em sua imagem. 

Algumas imagens são necessárias para criar um classificador ou detector. 50 imagens por classe são suficientes para iniciar seu protótipo. Os usos dos métodos de Serviço de Visão Personalizada são robustos para diferenças, o que permite que você inicie a criação de protótipos com poucos dados. Isso significa que o Serviço de Visão Personalizada não é adequado para cenários em que você deseja detectar diferenças sutis. Por exemplo, pequenas rachaduras ou amassados em cenários de garantia de qualidade.

## <a name="release-notes"></a>Notas de versão

### <a name="may-7-2018"></a>7 de maio de 2018
- Introdução da versão prévia do recurso de Detecção de Objetos para projetos de Avaliação Limitada.
- Atualização para APIs 2.0
- Camada S0 expandida para até 250 marcas e 50.000 imagens. 
- Melhorias significativas de back-end para o pipeline de aprendizado de máquina para projetos de classificação de imagem. Projetos treinados depois de 27 de abril de 2018 irão se beneficiar dessas atualizações.
- Adicionada a exportação de modelo para ONNX, para uso com o Windows ML.
- Adicionada a exportação de modelo para o Dockerfile. Isso permite que você baixe os artefatos para criar seus próprios contêineres do Windows ou Linux, incluindo um DockerFile, o modelo do TensorFlow e o código de serviço. 
- Para modelos recentemente treinados exportados para TensorFlow nos domínios Geral (Compacto) e Ponto de referência (Compacto), os [Valores Médios agora são (0, 0,0)](https://github.com/azure-samples/cognitive-services-android-customvision-sample), para manter a consistência em todos os projetos. 

### <a name="march-1-2018"></a>1º de março de 2018
- Inclusão da versão prévia paga e integração no Portal do Azure. Os projetos agora podem ser anexados aos recursos do Azure com a camada F0 (Gratuita) ou S0 (Standard). Introdução dos projetos da camada S0, que permite até 100 marcas e 25.000 imagens. 
- O back-end é alterado para o parâmetro de normalização/pipeline de aprendizado de máquina. Isso dará aos clientes um melhor controle das compensações de precisão e recall ao ajustar o Limite de Probabilidade. Como parte dessas alterações, o Limite de Probabilidade padrão no portal do CustomVision.ai foi definido para ser 50%.

### <a name="december-19-2017"></a>19 de dezembro de 2017

- Adicionada a exportação para o Android (TensorFlow), além de uma exportação lançada anteriormente para iOS (CoreML). Isso permite a exportação de um modelo compacto treinado para ser executado offline em um aplicativo.
- Adicionados os domínios “compactos” Varejo e Ponto de referência para habilitar a exportação de modelo para esses domínios.
- Lançamento das versões [API de Treinamento 1.2](https://southcentralus.dev.cognitive.microsoft.com/docs/services/f2d62aa3b93843d79e948fe87fa89554/operations/5a3044ee08fa5e06b890f11f) e [API de Previsão 1.1](https://southcentralus.dev.cognitive.microsoft.com/docs/services/57982f59b5964e36841e22dfbfe78fc1/operations/5a3044f608fa5e06b890f164). As APIs atualizadas oferecem suporte à exportação de modelo, uma nova operação de Previsão que não salva as imagens em “Previsões” e introdução das operações em lote para a API de Treinamento.
- Ajustes de experiência do usuário, incluindo a capacidade de ver qual domínio foi usado para treinar uma iteração.
- Atualização do [SDK e exemplo de C#](https://github.com/Microsoft/Cognitive-CustomVision-Windows).

## <a name="next-steps"></a>Próximas etapas

[Saiba como criar um classificador](getting-started-build-a-classifier.md)
