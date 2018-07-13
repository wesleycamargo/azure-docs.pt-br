---
title: Visão geral da API de Análise de Texto – Serviços Cognitivos do Azure | Microsoft Docs
description: API de Análise de Texto nos Serviços Cognitivos do Azure para análise de sentimento, extração de frases-chave e detecção de idioma.
services: cognitive-services
author: ashmaka
manager: cgronlun
ms.service: cognitive-services
ms.technology: text-analytics
ms.topic: article
ms.date: 5/02/2018
ms.author: ashmaka
ms.openlocfilehash: db5ea943f270aa512afb508668aec90cc4c90df4
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35364621"
---
# <a name="what-is-text-analytics-api-version-20"></a>O que é a API de Análise de Texto Versão 2.0?

A API de Análise de Texto é um serviço baseado em nuvem que fornece um processamento de idioma natural avançado sobre texto bruto e inclui quatro funções principais: análise de sentimento, extração de frases-chave, detecção de idioma e vinculação de entidade.

A API tem o apoio de recursos em [Serviços Cognitivos da Microsoft](https://docs.microsoft.com/azure/cognitive-services/), uma coleção de algoritmos de IA e aprendizado de máquina na nuvem, prontamente consumíveis em seus projetos de desenvolvimento.

## <a name="capabilities-in-text-analytics"></a>Funcionalidades em Análise de Texto

Análise de texto pode significar coisas diferentes, mas, em Serviços Cognitivos, a API de Análise de Texto oferece quatro tipos de análise conforme descrito na tabela a seguir.

| Operações| DESCRIÇÃO | APIs |
|-----------|-------------|------|
|[**Análise de sentimento**](how-tos/text-analytics-how-to-sentiment-analysis.md) | Descubra o que os clientes pensam sobre a sua marca ou tópico analisando texto bruto para encontrar pistas sobre sentimento positivo ou negativo. Essa API retorna uma pontuação de sentimento entre 0 e 1 para cada documento, em que 1 é a mais positiva.<br /> Os modelos de análise são pré-treinados usando um amplo corpo de texto e tecnologias de idioma natural da Microsoft. Para [idiomas selecionados](text-analytics-supported-languages.md), a API pode analisar e pontuar qualquer texto bruto que você forneça, retornando diretamente os resultados ao aplicativo responsável pela chamada. | [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9) <br /> [.NET](https://docs.microsoft.com/en-us/azure/cognitive-services/text-analytics/quickstarts/csharp#install-the-nuget-sdk-package)  |
|[**Extração de Frases-chave**](how-tos/text-analytics-how-to-keyword-extraction.md) | Extrai automaticamente frases-chave para identificar rapidamente os principais pontos. Por exemplo, para o texto de entrada "A comida estava deliciosa e a equipe era maravilhosa", a API retorna os principais pontos de discussão: "comida" e "equipe maravilhosa".  | [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) <br /> [.NET](https://docs.microsoft.com/en-us/azure/cognitive-services/text-analytics/quickstarts/csharp#install-the-nuget-sdk-package) |
|[**Detecção de Idioma**](how-tos/text-analytics-how-to-language-detection.md) | Para até 120 idiomas, detecte em que idioma o texto de entrada é escrito e relate um único código de idioma para cada documento enviado na solicitação. O código de idioma é emparelhado com uma pontuação que indica a intensidade da pontuação. | [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) <br />  [.NET](https://docs.microsoft.com/en-us/azure/cognitive-services/text-analytics/quickstarts/csharp#install-the-nuget-sdk-package) | 
|[**Vinculação de Entidade (versão prévia)**](how-tos/text-analytics-how-to-entity-linking.md) | Identifica as entidades conhecidas no seu texto e um vínculo para obter mais informações na Web. A vinculação de entidade reconhece e faz a desambiguação quando um termo é usado como uma das entidades, verbos e outras formas de palavra separadamente distinguíveis. | [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634) | 

## <a name="typical-workflow"></a>Fluxo de trabalho típico

O fluxo de trabalho é simples: você envia dados para análise e manipula as saídas no seu código. Os analisadores são consumidos como estão, sem nenhuma configuração ou personalização adicional.

1. [Inscreva-se](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para uma [chave de acesso](how-tos/text-analytics-how-to-access-key.md). A chave deve ser passada em cada solicitação.

2. [Formular uma solicitação](how-tos/text-analytics-how-to-call-api.md#json-schema) contendo seus dados como texto bruto não estruturado, em JSON.

3. Publique a solicitação para o ponto de extremidade estabelecido durante a inscrição, acrescentando o recurso desejado: análise de sentimento, extração de frases-chave, detecção de idioma ou identificação da entidade.

4. Transmita por streaming ou armazene a resposta localmente. Dependendo da solicitação, os resultados são uma pontuação de sentimento, uma coleção de frases-chave extraídas ou um código de idioma.

A saída é retornada como um único documento JSON, com resultados para cada documento de texto que você enviou, com base na ID. Posteriormente, você pode analisar, visualizar ou categorizar os resultados em insights acionáveis.

Os dados não são armazenados na sua conta. Operações executadas pela API de Análise de Texto não têm estado, o que significa que o texto que você fornece é processado e os resultados são retornados imediatamente.

<a name="supported-languages"></a>

## <a name="supported-languages"></a>Idiomas com suporte

Esta seção foi movida para um artigo separado para melhor capacidade de descoberta. Consulte [Idiomas compatíveis na API de Análise de Texto](text-analytics-supported-languages.md) para este conteúdo.

<a name="data-limits"></a>

## <a name="data-limits"></a>Limites de dados

Todos os pontos de extremidade da API de Análise de Texto aceitam dados de texto brutos. O limite atual é de cinco mil caracteres para cada documento; se você precisar analisar documentos maiores, poderá dividi-los em partes menores. Se você ainda exigir um limite superior, [entre em contato conosco](https://azure.microsoft.com/overview/sales-number/) para que possamos discutir suas necessidades.

| Limite | Valor |
|------------------------|---------------|
| Tamanho máximo de um único documento | Cinco mil caracteres conforme medido pelo `String.Length`. |
| Tamanho máximo de toda a solicitação | 1 MB |
| Número máximo de documentos em uma solicitação | Mil documentos |

O limite de taxa é de 100 chamadas por minuto. Observe que você pode enviar uma grande quantidade de documentos em uma única chamada (até mil documentos).

## <a name="unicode-encoding"></a>Codificação Unicode

A API de Análise de Texto usa codificação Unicode para representação de texto e cálculos de contagem de caracteres. As solicitações podem ser enviadas em UTF-8 e UTF-16 sem diferenças mensuráveis na contagem de caracteres. Pontos de código Unicode são usados como heurística para comprimento de caracteres e são considerados equivalentes para fins de limites de dados de análise de texto. Se você usar `String.Length` para obter a contagem de caracteres, você estará usando o mesmo método que usamos para medir o tamanho dos dados.

## <a name="next-steps"></a>Próximas etapas

Primeiro, tente a [demonstração interativa](https://azure.microsoft.com/services/cognitive-services/text-analytics/). Você pode colar uma entrada de texto (máximo de cinco mil caracteres) para detectar o idioma (até 120), calcular uma pontuação de sentimento ou extrair frases-chave. Sem a necessidade de inscrição.

Quando estiver pronto para chamar a API diretamente:

+ [Inscreva-se](how-tos/text-analytics-how-to-signup.md) para uma chave de acesso e examine as etapas para [chamar a API](how-tos/text-analytics-how-to-call-api.md).

+ [Guia de início rápido](quickstarts/csharp.md) é um passo a passo das chamadas à API REST escritas em C#. Saiba como enviar texto, escolher uma análise e exibir os resultados com o mínimo de código.

+ A [Documentação de referência de API](//go.microsoft.com/fwlink/?LinkID=759346) fornece a documentação técnica para as APIs. A documentação é compatível com chamadas internas para que você possa chamar a API de cada página de documentação.

+ [Conteúdo da Comunidade e Externo](text-analytics-resource-external-community.md) fornece uma lista de postagens em blog e vídeos demonstrando como usar a Análise de Texto com outras ferramentas e tecnologias.

## <a name="see-also"></a>Consulte também

 [Página Documentação dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/)
