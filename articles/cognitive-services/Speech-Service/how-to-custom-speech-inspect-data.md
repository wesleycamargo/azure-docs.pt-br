---
title: Inspecionar a qualidade de dados para conversão de fala personalizado – serviços de fala
titlesuffix: Azure Cognitive Services
description: Conversão de fala personalizada fornece ferramentas que permitem que você inspecionar visualmente a qualidade do reconhecimento de um modelo, comparando os dados de áudio com o resultado de reconhecimento correspondente. No portal de fala personalizado, você pode reproduzir áudio carregado e determinar se o resultado do reconhecimento fornecido está correto.  Essa ferramenta permite inspecionar rapidamente a qualidade do modelo de fala em texto de linha de base da Microsoft ou um modelo treinado personalizado sem ter que transcrevê qualquer dado de áudio.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 682f3df362a7fbb0e95a07aa8a8f3a068367eef2
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025813"
---
# <a name="inspect-custom-speech-data"></a>Inspecione os dados de fala personalizado

> [!NOTE]
> Esta página presume que você já leu [preparar dados de teste para conversão de fala personalizado](how-to-custom-speech-test-data.md) e carregou um conjunto de dados para inspeção.

Conversão de fala personalizada fornece ferramentas que permitem que você inspecionar visualmente a qualidade do reconhecimento de um modelo, comparando os dados de áudio com o resultado de reconhecimento correspondente. No portal de fala personalizado, você pode reproduzir áudio carregado e determinar se o resultado do reconhecimento fornecido está correto. Essa ferramenta permite inspecionar rapidamente a qualidade do modelo de fala em texto de linha de base da Microsoft ou um modelo treinado personalizado sem ter que transcrevê qualquer dado de áudio.

Neste documento, você aprenderá a inspecionar visualmente a qualidade de um modelo usando os dados de treinamento que você carregou anteriormente.

Nessa página, você aprenderá a inspecionar visualmente a qualidade do modelo de fala em texto de linha de base da Microsoft e/ou em um modelo personalizado que você treinou. Você usará os dados que você carregou para o **dados** guia para teste.

## <a name="create-a-test"></a>Criar um teste

Siga estas instruções para criar um teste:

1. Navegue até **fala em texto > fala personalizado > teste**.
2. Clique em **adicionar teste**.
3. Selecione **inspecionar qualidade (dados somente de áudio)**. Dê um nome, descrição, de teste e selecione seu conjunto de dados de áudio.
4. Selecione até dois modelos que você deseja testar.
5. Clique em **Criar**.

Depois que um teste tiver sido criado com êxito, você pode comparar os modelos de lado a lado.

## <a name="side-by-side-model-comparisons"></a>Comparações de modelo de lado a lado

Quando o status do teste está *bem-sucedido*, clique no nome do item de teste para ver os detalhes do teste. Esta página de detalhes lista todos os as declarações no conjunto de dados, que indica os resultados do reconhecimento dos dois modelos junto com a transcrição do conjunto de dados enviado.

Para ajudar a inspecionar a comparação lado a lado, você pode alternar a vários tipos de erro, incluindo a inserção, exclusão e substituição. Ao ouvir o áudio e comparando os resultados do reconhecimento em cada coluna (mostrando transcrição rotulada como humanos e os resultados dos dois modelos de fala em texto), você pode decidir qual modelo atende às suas necessidades e onde os aprimoramentos são necessários.

Inspecionando a testes de qualidade é útil para validar se a qualidade de um ponto de extremidade de reconhecimento de fala é suficiente para um aplicativo.  Para uma medida objetiva da precisão, exigindo transcrito áudio, siga as instruções encontradas em teste: Avalie a precisão.

## <a name="next-steps"></a>Próximas etapas

* [Avaliar seus dados](how-to-custom-speech-evaluate-data.md)
* [Treinar seu modelo](how-to-custom-speech-train-model.md)
* [Implantar seu modelo](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Recursos adicionais

* [Preparar os dados de teste para conversão de fala personalizado](how-to-custom-speech-test-data.md)
