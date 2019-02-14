---
title: 'Tutorial: Executar operações de imagem – Python'
titlesuffix: Azure Cognitive Services
description: Saiba como usar a API da Pesquisa Visual Computacional com o Python usando notebooks do Jupyter. Visualize os resultados usando bibliotecas populares.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: tutorial
ms.date: 11/06/2018
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: afb937f1be780c35ef44023acfedeb38a6b6a877
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55875063"
---
# <a name="tutorial-computer-vision-api-python"></a>Tutorial: API da Pesquisa Visual Computacional Python

Este tutorial mostra como usar a API da Pesquisa Visual Computacional no Python e como visualizar os resultados usando bibliotecas populares. Você usará o Jupyter para executar o tutorial. Para saber como começar a usar os notebooks interativos do Jupyter, veja: [Documentação do Jupyter](https://jupyter.readthedocs.io/en/latest/index.html).

## <a name="prerequisites"></a>Pré-requisitos

- [Python 2.7+ ou 3.5+](https://www.python.org/downloads/)
- Ferramenta [pip](https://pip.pypa.io/en/stable/installing/)
- [Jupyter Notebook](https://jupyter.org/install) instalado

## <a name="open-the-tutorial-notebook-in-jupyter"></a>Abrir o tutorial do Jupyter Notebook 

1. Vá para o repositório GitHub de [Python para Visão Cognitiva](https://github.com/Microsoft/Cognitive-Vision-Python). 
2. Clique no botão verde para clonar ou baixar o repositório. 
3. Abra um prompt de comando e navegue até a pasta **Cognitive-Vision-Python\Jupyter Notebook**.
1. Verifique se você tem todas as bibliotecas necessárias executando o comando `pip install requests opencv-python numpy matplotlib` no prompt de comando.
1. Inicie o Jupyter executando o comando `jupyter notebook` no prompt de comando.
1. Na janela do Jupyter, clique em _API da Pesquisa Visual Computacional Example.ipynb_ para abrir o notebook do tutorial.

## <a name="run-the-tutorial"></a>Executar o tutorial

Para usar esse bloco de anotações, você precisará de uma chave de assinatura para a API da Pesquisa Visual Computacional. Visite a [página Assinatura](https://azure.microsoft.com/try/cognitive-services/) para se inscrever. Na página **Entrar**, use sua conta Microsoft para entrar e você poderá assinar e obter chaves gratuitas. Depois de concluir o processo de inscrição, cole a chave na seção `Variables` do notebook (reproduzida abaixo). Tanto a chave primária quanto a secundária funcionarão. Certifique-se de colocar a chave entre aspas para transformá-la em uma cadeia de caracteres.

Você também precisará garantir que o campo `_region` corresponde à região que corresponde à sua assinatura.

```python
# Variables
_region = 'westcentralus' #Here you enter the region of your subscription
_url = 'https://{}.api.cognitive.microsoft.com/vision/v2.0/analyze'.format(_region)
_key = None #Here you have to paste your primary key
_maxNumRetries = 10
```

Quando você executar o tutorial, você poderá adicionar imagens para analisar, de uma URL e do armazenamento local. O script exibirá as informações de análise e imagens no notebook.
