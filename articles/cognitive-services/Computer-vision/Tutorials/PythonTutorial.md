---
title: 'Tutorial: API da Pesquisa Visual Computacional no Python'
titlesuffix: Azure Cognitive Services
description: Saiba como usar a API da Pesquisa Visual Computacional com o Python usando notebooks do Jupyter. Visualize os resultados usando bibliotecas populares.
services: cognitive-services
author: KellyDF
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: tutorial
ms.date: 02/25/2017
ms.author: kefre
ms.openlocfilehash: 046250d3d2142badaac35490eff27bcac220fea9
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49344890"
---
# <a name="tutorial-computer-vision-api-python"></a>Tutorial: API da Pesquisa Visual Computacional no Python

Este tutorial mostra como usar a API da Pesquisa Visual Computacional no Python e como visualizar os resultados usando algumas bibliotecas populares. Use o Jupyter para executar o tutorial. Para saber como começar a usar os blocos de anotações interativos do Jupyter, veja: [Documentação do Jupyter](http://jupyter.readthedocs.io/en/latest/index.html). 

## <a name="open-the-tutorial-notebook-in-jupyter"></a>Abrir o tutorial do Jupyter Notebook 

1. Navegue para o [bloco de anotações de tutorial no GitHub](https://github.com/Microsoft/Cognitive-Vision-Python). 
2. Clique no botão verde para clonar ou baixar o tutorial. 
3. Abra um prompt de comando e acesse a pasta _Cognitive-Vision-Python-master\Jupyter Notebook_. 
4. Execute o comando **jupyter notebook** no prompt de comando. Isso iniciará o Jupyter.
5. Na janela do Jupyter, clique em _Computer Vision API Example.ipynb_ para abrir o bloco de anotações de tutorial 

## <a name="run-the-tutorial"></a>Executar o tutorial

Para usar esse bloco de anotações, você precisará de uma chave de assinatura para a API da Pesquisa Visual Computacional. Visite a [página Assinatura](https://azure.microsoft.com/try/cognitive-services/) para se inscrever. Na página “Entre”, use sua conta Microsoft para entrar e você poderá assinar e obter chaves gratuitas. Depois de concluir o processo de inscrição, cole a chave na seção de variáveis do bloco de anotações (reproduzido abaixo). Tanto a chave primária quanto a secundária funcionam. Coloque a chave entre aspas para transformá-la em uma cadeia de caracteres.

```python
# Variables

_url = 'https://westcentralus.api.cognitive.microsoft.com/vision/v1/analyses'
_key = None #Here you have to paste your primary key
_maxNumRetries = 10
```
