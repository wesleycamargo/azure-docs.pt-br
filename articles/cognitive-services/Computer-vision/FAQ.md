---
title: Perguntas frequentes - Pesquisa Visual Computacional
titlesuffix: Azure Cognitive Services
description: Obtenha respostas a perguntas frequentes sobre a API do Computer Vision nos Serviços Cognitivos do Azure.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 01/26/2017
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: e1bc40f10547eb6c79a41cab8a3ac5fd2f500415
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55874066"
---
# <a name="computer-vision-api-frequently-asked-questions"></a>Perguntas frequentes da API da Pesquisa Visual Computacional

### <a name="if-you-cant-find-answers-to-your-questions-in-this-faq-try-asking-the-computer-vision-api-community-on-stackoverflowhttpsstackoverflowcomquestionstaggedproject-oxfordormicrosoft-cognitive-or-contact-help-and-support-on-uservoicehttpscognitiveuservoicecom"></a>Se você não conseguir encontrar respostas para suas perguntas nesta FAQ, tente perguntar à comunidade da API da Pesquisa Visual Computacional em [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) ou entre em contato com Ajuda e Suporte em [UserVoice](https://cognitive.uservoice.com/)

-----

**Pergunta**: *Posso treinar API da Pesquisa Visual Computacional para usar marcas personalizadas?  Por exemplo, gostaria de alimentar imagens de raça de gatos para 'treinar' o AI e receber o valor de raça em uma solicitação de AI.*

**Resposta**: Essa função não está disponível no momento. No entanto, nossos engenheiros estão trabalhando para trazer essa funcionalidade para a Pesquisa Visual Computacional.

-----

**Pergunta**: *A Pesquisa Visual Computacional pode ser usada localmente sem uma conexão de internet?*

**Resposta**: No momento não oferecemos uma solução local ou no local.

-----

**Pergunta**: *Quais idiomas são compatíveis com a Pesquisa Visual Computacional?*

**Resposta**: As linguagens com suporte incluem:

| | | Idiomas com suporte | | |
|---------------- |------------------ |------------------ |--------------------------- |--------------------
| Dinamarquês (da-DK)  | Holandês (nl-NL)     | Inglês           | Finlandês (fi-FI)            |Francês (fr-FR)
| Alemão (de-DE)  | Grego (el-GR)     | Húngaro (hu-HU) | Italiano (it-IT)            | Japonês (ja-JP)
| Coreano (ko-KR)  | Norueguês (nb-NO) | Polonês (pl-PL)    | Português (pt-BR) (pt-PT) | Russo (ru-RU)
| Espanhol (es-ES)   | Sueco (sv-SV)     | Turco (tr-TR)   |                            |

-----

**Pergunta**: *A Pesquisa Visual Computacional pode ser usada para ler placas de automóvel?*

**Resposta**: A API da Pesquisa Visual Computacional oferece boa detecção de texto com OCR, mas ele não é otimizado para placas de automóveis no momento. Estamos constantemente tentando melhorar nossos serviços e adicionamos OCR para reconhecimento de placas automaticamente à nossa lista de solicitações de recursos.

-----

**Pergunta:** *Quais idiomas são suportados para o reconhecimento de manuscrito?*

**Resposta**: Atualmente, há suporte apenas para inglês.

-----

**Pergunta**: *Quais tipos de gravação de superfícies têm suporte para o reconhecimento de manuscrito?*

**Resposta**: A tecnologia funciona com tipos diferentes de superfícies, incluindo quadros de comunicações, papel branco e notas amarelas.

-----

**Pergunta**: *Quanto tempo demora a operação de reconhecimento de manuscrito?*

**Resposta**: A quantidade de tempo que demora depende do tamanho do texto. Para textos mais longos, ele poderá demorar vários segundos. Portanto, após a operação de reconhecer texto manuscrito, talvez seja necessário aguardar antes que você possa recuperar os resultados usando a operação Obter resultados de operação de texto manuscrito.

-----

**Pergunta**: *Como faz a tecnologia reconhecimento de manuscrito identifica o texto que foi inserido usando um cursor no meio de uma linha?*

**Resposta**: Esse texto é retornado como uma linha separada pela operação de reconhecimento de manuscrito.

-----

**Pergunta**: *Como a tecnologia de reconhecimento de manuscrito identifica palavras ou linhas riscadas?*

**Resposta**: Se as palavras estão riscadas com várias linhas de forma irreconhecível, a operação de reconhecimento de manuscrito não as identifica. No entanto, se as palavras estão riscadas com uma única linha, essa linha é tratada como ruído e as palavras ainda escolhidas pela operação de reconhecimento de manuscrito.

-----

**Pergunta**: *Quais orientações de texto têm suporte para a tecnologia de reconhecimento de manuscrito?*

**Resposta**: O texto orientado em ângulos de até cerca de 30 graus para 40 graus pode ser identificado pela operação de reconhecimento de manuscrito.

-----
