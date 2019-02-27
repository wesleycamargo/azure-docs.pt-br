---
title: Extrair texto com reconhecimento óptico de caracteres (OCR) – Pesquisa Visual Computacional
titleSuffix: Azure Cognitive Services
description: Conceitos relacionados à extração de texto com o OCR (reconhecimento óptico de caracteres) usando a API da Pesquisa Visual Computacional.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: deb73eb9fdd6879a5fbe1fed820bf92b2d627b65
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/15/2019
ms.locfileid: "56310433"
---
# <a name="extract-text-with-optical-character-recognition"></a>Extrair texto com reconhecimento óptico de caracteres

O recurso de reconhecimento óptico de caracteres (OCR) da Pesquisa Visual Computacional detecta conteúdo de texto em uma imagem e converte o texto identificado em um fluxo de caracteres legível por computador. Você pode usar o resultado para várias finalidades, como pesquisa, registros médicos, segurança e serviços bancários. 

O OCR dá suporte a 25 idiomas: árabe, chinês simplificado, chinês tradicional, tcheco, dinamarquês, holandês, inglês, finlandês, francês, alemão, grego, húngaro, italiano, japonês, coreano, norueguês, polonês, português, romeno, russo, sérvio (cirílico e latino) eslovaco, espanhol, sueco e turco. O OCR detecta automaticamente o idioma do texto detectado.

Se necessário, o OCR corrige a rotação do texto reconhecido, retornando o deslocamento de rotação em graus do eixo horizontal da imagem. O OCR também fornece as coordenadas de quadro de cada palavra, como mostrado na ilustração a seguir.

![Um diagrama representando uma imagem sendo rotacionada e o texto sendo lido e delineado](./Images/vision-overview-ocr.png)

## <a name="image-requirements"></a>Requisitos de imagem

A Pesquisa Visual Computacional pode extrair texto usando OCR de imagens que atendem aos seguintes requisitos:

* A imagem deve ser apresentada no formato JPEG, PNG, GIF ou BMP
* O tamanho da imagem de entrada precisa ter entre 50 x 50 e 4200 x 4200 pixels
* O texto na imagem pode ser girado por um ângulo múltiplo de 90 graus mais um ângulo múltiplo de 40 graus.

## <a name="improving-ocr-accuracy"></a>Melhorando a precisão do OCR

A precisão do reconhecimento de texto depende da qualidade da imagem. Uma leitura imprecisa pode ser causada pelo seguinte:

* Imagens desfocadas.
* Texto manuscrito ou cursivo.
* Estilos de fonte artísticos.
* Tamanho de texto pequeno.
* Telas de fundo complexas, sombras, brilho sobre o texto ou distorção da perspectiva.
* Letras maiúsculas muito grandes ou ausentes no início das palavras
* Texto subscrito, sobrescrito ou tachado.

### <a name="ocr-limitations"></a>Limitações do OCR

Em imagens em que o texto é dominante, falsos positivos podem vir de palavras reconhecidas parcialmente. Em algumas imagens, especialmente, em fotos sem nenhum texto, a precisão pode variar muito dependendo do tipo de imagem.

## <a name="next-steps"></a>Próximas etapas

Confira a [Documentação de referência sobre OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) para saber mais.
