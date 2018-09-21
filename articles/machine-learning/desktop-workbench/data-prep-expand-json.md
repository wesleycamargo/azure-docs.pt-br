---
title: Transformação Expandir JSON usando o Azure Machine Learning Workbench
description: O documento de referência para a transformação 'Expandir JSON'
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc, reference
ms.topic: article
ms.date: 09/14/2017
ms.openlocfilehash: dbda4b7b6d82e8cf1e89dc78ce82efbac08b9933
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/12/2018
ms.locfileid: "35632028"
---
# <a name="expand-json-transformation"></a>Transformação Expandir JSON
A transformação **Expandir JSON** permite que os usuários expandam uma coluna existente que contém o texto JSON válido em várias colunas.

## <a name="how-to-perform-this-transformation"></a>Como executar essa transformação

Siga essas etapas para realizar essa transformação:
1. Selecione a coluna de origem que contém texto JSON.
2. Selecione **Expandir JSON** no menu **Transformações**. Ou, clique com o botão direito do mouse no cabeçalho da coluna de origem e selecione **Expandir JSON** no menu de contexto. 
3. Clique em **OK**. 
 
Novas colunas são adicionadas ao lado da coluna de origem. Essas colunas contêm propriedades do próximo nível da hierarquia no texto JSON. A Chave de Propriedade, se presente, é usada para criar o nome da coluna correspondente. As propriedades aninhadas são preservadas como texto JSON que o usuário pode expandir iterativamente ou descartar conforme necessário.

## <a name="examples"></a>Exemplos

A coluna de origem *Customer* é expandida em duas colunas *Customer.Name* e *Customer.Phone*.

| Cliente                                                | Customer.Name   | Customer.Phone |
|---------------------------------------------------------|-----------------|----------------|
| { "Name" : "Carrie Dodson", "Phone" : "123-4567-890"}   | Carrie Dodson   | 123-4567-890   |
| { "Name" : "Leonard Robledo", "Phone" : "456-7890-123"} | Leonard Robledo | 456-7890-123   |

