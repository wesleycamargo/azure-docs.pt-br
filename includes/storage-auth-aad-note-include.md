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
ms.openlocfilehash: 2d641287bcf095f13719667a91b7f61295309b5d
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/19/2018
ms.locfileid: "49430350"
---
> [!NOTE]
> - A versão prévia da autenticação do Azure AD para blobs e filas destina-se somente a uso de não produção. SLAs (Contratos de Nível de Serviço) não estão disponíveis atualmente. Se a integração do Azure AD ainda não der suporte para seu cenário, continue usando os tokens SAS ou de autorização de chave compartilhada nos aplicativos.
>
> - Durante a versão prévia, as atribuições de função do RBAC podem levar até cinco minutos para serem propagadas.
>
> - Você deve usar HTTPS para autenticar com o Azure AD ao chamar operações de blob e de fila.
>
> - O portal do Azure agora suporta o uso de credenciais do Azure AD para ler e gravar dados de blob como parte da versão de visualização.
> 
> - No momento, o portal do Azure não suporta o uso de credenciais do Azure AD para ler e gravar dados de filas. Os dados da fila são acessados por meio das chaves da sua conta de armazenamento.
>
> - [O Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) atualmente usa sua chave de conta de armazenamento para acessar dados de blob e fila.
>
> - Os arquivos do Azure dá suporte à autenticação com o AD do Azure no SMB ingressado no domínio somente às VMs (visualização). Para saber mais sobre como usar o Azure AD em SMB para arquivos do Azure, consulte [Visão geral da autenticação do Active Directory do Azure em SMB para arquivos do Azure (visualização)](../articles/storage/files/storage-files-active-directory-overview.md).



