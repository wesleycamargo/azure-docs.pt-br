---
title: Visão geral da Análise de Texto – Serviços Cognitivos do Azure | Microsoft Docs
description: Análise de Texto dos Serviços Cognitivos do Azure para análise de sentimento, extração de frases-chave, detecção de idioma e vinculação de entidade.
services: cognitive-services
author: ashmaka
manager: cgronlun
ms.service: cognitive-services
ms.technology: text-analytics
ms.topic: article
ms.date: 5/02/2018
ms.author: ashmaka
ms.openlocfilehash: a514618713afe2306b6fb99b2f8c038aeac56009
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/27/2018
ms.locfileid: "43092196"
---
# <a name="what-is-text-analytics"></a>O que é a Análise de Texto?

O serviço de Análise de Texto fornece processamento avançado de idioma natural para texto não estruturado bruto. Ele inclui quatro funções principais: análise de sentimento, extração de frases-chave, detecção de idioma e vinculação de entidade.

## <a name="analyze-sentiment"></a>Analisar sentimento

[Descubra](how-tos/text-analytics-how-to-sentiment-analysis.md) o que os clientes pensam de sua marca ou tópico analisando texto bruto para encontrar pistas sobre sentimentos positivos ou negativos. Essa API retorna uma pontuação de sentimento entre 0 e 1 para cada documento, em que 1 é a mais positiva.<br />
Os modelos de análise são pré-treinados usando um amplo corpo de texto e tecnologias de idioma natural da Microsoft. Para [idiomas selecionados](text-analytics-supported-languages.md), a API pode analisar e pontuar qualquer texto bruto que você fornecer.

## <a name="extract-key-phrases"></a>Extraia frases-chave

[Extraia frases-chave](how-tos/text-analytics-how-to-keyword-extraction.md) automaticamente para identificar rapidamente os principais pontos. Por exemplo, para o texto de entrada dado "A comida estava deliciosa e a equipe era maravilhosa", o serviço de Análise de Texto retorna os principais pontos de discussão: "comida" e "equipe maravilhosa".

## <a name="detect-language"></a>Detectar o idioma

Para até 120 idiomas, [detecte](how-tos/text-analytics-how-to-language-detection.md) em que idioma o texto de entrada está escrito e relate um único código de idioma para cada documento enviado na solicitação. O código de idioma é emparelhado com uma pontuação que indica a intensidade da pontuação.

## <a name="identify-linked-entities-preview"></a>Identificar entidades vinculadas (versão prévia)

[Identifique](how-tos/text-analytics-how-to-entity-linking.md) entidades conhecidas em seu texto e forneça um link para obter mais informações na Web. A vinculação de entidade reconhece e faz a desambiguação quando um termo é usado como uma das entidades, verbos e outras formas de palavra separadamente distinguíveis.

## <a name="typical-workflow"></a>Fluxo de trabalho típico

O fluxo de trabalho é simples: você envia dados para análise e manipula as saídas no seu código. Os analisadores são consumidos como estão, sem nenhuma configuração ou personalização adicional.

1. [Inscreva-se](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para uma [chave de acesso](how-tos/text-analytics-how-to-access-key.md). A chave deve ser passada em cada solicitação.

2. [Crie uma solicitação](how-tos/text-analytics-how-to-call-api.md#json-schema) em JSON contendo seus dados como texto não estruturado bruto.

3. Poste a solicitação no ponto de extremidade estabelecido durante a inscrição, acrescentando a API que deseja chamar: análise de sentimento, extração de frases-chave, detecção de idioma ou identificação da entidade.

4. Transmita por streaming ou armazene a resposta localmente. Dependendo da solicitação, os resultados são uma pontuação de sentimento, uma coleção de frases-chave extraídas ou um código de idioma.

A saída é retornada como um único documento JSON, com resultados para cada documento de texto que você enviou, com base na ID. Em seguida, você pode analisar, visualizar ou categorizar os resultados em insights acionáveis.

As operações realizadas pelo serviço de Análise de Texto são sem monitoração de estado. Os dados não são armazenados na sua conta.

<a name="data-limits"></a>

## <a name="specifications"></a>Especificações

### <a name="supported-languages"></a>Idiomas com suporte

Consulte [Idiomas com suporte na Análise de Texto](text-analytics-supported-languages.md).

### <a name="data-limits"></a>Limites de dados

Todos os pontos de extremidade da Análise de Texto aceitam dados de texto brutos. O limite atual é de cinco mil caracteres para cada documento; se você precisar analisar documentos maiores, poderá dividi-los em partes menores. Se você ainda exigir um limite superior, [entre em contato conosco](https://azure.microsoft.com/overview/sales-number/) para que possamos discutir suas necessidades.

| Limite | Valor |
|------------------------|---------------|
| Tamanho máximo de um único documento | Cinco mil caracteres conforme medido pelo `String.Length`. |
| Tamanho máximo de toda a solicitação | 1 MB |
| Número máximo de documentos em uma solicitação | Mil documentos |

O limite de taxa é de 100 chamadas por minuto. Observe que você pode enviar uma grande quantidade de documentos em uma única chamada (até 1000 documentos).

### <a name="unicode-encoding"></a>Codificação Unicode

O serviço de Análise de Texto usa codificação Unicode para representação de texto e cálculos de contagem de caracteres. É possível enviar solicitações em UTF-8 ou UTF-16 sem diferenças mensuráveis na contagem de caracteres. Se usar `String.Length` para obter a contagem de caracteres, você estará usando o mesmo método que usamos para medir o tamanho dos dados.

## <a name="next-steps"></a>Próximas etapas

Primeiro, tente a [demonstração interativa](https://azure.microsoft.com/services/cognitive-services/text-analytics/). Você pode colar uma entrada de texto (máximo de cinco mil caracteres) para detectar o idioma (até 120), calcular uma pontuação de sentimento, extrair frases-chave ou identificar entidades vinculadas. Não é necessário se inscrever.

Quando estiver pronto para chamar o serviço de Análise de Texto diretamente:

+ [Inscreva-se](how-tos/text-analytics-how-to-signup.md) para uma chave de acesso e examine as etapas para [chamar a API](how-tos/text-analytics-how-to-call-api.md).

+ [Guia de início rápido](quickstarts/csharp.md) é um passo a passo das chamadas à API REST escritas em C#. Saiba como enviar texto, escolher uma análise e exibir os resultados com o mínimo de código.

+ A [Documentação de referência de API](//go.microsoft.com/fwlink/?LinkID=759346) fornece a documentação técnica das APIs REST. A documentação é compatível com chamadas internas para que você possa chamar a API de cada página de documentação.

+ [Conteúdo da Comunidade e Externo](text-analytics-resource-external-community.md) fornece uma lista de postagens em blog e vídeos demonstrando como usar a Análise de Texto com outras ferramentas e tecnologias.

## <a name="see-also"></a>Consulte também

 [Página Documentação dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/)
