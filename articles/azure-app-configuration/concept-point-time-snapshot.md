---
title: Instantâneo pontual da Configuração de Aplicativo do Azure | Microsoft Docs
description: Uma visão geral de como funciona o instantâneo pontual na Configuração de Aplicativo do Azure
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.service: azure-app-configuration
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: 4f26f3a31dc6303e991c39fc7f85d9bf254d57bc
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56885131"
---
# <a name="point-in-time-snapshot"></a>Instantâneo pontual

A Configuração de Aplicativo do Azure mantém registros dos tempos precisos de quando um par chave-valor é criado e modificado posteriormente. Esses registros formam uma linha do tempo completa nas alterações de pares chave-valor. Um repositório de configurações de aplicativo pode reconstruir o histórico de qualquer par chave-valor e reproduzir seu valor anterior em qualquer momento determinado, até o presente. Esse recurso permite que você “viaje no tempo” e recupere um par chave-valor antigo. Por exemplo, você pode obter as definições de configuração de ontem, logo antes da implantação mais recente, para recuperar uma configuração anterior caso precise reverter o aplicativo.

## <a name="key-value-retrieval"></a>Recuperação de par chave-valor

Para recuperar os pares chave-valor anteriores, você precisa especificar uma hora na qual os pares chave-valor são um instantâneo no cabeçalho HTTP de uma chamada à API REST. Por exemplo: 

        GET /revisions HTTP/1.1
        Accept-Datetime: Sat, 1 Jan 2019 02:10:00 GMT

Atualmente, a Configuração de Aplicativo reterá 7 dias do histórico de alterações.

## <a name="next-steps"></a>Próximas etapas

* [Início Rápido: Criar um aplicativo Web ASP.NET](quickstart-aspnet-core-app.md)  
