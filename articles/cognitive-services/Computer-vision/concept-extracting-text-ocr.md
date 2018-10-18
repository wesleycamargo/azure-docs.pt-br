---
title: Extraindo texto com OCR – Pesquisa Visual Computacional
titleSuffix: Azure Cognitive Services
description: Conceitos relacionados à extração de texto com o OCR (reconhecimento óptico de caracteres) usando a API da Pesquisa Visual Computacional.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.openlocfilehash: 52b6265722d5cfbf8baf54e1785ace627aa0892f
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49341660"
---
# <a name="extracting-text-with-ocr"></a>Extrair texto com OCR

A tecnologia OCR (reconhecimento óptico de caracteres) na Pesquisa Visual Computacional detecta conteúdo de texto em uma imagem e extrai o texto identificado em um fluxo de caracteres legível por computador. Você pode usar o resultado para pesquisa e várias outras finalidades, como registros médicos, segurança e serviços bancários. Ele detecta o idioma automaticamente. O OCR economiza tempo e fornece conveniência aos usuários, permitindo que eles tirem fotos do texto em vez de transcrevê-lo.

O OCR dá suporte a 25 idiomas. Esses idiomas são: árabe, chinês simplificado, chinês tradicional, tcheco, dinamarquês, holandês, inglês, finlandês, francês, alemão, grego, húngaro, italiano, japonês, coreano, norueguês, polonês, português, romeno, russo, sérvio (cirílico e latino) eslovaco, espanhol, sueco e turco.

Se necessário, o OCR corrige a rotação do texto reconhecido, em graus, ao redor do eixo horizontal da imagem. O OCR fornece as coordenadas de quadro de cada palavra, como mostrado na ilustração a seguir.

![Visão geral de OCR](./Images/vision-overview-ocr.png)

## <a name="ocr-requirements"></a>Requisitos de OCR

A Pesquisa Visual Computacional pode extrair texto usando OCR de imagens que atendem aos seguintes requisitos:

* A imagem deve ser apresentada no formato JPEG, PNG, GIF ou BMP
* O tamanho da imagem de entrada precisa ter entre 50 x 50 e 4200 x 4200 pixels


A imagem de entrada pode ser girada por um ângulo múltiplo de 90 graus mais um ângulo múltiplo de 40 graus.

## <a name="improving-ocr-accuracy"></a>Melhorando a precisão do OCR

A precisão do reconhecimento de texto depende da qualidade da imagem. Uma leitura imprecisa pode ser causada pelas seguintes situações:

* Imagens desfocadas.
* Texto manuscrito ou cursivo.
* Estilos de fonte artísticos.
* Tamanho de texto pequeno.
* Telas de fundo complexas, sombras, brilho sobre o texto ou distorção da perspectiva.
* Letras maiúsculas muito grandes ou ausentes no início das palavras
* Texto subscrito, sobrescrito ou tachado.

### <a name="ocr-limitations"></a>Limitações do OCR

Em fotografias em que o texto é dominante, falsos positivos podem vir de palavras reconhecidas parcialmente. Em algumas fotografias, especialmente as fotos sem texto, a precisão pode variar muito dependendo do tipo de imagem.

## <a name="next-steps"></a>Próximas etapas

Conheça os conceitos sobre o [reconhecimento de texto impresso e manuscrito](concept-recognizing-text.md).
