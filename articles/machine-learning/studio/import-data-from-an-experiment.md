---
title: Importar dados no Machine Learning Studio de outro experimento - Azure | Microsoft Docs
description: Como salvar os dados de treinamento no Azure Machine Learning Studio e usá-los em outro experimento.
keywords: importar dados, dados, fontes de dados, dados de treinamento
services: machine-learning
documentationcenter: ''
author: ericlicoding
ms.custom: previous-ms.author=deguhath, previous-author=deguhath
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: 7da9dcec-5693-4bb6-8166-15904e7f75c3
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.openlocfilehash: ea6e813d78cafa4e31ea37afa2675f30bccc5c4f
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2018
ms.locfileid: "53258060"
---
# <a name="import-your-data-into-azure-machine-learning-studio-from-another-experiment"></a>Importar os dados para o Azure Machine Learning Studio de outro experimento

Haverá momentos em que você desejará usar o resultado intermediário de um teste como parte de outro teste. Para fazer isso, salve o módulo como um conjunto de dados:

1. Clique na saída do módulo que você deseja salvar como um conjunto de dados.
2. Clique em **Salvar como Conjunto de Dados**.
3. Quando solicitado, insira um nome e uma descrição que permitiriam identificar o conjunto de dados facilmente.
4. Clique na marca de seleção **OK** .

Quando o salvamento for concluído, o conjunto de dados ficará disponível para uso em qualquer experimento do seu workspace. Você pode encontrá-lo na lista **Conjuntos de Dados Salvos** na paleta de módulos.

