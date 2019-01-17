---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 01/02/2019
ms.author: adgera
ms.custom: include file
ms.openlocfilehash: 6eb7993b4dbec3ab4901dc7071d18eae98ab8ae4
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54079248"
---
> [!NOTE]
> Normalmente, solicitações com várias partes requerem três partes:
> * Um **Content-Type** cabeçalho:
>   * `application/json; charset=utf-8`
>   * `multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`
> * Um **Content-Disposition**:
>   * `form-data; name="metadata"`
> * O conteúdo do arquivo que será carregado
>
> **Content-Type** e **Content-Disposition** variam dependendo do cenário de uso.

Solicitações com várias partes podem ser feitas de forma programática (usando C#), por meio de um cliente REST ou de uma ferramenta como [Postman](https://www.getpostman.com/). Ferramentas de cliente REST podem ter diferentes níveis de suporte para solicitações com várias partes complexas. Verifique qual ferramenta que é mais adequada às suas necessidades.

> [!IMPORTANT]
> As solicitações multipartes feitas para as APIs do Gerenciamento dos Gêmeos Digitais do Azure são divididas em duas partes:
> * metadados de blob (como um tipo MIME associado), declarados por **Content-Type** e **Content-Disposition**
> * Conteúdo do blob, que inclui o conteúdo não estruturado de um arquivo a ser carregado
>
> Nenhuma das duas partes é necessária para solicitações de **PATCH**. Ambos são necessários para **POST** ou criam operações.

O [Código-fonte do Início Rápido da Ocupação](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/api/update.cs) contém exemplos de C# completos que demonstram como fazer solicitações com várias partes para as APIs de Gerenciamento dos Gêmeos Digitais do Azure.