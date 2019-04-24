---
title: 'Azure Active Directory Domain Services: Habilitar o suporte para o Serviço de Perfil de Usuário do SharePoint | Microsoft Docs'
description: Configurar os domínios gerenciados do Azure Active Directory Domain Services para dar suporte à sincronização de perfil do SharePoint Server
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: ergreenl
ms.openlocfilehash: deef9b317f394213eabb5ce0ce31dd294bc0dfd1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60359514"
---
# <a name="configure-a-managed-domain-to-support-profile-synchronization-for-sharepoint-server"></a>Configurar um domínio gerenciado para dar suporte à sincronização de perfil do SharePoint Server
O SharePoint Server inclui um Serviço de Perfil de Usuário que é usado para sincronização de perfil do usuário. Para configurar o Serviço de Perfil de Usuário, é necessário conceder as permissões apropriadas em um domínio do Active Directory. Para saber mais, consulte [conceder permissões do Active Directory Domain Services para sincronização de perfil no SharePoint Server 2013](https://technet.microsoft.com/library/hh296982.aspx).

Este artigo explica como configurar domínios gerenciados do Azure AD Domain Services para implantar o serviço de Sincronização de Perfil de Usuário do SharePoint Server.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="the-aad-dc-service-accounts-group"></a>O grupo 'Contas de Serviço do Controlador de Domínio do AAD'
Um grupo de segurança chamado '**Contas de Serviço do Controlador de Domínio do AAD**' está disponível na unidade organizacional 'Usuários' em seu domínio gerenciado. Veja esse grupo no snap-in do MMC **Usuários e Computadores do Active Directory** em seu domínio gerenciado.

![Grupo de segurança Contas de Serviço do Controlador de Domínio do AAD](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts.png)

Os membros desse grupo de segurança recebem os seguintes privilégios:
- O privilégio 'Replicar Alterações de Diretório' no DSE raiz do domínio gerenciado.
- O privilégio 'Replicar Alterações de Diretório' no contexto de nomenclatura de configuração (cn = contêiner de configuração) do domínio gerenciado.

Esse grupo de segurança também é membro do grupo interno **Acesso compatível anterior ao Windows 2000**.

![Grupo de segurança Contas de Serviço do Controlador de Domínio do AAD](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-properties.png)


## <a name="enable-your-managed-domain-to-support-sharepoint-server-user-profile-sync"></a>Habilitar seu domínio gerenciado para dar suporte à sincronização de perfil de usuário do SharePoint Server
Você pode adicionar a conta de serviço usada para sincronização de perfil de usuário do SharePoint ao grupo **Contas de Serviço do Controlador de Domínio do AAD**. Como resultado, a conta de sincronização recebe privilégios adequados para replicar as alterações no diretório. Essa etapa de configuração permite que a sincronização de perfil de usuário do SharePoint Server funcione corretamente.

![Contas de Serviço do Controlador de Domínio do AAD - adicionar membros](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-add-member.png)

![Contas de Serviço do Controlador de Domínio do AAD - adicionar membros](./media/active-directory-domain-services-admin-guide/aad-dc-service-accounts-add-member2.png)

## <a name="related-content"></a>Conteúdo relacionado
* [Referência técnica - Conceder permissões do Active Directory Domain Services para sincronização de perfil no SharePoint Server 2013](https://technet.microsoft.com/library/hh296982.aspx)
