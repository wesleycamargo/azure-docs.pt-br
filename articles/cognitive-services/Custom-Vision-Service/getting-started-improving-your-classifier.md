---
title: Melhorar o classificador - Serviço de Visão Personalizada
titlesuffix: Azure Cognitive Services
description: Saiba como melhorar a qualidade do classificador.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: conceptual
ms.date: 07/05/2018
ms.author: pafarley
ms.openlocfilehash: 2bee7f0af98bf03a13e376dea9dbf083b3f61815
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49340283"
---
# <a name="how-to-improve-your-classifier"></a>Como melhorar o seu classificador

Saiba como melhorar a qualidade do seu classificador de Serviço de Visão Personalizada. A qualidade do classificador depende da quantidade, qualidade e variedade dos dados rotulados fornecidos a ele, assim como do balanceamento do conjunto de dados. Um bom classificador normalmente tem um conjunto de dados de treinamento balanceado que é representativo do que será submetido ao classificador. O processo de compilar um classificador desse tipo geralmente é iterativo. É comum fazer algumas rodadas de treinamento para alcançar os resultados esperados.

Aqui, estão as etapas comuns tomadas para melhorar um classificador. Essas etapas não são regras prontas e rígidas, mas heurísticas que ajudarão a compilar um classificador melhor.

1. Treinamento de primeira rodada
1. Adicionar mais imagens e balancear dados
1. Treinar novamente
1. Adicionar imagens com variação de tela de fundo, iluminação, tamanho do objeto, ângulo da câmera e estilo
1. Treinar novamente e feed em imagem para previsão
1. Examinar os resultados da previsão
1. Modificar dados de treinamento existentes

## <a name="data-quantity-and-data-balance"></a>Quantidade de dados e balanceamento de dados

O mais importante é carregar imagens suficientes para treinar o classificador. É recomendável pelo menos 50 imagens por rótulo para o conjunto de treinamento como um ponto de partida. Com menos imagens, haverá um forte risco de estar sobreajustando. Embora os números de desempenho possam sugerir boa qualidade, pode haver conflitos em relação aos dados do mundo real. Treinar o classificador com mais imagens, geralmente aumentará a precisão dos resultados de previsão.

Outro aspecto ser considerado é certificar-se de que os dados estão balanceados. Por exemplo, ter 500 imagens para um rótulo e 50 imagens para outro rótulo produzirá um conjunto de dados de treinamento desbalanceado, fazendo com que o modelo seja mais preciso na previsão de um rótulo do que outro. É provável que você veja melhores resultados se mantiver uma proporção de pelo menos 1: 2 entre o rótulo com o menor número de imagens e o rótulo com o maior número de imagens. Por exemplo, se o rótulo com o maior número de imagens tiver 500 imagens, o rótulo com o mínimo de imagens precisará ter pelo menos 250 imagens para treinamento.

## <a name="train-more-diverse-images"></a>Treinar imagens mais diversificadas

Forneça imagens representativas do que será enviado ao classificador durante o uso normal. Por exemplo, se estiver treinando um classificador “apple”, o classificador poderá não ser tão preciso se você treinar apenas fotos de maçãs em pratos, mas fazer previsões sobre fotos de maçãs em árvores. Incluir uma variedade de imagens garante que o classificador não seja tendencioso e possa generalizar. Abaixo, estão algumas maneiras de tornar o treinamento mais diversificado:

__Tela de fundo:__ forneça imagens do objeto na frente de diferentes telas de fundo (ou seja, fruta no prato em comparação com fruta na sacola). Foto em contextos são melhores que fotos na frente de telas de fundo neutras, pois fornecem mais informações ao classificador.

![Imagem de exemplos de tela de fundo](./media/getting-started-improving-your-classifier/background.png)

__Iluminação:__ forneça imagens com iluminação variada (ou seja, tomadas com flash, alta exposição, etc.), especialmente se as imagens usadas para previsão tiverem iluminação diferente. Também é útil incluir imagens com saturação, matiz e brilho variados.

![Imagem de exemplos de iluminação](./media/getting-started-improving-your-classifier/lighting.png)

__Tamanho do objeto:__ forneça imagens nas quais os objetos são de tamanhos variados, capturando diferentes partes do objeto. Por exemplo, uma foto de cachos de bananas e um close de uma única banana. O dimensionamento diferente ajuda o classificador a generalizar melhor.

![Imagem de exemplos de tamanho](./media/getting-started-improving-your-classifier/size.png)

__Ângulo da câmera:__ forneça imagens tiradas com diferentes ângulos de câmera. Se todas as fotos forem tiradas com um conjunto de câmeras fixas (como câmeras de vigilância), certifique-se de atribuir um rótulo diferente a cada câmera, mesmo que capturem os mesmos objetos para evitar sobreajuste - modelando objetos não relacionados (como postes de luz) como característica chave.

