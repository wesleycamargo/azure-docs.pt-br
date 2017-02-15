---
title: "Amostra de dados em contêineres de blob do Azure, SQL Server e tabelas Hive | Microsoft Docs"
description: "Como explorar dados armazenados em vários ambientes do Azure."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 80a9dfae-e3a6-4cfb-aecc-5701cfc7e39d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2016
ms.author: fashah;garye;bradsev
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b62d42ccf9a62c0b7057a9c08cdd2d7793ffaedc


---
# <a name="a-nameheadingasample-data-in-azure-blob-containers-sql-server-and-hive-tables"></a><a name="heading"></a>Amostra de dados em contêineres de blob do Azure, SQL Server e tabelas Hive
Este documento leva a tópicos que abordam como obter amostras de dados armazenados em um dos três diferentes locais do Azure:

* **dados no contêiner de blobs do Azure** é realizada baixando-os programaticamente e realizando a amostragem com um exemplo de código Python.
* **dados do SQL Server** é realizada usando SQL e a linguagem de programação Python. 
* **dados da tabela do Hive** é realizada usando consultas de Hive.

O **menu** abaixo leva a tópicos que descrevem como obter amostras de dados de cada um desses ambientes de armazenamento do Azure. 

[!INCLUDE [cap-sample-data-selector](../../includes/cap-sample-data-selector.md)]

Essa tarefa de amostragem é uma etapa do [TDSP (Processo de Ciência de Dados de Equipe)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

## <a name="why-sample-data"></a>Por que fazer a amostragem de dados?
Se o conjunto de dados que você deseja analisar é grande, geralmente é uma boa ideia reduzir a amostra de dados para um tamanho menor, mas representativo e mais gerenciável. Isso facilita a compreensão de dados, exploração e engenharia de recursos. Sua função no Processo de Análise do Cortana é habilitar a rápida criação de protótipos de funções de processamento de dados e modelos de aprendizado de máquina.




<!--HONumber=Nov16_HO3-->


