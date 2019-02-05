---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 07/18/2018
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 6d9370ce6a031377e37c4e830400337f3e57a62e
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2019
ms.locfileid: "55084793"
---
| Recurso | Destino | Limite rígido |
|----------|--------------|------------|
| Serviços de Sincronização de Armazenamento por região | 15 Serviços de Sincronização de Armazenamento | SIM |
| Grupos de sincronização por Serviço de Sincronização de Armazenamento | 100 grupos de sincronização | SIM |
| Servidores registrados por Serviço de Sincronização de Armazenamento | 99 servidores | SIM |
| Pontos de extremidade de nuvem por grupo de sincronização | 1 ponto de extremidade de nuvem | SIM |
| Pontos de extremidade de servidor por grupo de sincronização | 50 pontos de extremidade de servidor | Não  |
| Pontos de extremidade de servidor por servidor | 30 pontos de extremidade de servidor | SIM |
| Tamanho do ponto de extremidade | 4 TiB | Não  |
| Objetos do sistema de arquivos (diretórios e arquivos) por grupo de sincronização | 25 milhões de objetos | Não  |
| Número máximo de objetos do sistema de arquivos (diretórios e arquivos) em um diretório | 200.000 objetos | SIM |
| Tamanho máximo do nome do objeto (diretórios e arquivos) | 255 caracteres | SIM |
| Tamanho máximo do descritor de segurança (diretórios e arquivos) do objeto | 4 KiB | SIM |
| Tamanho do arquivo | 100 GiB | Não  |
| Tamanho mínimo do arquivo para que um arquivo seja colocado em camadas | 64 KiB | SIM |
| Sessões de sincronização simultâneas | Agente V4: O limite varia com base nos recursos de sistema disponíveis. <BR> Agente V3: 2 sessões de sincronização ativas por processador ou, no máximo, 8 sessões de sincronização ativas por servidor | SIM
