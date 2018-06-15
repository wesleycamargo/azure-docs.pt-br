---
title: Inteligência de mídia do Azure | Microsoft Docs
description: Ao usar os Serviços de Mídia do Azure, você pode analisar seu conteúdo de áudio e vídeo usando AudioAnalyzerPreset e VideoAnalyzerPreset.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/24/2018
ms.author: juliako
ms.openlocfilehash: 804a418f6ee88974d6e74a2c18bc5d01b6adf838
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
ms.locfileid: "33783545"
---
# <a name="media-intelligence"></a>Inteligência de mídia

API REST v3 dos Serviços de Mídia do Azure permite que você analise o conteúdo de áudio e vídeo. Para analisar seu conteúdo, crie uma **Transformação** e envie um **Trabalho** que usa uma destas predefinições: **AudioAnalyzerPreset** ou **VideoAnalyzerPreset**. 

## <a name="audioanalyzerpreset"></a>AudioAnalyzerPreset

**AudioAnalyzerPreset** permite a extração de várias informações de áudio de um arquivo de áudio ou vídeo. A saída inclui um arquivo JSON (com todas as informações) e um arquivo VTT para a transcrição do áudio. Essa predefinição aceita uma propriedade que especifica o idioma do arquivo de entrada na forma de uma cadeia de caracteres [BCP47](https://tools.ietf.org/html/bcp47). As informações de áudio incluem:

* Transcrição de áudio: uma transcrição das palavras faladas com carimbos de hora. Há suporte para vários idiomas
* Indexação de locutores: um mapeamento dos locutores e das palavras faladas correspondentes
* Análise de sentimento da fala: a saída da análise de sentimento executada na transcrição de áudio
* Palavras-chave: palavras-chave extraídas da transcrição de áudio.

## <a name="videoanalyzerpreset"></a>VideoAnalyzerPreset

**VideoAnalyzerPreset** permite a extração de várias informações de áudio e de vídeo de um arquivo de vídeo. A saída inclui um arquivo JSON (com todas as informações) e um arquivo VTT para a transcrição do vídeo, e uma coleção de miniaturas. Essa predefinição também aceita uma cadeia de caracteres [BCP47](https://tools.ietf.org/html/bcp47) (que representa o idioma do vídeo) como propriedade. As informações de vídeos incluem todas as informações de áudio mencionadas acima, e os itens adicionais a seguir:

* Controle de face: o tempo durante o qual há faces presentes no vídeo. Cada face tem uma id de face e um conjunto correspondente de miniaturas
* Texto visual: o texto detectado por meio de reconhecimento óptico de caracteres. O texto recebe um carimbo de data/hora e também é usado para extrair as palavras-chave (além da transcrição de áudio)
* Quadros-chave: um conjunto de quadros-chave que são extraídos do vídeo
* Moderação de conteúdo visual: a parte dos vídeos que foi sinalizada como de natureza adulta
* Anotação: resultado da anotação dos vídeos com base em um modelo de objeto predefinido

##  <a name="insightsjson-elements"></a>Elementos insights.json

A saída inclui um arquivo JSON (insights.json) com todas as informações que foram encontradas no vídeo ou áudio. O json pode conter os seguintes elementos:

### <a name="transcript"></a>transcript

|NOME|DESCRIÇÃO|
|---|---|
|ID|A ID da linha.|
|text|A própria transcrição.|
|Linguagem|O idioma da transcrição. Tem o objetivo dar suporte à transcrição na qual cada linha pode ter um idioma diferente.|
|instances|Uma lista com os intervalos de tempo nos quais essa linha apareceu. Se a instância for uma transcrição, ele terá apenas 1 instância.|

Exemplo:

```json
"transcript": [
{
    "id": 0,
    "text": "Hi I'm Doug from office.",
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:00.5100000",
        "end": "00:00:02.7200000"
    }
    ]
},
{
    "id": 1,
    "text": "I have a guest. It's Michelle.",
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:02.7200000",
        "end": "00:00:03.9600000"
    }
    ]
}
] 
```

### <a name="ocr"></a>ocr

|NOME|DESCRIÇÃO|
|---|---|
|ID|A id da linha de OCR.|
|text|O texto de OCR.|
|confidence|A confiança do reconhecimento.|
|Linguagem|O idioma do OCR.|
|instances|Uma lista de intervalos de tempo nos quais essa OCR apareceu (o mesmo OCR pode aparecer várias vezes).|

```json
"ocr": [
    {
      "id": 0,
      "text": "LIVE FROM NEW YORK",
      "confidence": 0.91,
      "language": "en-US",
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:52"
        }
      ]
    },
    {
      "id": 1,
      "text": "NOTICIAS EN VIVO",
      "confidence": 0.9,
      "language": "es-ES",
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:28"
        },
        {
          "start": "00:00:32",
          "end": "00:00:38"
        }
      ]
    }
  ],
```

### <a name="keywords"></a>palavras-chave

|NOME|DESCRIÇÃO|
|---|---|
|ID|A id da palavra-chave.|
|text|O texto da palavra-chave.|
|confidence|A confiança do reconhecimento da palavra-chave.|
|Linguagem|O idioma da palavra-chave (quando traduzida).|
|instances|Uma lista de intervalos de tempo nos quais essa palavra-chave apareceu (uma palavra-chave pode aparecer várias vezes).|

```json
"keywords": [
{
    "id": 0,
    "text": "office",
    "confidence": 1.6666666666666667,
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:00.5100000",
        "end": "00:00:02.7200000"
    },
    {
        "start": "00:00:03.9600000",
        "end": "00:00:12.2700000"
    }
    ]
},
{
    "id": 1,
    "text": "icons",
    "confidence": 1.4,
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:03.9600000",
        "end": "00:00:12.2700000"
    },
    {
        "start": "00:00:13.9900000",
        "end": "00:00:15.6100000"
    }
    ]
}
] 

```

### <a name="faces"></a>faces

|NOME|DESCRIÇÃO|
|---|---|
|ID|A ID da face.|
|Nome|O nome da face. Pode ser "Desconhecido #0", uma celebridade identificada ou uma pessoa treinada pelo cliente.|
|confidence|A confiança de identificação da face.|
|Descrição|Se for uma celebridade, sua descrição ("Satya Nadella nasceu em...."). |
|thumbnalId|A id da miniatura dessa face.|
|knownPersonId|Se for uma pessoa conhecida, sua ID interna.|
|referenceId|Se for uma celebridade Bing, sua ID do Bing.|
|referenceType|No momento, apenas Bing.|
|título|Se for uma celebridade, seu título (por exemplo "CEO da Microsoft").|
|imageUrl|Se for uma celebridade, a URL da imagem.|
|instances|Essas são as ocorrências do aparecimento da face no intervalo de tempo determinado. Cada ocorrência também tem uma thumbnailsId. |

```json
"faces": [{
    "id": 2002,
    "name": "Xam 007",
    "confidence": 0.93844,
    "description": null,
    "thumbnailId": "00000000-aee4-4be2-a4d5-d01817c07955",
    "knownPersonId": "8340004b-5cf5-4611-9cc4-3b13cca10634",
    "referenceId": null,
    "title": null,
    "imageUrl": null,
    "instances": [{
        "thumbnailsIds": ["00000000-9f68-4bb2-ab27-3b4d9f2d998e",
        "cef03f24-b0c7-4145-94d4-a84f81bb588c"],
        "adjustedStart": "00:00:07.2400000",
        "adjustedEnd": "00:00:45.6780000",
        "start": "00:00:07.2400000",
        "end": "00:00:45.6780000"
    },
    {
        "thumbnailsIds": ["00000000-51e5-4260-91a5-890fa05c68b0"],
        "adjustedStart": "00:10:23.9570000",
        "adjustedEnd": "00:10:39.2390000",
        "start": "00:10:23.9570000",
        "end": "00:10:39.2390000"
    }]
}]
```

### <a name="labels"></a>rótulos

|NOME|DESCRIÇÃO|
|---|---|
|ID|A ID do rótulo.|
|Nome|O nome do rótulo (por exemplo, "Computador", "TV").|
|Linguagem|O idioma do nome do rótulo (quando traduzido). BCP-47|
|instances|Uma lista de intervalos de tempo nos quais esse rótulo apareceu (um rótulo pode aparecer várias vezes). Cada instância tem um campo de confiança. |


```json
"labels": [
    {
      "id": 0,
      "name": "person",
      "language": "en-US",
      "instances": [
        {
          "confidence": 1.0,
          "start": "00: 00: 00.0000000",
          "end": "00: 00: 25.6000000"
        },
        {
          "confidence": 1.0,
          "start": "00: 01: 33.8670000",
          "end": "00: 01: 39.2000000"
        }
      ]
    },
    {
      "name": "indoor",
      "language": "en-US",
      "id": 1,
      "instances": [
        {
          "confidence": 1.0,
          "start": "00: 00: 06.4000000",
          "end": "00: 00: 07.4670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 09.6000000",
          "end": "00: 00: 10.6670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 11.7330000",
          "end": "00: 00: 20.2670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 21.3330000",
          "end": "00: 00: 25.6000000"
        }
      ]
    }
  ] 
```

### <a name="shots"></a>shots

|NOME|DESCRIÇÃO|
|---|---|
|ID|A id da captura.|
|keyFrames|Uma lista com os quadros-chave dentro da captura (cada um tem uma Id e uma lista de intervalos de tempo de instâncias).|
|instances|Uma lista com os intervalos de tempo desta captura (as capturas têm apenas 1 instância).|

```json
"Shots": [
    {
      "id": 0,
      "keyFrames": [
        {
          "id": 0,
          "instances": [
            {
              "start": "00: 00: 00.1670000",
              "end": "00: 00: 00.2000000"
            }
          ]
        }
      ],
      "instances": [
        {
          "start": "00: 00: 00.2000000",
          "end": "00: 00: 05.0330000"
        }
      ]
    },
    {
      "id": 1,
      "keyFrames": [
        {
          "id": 1,
          "instances": [
            {
              "start": "00: 00: 05.2670000",
              "end": "00: 00: 05.3000000"
            }
          ]
        }
      ],
      "instances": [
        {
          "start": "00: 00: 05.2670000",
          "end": "00: 00: 10.3000000"
        }
      ]
    }
  ]
```

### <a name="audioeffects"></a>audioEffects

|NOME|DESCRIÇÃO|
|---|---|
|ID|A ID do efeito de áudio.|
|Tipo|O tipo de efeito de áudio (por exemplo, Palmas, Fala, Silêncio).|
|instances|Uma lista com os intervalos de tempo nos quais esse efeito de áudio apareceu.|

```json
"audioEffects": [
{
    "id": 0,
    "type": "Clapping",
    "instances": [
    {
        "start": "00:00:00",
        "end": "00:00:03"
    },
    {
        "start": "00:01:13",
        "end": "00:01:21"
    }
    ]
}
]
```


### <a name="sentiments"></a>sentiments

Os sentimentos são agregadas de acordo com seu campo sentimentType (Positivo/Neutro/Negativo). Por exemplo, 0-0.1, 0.1-0.2.

|NOME|DESCRIÇÃO|
|---|---|
|ID|A ID do sentimento.|
|averageScore |A média de todas as pontuações de todas as instâncias desse tipo de sentimento - Neutral/positivo/negativo|
|instances|Uma lista com os intervalos de tempo nos quais esse sentimento apareceu.|

```json
"sentiments": [
{
    "id": 0,
    "averageScore": 0.87,
    "instances": [
    {
        "start": "00:00:23",
        "end": "00:00:41"
    }
    ]
}, {
    "id": 1,
    "averageScore": 0.11,
    "instances": [
    {
        "start": "00:00:13",
        "end": "00:00:21"
    }
    ]
}
]
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial: Analisar vídeos com os Serviços de Mídia do Azure](analyze-videos-tutorial-with-api.md)
