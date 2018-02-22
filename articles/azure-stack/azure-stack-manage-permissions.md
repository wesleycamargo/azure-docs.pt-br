---
title: "Gerenciar permissões para recursos por usuário no Azure pilha (administrador de serviço e locatário) | Microsoft Docs"
description: "Como um administrador de serviços ou Locatário, saiba como gerenciar permissões RBAC."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: fenila
editor: 
ms.assetid: cccac19a-e1bf-4e36-8ac8-2228e8487646
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: mabrigg
ms.reviewer: thomas.roettinger
ms.openlocfilehash: 0e50ea44ebb0b0a7285dab04666dd55cad480c6a
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="manage-role-based-access-control"></a>Gerenciar o controle de acesso baseado em função

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Um usuário do Azure Stack pode ser um leitor, proprietário ou colaborador para cada instância de uma assinatura, um grupo de recursos ou serviço. Por exemplo, o usuário A pode ter permissões de leitura para uma assinatura, mas ter permissões de proprietário para a máquina Virtual sete.

 - Leitor: o usuário pode exibir tudo, mas não pode fazer alterações.
 - Colaborador: o usuário pode gerenciar tudo, exceto o acesso aos recursos.
 - Proprietário: o usuário pode gerenciar tudo, incluindo o acesso aos recursos.

## <a name="set-access-permissions-for-a-user"></a>Definir permissões de acesso para um usuário

1. Entre com uma conta que tenha permissões de proprietário ao recurso que deseja gerenciar.
2. Na folha do recurso, clique no **acesso** ícone ![](media/azure-stack-manage-permissions/image1.png).
3. No **usuários** folha, clique em **funções**.
4. No **funções** folha, clique em **adicionar** para adicionar permissões para o usuário.

## <a name="set-access-permissions-for-a-universal-group"></a>Definir permissões de acesso para um grupo universal 

> [!Note]  
Aplicável somente ao Active Directory Federated Services (AD FS).

1. Entre com uma conta que tenha permissões de proprietário ao recurso que deseja gerenciar.
2. Na folha do recurso, clique no **acesso** ícone ![](media/azure-stack-manage-permissions/image1.png).
3. No **usuários** folha, clique em **funções**.
4. No **funções** folha, clique em **adicionar** adicionar permissões para Universal grupo grupo do Active Directory.

## <a name="next-steps"></a>Próximas etapas
[Adicionar um locatário do Azure Stack](azure-stack-add-new-user-aad.md)

