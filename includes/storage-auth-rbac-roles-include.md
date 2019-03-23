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
ms.openlocfilehash: c7fa81f6d23962eedb3dfeafdd397b62a83d130e
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58372655"
---
O Azure fornece as seguintes funções internas de RBAC para acesso ao armazenamento de dados:

- [Proprietário de dados de Blob de armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner-preview): Use para definir a propriedade e gerenciar o controle de acesso do POSIX para armazenamento do Azure Data Lake Storage Gen2 (visualização). Para obter mais informações, consulte [Controle de acesso no Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Colaborador de dados de Blob de armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor-preview): Use para conceder permissões de leitura/gravação/exclusão de recursos de armazenamento de Blob.
- [Leitor de dados de Blob de armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader-preview): Use para conceder permissões somente leitura aos recursos de armazenamento de Blob.
- [Colaborador de dados da fila de armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor-preview): Use para conceder permissões de leitura/gravação/exclusão às filas do Azure.
- [Leitor de dados da fila de armazenamento](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader-preview): Use para conceder permissões somente leitura para as filas do Azure.
- [Processador de mensagem da fila de armazenamento de dados](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor-preview): Use grant inspecionar, recuperar e exclui as permissões para as mensagens nas filas de armazenamento do Azure.
- [Remetente de mensagem da fila de armazenamento de dados](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender-preview): Usar para conceder adicionar permissões para as mensagens nas filas de armazenamento do Azure.

Para obter mais informações sobre como as funções internas são definidas para o Armazenamento do Microsoft Azure, consulte [Compreender as definições de função](../articles/role-based-access-control/role-definitions.md#management-and-data-operations-preview). Para obter informações sobre como criar funções de RBAC personalizadas, consulte [criar funções personalizadas para o controle de acesso baseado em função](../articles/role-based-access-control/custom-roles.md). 
