---
title: Tutorial da API da Pesquisa Visual Computacional no Python | Microsoft Docs
description: Saiba como usar a API da Pesquisa Visual Computacional com o Python usando blocos de anotações do Jupyter nos Serviços Cognitivos da Microsoft. Visualize os resultados usando bibliotecas populares.
services: cognitive-services
author: KellyDF
manager: corncar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 02/25/2017
ms.author: kefre
ms.openlocfilehash: a093c2d066e70a8daf1fe1cd33ccf794ecb196af
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35363683"
---
# <a name="computer-vision-api-python-tutorial"></a>Tutorial da API da Pesquisa Visual Computacional no Python

Este tutorial mostra como usar a API da Pesquisa Visual Computacional no Python e como visualizar os resultados usando algumas bibliotecas populares. Use o Jupyter para executar o tutorial. Para saber como começar a usar os blocos de anotações interativos do Jupyter, veja: [Documentação do Jupyter](http://jupyter.readthedocs.io/en/latest/index.html). 

### <a name="opening-the-tutorial-notebook-in-jupyter"></a>Abrindo o bloco de anotações de tutorial no Jupyter 

1. Navegue para o [bloco de anotações de tutorial no GitHub](https://github.com/Microsoft/Cognitive-Vision-Python). 
2. Clique no botão verde para clonar ou baixar o tutorial. 
3. Abra um prompt de comando e acesse a pasta _Cognitive-Vision-Python-master\Jupyter Notebook_. 
4. Execute o comando **jupyter notebook** no prompt de comando. Isso iniciará o Jupyter.
5. Na janela do Jupyter, clique em _Computer Vision API Example.ipynb_ para abrir o bloco de anotações de tutorial 

### <a name="running-the-tutorial"></a>Executando o tutorial

Para usar esse bloco de anotações, você precisará de uma chave de assinatura para a API da Pesquisa Visual Computacional. Visite a [página Assinatura](https://azure.microsoft.com/try/cognitive-services/) para se inscrever. Na página “Entre”, use sua conta Microsoft para entrar e você poderá assinar e obter chaves gratuitas. Depois de concluir o processo de inscrição, cole a chave na seção de variáveis do bloco de anotações (reproduzido abaixo). Tanto a chave primária quanto a secundária funcionam. Coloque a chave entre aspas para transformá-la em uma cadeia de caracteres.

```python
# Variables

_url = 'https://westcentralus.api.cognitive.microsoft.com/vision/v1/analyses'
_key = None #Here you have to paste your primary key
_maxNumRetries = 10
```
