---
title: "Combinações compatíveis de ambientes de execução e de dados para Preparação de Dados do Azure Machine Learning | Microsoft Docs"
description: "Este documento fornece uma lista completa de combinações compatíveis de diferentes tempos de execução e fontes de dados para Preparação de Dados do Azure Machine Learning"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/15/2017
ms.openlocfilehash: 248cbcfe35db646a8bc71c6f825dcaa8a4661e91
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2017
---
# <a name="supported-matrix-for-this-release"></a>Matriz com suporte para esta versão 
Quando seu código estiver carregando dados usando Fontes de Dados ou Preparação de Dados do Azure Machine Learning, obtendo um dataframe Pandas ou Spark, as seguintes combinações de ambientes de computação de experimento e locais de dados são compatíveis:

|     |Arquivos locais  |Armazenamento de Blobs do Azure  |Banco de Dados do SQL Server***  |
|---------|---------|---------|---------|---------|
|Python local    |     Com suporte    |Sem suporte         | Sem suporte        |         |
|Docker (Linux VM) Python     |Compatível somente com arquivos de projeto*         | Sem suporte        |        Sem suporte |         |
|Docker (Linux VM) PySpark     |Compatível somente com arquivos de projeto*     |Com suporte         | Com suporte**        |         |
|Máquina Virtual de Ciência de Dados do Azure em Python     |Compatível somente com arquivos de projeto*         |Sem suporte         |Sem suporte         |         |
|Máquina Virtual de Ciência de Dados do Azure em PySPark     | Compatível somente com arquivos de projeto*        |Sem suporte         |Sem suporte         |         |
|Azure HDInsight PySpark     | Sem suporte        |Com suporte         |Com suporte**         |         |
|Azure HDInsight Python     | Sem suporte        | Sem suporte        | Sem suporte        |         |

O Azure Data Lake Store não tem suporte para nenhum destino de computação no momento.

*Quando caminhos de arquivo locais são usados, os arquivos no projeto são copiados para o ambiente de computação e, em seguida, são lidos lá. Arquivos fora do projeto não são copiados e os caminhos não serão mais resolvidos no ambiente de computação. Considere usar Substituição de Fonte de Dados para que seu código possa usar um arquivo local durante execuções locais. Em seguida, mude para um Azure Storage Blob para obter uma configuração de execução diferente. Você também pode usar o suporte de amostragem em fontes de dados para gerenciar execuções em dados grandes somente em determinadas configurações de execução.

**Usa o driver do SQL Server Maven JDBC 6.2.1. É necessário garantir que esse pacote (ou um pacote compatível) esteja incluído no arquivo spark_dependencies.yml para o ambiente de computação.

***Compatível com o Banco de Dados SQL do Azure ou SQL Server, desde que o banco de dados possa ser acessado do ambiente de computação. 
