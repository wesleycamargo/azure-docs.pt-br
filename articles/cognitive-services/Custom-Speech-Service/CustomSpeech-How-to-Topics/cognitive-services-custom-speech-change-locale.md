---
title: Localidades e idiomas compatíveis – Serviço de Fala Personalizado
titlesuffix: Azure Cognitive Services
description: Visão geral dos idiomas com suporte do Serviço de Fala Personalizado nos Serviços Cognitivos.
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: conceptual
ms.date: 02/08/2017
ms.author: panosper
ROBOTS: NOINDEX
ms.openlocfilehash: 34af6673689244364ab3a1fe3f2a6ab056ea8598
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47223948"
---
# <a name="supported-locales-in-custom-speech-service"></a>Localidades com suporte no Serviço de Fala Personalizado
Atualmente, o Serviço de Fala Personalizado dá suporte para a personalização de modelos nas seguintes localidades:

| Tipo de modelo | Suporte ao idioma |
|----|-----|
| Modelos acústicos | Inglês dos EUA (en-US) |
| Modelos de idioma | Inglês dos EUA (en-US), chinês (zh-CN) |

Embora a personalização do Modelo acústico tenha suporte apenas em inglês dos EUA, a importação de dados acústicos em Chinês tem suporte para fins de teste offline de Modelos de Idioma Chinês personalizados.

A localidade apropriada deve ser selecionada antes de executar qualquer ação. A localidade atual é indicada no título da tabela em todas as páginas de dados, modelo e implantação. Para alterar a localidade, clique no botão "Alterar Localidade", localizado abaixo do título da tabela. Isso direcionará para uma página de confirmação da localidade. Clique em “OK” para retornar à tabela.

Você deverá seguir as próximas etapas
* Saiba [como criar um modelo acústico personalizado](cognitive-services-custom-speech-create-acoustic-model.md) para melhorar a precisão do reconhecimento
* Saiba [como criar um modelo de idioma personalizado](cognitive-services-custom-speech-create-language-model.md) para melhorar a taxa de reconhecimento
* Siga as [diretrizes de transcrição](cognitive-services-custom-speech-transcription-guidelines.md) para preparar seus dados
