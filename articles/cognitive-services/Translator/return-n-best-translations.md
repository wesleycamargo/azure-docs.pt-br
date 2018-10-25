---
title: Retornar traduções N-Best - API de Tradução de Texto
titlesuffix: Azure Cognitive Services
description: Retorne as traduções N-Best com a API de Tradução de Texto da Microsoft.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: a0f4e4dd5cea9c3ea7b682e6372d2ffa8726e6ef
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49646466"
---
# <a name="how-to-return-n-best-translations"></a>Como retornar traduções N-Best

> [!NOTE]
> Este método foi preterido. Ele não está disponível na versão V3.0 da API de Tradução de Texto.

Os métodos GetTranslations() e GetTranslationsArray() da API do Microsoft Translator incluem um sinalizador Booliano "IncludeMultipleMTAlternatives" opcional.
O método retornará até maxTranslations alternativas em que o delta é fornecido na lista N-Best do mecanismo tradutor.

A assinatura é:

**Sintaxe**

| C# |
|:---|
| GetTranslationsResponse Microsoft.Translator.GetTranslations(appId, text, from, to, maxTranslations, options); |

**Parâmetros**

| Parâmetro | DESCRIÇÃO |
|:---|:---|
| appId | **Obrigatório** Se o cabeçalho de Autorização for usado, deixe o campo appid vazio, caso contrário, especifique uma cadeia de caracteres contendo "token de portador" + " " + token de acesso.|
| text | **Necessário** Uma cadeia de caracteres que representa o texto a ser traduzido. O tamanho do texto não deve exceder dez mil caracteres.|
| de | **Necessário** Uma cadeia de caracteres que representa o código de idioma do texto a ser traduzido. |
| para | **Necessário** Uma cadeia de caracteres que representa o código de idioma para o qual o texto será traduzido. |
| maxTranslations | **Necessário** Um inteiro que representa o número máximo de traduções para retornar. |
| options | **Opcional** Um objeto TranslateOptions que contém os valores listados abaixo. Eles são todos opcionais e padrão para as configurações mais comuns.

* Categoria: a única opção padrão com suporte é "geral".
* ContentType: a única opção padrão com suporte é "text/plain".
* Estado: o estado do usuário para ajudar a correlacionar solicitação e resposta. O mesmo conteúdo será retornado na resposta.
* IncludeMultipleMTAlternatives: o sinalizador para determinar se é preciso retornar mais de uma alternativa do mecanismo de MT. O padrão é false e inclui somente uma alternativa.

## <a name="ratings"></a>Classificações
As classificações são aplicadas da seguinte maneira: a melhor tradução automática tem uma classificação 5.
As alternativas de tradução (N-Best) geradas automaticamente têm uma classificação 0 e um grau de correspondência 100.

## <a name="number-of-alternatives"></a>Número de alternativas
O número de alternativas retornado é de até maxTranslations, mas pode ser menor.

## <a name="language-pairs"></a>Pacote de idiomas
Essa funcionalidade não está disponível para traduções entre chinês simplificado e tradicional, em ambas as direções. Ele está disponível para todos os outros pares de idiomas com suporte do Microsoft Translator.
