---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 04/30/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 443db1b4609e62fb7c57de417e42a2b4d0737ada
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553240"
---
Os seguintes limites se aplicam a tópicos de sistema de grade de eventos do Azure e tópicos personalizados, *não* domínios do evento.

| Recurso | Limite |
| --- | --- |
| Tópicos personalizados por assinatura do Microsoft Azure | 100 |
| Assinaturas de Evento por tópico | 500 |
| Publicar a taxa de um tópico personalizado (entrada) | 5.000 eventos por segundo, por tópico |

Os seguintes limites se aplicam a somente os domínios de evento.

| Recurso | Limite |
| --- | --- |
| Tópicos por domínio de evento | 1.000 durante a visualização pública |
| Assinaturas de evento por tópico dentro de um domínio | 50 durante a visualização pública |
| Assinaturas de evento de escopo de domínio | 50 durante a visualização pública |
| Publicar a taxa para um domínio do evento (entrada) | 5.000 eventos por segundo durante a visualização pública |