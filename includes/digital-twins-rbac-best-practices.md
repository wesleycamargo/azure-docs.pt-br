---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: azure-digital-twins
author: adamgerard
ms.service: azure-digital-twins
ms.topic: include
ms.date: 09/27/2018
ms.author: adgera
ms.custom: include file
ms.openlocfilehash: 075587df445b46c8b03c5448cebf8cd8b12f1179
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323655"
---
O controle de acesso baseado em função é uma estratégia de segurança controlada por herança para gerenciar acesso, permissões e funções. Funções descendentes herdam permissões das funções pai. As permissões também podem ser atribuídas sem serem herdadas de uma função pai. Elas também podem ser atribuídas para personalizar uma função, conforme necessário.

Por exemplo, uma **Administrador de Espaço** pode precisar de acesso global para executar todas as operações para um espaço especificado (incluindo todos os nós subjacentes ou internos), enquanto um **Instalador de Dispositivo** pode precisar apenas de permissões de *leitura* e *atualização* para dispositivos e sensores.

Em todos os casos, as funções precisam ser concedidas **com precisão e não mais do que o acesso necessário** para atender às tarefas conforme o **Princípio de privilégios mínimos**, que concede uma identidade *somente*:

* A extensão de acesso necessária para concluir o trabalho.
* Uma função apropriada e limitada para a realização do trabalho.

>[!IMPORTANT]
> **Sempre siga o Princípio de privilégios mínimos**.

Duas outras importantes práticas de controle de acesso baseado em função a serem seguidas:

> [!div class="checklist"]
> * Auditar periodicamente as atribuições de função para verificar se cada função tem as permissões corretas.
> * Funções e atribuições precisam ser limpos à medida que os indivíduos mudam de funções ou atribuições.