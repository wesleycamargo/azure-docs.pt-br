---
title: "Gerenciar permissões para recursos por usuário no Azure pilha | Microsoft Docs"
description: "Como um administrador de serviços ou Locatário, saiba como gerenciar permissões RBAC."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: cccac19a-e1bf-4e36-8ac8-2228e8487646
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: dfec5648ff383fd98965c1918cdab6472bb3c17f
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/22/2018
---
# <a name="manage-role-based-access-control"></a>Gerenciar o controle de acesso baseado em função

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Um usuário do Azure Stack pode ser um leitor, proprietário ou colaborador para cada instância de uma assinatura, um grupo de recursos ou serviço. Por exemplo, o Usuário A pode ter permissões de leitura à Assinatura 1, mas ter permissões de proprietário à Máquina Virtual 7.

* Leitor: o usuário pode exibir tudo, mas não pode fazer alterações.
* Colaborador: o usuário pode gerenciar tudo, exceto o acesso aos recursos.
* Proprietário: o usuário pode gerenciar tudo, incluindo o acesso aos recursos.

## <a name="set-access-permissions-for-a-user"></a>Definir permissões de acesso para um usuário
1. Entre com uma conta que tenha permissões de proprietário ao recurso que deseja gerenciar.
2. Na folha do recurso, clique no **acesso** ícone ![](media/azure-stack-manage-permissions/image1.png).
3. No **usuários** folha, clique em **funções**.
4. No **funções** folha, clique em **adicionar** para adicionar permissões para o usuário.

## <a name="next-steps"></a>Próximas etapas


