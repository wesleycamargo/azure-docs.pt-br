---
title: Importar dados no Estúdio de Machine Learning de outro experimento| Microsoft Docs
description: Como salvar os dados de treinamento no Azure Machine Learning Studio e usá-los em outro experimento.
keywords: importar dados, dados, fontes de dados, dados de treinamento
services: machine-learning
documentationcenter: ''
author: deguhath
ms.author: deguhath
manager: jhubbard
editor: cgronlun
ms.assetid: 7da9dcec-5693-4bb6-8166-15904e7f75c3
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.openlocfilehash: 5007657da11cb45c6511780398c5425d14dca900
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/21/2018
ms.locfileid: "40246388"
---
# <a name="import-your-data-into-azure-machine-learning-studio-from-another-experiment"></a>Importar os dados para o Azure Machine Learning Studio de outro experimento
[!INCLUDE [import-data-into-aml-studio-selector](../../../includes/machine-learning-import-data-into-aml-studio.md)]

Haverá momentos em que você desejará usar o resultado intermediário de um teste como parte de outro teste. Para fazer isso, salve o módulo como um conjunto de dados:

1. Clique na saída do módulo que você deseja salvar como um conjunto de dados.
2. Clique em **Salvar como Conjunto de Dados**.
3. Quando solicitado, insira um nome e uma descrição que permitiriam identificar o conjunto de dados facilmente.
4. Clique na marca de seleção **OK** .

Quando o salvamento for concluído, o conjunto de dados ficará disponível para uso em qualquer experimento do seu espaço de trabalho. Você pode encontrá-lo na lista **Conjuntos de Dados Salvos** na paleta de módulos.

