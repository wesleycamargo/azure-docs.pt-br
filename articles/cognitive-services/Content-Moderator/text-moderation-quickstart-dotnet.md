---
title: 'Início Rápido: Analisar o conteúdo de texto em C# – Content Moderator'
titlesuffix: Azure Cognitive Services
description: Como analisar conteúdo de texto para encontrar materiais indesejáveis usando o SDK do Content Moderator para .NET
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: fbbb58d4dc3774e3142787894ab6dfe0580c25b7
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55224608"
---
# <a name="quickstart-analyze-text-content-for-objectionable-material-in-c"></a>Início Rápido: Analisar o conteúdo de texto para material indesejável em C# 

Este artigo fornece informações e exemplos de código para ajudá-lo a começar a usar o [SDK do Content Moderator para .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/). Você aprenderá a executar filtragem e classificação de conteúdo de texto baseadas em termos com o objetivo de moderar material potencialmente indesejável.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

## <a name="prerequisites"></a>Pré-requisitos
- Uma chave de assinatura do Content Moderator. Siga as instruções em [Criar uma conta dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para assinar o Content Moderator e obter sua chave.
- Qualquer edição do [Visual Studio 2015 ou 2017](https://www.visualstudio.com/downloads/)

> [!NOTE]
> Este guia usa uma assinatura do Content Moderator de camada gratuita. Para obter informações sobre o que é fornecido com cada camada de assinatura, consulte a página [Preços e limites](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/).

## <a name="create-the-visual-studio-project"></a>Criar o projeto do Visual Studio

1. N o Visual Studio, crie um novo projeto de **Aplicativo de Console (.NET Framework)** e dê o nome de **TextModeration**. 
1. Se houver outros projetos na solução, selecione este como o único projeto de inicialização.
1. Obtenha os pacotes necessários do NuGet. Clique com botão direito do mouse no projeto no Gerenciador de Soluções e selecione **Gerenciar Pacotes NuGet**; em seguida, localize e instale os seguintes pacotes:
    - Microsoft.Azure.CognitiveServices.ContentModerator
    - Microsoft.Rest.ClientRuntime
    - Newtonsoft.Json

## <a name="add-text-moderation-code"></a>Adicionar código de moderação de texto

Em seguida, você vai copiar e colar o código deste guia em seu projeto para implementar um cenário básico de moderação de conteúdo.

### <a name="include-namespaces"></a>Incluir namespaces

Adicione as instruções `using` a seguir à parte superior do seu arquivo *Program.cs*.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/text-moderation-quickstart-dotnet.cs?range=1-8)]

### <a name="create-the-content-moderator-client"></a>Criar o cliente do Content Moderator

Adicione o código a seguir ao arquivo *Program.cs* a fim de criar um provedor de cliente do Content Moderator para sua assinatura. Adicione o código junto com a classe **Program** no mesmo namespace. Você precisará atualizar os campos **AzureRegion** e **CMSubscriptionKey** com os valores da chave de assinatura e do identificador de região.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/text-moderation-quickstart-dotnet.cs?range=54-77)]

### <a name="set-up-input-and-output-targets"></a>Configurar destinos de entrada e saída

Adicione os seguintes campos estáticos à classe **Programa** em _Program.cs_. Eles especificam os arquivos para conteúdo de texto de entrada e conteúdo JSON de saída.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/text-moderation-quickstart-dotnet.cs?range=15-19)]

Você precisará criar o arquivo de entrada *TextFile.txt* e atualizar o caminho adequadamente (caminhos relativos são relativos ao diretório de execução). Abra _TextFile.txt_ e adicione o texto a ser moderado. Este início rápido usa os seguintes textos de exemplo:

```
Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.
These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 
0800 820 3300. Also, 999-99-9999 looks like a social security number (SSN).
```

### <a name="load-the-input-text"></a>Carregar o texto de entrada

Adicione o código a seguir ao método **Principal**. O método **ScreenText** é a operação essencial. Seus parâmetros especificam quais operações de moderação de conteúdo serão feitas. Neste exemplo, o método está configurado para:
- Detectar possíveis palavrões no texto.
- Normalizar os erros de digitação de texto e a correção automática.
- Detecte informações de identificação pessoal (PII), como números de telefone dos EUA e do Reino Unido, endereços de email e endereços dos EUA.
- Usar modelos com base em aprendizado de máquina para classificar o texto em três categorias.

Se você quiser saber mais sobre o que fazem essas operações, siga o link na seção [Próximas etapas](#next-steps).

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/text-moderation-quickstart-dotnet.cs?range=23-48)]

## <a name="run-the-program"></a>Execute o programa

O programa gravará dados de cadeia de caracteres JSON no arquivo _TextModerationOutput.txt_. O texto de exemplo usado neste início rápido fornece a seguinte saída:

```json
Autocorrect typos, check for matching terms, PII, and classify.
{
"OriginalText": "\"Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052. These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. Also, 999-99-9999 looks like a social security number (SSN).\"",
"NormalizedText": "\" Is this a garbage or crap email abide@ abed. com, phone: 6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, Redmond, WA 98052. These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. Also, 999-99-9999 looks like a social security number ( SSN) . \"",
"AutoCorrectedText": "\" Is this a garbage or crap email abide@ abed. com, phone: 6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, Redmond, WA 98052. These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. Also, 999-99-9999 looks like a social security number ( SSN) . \"",
"Misrepresentation": null,
    
"Classification": {
        "Category1": {
        "Score": 1.5113095059859916E-06
        },
        "Category2": {
        "Score": 0.12747249007225037
        },
        "Category3": {
        "Score": 0.98799997568130493
        },
        "ReviewRecommended": true
  },
  "Status": {
    "Code": 3000,
    "Description": "OK",
    "Exception": null
  },
  "PII": {
        "Email": [
            {
            "Detected": "abcdef@abcd.com",
            "SubType": "Regular",
            "Text": "abcdef@abcd.com",
            "Index": 33
            }
            ],
        "IPA": [
            {
            "SubType": "IPV4",
            "Text": "255.255.255.255",
            "Index": 73
            }
            ],
        "Phone": [
            {
            "CountryCode": "US",
            "Text": "6657789887",
            "Index": 57
            },
            {
            "CountryCode": "US",
            "Text": "870 608 4000",
            "Index": 211
            },
            {
            "CountryCode": "UK",
            "Text": "+44 870 608 4000",
            "Index": 207
            },
            {
            "CountryCode": "UK",
            "Text": "0344 800 2400",
            "Index": 227
            },
            {
        "CountryCode": "UK",
            "Text": "0800 820 3300",
            "Index": 244
            }
            ],
         "Address": [{
             "Text": "1 Microsoft Way, Redmond, WA 98052",
             "Index": 89
                }]
    },
  "Language": "eng",
  "Terms": [
    {
        "Index": 22,
        "OriginalIndex": 22,
        "ListId": 0,
        "Term": "crap"
    }
  ],
  "TrackingId": "9392c53c-d11a-441d-a874-eb2b93d978d3"
}
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você desenvolveu um aplicativo simples em .NET que usa o serviço Content Moderator para retornar informações relevantes sobre determinado texto de exemplo. A seguir, saiba mais sobre o que os vários sinalizadores e classificações significam para que você possa decidir quais dados são necessários e como seu aplicativo deve lidar com isso.

> [!div class="nextstepaction"]
> [Guia de moderação de texto](text-moderation-api.md)
