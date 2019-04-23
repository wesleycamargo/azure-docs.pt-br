---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/21/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 9b8418dba12748915666c6a91ee65b37c0f59ace
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59803204"
---
O Azure fornece as seguintes funções internas de RBAC para acesso ao armazenamento de dados:

- [Proprietário de dados de Blob de armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner): Use para definir a propriedade e gerenciar o controle de acesso do POSIX para armazenamento do Azure Data Lake Storage Gen2 (visualização). Para obter mais informações, consulte [Controle de acesso no Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Colaborador de dados de Blob de armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Use para conceder permissões de leitura/gravação/exclusão de recursos de armazenamento de Blob.
- [Leitor de dados de Blob de armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader): Use para conceder permissões somente leitura aos recursos de armazenamento de Blob.
- [Colaborador de dados da fila de armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor): Use para conceder permissões de leitura/gravação/exclusão às filas do Azure.
- [Leitor de dados da fila de armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader): Use para conceder permissões somente leitura para as filas do Azure.
- [Processador de mensagem da fila de armazenamento de dados](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor): Use grant inspecionar, recuperar e exclui as permissões para as mensagens nas filas de armazenamento do Azure.
- [Remetente de mensagem da fila de armazenamento de dados](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender): Usar para conceder adicionar permissões para as mensagens nas filas de armazenamento do Azure.

> [!IMPORTANT]
> As atribuições de função RBAC podem levar até cinco minutos para propagar.

Para obter mais informações sobre como as funções internas são definidas para o Armazenamento do Microsoft Azure, consulte [Compreender as definições de função](../articles/role-based-access-control/role-definitions.md#management-and-data-operations-preview). Para obter informações sobre como criar funções de RBAC personalizadas, consulte [criar funções personalizadas para o controle de acesso baseado em função](../articles/role-based-access-control/custom-roles.md). 
