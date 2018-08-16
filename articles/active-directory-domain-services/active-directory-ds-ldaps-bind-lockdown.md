---
title: Associar usando o LDAP Seguro (LDAPS) para um domínio gerenciado do Azure Active Directory Domain Services | Microsoft Docs
description: Associar a um domínio gerenciado do Azure Active Directory Domain Services usando LDAP seguro (LDAPS)
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 6871374a-0300-4275-9a45-a39a52c65ae4
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: maheshu
ms.openlocfilehash: 9728d42710ce44226363ea4954d83fcc3efbfb75
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/03/2018
ms.locfileid: "39502945"
---
# <a name="bind-to-an-azure-ad-domain-services-managed-domain-using-secure-ldap-ldaps"></a>Associar a um domínio gerenciado do Azure Active Directory Domain Services usando LDAP seguro (LDAPS)

## <a name="before-you-begin"></a>Antes de começar
Concluir [Tarefa 4: configurar o DNS para acessar o domínio gerenciado pela Internet](active-directory-ds-ldaps-configure-dns.md).


## <a name="task-5-bind-to-the-managed-domain-over-ldap-using-ldpexe"></a>Tarefa 5: associar para o domínio gerenciado por LDAP usando LDP.exe
Você pode usar a ferramenta LDP.exe que está incluída no pacote da ferramenta de administração de servidor remoto para associar e pesquisar através de LDAP.

Primeiro, abra o LDP e conecte-se ao domínio gerenciado. Clique em **Conexão** e clique em **conectar...**  no menu. Especifique o nome de domínio DNS do domínio gerenciado. Especifique a porta a ser usada para conexões. Para conexões LDAP, use a porta 389. Para LDAPS conexões que usam a porta 636. Clique em **Okey** botão para se conectar ao domínio gerenciado.

Em seguida, vincule o domínio gerenciado. Clique em **Conexão** e clique em **vincular...**  no menu. Forneça as credenciais de uma conta de usuário que pertencem ao grupo 'Administradores de controlador de domínio do AAD'.

Selecione **exibição**e, em seguida, selecione **árvore** no menu. Deixe o campo Base DN em branco e clique em Okey. Navegue até o contêiner que você deseja pesquisar, o contêiner e selecione a pesquisa.

> [!TIP]
> - Os usuários e grupos sincronizados do AD do Azure são armazenados no **AADDC usuários** contêiner. O caminho de pesquisa para este contêiner aparência ```CN=AADDC\ Users,DC=CONTOSO100,DC=COM```.
> - Contas de computador para computadores que ingressaram no domínio gerenciado são armazenadas na **AADDC computadores** contêiner. O caminho de pesquisa para este contêiner aparência ```CN=AADDC\ Computers,DC=CONTOSO100,DC=COM```.
>
>

Mais informações - [Noções básicas de consulta LDAP](https://technet.microsoft.com/library/aa996205.aspx)


## <a name="task-6-lock-down-secure-ldap-access-to-your-managed-domain-over-the-internet"></a>Tarefa 6 – bloquear o acesso LDAP seguro a seu domínio gerenciado pela Internet
> [!NOTE]
> Se você não habilitou o acesso LDAPS para o domínio gerenciado pela Internet, ignore esta tarefa de configuração.
>
>

Antes de iniciar esta tarefa, conclua as etapas descritas na [Tarefa 3](active-directory-ds-admin-guide-configure-secure-ldap-enable-ldaps.md).

Ao habilitar o acesso LDAPS na internet para o seu domínio gerenciado, isso cria uma ameaça à segurança. O domínio gerenciado é acessível pela Internet na porta usada para LDAP seguro (ou seja, a porta 636). Você pode optar por restringir o acesso ao domínio gerenciado a endereços IP conhecidos específicos. Crie um grupo de segurança de rede (NSG) e associe-o à sub-rede na qual você habilitou o Azure AD Domain Services.

O exemplo de NSG na tabela a seguir bloqueia o acesso LDAP seguro pela internet. As regras no NSG permitem o acesso LDAP seguro de entrada pela porta TCP 636 de um conjunto especificado de endereços IP. A regra “DenyAll” padrão se aplica a todos os outros tráfegos de entrada da Internet. A regra de NSG para permitir o acesso LDAPS pela Internet de endereços IP especificados tem prioridade mais alta que a regra de NSG DenyAll.

![Exemplo de NSG para acesso LDAPS seguro pela Internet](./media/active-directory-domain-services-admin-guide/secure-ldap-sample-nsg.png)

**Mais informações** - [Grupos de segurança de rede](../virtual-network/security-overview.md).


## <a name="related-content"></a>Conteúdo relacionado
* [Serviços de Domínio do Azure AD - guia de Introdução](active-directory-ds-getting-started.md)
* [Administrar um domínio gerenciado dos Serviços de Domínio do Azure AD](active-directory-ds-admin-guide-administer-domain.md)
* [Noções básicas de consulta LDAP](https://technet.microsoft.com/library/aa996205.aspx)
* [Administrar a política de grupo em um domínio gerenciado do Azure AD Domain Services](active-directory-ds-admin-guide-administer-group-policy.md)
* [Grupos de segurança de rede](../virtual-network/security-overview.md)
* [Criar um Grupo de Segurança de Rede](../virtual-network/tutorial-filter-network-traffic.md)


## <a name="next-step"></a>Próxima etapa
[Solucionar problemas de LDAP seguro em um domínio gerenciado](active-directory-ds-ldaps-troubleshoot.md)
