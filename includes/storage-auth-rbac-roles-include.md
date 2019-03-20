---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 02/19/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 9402c4b24c9d64b4b69d750fbd19de40cda396f3
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/01/2019
ms.locfileid: "57218083"
---
O Azure fornece as seguintes funções internas de RBAC para acesso ao armazenamento de dados:

- [Proprietário de Dados do Blob de Armazenamento (Versão Prévia)](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner-preview): Use para definir a propriedade e gerenciar o controle de acesso do POSIX para armazenamento do Azure Data Lake Storage Gen2 (visualização). Para obter mais informações, consulte [Controle de acesso no Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Colaborador de dados de blob de armazenamento (pré-visualização)](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor-preview): Use para conceder permissões de leitura/gravação/exclusão de recursos de armazenamento de Blob.
- [Leitor de Dados do Blob de Armazenamento (Versão prévia)](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader-preview): Use para conceder permissões somente leitura aos recursos de armazenamento de Blob.
- [Colaborador de Dados da Fila de Armazenamento (Versão prévia)](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor-preview): Use para conceder permissões de leitura/gravação/exclusão às filas do Azure.
- [Leitor de Dados da Fila de Armazenamento (Versão prévia)](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader-preview): Use para conceder permissões somente leitura para as filas do Azure.

Para obter mais informações sobre como as funções internas são definidas para o Armazenamento do Microsoft Azure, consulte [Compreender as definições de função](../articles/role-based-access-control/role-definitions.md#management-and-data-operations-preview). Para obter informações sobre como criar funções de RBAC personalizadas, consulte [criar funções personalizadas para o controle de acesso baseado em função](../articles/role-based-access-control/custom-roles.md). 
