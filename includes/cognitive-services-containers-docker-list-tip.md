---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/24/2019
ms.openlocfilehash: e7960bd288e87c4e3c6b395322a5ab92f3040d88
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55302268"
---
> [!TIP]
> Você pode usar o comando [imagens do estivador](https://docs.docker.com/engine/reference/commandline/images/) para listar as imagens do contêiner transferidas por download. Por exemplo, o comando a seguir lista o ID, o repositório e a tag de cada imagem do contêiner transferida por download, formatada como uma tabela:
>
>  ```Docker
>  docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
>
>  IMAGE ID            REPOSITORY                                                                TAG
>  ebbee78a6baa        mcr.microsoft.com/azure-cognitive-services/luis                           latest
>  ``` 