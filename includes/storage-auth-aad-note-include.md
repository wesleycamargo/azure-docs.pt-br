---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/09/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 5d0e919159569e7512b6e7dc0458a4a3e397943b
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2019
ms.locfileid: "57251946"
---
> [!NOTE]
> - A versão prévia da autenticação do Azure AD para blobs e filas destina-se somente a uso de não produção. SLAs (Contratos de Nível de Serviço) não estão disponíveis atualmente. Se a integração do Azure AD ainda não der suporte para seu cenário, continue usando os tokens SAS ou de autorização de chave compartilhada nos aplicativos.
>
> - Durante a versão prévia, as atribuições de função do RBAC podem levar até cinco minutos para serem propagadas.
>
> - Para autorizar operações de blob e fila com um token OAuth, você deve usar HTTPS.
>
> - O portal do Azure agora suporta o uso de credenciais do Azure AD para ler e gravar dados de blobs e filas, como parte da versão de visualização. Para acessar dados de blob e fila no portal do Azure, um usuário deve ser atribuído a função de RBAC de leitor do Gerenciador de recursos do Azure, além da função apropriada de visualização para acesso de blob ou fila. Para obter mais informações, consulte [conceder acesso aos contêineres do Azure e filas com o RBAC no portal do Azure (visualização)](../articles/storage/common/storage-auth-aad-rbac.md). 
> 
> - [O Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) atualmente usa sua chave de conta de armazenamento para acessar dados de blob e fila. O acesso ao OAuth dá suporte para blobs.
>
> - Os arquivos do Azure dá suporte à autenticação com o AD do Azure no SMB ingressado no domínio somente às VMs (visualização). Para saber mais sobre como usar o Azure AD em SMB para arquivos do Azure, consulte [Visão geral da autenticação do Active Directory do Azure em SMB para arquivos do Azure (visualização)](../articles/storage/files/storage-files-active-directory-overview.md).