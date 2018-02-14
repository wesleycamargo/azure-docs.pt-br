---
title: Preenchimento de UserPrincipalName do Azure AD
description: "Este documento descreve como o atributo UserPrincipalName é preenchido."
author: billmath
ms.author: billmath
ms.date: 02/02/2018
ms.topic: article
ms.workload: identity
ms.service: active-Directory
manager: mtillman
ms.openlocfilehash: 1fca41a8498cec506298748acd3511a5c5802d26
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2018
---
# <a name="azure-ad-userprincipalname-population"></a>Preenchimento de UserPrincipalName do Azure AD

Este artigo descreve como o atributo UserPrincipalName é preenchido no Azure AD (Azure Active Directory).
O valor do atributo UserPrincipalName é o nome de usuário do Azure AD para as contas de usuário.

## <a name="upn-terminology"></a>Terminologia de UPN
A seguinte terminologia é usada neste artigo:

|Termo|DESCRIÇÃO|
|-----|-----|
|Domínio inicial|O domínio padrão (monmicrosoft.com) no locatário do Azure AD. Por exemplo, contoso.onmicrosoft.com.|
|MOERA (endereço de roteamento de email online da Microsoft)|O Azure AD calcula o MOERA por meio do atributo MailNickName do Azure AD e do domínio inicial do Azure AD como: &lt;MailNickName&gt;&#64;&lt;domínio inicial&gt;.|
|Atributo mailNickName local|Um atributo no Active Directory, cujo valor representa o alias de um usuário em uma organização do Exchange.|
|Atributo de email local|Um atributo no Active Directory, cujo valor representa o endereço de email de um usuário|
|Endereço SMTP primário|O endereço de email primário de um objeto de destinatário do Exchange. Por exemplo, SMTP:user@contoso.com.|
|ID de logon alternativa|Um atributo local diferente do UserPrincipalName, como um atributo de email, usado para conexão.|

