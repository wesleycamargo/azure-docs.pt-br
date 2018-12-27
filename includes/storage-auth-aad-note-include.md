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
ms.openlocfilehash: 0182df40a4e7815560a85e60fe9062ccd8001c18
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52978794"
---
> [!NOTE]
> - A versão prévia da autenticação do Azure AD para blobs e filas destina-se somente a uso de não produção. SLAs (Contratos de Nível de Serviço) não estão disponíveis atualmente. Se a integração do Azure AD ainda não der suporte para seu cenário, continue usando os tokens SAS ou de autorização de chave compartilhada nos aplicativos.
>
> - Durante a versão prévia, as atribuições de função do RBAC podem levar até cinco minutos para serem propagadas.
>
> - Para autorizar operações de blob e fila com um token OAuth, você deve usar HTTPS.
>
> - O portal do Azure agora suporta o uso de credenciais do Azure AD para ler e gravar dados de blobs e filas, como parte da versão de visualização.
> 
> - [O Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) atualmente usa sua chave de conta de armazenamento para acessar dados de blob e fila. O acesso ao OAuth dá suporte para blobs.
>
> - Os arquivos do Azure dá suporte à autenticação com o AD do Azure no SMB ingressado no domínio somente às VMs (visualização). Para saber mais sobre como usar o Azure AD em SMB para arquivos do Azure, consulte [Visão geral da autenticação do Active Directory do Azure em SMB para arquivos do Azure (visualização)](../articles/storage/files/storage-files-active-directory-overview.md).