---
title: Reconhecer texto impresso/manuscritos, pesquisa Visual computacional
titleSuffix: Azure Cognitive Services
description: Conceitos relacionados ao reconhecimento de texto impresso e manuscrito em imagens usando a API da Pesquisa Visual Computacional.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: f7fd13b0b6df0b07543216e3c612520e528c1176
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60367993"
---
# <a name="recognize-printed-and-handwritten-text"></a>Reconhecer texto impresso e manuscrito

Visão do computador fornece um número de serviços que detecte e extraia textos manuscritos ou impressos que aparece nas imagens. Isso é útil em uma variedade de cenários, como anotações, registros médicos, segurança e transações bancárias. O seguintes três seções detalhes três diferentes reconhecimento de texto APIs, todos otimizados para diferentes casos de uso.

## <a name="read-api"></a>API de leitura

A API de leitura detecta conteúdo de texto em uma imagem usando nossos modelos mais recentes de reconhecimento e converte o texto identificado em um fluxo de caracteres legível por máquina. Ele é otimizado para imagens com uso intenso de texto (como documentos que foram verificados digitalmente) e para imagens com muito ruído visual. Ele executa de forma assíncrona como documentos maiores podem levar vários minutos para retornar um resultado.

A operação de leitura mantém os agrupamentos de linha original de palavras reconhecidas em sua saída. Cada linha é fornecido com as coordenadas da caixa de delimitação e cada palavra dentro da linha também tem seus próprio coordenadas. Se uma palavra foi reconhecida com confiança baixa, essas informações são transmitidas também. Consulte a [documentos de referência de API de leitura](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) para saber mais.

> [!NOTE]
> Esse recurso está atualmente em visualização e está disponível somente para texto em inglês.

### <a name="image-requirements"></a>Requisitos de imagem

A API de leitura funcionam com imagens que atendem aos seguintes requisitos:

- A imagem deve ser apresentada no formato JPEG, PNG, BMP, PDF ou TIFF.
- As dimensões da imagem devem estar entre 50 x 50 e 4200 x 4200 pixels. Páginas PDF devem ser 17 x 17 polegadas ou menos.
- O tamanho do arquivo da imagem deve ser menor que 20 megabytes (MB).

### <a name="limitations"></a>Limitações

Se você estiver usando uma assinatura de camada gratuita, a API de leitura só processará as duas primeiras páginas de um documento PDF ou TIFF. Com uma assinatura paga, ele processa até 200 páginas. Observe também que a API detectará um máximo de 300 linhas por página.

## <a name="ocr-optical-character-recognition-api"></a>OCR (reconhecimento óptico de caracteres) API

API de reconhecimento óptico de caracteres (OCR da pesquisa Visual computacional) é semelhante à API de leitura, mas ele executa de forma síncrona e não é otimizado para documentos grandes. Ele usa um modelo de reconhecimento anterior, mas funciona com mais linguagens; ver [suporte ao idioma](language-support.md#text-recognition) para obter uma lista completa dos idiomas com suporte.

Se necessário, o OCR corrige a rotação do texto reconhecido, retornando o deslocamento de rotação em graus do eixo horizontal da imagem. OCR também fornece as coordenadas de quadro de cada palavra, como mostrado na ilustração a seguir.

![Uma imagem que está sendo girada e seu texto sendo leia e delineados](./Images/vision-overview-ocr.png)

Consulte a [documentos de referência de OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) para saber mais.

### <a name="image-requirements"></a>Requisitos de imagem

A API de OCR funciona nas imagens que atendem aos seguintes requisitos:

* A imagem deve ser apresentada no formato JPEG, PNG, GIF ou BMP.
* O tamanho da imagem de entrada deve estar entre 50 x 50 e 4200 x 4200 pixels.
* O texto na imagem pode ser girado por um ângulo múltiplo de 90 graus mais um ângulo múltiplo de 40 graus.

### <a name="limitations"></a>Limitações

Em fotografias em que o texto é dominante, falsos positivos podem vir de palavras reconhecidas parcialmente. Em alguns fotografias, especialmente as fotos sem qualquer texto, a precisão pode variar dependendo do tipo de imagem.

## <a name="recognize-text-api"></a>Reconhecer texto API

> [!NOTE]
> A API de texto reconhecer estão sendo substituída pela API de leitura. A API de leitura tem recursos semelhantes e é atualizada para lidar com arquivos de várias páginas, TIFF e PDF.

A API de texto reconhecer é semelhante ao OCR, mas ele executa de forma assíncrona e usa modelos de reconhecimento atualizado. Consulte a [documentos de referência de API de texto reconhecer](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) para saber mais.

### <a name="image-requirements"></a>Requisitos de imagem

A API de texto reconhecer funciona com as imagens que atendem aos seguintes requisitos:

- A imagem deve ser apresentada no formato JPEG, PNG ou BMP.
- As dimensões da imagem devem estar entre 50 x 50 e 4200 x 4200 pixels.
- O tamanho do arquivo da imagem deve ser menor que 4 megabytes (MB).

## <a name="improve-results"></a>Melhorar resultados

A precisão das operações de reconhecimento de texto depende a qualidade das imagens. Os seguintes fatores podem causar uma leitura imprecisa:

* Imagens desfocadas.
* Texto manuscrito ou cursivo.
* Estilos de fonte artísticos.
* Tamanho de texto pequeno.
* Telas de fundo complexas, sombras, brilho sobre o texto ou distorção da perspectiva.
* Letras maiusculas muito grandes ou estão ausentes no início das palavras.
* Texto subscrito, sobrescrito ou tachado.

## <a name="next-steps"></a>Próximas etapas

Siga as [extrair texto impresso (OCR)](./quickstarts/csharp-print-text.md) guia de início rápido para implementar o reconhecimento de texto em um simples C# aplicativo.
