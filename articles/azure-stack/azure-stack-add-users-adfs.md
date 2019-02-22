---
title: Adicionar usuários do AD FS do Azure Stack | Microsoft Docs
description: Saiba como adicionar usuários para implantações do AD FS do Azure Stack
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2019
ms.author: patricka
ms.reviewer: unknown
ms.lastreviewed: 02/21/2019
ms.openlocfilehash: 1b47739200c79317273ea0c788f21a7ee4a3b818
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2019
ms.locfileid: "56648499"
---
# <a name="add-azure-stack-users-in-ad-fs"></a>Adicionar usuários do Azure Stack no AD FS
Você pode usar o **Active Directory Users and Computers** snap-in para acrescentar usuários adicionais para um ambiente de pilha do Azure, aproveitando o AD FS como seu provedor de identidade.

## <a name="add-windows-server-active-directory-users"></a>Adicionar usuários do Active Directory do Windows Server
> [!TIP]
> Este exemplo usa o padrão azurestack ASDK do active directory. 

1. Faça logon em um computador com uma conta que fornece acesso para as ferramentas administrativas do Windows e abra um novo Microsoft Management Console (MMC).
2. Selecione **arquivo > Adicionar ou remover snap-in**.
3. Selecione **do Active Directory Users and Computers** > **azurestack. local** > **usuários**.
4. Selecione **ação** > **nova** > **usuário**.
5. No novo objeto – usuário, forneça os detalhes de informações do usuário. Selecione **Avançar**.
6. Forneça e confirme uma senha.
7. Selecione **próxima** para finalizar os valores. Selecione **concluir** para criar o usuário.


## <a name="next-steps"></a>Próximas etapas
[Criar entidades de serviço](azure-stack-create-service-principals.md)