## <a name="what-is-userprincipalname"></a>O que é o UserPrincipalName?
O UserPrincipalName é um atributo que é um nome de logon de um usuário, no estilo da Internet, com base no padrão [RFC 822](http://www.ietf.org/rfc/rfc0822.txt) da Internet. 

### <a name="upn-format"></a>Formato UPN
Um UPN consiste em um prefixo UPN (o nome da conta de usuário) e um sufixo UPN (um nome de domínio de DNS). O prefixo é unido ao sufixo com o uso do símbolo "@". Por exemplo, "someone@example.com". Um UPN deve ser exclusivo entre todos os objetos de entidade de segurança em uma floresta de diretórios. 

## <a name="upn-in-azure-ad"></a>UPN no Azure AD 
O UPN é usado pelo Azure AD para permitir que os usuários se conectem.  O UPN que um usuário pode usar depende de se o domínio foi ou não verificado.  Se o domínio foi verificado, um usuário com sufixo terá permissão para entrar no Azure AD.  

O atributo é sincronizado pelo Azure AD Connect.  Durante a instalação, você pode exibir os domínios que foram verificados e aqueles que não foram.

   ![Domínios não verificados](./media/active-directory-aadconnect-get-started-express/unverifieddomain.png) 

## <a name="alternate-login-id"></a>ID de logon alternativa
Em alguns ambientes, por causa da política corporativa ou das dependências locais de aplicativos de linha de negócios, os usuários finais podem ter conhecimento somente de seus endereços de email e não dos UPNs.

A ID de logon alternativa permite configurar uma experiência de logon na qual os usuários podem se conectar com um atributo que não seja o UPN, como o email.

Ao usar o Azure AD Connect, não são necessárias etapas de configurações adicionais para se habilitar a ID de logon alternativa com o Azure AD. A ID alternativa pode ser configurada no assistente. Consulte a configuração de conexão do Azure AD dos usuários na seção Sincronização. Na lista suspensa **Nome UPN**, selecione o atributo para a ID de logon alternativa.

![Domínios não verificados](./media/active-directory-aadconnect-userprincipalname/altloginid.png)  

Para obter mais informações, consulte [Configurar ID de logon alternativa](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) e [Configuração de conexão do Azure AD](active-directory-aadconnect-get-started-custom.md#azure-ad-sign-in-configuration)

## <a name="non-verified-upn-suffix"></a>Sufixo UPN não verificado
Se o atributo local UserPrincipalName/sufixo de ID de logon alternativa não for verificado com o locatário do Azure AD, o valor do atributo UserPrincipalName do Azure AD será definido como MOERA. O Azure AD calcula o MOERA usando o atributo MailNickName do Azure AD e o domínio inicial do Azure AD como: &lt;MailNickName&gt;&#64;&lt;domínio inicial&gt;.

## <a name="verified-upn-suffix"></a>Sufixo UPN verificado
Se o atributo local UserPrincipalName/sufixo de ID de logon alternativa for verificado com o locatário do Azure AD, o valor do atributo UserPrincipalName do Azure AD será igual ao atributo local UserPrincipalName/valor de ID de logon alternativa.

## <a name="azure-ad-mailnickname-attribute-value-calculation"></a>Cálculo do valor do atributo MailNickName do Azure AD
Como o valor do atributo UserPrincipalName do Azure AD pode ser definido como MOERA, é importante entender como o valor do atributo MailNickName do Azure AD, que é o prefixo de MOERA, é calculado.

Quando um objeto de usuário é sincronizado com um locatário do Azure AD pela primeira vez, o Azure AD verifica o seguinte, na ordem definida e define o valor do atributo MailNickName como o primeiro existente:

- Atributo mailNickName local
- Prefixo do atributo de email local
- Prefixo do endereço SMTP primário
- Prefixo do atributo local userPrincipalName/ID de logon alternativa

Quando as atualizações de um objeto de usuário são sincronizadas com o locatário do Azure AD, o Azure AD atualiza o valor do atributo MailNickName somente no caso de uma atualização no valor do atributo mailNickName local.

>[!IMPORTANT]
>O Azure AD recalculará o valor do atributo UserPrincipalName somente se uma atualização no valor do atributo UserPrincipalName local/ID de logon alternativa for sincronizada com o locatário do Azure AD. 
>
>Sempre que o Azure AD recalcula o atributo UserPrincipalName, ele também recalcula o MOERA. 

## <a name="upn-scenarios"></a>Cenários de UPN
A seguir estão alguns cenários de exemplo de como o UPN é calculado com base em uma determinada situação.

### <a name="scenario-1-non-verified-upn-suffix--initial-synchronization"></a>Cenário 1: sufixo UPN não verificado – sincronização inicial

Objeto de usuário local:
- mailNickName: &lt;não definido&gt;
- email: us1@contoso.com
- proxyAddresses: {SMTP:us2@contoso.com}
- userPrincipalName: us3@contoso.com`

Objeto de usuário sincronizado com o locatário do Azure AD pela primeira vez
- Defina o atributo MailNickName do Azure AD como prefixo de atributo de email local.
- Defina MOERA como &lt;MailNickName&gt;&#64;&lt;domínio inicial&gt;.
- Defina o atributo UserPrincipalName do Azure AD como MOERA.

Objeto de usuário do locatário do Azure AD:
- MailNickName: us1           
- UserPrincipalName: us1@contoso.onmicrosoft.com


### <a name="scenario-2-non-verified-upn-suffix--set-on-premises-mailnickname-attribute"></a>Cenário 2: sufixo UPN não verificado – definir atributo mailNickName local

Objeto de usuário local:
- mailNickName      : us4
- email: us1@contoso.com
- proxyAddresses: {SMTP:us2@contoso.com}
- userPrincipalName: us3@contoso.com

Sincronizar a atualização no atributo mailNickName local com o locatário do Azure AD
- Atualize o atributo MailNickName do Azure AD com o atributo de mailNickName local.
- Como não há atualização para o atributo userPrincipalName local, não há alteração no atributo UserPrincipalName do Azure AD.

Objeto de usuário do locatário do Azure AD:
- MailNickName      : us4
- UserPrincipalName: us1@contoso.onmicrosoft.com

### <a name="scenario-3-non-verified-upn-suffix--update-on-premises-userprincipalname-attribute"></a>Cenário 3: sufixo UPN não verificado – atualizar atributo userPrincipalName local

Objeto de usuário local:
- mailNickName      : us4
- email: us1@contoso.com
- proxyAddresses: {SMTP:us2@contoso.com}
- userPrincipalName: us5@contoso.com

Sincronizar a atualização no atributo userPrincipalName local com o locatário do Azure AD
- A atualização no atributo userPrincipalName local dispara o recálculo do MOERA e do atributo UserPrincipalName do Azure AD.
- Defina MOERA como &lt;MailNickName&gt;&#64;&lt;domínio inicial&gt;.
- Defina o atributo UserPrincipalName do Azure AD como MOERA.

Objeto de usuário do locatário do Azure AD:
- MailNickName      : us4
- UserPrincipalName: us4@contoso.onmicrosoft.com

### <a name="scenario-4-non-verified-upn-suffix--update-on-premises-mail-attribute-and-primary-smtp-address"></a>Cenário 4: sufixo UPN não verificado – atualizar atributo de email local e o endereço SMTP primário

Objeto de usuário local:
- mailNickName      : us4
- email: us6@contoso.com
- proxyAddresses: {SMTP:us7@contoso.com}
- userPrincipalName: us5@contoso.com

Sincronizar a atualização no atributo de email local e no endereço SMTP primário para o locatário do Azure AD
- Após a sincronização inicial do objeto de usuário, as atualizações no atributo de email local e no endereço SMTP primário não afetarão o atributo MailNickName nem o UserPrincipalName do Azure AD.

Objeto de usuário do locatário do Azure AD:
- MailNickName      : us4
- UserPrincipalName: us4@contoso.onmicrosoft.com

### <a name="scenario-5-verified-upn-suffix--update-on-premises-userprincipalname-attribute-suffix"></a>Cenário 5: sufixo UPN verificado – atualizar o sufixo do atributo userPrincipalName local

Objeto de usuário local:
- mailNickName      : us4
- email: us6@contoso.com
- proxyAddresses: {SMTP:us7@contoso.com}
- serPrincipalName  : us5@verified.contoso.com

Sincronizar a atualização no atributo userPrincipalName local com o locatário do Azure AD
- A atualização no atributo userPrincipalName local dispara o recálculo do atributo UserPrincipalName do Azure AD.
- Defina o atributo UserPrincipalName do Azure AD como o atributo userPrincipalName local, pois o sufixo UPN está verificado no locatário do Azure AD.

Objeto de usuário do locatário do Azure AD:
- MailNickName      : us4     
- UserPrincipalName: us5@verified.contoso.com

## <a name="next-steps"></a>Próximas etapas
- [Integrar seus diretórios locais no Azure Active Directory](active-directory-aadconnect.md)
- [Instalação personalizada do Azure AD Connect](active-directory-aadconnect-get-started-custom.md)
