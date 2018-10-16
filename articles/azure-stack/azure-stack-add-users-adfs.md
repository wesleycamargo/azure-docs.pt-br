---
title: Adicionar usuários do AD FS do Azure Stack | Microsoft Docs
description: Saiba como adicionar usuários para implantações do AD FS do Azure Stack
services: azure-stack
documentationcenter: ''
author: patricka
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: patricka
ms.reviewer: unknown
ms.openlocfilehash: f8abacbcb05d1346931b5c2e1097660cfbd8e594
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49344159"
---
# <a name="add-azure-stack-users-in-ad-fs"></a>Adicionar usuários do Azure Stack no AD FS
Você pode usar o **Active Directory Users and Computers** snap-in para acrescentar usuários adicionais para um ambiente de pilha do Azure, aproveitando o AD FS como seu provedor de identidade.

## <a name="add-windows-server-active-directory-users"></a>Adicionar usuários do Active Directory do Windows Server
> [!TIP]
> Este exemplo usa o padrão azurestack ASDK do active directory. 

1.  Faça logon em um computador com uma conta que fornece acesso para as ferramentas administrativas do Windows e abra um novo Microsoft Management Console (MMC).
2.  Clique em **arquivo > Adicionar ou remover snap-in**.
3.  Selecione **do Active Directory Users and Computers** > **azurestack. local** > **usuários**.
4.  Clique em **ação** > **nova** > **usuário**.
5.  No novo objeto – janela de usuário, forneça e confirme uma senha
6.  Clique em **próxima** para finalizar os valores e clique em Concluir para criar o usuário.


## <a name="next-steps"></a>Próximas etapas
[Criar entidades de serviço](azure-stack-create-service-principals.md)