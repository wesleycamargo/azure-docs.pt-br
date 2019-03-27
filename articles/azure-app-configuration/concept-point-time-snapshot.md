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
ms.openlocfilehash: e833146d05f0c35449915c1d1293873258a7b7eb
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58226769"
---
# <a name="point-in-time-snapshot"></a>Instantâneo pontual

A Configuração de Aplicativo do Azure manterá os registros de horas precisas quando um novo par chave-valor for criado e, em seguida, modificado. Esses registros formam uma linha do tempo completa nas alterações de pares chave-valor. Um repositório de configurações de aplicativo pode reconstruir o histórico de qualquer par chave-valor e reproduzir seu valor anterior em qualquer momento determinado, até o presente. Com esse recurso, é possível “voltar no tempo” e recuperar um par chave-valor antigo. Por exemplo, você pode obter as configurações de ontem, logo antes da implantação mais recente, para recuperar uma configuração anterior e reverter o aplicativo.

## <a name="key-value-retrieval"></a>Recuperação de par chave-valor

Para recuperar os pares chave-valor anteriores, especifique um horário no qual os pares chave-valor sejam instantâneos no cabeçalho HTTP de uma chamada à API REST. Por exemplo: 

        GET /revisions HTTP/1.1
        Accept-Datetime: Sat, 1 Jan 2019 02:10:00 GMT

Atualmente, a Configuração de Aplicativo mantém sete dias de histórico de alterações.

## <a name="next-steps"></a>Próximas etapas

* [Início Rápido: Criar um aplicativo Web ASP.NET](quickstart-aspnet-core-app.md)  
