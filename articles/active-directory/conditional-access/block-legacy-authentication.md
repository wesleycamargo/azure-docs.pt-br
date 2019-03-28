---
title: Como bloquear autenticação herdada para Azure AD (Azure Active Directory) com acesso condicional | Microsoft Docs
description: Saiba como melhorar sua postura de segurança bloqueando a autenticação herdada usando o acesso condicional do Azure AD.
services: active-directory
keywords: acesso condicional para aplicativos, acesso condicional com o Azure AD, acesso seguro aos recursos da empresa, políticas de acesso condicional
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.subservice: conditional-access
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/25/2019
ms.author: joflore
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d2841d3be584cae45ef49ad9ff20da8a232c366
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58519911"
---
# <a name="how-to-block-legacy-authentication-to-azure-ad-with-conditional-access"></a>Como: Bloquear autenticação herdada para Azure AD com acesso condicional   

Para fornecer aos usuários acesso fácil aos aplicativos na nuvem, o Azure AD (Azure Active Directory) dá suporte a uma ampla variedade de protocolos de autenticação, incluindo a autenticação herdada. No entanto, protocolos herdados não dão suporte para MFA (autenticação multifator). Em muitos ambientes, a MFA é um requisito comum para lidar com roubo de identidade. 


Se o ambiente estiver pronto para bloquear a autenticação herdada para melhorar a proteção do locatário, você poderá atingir essa meta com acesso condicional. Este artigo explica como é possível configurar políticas de acesso condicional que bloqueiam autenticação herdada para locatário.



## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você esteja familiarizado com: 

- Os [conceitos básicos](overview.md) de acesso condicional do Azure AD 
- As [melhores práticas](best-practices.md) para configurar políticas de acesso condicional no portal do Azure



## <a name="scenario-description"></a>Descrição do cenário

O Azure AD dá suporte para vários dos protocolos de autenticação e autorização mais amplamente utilizados, incluindo a autenticação herdada. Autenticação herdada refere-se a protocolos que usam autenticação básica. Normalmente, esses protocolos não podem impor nenhum tipo de autenticação de segundo fator. Exemplos de aplicativos baseados em autenticação herdada são:

- Aplicativos mais antigos do Microsoft Office

- Aplicativos que usam protocolos de email como POP, IMAP e SMTP

Autenticação de fator único (por exemplo, nome de usuário e senha) atualmente não é suficiente. Senhas são ruins porque são fáceis de adivinhar e nós (humanos) dificilmente escolhemos boas senhas. Senhas também são vulneráveis a uma variedade de ataques, como pulverização de senha e phishing. Uma das medidas mais fáceis que você pode tomar para proteção contra ameaças de senha é implementar MFA. Com MFA, mesmo se um invasor possuir a senha de um usuário, somente a senha não será suficiente para autenticar e acessar os dados com êxito.

Como é possível impedir que aplicativos usando autenticação herdada acessem os recursos do locatário? A recomendação é apenas bloqueá-los com uma política de acesso condicional. Se necessário, você permite que apenas determinados usuários e locais de rede específicos usem aplicativos baseados em autenticação herdada.

As políticas de acesso condicional são impostas após a conclusão da autenticação multifator. Portanto, o acesso condicional não funciona como uma primeira linha de defesa para cenários como ataques de DoS (ataque de negação de serviço), mas pode utilizar os sinais desses eventos (por exemplo, o nível de risco de entrada, a localização da solicitação e assim por diante) para determinar o acesso.




## <a name="implementation"></a>Implementação

Esta seção explica como configurar uma política de acesso condicional para bloquear a autenticação herdada. 

### <a name="block-legacy-authentication"></a>Bloquear a autenticação herdada 

Em uma política de acesso condicional, é possível definir uma condição vinculada aos aplicativos clientes usados para acessar os recursos. A condição de aplicativos cliente permite restringir o escopo a aplicativos usando autenticação herdada, selecionando **Outros clientes** para **Aplicativos móveis e clientes de desktop**.

![Outros clientes](./media/block-legacy-authentication/01.png)