![Imagem de amostras de ângulo](./media/getting-started-improving-your-classifier/angle.png)

__Estilo:__ forneça imagens de diferentes estilos da mesma classe (ou seja, diferentes tipos de cítricos). No entanto, se houver imagens de objetos de estilos drasticamente diferentes (ou seja, Mickey Mouse em comparação com um rato da vida real), é recomendável rotulá-las como classes separadas para melhor representar os recursos distintos.

![Imagem de exemplos de estilo](./media/getting-started-improving-your-classifier/style.png)

## <a name="use-images-submitted-for-prediction"></a>Usar imagens enviadas para previsão

O Serviço de Visão Personalizada armazena imagens enviadas ao ponto de extremidade de previsão. Para usar essas imagens para melhorar o classificador, use as seguintes etapas:

1. Para exibir as imagens enviadas ao classificador, abra a [página da Web Visão Personalizada](https://customvision.ai), acesse o projeto e selecione a guia __Previsões__. A exibição padrão mostra imagens da iteração atual. Você pode usar o campo suspenso __Iteração__ para exibir as imagens enviadas durante as iterações anteriores.

    ![Imagem da guia de previsões](./media/getting-started-improving-your-classifier/predictions.png)

2. Passe o mouse sobre uma imagem para ver as marcas que foram previstas pelo classificador. As imagens são classificadas de forma que as imagens que possam trazer mais ganhos ao classificador fiquem na parte superior. Para selecionar uma classificação diferente, use a seção __Classificar__. Para adicionar uma imagem aos dados de treinamento existentes, selecione a imagem, selecione a marca correta e clique em __Salvar e fechar__. A imagem será removida de __Previsões__ e adicionada às imagens de treinamento. Você pode exibi-la selecionando a guia __Imagens de Treinamento__.

    ![Imagem da página de marcação](./media/getting-started-improving-your-classifier/tag.png)

3. Use o botão __Treinar__ para treinar novamente o classificador.

## <a name="visually-inspect-predictions"></a>Inspecionar visualmente previsões

Para inspecionar previsões de imagem, selecione a guia __Treinar imagens__ guia e, em seguida, selecione __Histórico de iteração__. Imagens contornadas com uma caixa vermelha foram previstas incorretamente.

![Imagem do histórico de iteração](./media/getting-started-improving-your-classifier/iteration.png)

Às vezes, a inspeção visual pode identificar padrões que você pode corrigir adicionando dados de treinamento adicionais ou modificando dados de treinamento existentes. Por exemplo, um classificador para maçã em comparação com limão pode rotular incorretamente todas as maçãs verdes como limões. É possível corrigir esse problema, adicionando e fornecendo dados de treinamento que contenham imagens marcadas de maçãs verdes.

## <a name="unexpected-classification"></a>Classificação inesperada

Às vezes, o classificador aprende incorretamente as características que as imagens tenham em comum. Por exemplo, se estiver criando um classificador para maçãs em comparação com cítricos e forem fornecidas imagens de maçãs nas mãos e de frutas cítricas em pratos brancos, o classificador poderá treinar para mãos em comparação com pratos brancos, em vez de maçãs em comparação com cítricos.

![Imagem de classificação inesperada](./media/getting-started-improving-your-classifier/unexpected.png)

Para corrigir esse problema, use as diretrizes acima sobre treinamento com imagens mais variadas: forneça imagens com diferentes ângulos, telas de fundo, tamanho do objeto, grupos e outras variantes.

## <a name="negative-image-handling"></a>Manipulação de imagem negativa

O Serviço de Visão Personalizada dá suporte a alguns tratamentos de imagem negativo automática. No caso em que você está compilando um classificador de uva em comparação com banana e envia uma imagem de um sapato para previsão, o classificador deve classificar essa imagem em cerca de 0 % tanto para uva como para banana.

Por outro lado, nos casos em que as imagens negativas são apenas uma variação das imagens utilizadas no treinamento, é provável que o modelo classifique as imagens negativas como uma classe rotulada devido às grandes semelhanças. Por exemplo, se você tiver um classificador de laranja em comparação com toranja, e feed em uma imagem de clementina, o classificador poderá classificar a clementina como laranja. Isso pode ocorrer uma vez que muitas características da clementina (cor, forma, textura, habitat natural, etc.) se assemelham às das laranjas.  Se as imagens negativas forem dessa natureza, é recomendável criar uma ou mais marcas separadas ("Outro") e rotular imagens negativas com essa marca durante o treinamento para permitir que o modelo diferencie melhor essas classes.

## <a name="next-steps"></a>Próximas etapas

Saiba como você pode testar imagens programaticamente enviando-as à API de Previsão.

> [!div class="nextstepaction"]
[Use a API de previsão](use-prediction-api.md)
