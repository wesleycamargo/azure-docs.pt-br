---
title: Personalização da Tradução - API de Tradução de Texto
titlesuffix: Azure Cognitive Services
description: Use o Hub do Microsoft Translator para compilar seu próprio sistema de tradução automática usando seu estilo e sua terminologia preferenciais.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 05/10/2018
ms.author: v-jansko
ms.openlocfilehash: bbfffe948080c87feaa8c9f0a46ef9afd90eae96
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51229672"
---
# <a name="customize-your-text-translations"></a>Personalizar suas traduções de texto

A visualização do Microsoft Custom Translator é um recurso do serviço Microsoft Translator, que permite aos usuários personalizar a tradução avançada de máquina neural do Microsoft Translator ao traduzir texto usando a API de texto do tradutor (somente versão 3).

O recurso também pode ser usado para personalizar a tradução de fala quando usado com a [versão prévia de Fala de Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/speech-service/).

## <a name="custom-translator"></a>Tradutor personalizado

Com o Tradutor Personalizado, você pode compilar sistemas de tradução neural que entendam a terminologia usada na sua própria empresa e setor. O sistema de tradução personalizada então se integrará aos aplicativos, fluxos de trabalho e sites existentes.

### <a name="how-does-it-work"></a>Como ele funciona?

Use seus documentos traduzidos anteriormente (panfletos, páginas da Web, documentação etc.) para criar um sistema de tradução que reflita o estilo e a terminologia específica do domínio melhor do que um sistema de tradução genérico. Os usuários podem fazer upload de documentos TMX XLIFF, TXT, DOCX e XLSX.  

O sistema também aceita dados que são paralelos no nível do documento, mas ainda não estão alinhados no nível de frase. Se os usuários tiverem acesso às versões do mesmo conteúdo em vários idiomas, mas em documentos separados, o Tradutor Personalizado conseguirá criar automaticamente a correspondência de frases entre os documentos.  O sistema também pode usar dados monolíngues em um ou ambos os idiomas para complementar os dados de treinamento paralelos para melhorar as traduções.

O sistema personalizado então fica disponível por meio de uma chamada regular à API de Tradução de Texto da Microsoft usando o parâmetro de categoria.

Dado o tipo e a quantidade apropriados de dados de treinamento, não é incomum esperar ganhos entre 5 e 10, ou até mais pontos BLEU na qualidade de tradução usando o Tradutor Personalizado.

Para obter mais detalhes sobre os vários níveis de personalização com base nos dados disponíveis, veja o [Guia do usuário do Tradutor Personalizado](https://aka.ms/CustomTranslatorDocs).


## <a name="microsoft-translator-hub"></a>Hub do Microsoft Translator

O Hub do Microsoft Translator herdado pode ser usado para traduzir a tradução automática estatística. [Saiba mais](https://www.microsoft.com/en-us/translator/hub.aspx)

## <a name="custom-translator-versus-hub"></a>Tradutor Personalizado versus Hub

|   | **Hub** | **Tradutor personalizado**|
|:-----|:----:|:----:|
|Status do recurso de personalização   | Disponibilidade geral  | Visualização |
| Versão da API de texto  | Somente V2   | Somente V3 |
| Personalização de SMT | SIM   | Não  |
| Personalização de NMT | Não     | SIM |
| Nova personalização de serviços de Fala unificados | Não     | SIM |
| [Sem rastreamento](https://www.aka.ms/notrace) | SIM  | SIM |

## <a name="collaborative-translations-framework"></a>Estrutura de traduções colaborativas

> [!NOTE]
> A partir de 1º de fevereiro de 2018 AddTranslation() e AddTranslationArray() não estarão mais disponíveis para uso com a API de Tradução de Texto V2.0. Esses métodos falharão e nada será gravado. A API de Tradução de Texto V3.0 não é compatível com esses métodos.

>Funcionalidade semelhante está disponível na API do Hub do Translator. Veja [https://hub.microsofttranslator.com/swagger](https://hub.microsofttranslator.com/swagger).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Configurar um sistema de idioma personalizado usando o Tradutor Personalizado](https://aka.ms/CustomTranslatorDocs)
