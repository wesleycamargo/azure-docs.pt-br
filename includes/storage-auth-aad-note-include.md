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
ms.openlocfilehash: 50217829be7aff3bcdbf417f19fff9f1fd7c6901
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58372654"
---
> [!NOTE]
> - As atribuições de função RBAC podem levar até dois minutos para propagar.
>
> Para autorizar operações de blob e fila com um token OAuth, você deve usar HTTPS.
>
> - O portal do Azure agora suporta o uso de credenciais do Azure AD para ler e gravar dados de blobs e filas, como parte da versão de visualização. Para acessar dados de blob e fila no portal do Azure, um usuário deve ser atribuído, o Azure Resource Manager **leitor** função, além da função apropriada para acesso de blob ou fila. Para obter mais informações, consulte [conceder acesso aos contêineres do Azure e filas com o RBAC no portal do Azure](../articles/storage/common/storage-auth-aad-rbac.md). 
> 
> - [O Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) atualmente usa sua chave de conta de armazenamento para acessar dados de blob e fila. Se a chave não estiver disponível, a autorização do Azure AD é usada para acesso a blobs. Autorização do Azure AD não é suportada para filas. 
>
> - Os arquivos do Azure dá suporte à autenticação com o AD do Azure no SMB ingressado no domínio somente às VMs (visualização). Para saber mais sobre como usar o Azure AD em SMB para arquivos do Azure, consulte [Visão geral da autenticação do Active Directory do Azure em SMB para arquivos do Azure (visualização)](../articles/storage/files/storage-files-active-directory-overview.md).