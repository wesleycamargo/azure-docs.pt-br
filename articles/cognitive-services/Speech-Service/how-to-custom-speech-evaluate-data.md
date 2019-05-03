---
title: Avaliar a precisão para conversão de fala personalizado – serviços de fala
titlesuffix: Azure Cognitive Services
description: Este documento, você aprenderá a quantitativa medem a qualidade do modelo de fala em texto da Microsoft ou seu modelo personalizado. Dados de transcrição de áudio + rotulada como humanos são necessários para testar a precisão e 30 minutos para 5 horas de representante de áudio deve ser fornecidos.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 7c1b3602b09c1a129923180c4b1d4a5f8293de2c
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025904"
---
# <a name="evaluate-custom-speech-accuracy"></a>Avaliar a precisão de fala personalizado

Neste documento, você aprenderá como quantitativa medem a qualidade do modelo de fala em texto da Microsoft ou seu modelo personalizado. Dados de transcrição de áudio + rotulada como humanos são necessários para testar a precisão e 30 minutos para 5 horas de representante de áudio deve ser fornecidos.

## <a name="what-is-word-error-rate-wer"></a>O que é a taxa de erros do Word (WER)?

É o padrão para medir a precisão do modelo do setor *taxa de erros do Word* (WER). WER conta o número de palavras incorretas identificadas durante o reconhecimento, em seguida, divide pelo número total de palavras fornecida na transcrição rotulada como humanos. Por fim, esse número é multiplicado por 100% para calcular o WER.

![Fórmula do WER](./media/custom-speech/custom-speech-wer-formula.png)

Identificado incorretamente palavras enquadram-se em três categorias:

* Inserção (I): Palavras que são adicionadas incorretamente na transcrição de hipótese
* Exclusão (D): Palavras que são detectadas na transcrição de hipótese
* Substituição (S): Palavras que foram trocadas entre hipótese e referência

Aqui está um exemplo:

![Exemplo de palavras identificados incorretamente](./media/custom-speech/custom-speech-dis-words.png)

## <a name="resolve-errors-and-improve-wer"></a>Resolver erros e melhorar o WER

Você pode usar o WER dos resultados de reconhecimento de máquina para avaliar a qualidade do modelo que você está usando com seu aplicativo, a ferramenta ou o produto. Um WER de 5 a 10% é considerado ser boa qualidade e está pronto para uso. Um WER de 20% é aceitável, mas você talvez queira considerar o treinamento adicional. Um WER de 30% ou mais sinais de baixa qualidade e requer personalização e treinamento.

Como os erros são distribuídos é importante. Quando são encontrados muitos erros de exclusão, geralmente é devido a intensidade do sinal de áudio fraca. Para resolver esse problema, você precisará coletar dados de áudio mais próximo à fonte. Erros de inserção significam que o áudio foi gravado em um ambiente barulhento e conversa cruzada pode estar presente, causando problemas de reconhecimento. Muitas vezes são encontrados erros de substituição quando uma amostra suficiente de termos específicos de domínio foi fornecida como transcrições rotulada como humanos ou relacionados ao texto.

Analisando arquivos individuais, você pode determinar que tipo de erros existir e os erros que são exclusivos para um arquivo específico. Entendendo problemas no nível de arquivo ajudará você a aprimoramentos de destino.

## <a name="create-a-test"></a>Criar um teste

Se você quiser testar a qualidade do modelo de fala em texto de linha de base da Microsoft ou um modelo personalizado que você treinou, você pode comparar dois modelos lado a lado para avaliar a precisão. A comparação inclui resultados de reconhecimento e WER. Normalmente, um modelo personalizado é comparado com o modelo de linha de base da Microsoft.

Avaliar os modelos lado a lado:

1. Navegue até **fala em texto > fala personalizado > teste**.
2. Clique em **adicionar teste**.
3. Selecione **avaliar a precisão**. Dê um nome, descrição, de teste e selecione seu conjunto de dados de transcrição de áudio + rotulada como humanos.
4. Selecione até dois modelos que você deseja testar.
5. Clique em **Criar**.

Depois que o teste tiver sido criado com êxito, você pode comparar os resultados lado a lado.

## <a name="side-by-side-comparison"></a>Comparação lado a lado

Quando o teste for concluído, indicado pela alteração de status para *bem-sucedido*, você encontrará uma série WER para ambos os modelos incluídos no seu teste. Clique no nome do teste para exibir a página de detalhes do teste. Esta página de detalhes lista todos os as declarações no conjunto de dados, que indica os resultados do reconhecimento dos dois modelos junto com a transcrição do conjunto de dados enviado. Para ajudar a inspecionar a comparação lado a lado, você pode alternar a vários tipos de erro, incluindo a inserção, exclusão e substituição. Ao ouvir o áudio e comparando os resultados do reconhecimento em cada coluna, que mostra a transcrição rotulada como humanos e os resultados para dois modelos de fala em texto, você pode decidir qual modelo atende às suas necessidades e onde treinamento adicional e melhorias são Necessário.

## <a name="next-steps"></a>Próximas etapas

* [Treinar seu modelo](how-to-custom-speech-train-model.md)
* [Implantar seu modelo](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Recursos adicionais

* [Preparar e testar seus dados](how-to-custom-speech-test-data.md)
* [Inspecionar seus dados](how-to-custom-speech-inspect-data.md)
