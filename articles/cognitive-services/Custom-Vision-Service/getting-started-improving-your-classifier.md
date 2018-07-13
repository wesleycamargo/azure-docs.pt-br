---
title: Melhorar o classificador usando o Serviço de Visão Personalizada – Serviços Cognitivos do Azure | Microsoft Docs
description: Saiba como melhorar a qualidade do seu classificador de Serviço de Visão Personalizada.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/03/2018
ms.author: anroth
ms.openlocfilehash: 65b1424f259066c7d5bd6b2b508d2a4052ff0527
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35363848"
---
# <a name="how-to-improve-your-classifier"></a>Como melhorar o seu classificador

Saiba como melhorar a qualidade do seu classificador de Serviço de Visão Personalizada. A qualidade do seu classificador dependem da qualidade dos dados rotulados fornecidos a ele. 

## <a name="train-more-varied-images"></a>Treinar imagens mais variadas

Fornecer imagens marcadas com diferentes ângulos, telas de fundo, o tamanho do objeto, grupos de fotos e outras variantes melhora o classificador. Fotos com contexto são melhores que fotos na frente de planos de fundo neutros. Inclua imagens que representam o que será enviado para o classificador durante o uso normal.

Para obter mais informações sobre como adicionar imagens, consulte o documento [Criar um classificador](getting-started-build-a-classifier.md).

> [!IMPORTANT]
> Lembre-se de treinar o classificador depois de adicionar imagens.

## <a name="use-images-submitted-for-prediction"></a>Usar imagens enviadas para previsão

O Serviço de Visão Personalizada armazena imagens enviadas ao ponto de extremidade de previsão. Para usar essas imagens para melhorar o classificador, use as seguintes etapas:

1. Para exibir imagens enviadas para o classificador, abra a [página da Web de Visão Personalizada](https://customvision.ai) e selecione a guia __Previsões__.

    ![Imagem da guia de previsões](./media/getting-started-improving-your-classifier/predictions-tab.png)

    > [!TIP]
    > A exibição padrão mostra imagens da iteração atual. Você pode usar o campo suspenso __Iteração__ para exibir as imagens enviadas durante as iterações anteriores.

2. Passe o mouse sobre uma imagem para ver as marcas que foram previstas pelo classificador.

    > [!TIP]
    > As imagens são classificadas, de forma que as imagens que possam trazer mais ganhos ao classificador fiquem na parte superior. Para selecionar uma classificação diferente, use a seção __Classificar__.

    Para adicionar uma imagem aos seus dados de treinamento, selecione a imagem, selecione a marca e, em seguida, selecione __Salvar e fechar__. A imagem será removida de __Previsões__ e adicionada às imagens de treinamento. Você pode exibi-la selecionando a guia __Imagens de Treinamento__.

    ![Imagem da página de marcação](./media/getting-started-improving-your-classifier/tag-image.png)

3. Use o botão __Treinar__ para treinar novamente o classificador.

## <a name="visually-inspect-predictions"></a>Inspecionar visualmente previsões

Para inspecionar previsões de imagem, selecione a guia __Treinar imagens__ guia e, em seguida, selecione __Histórico de iteração__. Imagens contornadas com uma caixa vermelha foram previstas incorretamente.

![Imagem do histórico de iteração](./media/getting-started-improving-your-classifier/iteration-history.png)

Algumas vezes a inspeção visual às vezes pode identificar padrões que você pode corrigir adicionando dados de treinamento adicional. Por exemplo, um classificador para rosas versus margaridas pode rotular incorretamente todas as rosas brancas como margaridas. Você poderá corrigir esse problema adicionando e fornecendo os dados de treinamento que contêm imagens marcadas de rosas brancas.

## <a name="unexpected-classification"></a>Classificação inesperada

Às vezes, o classificador aprende características que suas imagens têm em comum. Por exemplo, você deseja criar um classificador para rosas versus tulipas. Você fornece imagens de tulipas nos campos e de rosas em um vaso vermelho na frente de uma parede azul. Devido a esses dados, o classificador pode treinar para campo versus parede + vaso em vez de rosas versus tulipas.

Para corrigir esse problema, use as diretrizes sobre treinamento com imagens mais variadas: forneça imagens com diferentes ângulos, telas de fundo, tamanho de objeto, grupos e outras variantes.

## <a name="negative-image-handling"></a>Manipulação de imagem negativa

O Serviço de Visão Personalizada dá suporte a alguns tratamentos de imagem negativo automática. Se você estiver criando um classificador de gato versus cachorro e enviar uma imagem de um sapato para previsão, o classificador deve classificar essa imagem como próximo a 0% para gato e para cachorro. 

> [!WARNING]
> A abordagem automática funciona para imagens claramente negativas. Ela pode não funcionar bem em casos nos quais as imagens negativas são apenas uma variação das imagens usadas no treinamento. 
>
> Por exemplo, se você tiver um classificador de husky versus corgi e envia uma imagem de um Pomerânio, ele pode classificar o Pomerânio como um Husky. Se as suas imagens negativas são dessa natureza, crie uma nova marca (como “Outros”) e aplique-a nas imagens de treinamento negativas.

## <a name="next-steps"></a>Próximas etapas

[Use a API de previsão](use-prediction-api.md)