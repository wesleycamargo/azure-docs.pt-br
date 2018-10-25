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
ms.openlocfilehash: ac7aa294dee25a6dea4ec2ee614f2b2d34d3a6bc
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2018
ms.locfileid: "49117670"
---
> [!IMPORTANT]
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


