---
title: 'Início Rápido: Analisar o conteúdo de texto para material indesejável em Python'
titlesuffix: Azure Cognitive Services
description: Como analisar conteúdo de texto para encontrar materiais indesejáveis usando o SDK do Content Moderator para Python
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: quickstart
ms.date: 11/19/2018
ms.author: pafarley
ms.openlocfilehash: acafd666b2d3791450499ebdf173ffb67ad2c932
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53095181"
---
# <a name="quickstart-analyze-text-content-for-objectionable-material-in-python"></a>Início Rápido: Analisar o conteúdo de texto para material indesejável em Python

Este artigo fornece informações e exemplos de código para ajudá-lo a começar a usar o SDK do Content Moderator para Python. Você aprenderá a executar filtragem e classificação de conteúdo de texto baseadas em termos com o objetivo de moderar material potencialmente indesejável.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

## <a name="prerequisites"></a>Pré-requisitos
- Uma chave de assinatura do Content Moderator. Siga as instruções em [Criar uma conta dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para assinar o Content Moderator e obter sua chave.
- [Python 2.7+ ou 3.5+](https://www.python.org/downloads/)
- Ferramenta [pip](https://pip.pypa.io/en/stable/installing/)

## <a name="get-the-content-moderator-sdk"></a>Obtenha o SDK do Content Moderator

Instale o SDK do Python do Content Moderator abrindo um prompt de comando e executando o seguinte comando:

```
pip install azure-cognitiveservices-vision-contentmoderator
```

## <a name="import-modules"></a>Importar módulos

Crie um novo script do Python chamado _ContentModeratorQS.py_ e adicione o código a seguir para importar as partes necessárias do SDK.

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/text-moderation-quickstart-python.py?range=1-10)]

Também importe a função "bela impressão" para manipular a saída final.

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/text-moderation-quickstart-python.py?range=12)]


## <a name="initialize-variables"></a>Inicializar variáveis

Em seguida, adicione variáveis à sua chave de assinatura do Content Moderator e URL de ponto de extremidade. Será necessário substituir `<your subscription key>` pelo valor da sua chave. Você também poderá precisar alterar o valor de `endpoint_url` para usar o identificador de região que corresponde à chave de assinatura. As chaves de assinatura de avaliação gratuita são geradas na região **westus**.

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/text-moderation-quickstart-python.py?range=14-16)]


Para simplificar, você analisará o texto diretamente do script. Defina uma nova cadeia de caracteres de conteúdo de texto como moderada:

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/text-moderation-quickstart-python.py?range=18-21)]


## <a name="query-the-moderator-service"></a>Consultar o serviço do Moderador

Criar uma instância **ContentModeratorClient** usando sua URL de ponto de extremidade e a chave de assinatura. Em seguida, use a instância **TextModerationOperations** do membro para chamar a API de moderação. Confira a documentação de referência **[screen_text](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-contentmoderator/azure.cognitiveservices.vision.contentmoderator.operations.textmoderationoperations?view=azure-python#screen-text)** para obter mais informações sobre como chamá-la.

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/text-moderation-quickstart-python.py?range=23-36)]

## <a name="print-the-response"></a>Imprima a resposta

Por fim, verifique se a chamada foi concluída com êxito e retornou uma instância **Tela**. Em seguida, imprima os dados retornados para o console.

[!code-python[](~/cognitive-services-content-moderator-samples/documentation-samples/python/text-moderation-quickstart-python.py?range=38-39)]


O texto de exemplo usado neste início rápido resulta na seguinte saída:

```console
{'auto_corrected_text': '" Is this a garbage email abide@ abed. com, phone: '
                        '6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, '
                        'Redmond, WA 98052. Crap is the profanity here. Is '
                        'this information PII? phone 3144444444\\ n"',
 'classification': {'category1': {'score': 0.00025233393535017967},
                    'category2': {'score': 0.18468093872070312},
                    'category3': {'score': 0.9879999756813049},
                    'review_recommended': True},
 'language': 'eng',
 'normalized_text': '" Is this a garbage email abide@ abed. com, phone: '
                    '6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, '
                    'Redmond, WA 98052. Crap is the profanity here. Is this '
                    'information PII? phone 3144444444\\ n"',
 'original_text': '"Is this a grabage email abcdef@abcd.com, phone: '
                  '6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, '
                  'WA 98052. Crap is the profanity here. Is this information '
                  'PII? phone 3144444444\\n"',
 'pii': {'address': [{'index': 82,
                      'text': '1 Microsoft Way, Redmond, WA 98052'}],
         'email': [{'detected': 'abcdef@abcd.com',
                    'index': 25,
                    'sub_type': 'Regular',
                    'text': 'abcdef@abcd.com'}],
         'ipa': [{'index': 65, 'sub_type': 'IPV4', 'text': '255.255.255.255'}],
         'phone': [{'country_code': 'US', 'index': 49, 'text': '6657789887'},
                   {'country_code': 'US', 'index': 177, 'text': '3144444444'}]},
 'status': {'code': 3000, 'description': 'OK'},
 'terms': [{'index': 118, 'list_id': 0, 'original_index': 118, 'term': 'crap'}],
 'tracking_id': 'b253515c-e713-4316-a016-8397662a3f1a'}
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você desenvolveu um script Python simples que usa o serviço Content Moderator para retornar informações relevantes sobre determinado texto de exemplo. A seguir, saiba mais sobre o que os vários sinalizadores e classificações significam para que você possa decidir quais dados são necessários e como seu aplicativo deve lidar com isso.

> [!div class="nextstepaction"]
> [Guia de moderação de texto](text-moderation-api.md)