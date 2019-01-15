---
title: Gerenciar permissões aos recursos por usuário no Azure Stack (administrador de serviços e locatário) | Microsoft Docs
description: Como um administrador de serviços ou Locatário, saiba como gerenciar permissões de RBAC.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/14/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: 26ce7139b856fc2f8d7d2cad549b3dd3c0f5e406
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/15/2019
ms.locfileid: "54304669"
---
# <a name="manage-role-based-access-control"></a>Gerenciar o controle de acesso baseado em função

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Um usuário do Azure Stack pode ser um leitor, proprietário ou colaborador para cada instância de uma assinatura, um grupo de recursos ou serviço. Por exemplo, o usuário A pode ter permissões de leitura para uma assinatura, mas ter permissões de proprietário para sete de máquina Virtual.

 - Leitor: Usuário pode ver tudo, mas não é possível fazer alterações.
 - Colaborador: Usuário pode gerenciar tudo, exceto o acesso aos recursos.
 - Proprietário: Usuário pode gerenciar tudo, incluindo o acesso aos recursos.

## <a name="set-access-permissions-for-a-user"></a>Definir permissões de acesso para um usuário

1. Entre com uma conta que tenha permissões de proprietário ao recurso que deseja gerenciar.
2. Na folha do recurso, clique o **acesso** ícone ![](media/azure-stack-manage-permissions/image1.png).
3. No **os usuários** folha, clique em **funções**.
4. No **funções** folha, clique em **Add** para adicionar permissões para o usuário.

## <a name="set-access-permissions-for-a-universal-group"></a>Definir permissões de acesso para um grupo universal 

> [!Note]  
Aplicável somente ao Active Directory Federated Services (AD FS).

1. Entre com uma conta que tenha permissões de proprietário ao recurso que deseja gerenciar.
2. Na folha do recurso, clique o **acesso** ícone ![](media/azure-stack-manage-permissions/image1.png).
3. No **os usuários** folha, clique em **funções**.
4. No **funções** folha, clique em **Add** adicionar permissões para Universal grupo grupo do Active Directory.

## <a name="next-steps"></a>Próximas etapas
[Adicionar um locatário do Azure Stack](azure-stack-add-new-user-aad.md)

