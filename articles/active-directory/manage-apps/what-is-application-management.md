---
title: Gerenciando aplicativos com o Azure Active Directory | Microsoft Docs
description: Este artigo apresenta os benefícios da integração do Active Directory do Azure com seus aplicativos locais, de nuvem e SaaS.
services: active-directory
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: overview
ms.workload: identity
ms.date: 10/30/2018
ms.author: celested
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a65c42ff2d03946c021497e350836623a408ce7
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56197514"
---
# <a name="application-management-with-azure-active-directory"></a>Gerenciamento de aplicativo com o Azure Active Directory

O Azure AD (Azure Active Directory) oferece acesso seguro e simples à nuvem e a aplicativos locais. Os usuários podem entrar uma vez para acessar o Office 365 e outros aplicativos de negócios da Microsoft, aplicativos SaaS (software como serviço), aplicativos locais e aplicativos de LOB (linha de negócios). Reduza os custos administrativos automatizando o provisionamento de usuários. Use a autenticação multifator e as políticas de acesso condicional para oferecer acesso seguro aos aplicativos.

![Aplicativos federados por meio do Azure AD](media/what-is-application-management/app-management-overview.png)

## <a name="why-manage-applications-with-a-cloud-solution"></a>Por que gerenciar aplicativos com uma solução de nuvem?

Geralmente, as organizações têm centenas de aplicativos que os usuários precisam para trabalhar. Os usuários acessam esses aplicativos em vários dispositivos e locais. Novos aplicativos são adicionados, desenvolvidos e publicados todos os dias. Com tantos aplicativos e pontos de acesso, é mais importante do que nunca usar uma solução baseada em nuvem para gerenciar o acesso dos usuários a todos os aplicativos.

## <a name="manage-risk-with-conditional-access-policies"></a>Gerenciar os riscos com as políticas de acesso condicional
Combinar o SSO (logon único) do Azure AD com as políticas de acesso condicional aumenta a segurança no acesso aos aplicativos. As funcionalidades de segurança incluem a proteção de identidade em escala de nuvem, o controle de acesso baseado em risco, a autenticação multifator nativa e as políticas de acesso condicional. Essas funcionalidades permitem ter um controle granular das políticas baseadas em aplicativos ou em grupos que precisam de mais segurança.

## <a name="improve-productivity-with-single-sign-on"></a>Aumentar a produtividade com o logon único
Habilitar o SSO (logon único) em todos os aplicativos e no Office 365 aprimora a experiência de logon para os usuários atuais ao reduzir ou eliminar as solicitações de entrada. O ambiente do usuário parece mais coeso e há menos distração sem as várias solicitações ou a necessidade de gerenciar diversas senhas. O grupo de negócios pode gerenciar e aprovar o acesso por meio de uma associação dinâmica de autoatendimento. Permitir que as pessoas certas na empresa gerenciem o acesso a um aplicativo aumenta a segurança do sistema de identidade.

O SSO melhora a segurança. *Sem o logon único*, os administradores precisam criar e atualizar as contas de usuário para cada aplicativo individual, o que leva tempo. Além disso, os usuários precisam controlar várias credenciais para acessar seus aplicativos. Dessa maneira, os usuários tendem a anotar suas senhas ou usar outras soluções de gerenciamento de senhas que apresentam riscos de segurança de dados. 

## <a name="address-governance-and-compliance"></a>Abordar a governança e a conformidade
Com o Azure AD, é possível monitorar os acessos ao aplicativo por meio de relatórios que usam as ferramentas de SIEM (gerenciamento de eventos e informações de segurança). É possível acessar os relatórios no portal ou nas APIs. Programaticamente, é possível auditar quem tem acesso aos aplicativos e remover o acesso de usuários inativos nas revisões de acesso.

## <a name="manage-costs"></a>Gerenciar os custos
Ao migrar para o Azure AD, você economiza custos e elimina a dificuldade de gerenciar uma infraestrutura local. O Azure AD também oferece acesso de autoatendimento para aplicativos, o que economiza tempo para administradores e usuários. O logon único elimina as senhas específicas de aplicativo. Essa capacidade de iniciar sessão uma vez economiza custos relacionados com a redefinição de senha para aplicativos e acaba com a perda de produtividade ao recuperar senhas.

