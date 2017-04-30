---
title: "Acesso condicional para usuários de colaboração B2B do Azure Active Directory | Microsoft Docs"
description: "A colaboração B2B do Azure Active Directory dá suporte à autenticação multifator (MF) para acesso seletivo aos seus aplicativos corporativos"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 04/11/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: 7f469fb309f92b86dbf289d3a0462ba9042af48a
ms.openlocfilehash: fac4657e9b78e900c052384e212aa9f3915f970d
ms.lasthandoff: 04/13/2017


---

# <a name="conditional-access-for-b2b-collaboration-users"></a>Acesso condicional para usuários de colaboração B2B

## <a name="multi-factor-authentication-for-b2b-users"></a>Autenticação multifator para usuários B2B
Com a colaboração B2B do Azure AD, as organizações têm a capacidade de aplicar políticas de autenticação multifator para usuários B2B de forma exclusiva. Essas políticas podem ser impostas no nível dos locatários, nível do aplicativo ou nível de usuário individual, da mesma maneira que essas políticas podem ser habilitadas para membros e funcionários em tempo integral da organização. Politicas de MFA são impostas na organização do recurso.

Isso significa que:
1. O Administrador ou o operador de informações na Empresa A convida o usuário da Empresa B para um aplicativo Foo na Empresa A.
2. O aplicativo *Foo* na empresa A está configurado para exigir MFA no acesso.
3. Quando o usuário da empresa B tenta acessar o aplicativo Foo do locatário da empresa A, receberá uma solicitação para concluir um desafio de MFA, conforme exigido pela política da MFA da empresa.
4. Usuário pode configurar sua MFA com a empresa A, e escolher a opção de MFA
5. Isso funcionará para qualquer identidade (Azure AD ou MSA, por exemplo, se os usuários na Empresa B autenticarem usando a ID social)
6. A Empresa A precisará ter SKUs premium do Azure AD adequados que ofereçam suporte a MFA. O usuário da Empresa B consumirá essa licença da Empresa A.

Resumindo, o locatário que convida *sempre* é responsável pela MFA dos usuários de colaboração B2B da organização parceira, e não pela organização parceira (mesmo que tenha recursos de MFA).

### <a name="setting-up-mfa-for-b2b-collaboration-users"></a>Definindo a MFA para usuários de colaboração B2B
Para descobrir como é fácil configurar a MFA para usuários de colaboração B2B, confira o vídeo a seguir:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup/Player]

### <a name="b2b-users-mfa-experience-for-offer-redemption"></a>Experiência de MFA de usuários B2B para resgate de oferta
Confira a animação abaixo para ver a experiência de resgate, conforme mostrado no vídeo a seguir:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/MFA-redemption/Player]

### <a name="mfa-reset-for-b2b-collaboration-users"></a>Redefinição de MFA para usuários de colaboração B2B
Atualmente, o administrador pode exigir que os usuários de colaboração B2B façam uma verificação novamente usando somente os seguintes cmdlets do PowerShell. Portanto, os seguintes cmdlets do PowerShell devem ser usados se você quiser redefinir o método de verificação do usuário de colaboração B2B.

1. Conecte-se ao AD do Azure

  ```
  $cred = Get-Credential
  Connect-MsolService -Credential $cred
  ```
2. Obter todos os usuários com métodos de verificação

  ```
  Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
  ```
  Aqui está um exemplo:

  ```
  PS C:\Users\tjwasserGet-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
  ```

3. Redefina o método de MFA para um usuário específico. Você pode usar esse UserPrincipalName para executar o comando reset a fim de exigir que o usuário de colaboração B2B defina os métodos de verificação novamente. Exemplo:

  ```
  Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
  ```

### <a name="why-do-we-perform-mfa-at-the-resource-tenancy"></a>Por que realizamos MFA na locação de recursos?

Na versão atual, a MFA está sempre na locação de recursos. A razão para isso é previsibilidade.

Por exemplo, digamos que um usuário da Contoso (Sally) é convidado para a Fabrikam e a Fabrikam habilitou MFA para usuários B2B.

Agora, se a Contoso tem política MFA habilitada para App1 mas não para App2 – então, se observarmos a declaração de MFA da Contoso no token para determinar se Sally deveria habilitar MFA na Fabrikam ou não, o seguinte problema poderia acontecer:

* Dia 1: Sally tem MFA-ed na Contoso porque ela está acessando App1, então, ela não verá o prompt de MFA na Fabrikam.

