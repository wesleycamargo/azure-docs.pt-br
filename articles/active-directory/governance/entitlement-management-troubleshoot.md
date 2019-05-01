---
title: Solucionar problemas de gerenciamento de direitos do AD do Azure (visualização) – Azure Active Directory
description: Conheça alguns itens que você deve verificar para ajudá-lo a solucionar problemas do gerenciamento de direitos do Active Directory do Azure (visualização).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/25/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ca7955186d6c6fb1dff2acd6a2d5badd1cedaef
ms.sourcegitcommit: 9ad75f83bbf0fc4623b7995794f33bbf823b31c0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/26/2019
ms.locfileid: "64541504"
---
# <a name="troubleshoot-azure-ad-entitlement-management-preview"></a>Solucionar problemas do gerenciamento de direitos do AD do Azure (visualização)

> [!IMPORTANT]
> Gerenciamento de direitos do Active Directory (Azure AD) do Azure está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo descreve alguns itens que você deve verificar para ajudá-lo a solucionar problemas de gerenciamento de direitos do Active Directory (Azure AD).

## <a name="checklist-for-entitlement-management-administration"></a>Lista de verificação para administração de gerenciamento de direitos

* Se você receber uma mensagem de acesso negado ao configurar o gerenciamento de direitos, e você for um Administrador Global, certifique-se de que o diretório tiver um [licença do Azure AD Premium P2 (ou EMS E5)](entitlement-management-overview.md#prerequisites).  
* Se você receber um acesso negado a mensagem ao criar ou exibir pacotes de acesso, e você for um membro de um grupo de criador de catálogo, você deve criar um catálogo antes de criar seu primeiro pacote de acesso.

## <a name="checklist-for-adding-a-resource"></a>Lista de verificação para adicionar um recurso

* Se houver usuários que já tenham sido atribuídos a um recurso que você deseja gerenciar com um pacote de acesso, certifique-se de que os usuários são atribuídos ao pacote de acesso com uma política apropriada. Por exemplo, você talvez queira incluir um grupo em um pacote de acesso que já tenha os usuários no grupo. Se esses usuários em exigem o grupo de acesso continuaram, eles devem ter uma política apropriada para os pacotes de acesso para que eles não percam o acesso ao grupo. Você pode atribuir o pacote de acesso fazendo os que os usuários solicitem o pacote de acesso que contém esse recurso, ou atribuindo-lhes diretamente para o pacote de acesso. Para obter mais informações, consulte [editar e gerenciar um pacote existente de acesso](entitlement-management-access-package-edit.md).

## <a name="checklist-for-request-issues"></a>Lista de verificação para problemas de solicitação

* Quando um usuário deseja solicitar acesso a um pacote de acesso, certifique-se de que estão usando o **link do portal de acesso Meus** para o pacote de acesso. Para obter mais informações, consulte [link do portal de acesso de minha cópia](entitlement-management-access-package-edit.md#copy-my-access-portal-link).

* Quando um usuário entra portal do meu acesso para solicitar um pacote de acesso, certifique-se de que eles se autenticam usando suas contas institucionais. A conta organizacional pode ser uma conta no diretório de recursos ou em um diretório que está incluído em uma das políticas do pacote de acesso. Se a conta do usuário não é uma conta organizacional ou o diretório não está incluído na política, o usuário não verá o pacote de acesso. Para obter mais informações, consulte [solicitar acesso a um pacote de acesso](entitlement-management-request-access.md).

* Se um usuário é impedido de entrar no diretório de recursos, eles não poderão solicitar acesso no portal do meu acesso. Antes do usuário pode solicitar o acesso, você deve remover o bloco de entrada do perfil do usuário. Para remover o bloco entrar no portal do Azure, clique em **Azure Active Directory**, clique em **usuários**, clique no usuário e, em seguida, clique em **perfil**. Editar o **as configurações** seção e altere **bloquear entrada** para **não**. Para obter mais informações, consulte [adicionar ou atualizar informações de perfil do usuário usando o Azure Active Directory](../fundamentals/active-directory-users-profile-azure-portal.md)

* No portal do meu acesso, se um usuário for um solicitante e um aprovador, eles não verão sua solicitação para um pacote de acesso sobre os **aprovações** página. Esse comportamento é intencional: um usuário não pode aprovar a solicitação de seu próprios. Certifique-se de que o pacote de acesso que ele está solicitando tem outros aprovadores configurados na política. Para obter mais informações, consulte [editar uma política existente](entitlement-management-access-package-edit.md#edit-an-existing-policy).

* Se um novo usuário externo, que tem não entrou anteriormente no seu diretório, recebe um pacote de acesso, incluindo um site do SharePoint Online, o seu pacote de acesso será exibido como não totalmente entregue até que sua conta é provisionada no SharePoint Online.

## <a name="next-steps"></a>Próximas etapas

- [Exibir relatórios de como os usuários têm acesso no gerenciamento de direitos](entitlement-management-reports.md)