Para bloquear o acesso a esses aplicativos, é necessário selecionar **Bloquear acesso**.

![Acesso bloqueado](./media/block-legacy-authentication/02.png)


### <a name="select-users-and-cloud-apps"></a>Selecione usuários e aplicativos na nuvem

Se você quiser bloquear a autenticação herdada para sua organização, provavelmente pressupõe que é possível fazer isso, selecionando:

- Todos os usuários

- Todos os aplicativos em nuvem

- Acesso bloqueado
 

![Atribuições](./media/block-legacy-authentication/03.png)



O Azure tem um recurso de segurança que impede a criação de uma política como essa, pois essa configuração viola as [melhores práticas](best-practices.md) para políticas de acesso condicional.
 
![Sem suporte para configuração de política](./media/block-legacy-authentication/04.png)


O recurso de segurança é necessário porque *bloqueia todos os usuários e todos os aplicativos na nuvem* e tem o potencial de impedir que toda a organização faça autenticação no locatário. É necessário excluir pelo menos um usuário para satisfazer o requisito mínimo de melhor prática. Também é possível excluir uma função de diretório.

![Sem suporte para configuração de política](./media/block-legacy-authentication/05.png)


Você pode satisfazer esse recurso de segurança, excluindo um usuário da sua política. O ideal, é definir algumas [contas administrativas de acesso para emergência no Azure AD](../users-groups-roles/directory-emergency-access.md) e excluí-las da política.
 

## <a name="policy-deployment"></a>Implantação de política

Antes de colocar a política em produção, atente-se para:
 
- **Contas de serviço** - Identifique as contas de usuário que são usadas como contas de serviço ou por dispositivos, como telefones de sala de conferência. Certifique-se de que essas contas têm senhas fortes e adicione-as a um grupo excluído.
 
- **Relatórios de entradas** - Analise o relatório de entrada e procure **outro tráfego de cliente**. Identifique o uso principal e investigue por que ele está em uso. Normalmente, o tráfego é gerado por clientes mais antigos do Office que não usam autenticação moderna ou alguns aplicativos de email de terceiros. Faça um plano para afastar o uso desses aplicativos ou, se o impacto for baixo, notifique os usuários de que eles não podem mais usar esses aplicativos.
 
Para obter mais informações, consulte [Como implantar uma nova política?](best-practices.md#how-should-you-deploy-a-new-policy).



## <a name="what-you-should-know"></a>O que você deve saber

Bloqueio de acesso usando **outros clientes** também bloqueia o PowerShell do Exchange Online usando a autenticação básica.

A configuração de uma política para **Outros clientes** bloqueia determinados clientes, como SPConnect, para toda a organização. Esse bloqueio acontece porque os clientes mais antigos são autenticados de maneiras inesperadas. O problema não se aplica aos principais aplicativos do Office, como os antigos clientes do Office.

Pode levar até 24 horas para que a política entre em vigor.

É possível selecionar todos os controles de concessão disponíveis para a condição de outros clientes, no entanto, a experiência do usuário final será sempre a mesma - acesso bloqueado.

Se você bloquear a autenticação herdada usando a condição de outros clientes, também poderá definir a plataforma do dispositivo e a condição da localização. Por exemplo, se você quiser bloquear apenas a autenticação herdada para dispositivos móveis, defina a condição **plataformas de dispositivo** selecionando:

- Android

- iOS

- Windows Phone

![Sem suporte para configuração de política](./media/block-legacy-authentication/06.png)




## <a name="next-steps"></a>Próximas etapas

- Se você ainda não estiver familiarizado com a configuração de políticas de acesso condicional, consulte [Exigir MFA para aplicativos específicos com acesso condicional do Azure Active Directory](app-based-mfa.md), para obter um exemplo.

- Para obter mais informações sobre o suporte a autenticação moderna, consulte [como a autenticação moderna funciona para aplicativos de cliente do Office 2013 e Office 2016](https://docs.microsoft.com/en-us/office365/enterprise/modern-auth-for-office-2013-and-2016) 
