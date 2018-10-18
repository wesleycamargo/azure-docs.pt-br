---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/06/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 3a3ee0cda3643ac73581f868e47905841d9f1563
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44095584"
---
> [!IMPORTANT]
> - A versão prévia da autenticação do Azure AD para blobs e filas destina-se somente a uso de não produção. SLAs (Contratos de Nível de Serviço) não estão disponíveis atualmente. Se a integração do Azure AD ainda não der suporte para seu cenário, continue usando os tokens SAS ou de autorização de chave compartilhada nos aplicativos.
>
> - Durante a versão prévia, as atribuições de função do RBAC podem levar até cinco minutos para serem propagadas.
>
> - Você deve usar HTTPS para autenticar com o Azure AD ao chamar operações de blob e de fila.
>
> - Na versão prévia, o portal do Azure não usa credenciais do Azure AD para ler e gravar dados blob e de fila. Em vez disso, o portal continua a contar com a chave de acesso da conta. Para exibir ou atualizar dados de blob ou de fila no portal, é preciso que seja atribuída ao usuário uma função RBAC que abrange [Microsoft.Storage/storageAccounts/listkeys/action](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-account-key-operator-service-role), que concede permissões para chamar [Contas de Armazenamento – Listar Chaves](https://docs.microsoft.com/rest/api/storagerp/storageaccounts/listkeys). As funções de Colaborador e de Leitor para blobs e filas não incluem a ação **listkeys** como parte da versão prévia e, portanto, não fornecem acesso a dados por meio do portal do Azure. Para explorar o acesso com base em identidade aos dados de blob e de fila durante a versão prévia, use PowerShell ou CLI do Azure.
