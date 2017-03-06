---
title: "Adicionar o Microsoft Translator aos aplicativos lógicos | Microsoft Docs"
description: "Visão geral do conector do Microsoft Translator com parâmetros da API REST"
services: 
suite: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: da782baf-8bf8-4973-8238-e469865f5328
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: add307420b9e16a60b0e57b0ba08188b2509978e
ms.lasthandoff: 02/16/2017


---
# <a name="get-started-with-the-microsoft-translator-connector"></a>Introdução ao conector do Microsoft Translator
Conecte-se ao Microsoft Translator para traduzir texto, detectar um idioma e muito mais. Com o Microsoft Translator, você pode: 

* Compile seu fluxo de negócios baseado nos dados obtidos do Microsoft Translator. 
* Use ações para traduzir texto, detectar um idioma e muito mais. Essas ações obtém uma resposta e disponibilizam a saída para outras ações. Por exemplo, quando um novo arquivo é criado no Dropbox, você pode traduzir o texto contido no arquivo para outro idioma usando o Microsoft Translator.

Para adicionar uma operação a aplicativos lógicos, confira [Criar um aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Gatilhos e ações
O Microsoft Translator inclui as seguintes ações. Não há nenhum gatilho.

| Gatilhos | Ações |
| --- | --- |
| Nenhum |<ul><li>Detectar o idioma</li><li>Texto em fala</li><li>Traduzir o texto</li><li>Obter linguagens</li><li>Obter idiomas de fala</li></ul> |

Todos os conectores dão suporte a dados nos formatos JSON e XML.

## <a name="create-a-connection-to-microsoft-translator"></a>Criar uma conexão com o Microsoft Translator
> [!INCLUDE [Steps to create a connection to Microsoft Translator](../../includes/connectors-create-api-microsofttranslator.md)]
> 
> 

## <a name="swagger-rest-api-reference"></a>Referência da API REST do Swagger
Aplica-se à versão: 1.0.

### <a name="detect-language"></a>Detectar o idioma
Detecta o idioma de origem de determinado texto.  
```GET: /Detect```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| query |string |sim |query |Nenhum |O texto cujo idioma será identificado |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na Operação. |

### <a name="text-to-speech"></a>Texto em fala
Converte determinado texto em fala como uma transmissão de áudio no formato wave.  
```GET: /Speak```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| query |string |sim |query |Nenhum |Texto a ser convertido |
| idioma |string |sim |query |nenhum |Código de idioma para gerar fala (exemplo: “en-us”) |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na Operação. |

### <a name="translate-text"></a>Traduzir o texto
Traduz o texto para um idioma especificado usando o Microsoft Translator.  
```GET: /Translate```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| query |string |sim |query |Nenhum |Texto a ser traduzido |
| languageTo |string |sim |query |Nenhum |Código do idioma de destino (exemplo: “fr”) |
| languageFrom |string |não |query |Nenhum |Idioma de origem; se não tiver sido fornecido, o Microsoft Translator tentará detectá-lo automaticamente. (exemplo: en) |
| categoria |string |não |query |geral |Categoria de tradução (padrão: “geral”) |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na Operação. |

### <a name="get-languages"></a>Obter linguagens
Recupera todos os idiomas com suporte do Microsoft Translator.  
```GET: /TranslatableLanguages```

Não existem parâmetros para esta chamada. 

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na Operação. |

### <a name="get-speech-languages"></a>Obter idiomas de fala
Recupera os idiomas disponíveis para a sintetização de voz.  
```GET: /SpeakLanguages``` 

Não existem parâmetros para esta chamada.

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na operação. |

## <a name="object-definitions"></a>Definições de objeto
#### <a name="language-language-model-for-microsoft-translator-translatable-languages"></a>Idioma: modelo de idioma para os idiomas traduzíveis do Microsoft Translator
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| Código |string |não |
| Nome |string |não |

## <a name="next-steps"></a>Próximas etapas
[Crie um aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md).

Volte para a [Lista de APIs](apis-list.md).

<!--References-->
[5]: https://datamarket.azure.com/developer/applications/
[6]: ./media/connectors-create-api-microsofttranslator/register-your-application.png

