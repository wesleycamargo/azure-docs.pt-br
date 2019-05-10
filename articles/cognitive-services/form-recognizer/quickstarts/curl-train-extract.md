---
title: 'Início Rápido: Treinar um modelo e extrair dados de formulário usando o cURL – Reconhecimento de Formulários'
titleSuffix: Azure Cognitive Services
description: Neste Início Rápido, você usará a API REST do Reconhecimento de Formulários com o cURL para treinar um modelo e extrair dados de formulários.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: quickstart
ms.date: 04/15/2019
ms.author: pafarley
ms.openlocfilehash: 1afe9239dcc3f5a24d2e950ec7b563bf53d1f04c
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65143228"
---
# <a name="quickstart-train-a-form-recognizer-model-and-extract-form-data-using-rest-api-with-curl"></a>Início Rápido: Treinar um modelo do Reconhecimento de Formulários e extrair dados de formulário usando a API REST com o cURL

Neste Início Rápido, você usará a API REST do Reconhecimento de Formulários com o cURL para treinar e pontuar formulários a fim de extrair pares chave-valor e tabelas.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Você obteve acesso à versão prévia de acesso limitado do Reconhecimento de Formulários. Para obter acesso à versão prévia, preencha e envie o formulário de [solicitação de acesso do Reconhecimento de Formulários dos Serviços Cognitivos](https://aka.ms/FormRecognizerRequestAccess). 
* É necessário ter [cURL](https://curl.haxx.se/windows/).
* É necessário ter uma chave de assinatura do Reconhecimento de Formulários. Siga as instruções em [Criar uma conta dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para assinar o Reconhecimento de Formulários e obter sua chave.
* É necessário ter um conjunto mínimo de cinco formulários do mesmo tipo. Você pode usar um [conjunto de dados de exemplo](https://go.microsoft.com/fwlink/?linkid=2090451) para este Início Rápido.

## <a name="train-a-form-recognizer-model"></a>Treinar um modelo do Reconhecimento de Formulários

Primeiro, você precisará de um conjunto de dados de treinamento. Você pode usar dados em um Blob do Azure ou seus próprios dados de treinamento locais. É necessário ter um mínimo de cinco formulários de exemplo (documentos PDF e/ou imagens) do mesmo tipo/da mesma estrutura dos dados de entrada principais. Como alternativa, é possível usar um único formulário vazio; o nome do arquivo do formulário inclui a palavra "vazio".

Para treinar um modelo do Reconhecimento de Formulários usando os documentos em seu contêiner de Blob do Azure, chame a API **Treinar** executando o comando do cURL abaixo. Antes de executar o comando, faça as seguintes alterações:

* Substitua `<Endpoint>` pelo ponto de extremidade que você obteve da chave de assinatura do Reconhecimento de Formulários. Encontre-o na guia de visão geral de recursos do Reconhecimento de Formulários.
* Substitua `<SAS URL>` pela URL de SAS (Assinatura de Acesso Compartilhado) de um contêiner do Armazenamento de Blobs do Azure no qual os dados de treinamento estão localizados.  
* Substitua `<subscription key>` por sua chave de assinatura.

```bash
curl -X POST "https://<Endpoint>/formrecognizer/v1.0-preview/custom/train" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"source\": \""<SAS URL>"\"}"
```

Você receberá uma resposta `200 (Success)` com a seguinte saída JSON:

```json
{
  "modelId": "59e2185e-ab80-4640-aebc-f3653442617b",
  "trainingDocuments": [
    {
      "documentName": "Invoice_1.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    },
    {
      "documentName": "Invoice_2.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    },
    {
      "documentName": "Invoice_3.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    },
    {
      "documentName": "Invoice_4.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    },
    {
      "documentName": "Invoice_5.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    }
  ],
  "errors": []
}
```

Anote o valor `"modelId"`; você precisará dele para as etapas a seguir.
  
## <a name="extract-key-value-pairs-and-tables-from-forms"></a>Extrair pares chave-valor e tabelas de formulários

Em seguida, você analisará um documento e extrairá pares chave-valor e tabelas dele. Chame a API **Modelar – Analisar** executando o comando do cURL abaixo. Antes de executar o comando, faça as seguintes alterações:

* Substitua `<Endpoint>` pelo ponto de extremidade que você obteve da chave de assinatura do Reconhecimento de Formulários. Encontre-o na guia **Visão geral** de recursos do Reconhecimento de Formulários.
* Substitua `<modelID>` pela ID do modelo recebida na etapa anterior do treinamento do modelo.
* Substitua `<path to your form>` pelo caminho do arquivo para o formulário.
* Substitua `<subscription key>` por sua chave de assinatura.
* Substitua `<file type>` pelo tipo de arquivo – os tipos compatíveis são PDF, imagem/JPEG e imagem/PNG.

```bash
curl -X POST "https://<Endpoint>/formrecognizer/v1.0-preview/custom/models/<modelID>/analyze" -H "Content-Type: multipart/form-data" -F "form=@\"<path to your form>\";type=application/<file type>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

### <a name="examine-the-response"></a>Examinar a resposta

Uma resposta bem-sucedida é retornada em JSON e representa as tabelas e os pares chave-valor extraídos do formulário.

```bash
{
  "status": "success",
  "pages": [
    {
      "number": 1,
      "height": 792,
      "width": 612,
      "clusterId": 0,
      "keyValuePairs": [
        {
          "key": [
            {
              "text": "Address:",
              "boundingBox": [
                57.4,
                683.1,
                100.5,
                683.1,
                100.5,
                673.7,
                57.4,
                673.7
              ]
            }
          ],
          "value": [
            {
              "text": "1 Redmond way Suite",
              "boundingBox": [
                57.4,
                671.3,
                154.8,
                671.3,
                154.8,
                659.2,
                57.4,
                659.2
              ],
              "confidence": 0.86
            },
            {
              "text": "6000 Redmond, WA",
              "boundingBox": [
                57.4,
                657.1,
                146.9,
                657.1,
                146.9,
                645.5,
                57.4,
                645.5
              ],
              "confidence": 0.86
            },
            {
              "text": "99243",
              "boundingBox": [
                57.4,
                643.4,
                85,
                643.4,
                85,
                632.3,
                57.4,
                632.3
              ],
              "confidence": 0.86
            }
          ]
        },
        {
          "key": [
            {
              "text": "Invoice For:",
              "boundingBox": [
                316.1,
                683.1,
                368.2,
                683.1,
                368.2,
                673.7,
                316.1,
                673.7
              ]
            }
          ],
          "value": [
            {
              "text": "Microsoft",
              "boundingBox": [
                374,
                687.9,
                418.8,
                687.9,
                418.8,
                673.7,
                374,
                673.7
              ],
              "confidence": 1
            },
            {
              "text": "1020 Enterprise Way",
              "boundingBox": [
                373.9,
                673.5,
                471.3,
                673.5,
                471.3,
                659.2,
                373.9,
                659.2
              ],
              "confidence": 1
            },
            {
              "text": "Sunnayvale, CA 87659",
              "boundingBox": [
                373.8,
                659,
                479.4,
                659,
                479.4,
                645.5,
                373.8,
                645.5
              ],
              "confidence": 1
            }
          ]
        }
      ],
      "tables": [
        {
          "id": "table_0",
          "columns": [
            {
              "header": [
                {
                  "text": "Invoice Number",
                  "boundingBox": [
                    38.5,
                    585.2,
                    113.4,
                    585.2,
                    113.4,
                    575.8,
                    38.5,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "34278587",
                    "boundingBox": [
                      38.5,
                      547.3,
                      82.8,
                      547.3,
                      82.8,
                      537,
                      38.5,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            },
            {
              "header": [
                {
                  "text": "Invoice Date",
                  "boundingBox": [
                    139.7,
                    585.2,
                    198.5,
                    585.2,
                    198.5,
                    575.8,
                    139.7,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "6/18/2017",
                    "boundingBox": [
                      139.7,
                      546.8,
                      184,
                      546.8,
                      184,
                      537,
                      139.7,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            },
            {
              "header": [
                {
                  "text": "Invoice Due Date",
                  "boundingBox": [
                    240.5,
                    585.2,
                    321,
                    585.2,
                    321,
                    575.8,
                    240.5,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "6/24/2017",
                    "boundingBox": [
                      240.5,
                      546.8,
                      284.8,
                      546.8,
                      284.8,
                      537,
                      240.5,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            },
            {
              "header": [
                {
                  "text": "Charges",
                  "boundingBox": [
                    341.3,
                    585.2,
                    381.2,
                    585.2,
                    381.2,
                    575.8,
                    341.3,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "$56,651.49",
                    "boundingBox": [
                      387.6,
                      546.4,
                      437.5,
                      546.4,
                      437.5,
                      537,
                      387.6,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            },
            {
              "header": [
                {
                  "text": "VAT ID",
                  "boundingBox": [
                    442.1,
                    590,
                    474.8,
                    590,
                    474.8,
                    575.8,
                    442.1,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "PT",
                    "boundingBox": [
                      447.7,
                      550.6,
                      460.4,
                      550.6,
                      460.4,
                      537,
                      447.7,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            }
          ]
        }
      ]
    }
  ],
  "errors": []
}
```

## <a name="next-steps"></a>Próximas etapas

Neste guia, você usou as APIs REST do Reconhecimento de Formulários com o cURL para treinar um modelo e executá-lo em um caso de exemplo. Em seguida, confira a documentação de referência para explorar a API de Reconhecimento de Formulários de forma mais aprofundada.

> [!div class="nextstepaction"]
> [Documentação de referência da API REST](https://aka.ms/form-recognizer/api)
