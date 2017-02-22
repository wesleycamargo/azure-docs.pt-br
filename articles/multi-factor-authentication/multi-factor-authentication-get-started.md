---
title: Escolha entre a nuvem ou servidor do Azure MFA | Microsoft Docs
description: "Escolha a solução de segurança de autenticação multifator ideal para você perguntando o que estou tentando proteger e onde os meus usuários estão localizados.  Em seguida, escolha nuvem, Servidor MFA ou AD FS."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: ec2270ea-13d7-4ebc-8a00-fa75ce6c746d
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/23/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 92c552f42f2645281f5039242825cfdaf6b683c9
ms.openlocfilehash: 1a42c137204c2d2f6fff9a005b2b0e885c2002b2


---
# <a name="choose-the-azure-multi-factor-authentication-solution-for-you"></a>Escolha a solução Autenticação Multifator para você
Como há vários tipos de MFA (Autenticação Multifator do Azure), precisamos responder a algumas perguntas para descobrir qual versão é a correta para usar.  Essas perguntas são:

* [O que estou tentando proteger](#what-am-i-trying-to-secure)
* [Onde os usuários estão localizados](#where-are-the-users-located)
* [De quais recursos preciso?](#what-featured-do-i-need)

As seções a seguir fornecem orientações sobre como determinar a cada uma delas.

## <a name="what-am-i-trying-to-secure"></a>O que estou tentando proteger?
Para determinar a solução de verificação em duas etapas correta, precisamos saber o que você está tentando proteger com um segundo método de autenticação.  É um aplicativo no Azure?  Ou um sistema de acesso remoto?  Ao determinar o que estamos tentando proteger, passaremos saber onde a Autenticação Multifator precisa ser habilitada.  

| O que você está tentando proteger | MFA na nuvem | Servidor MFA |
| --- |:---:|:---:|
| Aplicativos primários da Microsoft |● |● |
| Aplicativos SaaS da Galeria de Aplicativos |● |● |
| Aplicativos Web publicados por meio da Proxy de Aplicativo do Azure AD |● |● |
| Aplicativos IIS não publicados por meio da Proxy de aplicativo do Azure AD | |● |
| Acesso remoto, como VPN, RDG | |● |

## <a name="where-are-the-users-located"></a>Onde os usuários estão localizados
Em seguida, ver onde se encontram nossos usuários ajuda a determinar a solução correta a ser usada, seja na nuvem ou no local, usando o servidor MFA.

| Local do usuário | MFA na nuvem | Servidor MFA |
| --- |:---:|:---:|
| Azure Active Directory |● | |
| Azure AD e AD local usando federação com AD FS |● |● |
| Azure AD e AD local usando o DirSync, o Azure AD Sync, o Azure AD Connect - sem sincronização de senha |● |● |
| Azure AD e AD local usando o DirSync, o Azure AD Sync, o Azure AD Connect - com sincronização de senha |● | |
| Active Directory local | |● |

## <a name="what-features-do-i-need"></a>De quais recursos preciso?
A tabela a seguir é uma comparação dos recursos que estão disponíveis com Autenticação Multifator na nuvem e com o Servidor de Autenticação Multifator.

| Recurso | MFA na nuvem | Servidor MFA |
| --- |:---:|:---:|
| Notificação de aplicativo móvel como um segundo fator | ● | ● |
| Código de verificação de aplicativo móvel como um segundo fator | ● | ● |
| Chamada telefônica como um segundo fator | ● | ● |
| SMS unidirecional como segundo fator | ● | ● |
| SMS bidirecional como segundo fator | | ● |
| Tokens de hardware como segundo fator | | ● |
| Senhas de aplicativos para clientes do Office 365 que não oferecem suporte a MFA | ● | |
| Controle do administrador sobre métodos de autenticação | ● | ● |
| Modo PIN | | ● |
| Alerta de fraude |● | ● |
| Relatórios de MFA |● | ● |
| Desvio único | | ● |
| Saudações personalizadas para chamadas telefônicas | ● | ● |
| ID do chamador personalizável para chamadas telefônicas | ● | ● |
| IPs confiáveis | ● | ● |
| Lembrar MFA para dispositivos confiáveis | ● | |
| Acesso condicional | ● | ● |
| Cache |  | ● |

Agora que determinamos se usar a autenticação multifator na nuvem ou o Servidor MFA local, podemos pode começar a configurar e usar o Azure Multi-Factor Authentication. **Selecione o ícone que representa seu cenário.**

<center>




[![Nuvem](./media/multi-factor-authentication-get-started/cloud2.png)](multi-factor-authentication-get-started-cloud.md)  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[![Prova](./media/multi-factor-authentication-get-started/server2.png)](multi-factor-authentication-get-started-server.md) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
</center>



<!--HONumber=Jan17_HO4-->


