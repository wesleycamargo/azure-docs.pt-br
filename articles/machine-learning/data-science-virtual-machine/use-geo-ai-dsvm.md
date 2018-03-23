---
title: Usar a Máquina Virtual de Ciência de Dados de Inteligência Artificial Geográfica – Azure | Microsoft Docs
description: Como usar uma Máquina Virtual de IA Geográfica no Azure.
keywords: aprendizado profundo, IA, ferramentas de ciência de dados, máquina virtual de ciência de dados, análise Geoespacial
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: gokuma; bradsev;
ms.openlocfilehash: f87b56ad2b19b10bca1e797f0a5aef5c2a92a5dd
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2018
---
# <a name="using-the-geo-artificial-intelligence-data-science-virtual-machine"></a>Usar a Máquina Virtual de Ciência de Dados de Inteligência Artificial Geográfica

Use Máquina Virtual da Ciência de Dados de Inteligência Artificial Geográfica para buscar dados para análise, executar a disputa de dados e criar modelos para aplicativos IA que consomem informações geoespaciais. Depois de ter provisionado a máquina virtual de ciência de dados de inteligência artificial geográfica e conectado no ArcGIS Pro com sua conta do ArcGIS, você pode começar a interagir com a área de trabalho do ArcGIS e ArcGis online. Você também pode acessar o ArcGIS de interfaces Python e uma ponte de linguagem R pré-configurada na máquina virtual de ciência de dados geográficos. Para criar aplicativos de IA sofisticados, combine-os com o aprendizado de máquina às estruturas de aprendizado profundo outro softwares de ciência de dados disponíveis na máquina virtual de ciência de dados.  


## <a name="configuration-details"></a>Detalhes da configuração

A Biblioteca do Python, [arcpy](http://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm), que é usada para a interface com o ArcGIS está instalada no ambiente raiz do conda da máquina virtual de Ciência de Dados encontrado em ```c:\anaconda```. 

- Se você estiver executando o Python em um prompt de comando, execute ```activate``` para ativar no ambiente conda raiz Python. 
- Se você estiver usando um bloco de anotações do IDE ou Jupyter, você pode selecionar o ambiente ou kernel para garantir que está no ambiente conda correto. 

A ponte de R para ArcGIS é instalada como uma biblioteca R chamada [arcgisbinding](https://github.com/R-ArcGIS/r-bridge) na instância independente do servidor Microsoft R localizado em```C:\Program Files\Microsoft\ML Server\R_SERVER```. Visual Studio, RStudio e Jupyter já são pré-configurados para usar esse ambiente R e ter acesso à ```arcgisbinding``` biblioteca R. 


## <a name="geo-ai-data-science-vm-samples"></a>Amostras de máquina virtual de ciência de dados de IA Geográfica

Além das amostras baseadas em ML e estrutura de aprendizado profundo da Máquina Virtual de Ciência de dados, um conjunto de amostras geoespacial também é fornecido como parte da Máquina Virtual de Ciência de Dados de IA Geográfica. Esses exemplos podem ajudar a impulsionar o desenvolvimento de aplicativos da IA usando dados geoespaciais e o software do ArcGIS. 


1. [Iniciando as análises geoespaciais com o Python](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/Python%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb): uma amostra introdutória mostrando como trabalhar com dados geoespaciais usando a interface do Python para ArcGIS fornecidos à biblioteca [arcpy](http://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm). Também mostra como você pode combinar a máquina tradicional de aprendizado com dados geoespaciais e visualizar o resultado em um mapa no ArcGIS. 

2. [Iniciando as análises geoespaciais com R](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/R%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb): uma amostra introdutória mostrando como trabalhar com dados geoespaciais usando a interface do Python para ArcGIS fornecidos à biblioteca [arcgisbinding](https://github.com/R-ArcGIS/r-bridge). 

3. [Classificação do uso de terra em nível de pixel](https://github.com/Azure/pixel_level_land_classification): um tutorial que mostra como criar um modelo de rede neural profundo que aceita uma imagem aérea como entrada e retorna um rótulo de cobertura de terra. Exemplos de rótulos de cobertura de terra são "floresta" ou "água". O modelo retorna um rótulo para cada pixel da imagem. O modelo é criado usando a estrutura de aprendizado profundo [Cognitive Toolkit (CNTK)](https://www.microsoft.com/en-us/cognitive-toolkit/) de código aberto da Microsoft. O exemplo também mostra como escalar horizontalmente o treinamento da [IA do Lote do Azure](https://docs.microsoft.com/azure/batch-ai/) e consumir as previsões do modelo no software ArcGIS Pro. 


## <a name="next-steps"></a>Próximas etapas

Exemplos adicionais que usam a máquina virtual de ciência de dados estão disponíveis aqui:

* [Exemplos](dsvm-samples-and-walkthroughs.md)