* Dia 2: Sally acessou o App 2 na Contoso, agora, quando ela acessar Fabrikam, ela terá que fazer o registro para MFA na Fabrikam.

Isso pode ser bastante confuso para Sally e, provavelmente, resultará em falha ao concluir o acesso.

Além disso, mesmo se a Contoso tiver o recurso MFA, nem sempre aconteceria da Fabrikam confiar na política do MFA.

Finalmente, o MFA de locatários de recursos também funciona para IDs sociais e MSAs e para organizações parceiras que não tenham um MFA configurado.

Portanto, a recomendação para MFA para usuários B2B é sempre exigir recursos MFA. Em alguns casos isso poderia conduzir a um MFA duplo, no entanto, sempre que acessar a locação de recursos a experiência dos usuários finais será previsível: Sally deve se registrar para MFA com a locação de recursos.

### <a name="device-location-and-risk-based-conditional-access-for-b2b-users"></a>Acesso condicional com base em risco, local e dispositivo para usuários B2B

Quando o dispositivo da organização Contoso habilita políticas de acesso condicional com base em dispositivo para seus dados corporativos, o acesso é impedido para dispositivos não gerenciados (ou seja, dispositivos que não são gerenciados pela organização Contoso e incompatíveis com as políticas de dispositivo da Contoso).

Se o dispositivo do usuário B2B não for gerenciado pela Contoso, isso significa que o acesso de usuários B2B das organizações parceiras será bloqueado em quaisquer contextos em que essas políticas forem impostas.

É complicado esperar que usuários de outra organização tenham seu dispositivo gerenciado pela organização emissora do convite. Portanto, em futuras atualizações permitiremos que a Contoso confie no status de conformidade do dispositivo de um parceiro específico. Isso permitiria à Contoso impor políticas em que um usuário da Fabrikam poderia acessar recursos da Contoso se também estivesse utilizando um dispositivo gerenciado da Fabrikam.

Entretanto, a Contoso pode criar listas de exclusão contendo usuários parceiros específicos a partir da política de acesso condicional com base em dispositivo.

#### <a name="location-based-conditional-access-for-b2b"></a>Acesso condicional com base em local para B2B

Políticas de acesso condicional com base em local podem ser impostas para os usuários B2B se a organização convidando (por exemplo, a Contoso) for capaz de criar um perímetro de rede confiável (ou seja, um intervalo de endereços IP) que define suas organizações parceiras (por exemplo, a Fabrikam).

#### <a name="risk-based-conditional-access-for-b2b"></a>Acesso condicional com base em risco para B2B

Atualmente, as políticas baseadas em risco de entrada não podem ser aplicadas a usuários B2B porque a avaliação de risco é realizada na organização inicial do usuário B2B (ou seja, a locação de identidade do usuário B2B).

Para atualizações futuras, estamos considerando federar a pontuação de risco dos parceiros (com o consentimento do parceiro) para que a Contoso possa proteger dados e aplicativos compartilhados externamente não apenas dos riscos conhecidos, mas também, dos riscos desconhecidos, já que podem estar ocorrendo em outros lugares.

## <a name="next-steps"></a>Próximas etapas

Procure nossos outros artigos sobre a colaboração B2B do AD do Azure:

* [O que é a colaboração B2B do AD do Azure?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Como os administradores do Azure Active Directory adicionam usuários de colaboração B2B?](active-directory-b2b-admin-add-users.md)
* [Como os operadores de informação adicionam usuários de colaboração B2B?](active-directory-b2b-iw-add-users.md)
* [The elements of the B2B collaboration invitation email](active-directory-b2b-invitation-email.md) (Os elementos do email de convite para colaboração B2B)
* [Resgate de convite de colaboração B2B](active-directory-b2b-redemption-experience.md)
* [Licenciamento da colaboração B2B do Azure AD](active-directory-b2b-licensing.md)
* [Solução de problemas de colaboração B2B do Azure Active Directory](active-directory-b2b-troubleshooting.md)
* [Perguntas frequentes sobre a colaboração B2B do Azure Active Directory](active-directory-b2b-faq.md)
* [API e personalização da colaboração B2B do Azure Active Directory](active-directory-b2b-api.md)
* [Adicionar usuários de colaboração B2B sem um convite](active-directory-b2b-add-user-without-invite.md)
* [Índice de artigos para Gerenciamento de Aplicativos no Active Directory do Azure](active-directory-apps-index.md)

