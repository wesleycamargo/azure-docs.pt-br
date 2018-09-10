---
title: Solucionar problemas do LDAP Seguro (LDAPS) nos Serviços de Domínio do Azure Active Directory Domain Services |Microsoft Docs
description: Solucionar problemas do LDAP Seguro (LDAPS) para um domínio gerenciado dos Serviços de Domínio do Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 445c60da-e115-447b-841d-96739975bdf6
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: maheshu
ms.openlocfilehash: 9713a06bbf6a61b316e061cb851721a3554cd632
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/03/2018
ms.locfileid: "39503665"
---
# <a name="troubleshoot-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Solucionar problemas do LDAP Seguro (LDAPS) para um domínio gerenciado dos Serviços de Domínio do Azure Active Directory Domain Services

## <a name="connection-issues"></a>Problemas de conexão
Se você tiver problemas para se conectar ao domínio gerenciado usando o LDAP seguro:

* A cadeia de emissor do certificado LDAP seguro deve ser confiável no cliente. Você pode optar por adicionar a autoridade de certificação raiz ao repositório de certificados raiz confiável no cliente para estabelecer a confiança.
* Verifique se o cliente LDAP (por exemplo, ldp.exe) se conecta ao ponto de extremidade LDAP seguro usando um nome DNS, não o endereço IP.
* Verifique o nome DNS que o cliente LDAP se conecta. Deve ser resolvido para o endereço IP público para LDAP seguro no domínio gerenciado.
* Verifique se o certificado LDAP seguro para seu domínio gerenciado tem o nome DNS no atributo Entidade ou Nome Alternativo da Entidade.
* As configurações de NSG para a rede virtual devem permitir o tráfego para a porta 636 da internet. Essa etapa se aplica apenas se você tiver habilitado o acesso LDAP seguro na internet.


## <a name="need-help"></a>Precisa de ajuda?
Se você ainda tiver problemas para se conectar com o domínio gerenciado usando o LDAP seguro, [contate a equipe do produto](active-directory-ds-contact-us.md) para obter ajuda. Inclua as seguintes informações para ajudar a diagnosticar melhor o problema:
* Uma captura de tela de ldp.exe fazendo a conexão e falhando.
* Sua ID de locatário do Azure AD e o nome de domínio DNS do seu domínio gerenciado.
* O nome de usuário exato como o qual você está tentando associar.


## <a name="related-content"></a>Conteúdo relacionado
* [Serviços de Domínio do Azure AD - guia de Introdução](active-directory-ds-getting-started.md)
* [Administrar um domínio gerenciado dos Serviços de Domínio do Azure AD](active-directory-ds-admin-guide-administer-domain.md)
* [Noções básicas de consulta LDAP](https://technet.microsoft.com/library/aa996205.aspx)
* [Administrar a política de grupo em um domínio gerenciado do Azure AD Domain Services](active-directory-ds-admin-guide-administer-group-policy.md)
* [Grupos de segurança de rede](../virtual-network/security-overview.md)
* [Criar um Grupo de Segurança de Rede](../virtual-network/tutorial-filter-network-traffic.md)
