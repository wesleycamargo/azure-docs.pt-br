---
title: Regiões de serviço de fala | Microsoft Docs
description: Referência para regiões do serviço de fala.
services: cognitive-services
author: mahilleb-msft
manager: wolmfa61
ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 06/27/2018
ms.author: mahilleb
ms.openlocfilehash: a201cc043f673e2285ea48950804d97b96f881ed
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37054864"
---
# <a name="regions-and-endpoints-of-the-speech-service"></a>Regiões e pontos de extremidade do serviço de fala

> [!NOTE]
> Nomes de região no [fala SDK](speech-sdk.md) corresponde a primeira parte do domínio dos pontos de extremidade indicados abaixo.
> Por exemplo, use `westus` para especificar a região Oeste dos EUA no SDK de fala.

## <a name="speech-to-text"></a>Conversão de fala em texto

[!include[](includes/endpoints-speech-to-text.md)]

## <a name="text-to-speech"></a>Texto em fala

[!include[](includes/endpoints-text-to-speech.md)]

## <a name="authentication"></a>Autenticação

[!include[](includes/endpoints-token-service.md)]

Consulte [aqui](rest-apis.md#authentication) para obter detalhes de como obter e atualizar tokens de autorização.

## <a name="language-understanding-speech-sdk-only"></a>Compreensão de idioma (somente SDK de fala)

Regiões para o serviço de compreensão de idioma são listados [aqui](/azure/cognitive-services/luis/luis-reference-regions).
No Speech SDK, especifique essas regiões pela primeira parte do nome do domínio do terminal (por exemplo, `westus`).
