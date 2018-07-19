---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: batch
author: dlepow
ms.service: batch
ms.topic: include
ms.date: 04/06/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 0dcb608553d4455403c073e34e83bccb81cc64db
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38750069"
---
Uma tarefa em execução em lote do Azure pode produzir dados de saída quando ele é executado. Com frequência, os dados de saída precisam ser armazenados para a recuperação por outras tarefas no trabalho, o aplicativo cliente que executou o trabalho, ou ambos. As tarefas gravam dados de saída no sistema de arquivos de um nó de computação do Lote, mas todos os dados no nó serão perdidos quando ele for reimaginado ou quando o nó deixar o pool. As tarefas também podem ter um período de retenção de arquivo, após o qual os arquivos criados pela tarefa são excluídos. Por esses motivos, é importante manter a saída da tarefa que você vai precisar posteriormente para um repositório de dados, como o [Armazenamento do Azure](https://docs.microsoft.com/azure/storage/).

Para opções de conta de armazenamento em Lote, confira a [Visão geral do recurso de Lote](../articles/batch/batch-api-basics.md#azure-storage-account).
