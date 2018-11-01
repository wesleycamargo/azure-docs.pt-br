---
title: 'Azure Active Directory Domain Services: recursos | Microsoft Docs'
description: Recursos dos Serviços de Domínio do Active Directory do Azure
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: mtillman
editor: curtand
ms.assetid: 8d1c3eb3-1022-4add-a919-c98cc6584af1
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: ergreenl
ms.openlocfilehash: cd4fdb1e59f5b85fdfd8eb7ca17b77d02dcdac05
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/26/2018
ms.locfileid: "50157030"
---
# <a name="azure-ad-domain-services"></a>Azure AD Domain Services
## <a name="features"></a>Recursos
Os seguintes recursos estão disponíveis nos domínios gerenciados dos Serviços de Domínio do Azure AD.

* **Experiência de implantação simples:** com apenas alguns cliques, você pode habilitar o Azure AD Domain Services para seu diretório do Azure AD. Seu domínio gerenciado inclui contas de usuário somente em nuvem e contas de usuário sincronizadas de um diretório local.
* **Suporte para ingresso no domínio:** você pode facilmente ingressar computadores no domínio na rede virtual do Azure em que seu domínio gerenciado está disponível. A experiência de ingresso em sistemas operacionais cliente e servidor do Windows funciona perfeitamente nos domínios atendidos pelos Serviços de Domínio do Azure AD. Você também pode usar as ferramentas do ingresso no domínio automatizado com relação a esses domínios.
* **Uma instância de domínio por diretório do AD do Azure:** você pode criar um único domínio do Active Directory para cada diretório do AD do Azure.
* **Criar domínios com nomes personalizados:** você pode criar domínios com nomes personalizados (por exemplo, 'contoso100.com') usando os Azure AD Domain Services. Você pode usar qualquer nome de domínio verificado ou não. Opcionalmente, você também pode criar um domínio com o sufixo de domínio interno (ou seja, *.onmicrosoft.com) oferecido pelo seu diretório do Azure AD.
* **Integrado ao AD do Azure:** não é necessário configurar nem gerenciar a replicação para os Serviços de Domínio do AD do Azure. As contas de usuário, as associações de grupo e as credenciais de usuário (senhas) do seu diretório do Azure AD estão automaticamente disponíveis nos Azure AD Domain Services. Os novos usuários, os grupos ou as alterações a atributos em seu locatário do Azure AD ou em seu diretório local são automaticamente sincronizados com os Azure AD Domain Services.
* **Autenticação NTLM e Kerberos:** com suporte para autenticação NTLM e Kerberos, é possível implantar aplicativos que usam a Autenticação Integrada do Windows.
* **Usar suas credenciais corporativas/senhas:** senhas de usuários em seu locatário do AD do Azure funcionam com os Serviços de Domínio do AD do Azure. Os usuários podem usar as credenciais corporativas para ingressar em computadores no domínio, fazer logon interativamente ou pela área de trabalho remota e autenticar em relação ao domínio gerenciado.
* **Suporte de associação LDAP e leitura LDAP:** você pode usar aplicativos que dependem de associações LDAP para autenticar usuários em domínios atendidos pelos Azure AD Domain Services. Além disso, os aplicativos que usam operações de leitura LDAP para consultar atributos de usuário/computador a partir do diretório também podem funcionar nos Serviços de Domínio do AD do Azure.
* **LDAPS (LDAP seguro):** você pode habilitar o acesso ao diretório sobre LDAPS (LDAP seguro). Acesso por LDAP seguro está disponível na rede virtual por padrão. No entanto, você também pode habilitar o acesso por LDAP seguro pela Internet.
* **Política de grupo:** você pode usar um único objeto de política de grupo (GPO) para cada um dos usuários e contêineres de computadores, a fim de impor a conformidade com políticas de segurança necessárias para contas de usuário, bem como computadores ingressados no domínio. Você também pode criar seus próprios GPOs personalizados e atribuí-los às unidades organizacionais personalizadas para [gerenciar a política de grupo](active-directory-ds-admin-guide-administer-group-policy.md).
* **Gerenciar DNS:** membros do grupo 'Administradores do controlador de domínio do AAD' podem gerenciar o DNS para seu domínio gerenciado usando ferramentas de administração do DNS familiares, como o snap-in do MMC de administração do DNS.
* **Criar UOs (unidades organizacionais) personalizadas:** membros do grupo 'Administradores do controlador de domínio do AAD' podem criar UOs personalizadas dentro do domínio gerenciado. Esses usuários recebem privilégios administrativos totais sobre UOs personalizadas, de modo que podem adicionar ou remover contas de serviço, computadores, grupos etc. dessas OUs personalizadas.
* **Disponível em várias regiões globais do Azure:**, confira a página [Serviços do Azure por região](https://azure.microsoft.com/regions/#services/) para saber em quais regiões do Azure o Azure AD Domain Services está disponível.
* **Alta disponibilidade:** os Azure AD Domain Services oferecem alta disponibilidade para seu domínio. Esse recurso oferece a garantia de maior tempo de atividade de serviço e resiliência a falhas. O monitoramento da integridade interna oferece a correção automatizada de falhas gerando novas instâncias para substituir instâncias com falha e fornecer um serviço contínuo para seu domínio.
* **Proteção de bloqueio de Conta do AD:** as contas de usuários são bloqueadas por 30 minutos quando 5 senhas inválidas são usadas em um período de 2 minutos. As contas são desbloqueadas automaticamente depois de 30 minutos.
* **Usar ferramentas de gerenciamento conhecidas:** você pode usar ferramentas de gerenciamento conhecidas do Active Directory do Windows Server, como o Centro Administrativo do Active Directory ou o Active Directory PowerShell, para administrar domínios gerenciados.
