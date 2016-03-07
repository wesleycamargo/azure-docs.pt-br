<properties
	pageTitle="Cenários de uso e considerações de implantação para a Junção do Azure AD| Microsoft Azure"
	description="Explica como os administradores podem configurar a Junção do AD do Azure para seus usuários finais (funcionários, estudantes, outros usuários). Ele também discute os diferentes cenários reais para usar a Junção do AD do Azure"
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""
	tags="azure-classic-portal"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/02/2016"

	ms.author="femila"/>

# Cenários de uso e considerações de implantação para a Junção do Azure AD

## Cenários de uso da Junção do Azure AD
### Cenário 1: empresas baseadas principalmente na nuvem

A Junção do Azure Active Directory (Junção do Azure AD) pode trazer benefícios se você operar e gerenciar identidades para seus negócios na nuvem ou se for migrar para a nuvem em breve. Você pode usar uma conta que já criou no Azure AD para entrar no Windows 10. Por meio de uma [experiência de FRX (experiência de primeira execução)](active-directory-azureadjoin-user-frx.md) ou unindo o Azure AD por meio do [menu de configurações](active-directory-azureadjoin-user-upgrade.md), seus usuários podem unir suas máquinas ao Azure AD. Agora seus usuários podem aproveitar as vantagens do SSO (logon único) a recursos de nuvem como o Office 365, no navegador ou em aplicativos do Office.

### Cenário 2: instituições de ensino

Instituições de ensino normalmente têm dois tipos de usuário: professores e alunos. Os professores são considerados integrantes de longo prazo da organização. É recomendável criar contas locais para eles. Os alunos, contudo, são membros temporários da organização e suas contas podem ser gerenciadas no Azure AD. Isso significa que a escala do diretório pode ser enviada para a nuvem em vez de ser armazenada localmente. Isso também significa que os alunos poderão se conectar no Windows com sua conta do Azure AD e obter acesso aos recursos do Office 365 em aplicativos do Office.

### Cenário 3: empresas de varejo

Lojas varejistas geralmente têm funcionários temporários e de longo prazo. Geralmente criamos contas locais e computadores unidos ao domínio para funcionários em tempo integral de longo prazo. Os funcionários temporários, contudo, são membros da organização temporariamente e, portanto, é recomendável gerenciar suas contas de maneira que seja possível mover as licenças de usuário mais facilmente. Ao criar essas contas de usuário na nuvem com licenças do Office 365, elas recebem os benefícios de entrar em aplicativos do Windows e do Office com uma conta do Azure AD, mantendo ainda uma maior flexibilidade das licenças quando eles deixam a empresa.

### Cenário 4: outros cenários

Além dos benefícios discutidos acima, você pode aproveitar as vantagens de ter usuários com dispositivos unidos ao Azure AD devido à experiência simplificada de junção, o gerenciamento eficiente de dispositivos no Azure AD, o registro automático de gerenciamento de dispositivo móvel e o logon único no Azure AD e em recursos locais.


## Considerações de implantação para a Junção do Azure AD

### Permitir que seus usuários unam um dispositivo da empresa diretamente ao Azure AD


As empresas podem fornecer contas somente de nuvem para organizações e empresas parceiras. Esses parceiros podem acessar facilmente os aplicativos da empresa e os recursos com o logon único. Esse cenário se aplica a usuários que acessam recursos primariamente na nuvem, tais como o Office 365 ou aplicativos SaaS que contam com o Azure AD para autenticação.

### Pré-requisitos
**No nível corporativo (administrador)**

*	Assinatura do Azure com o Active Directory do Azure  

**No nível do usuário**

*	Windows 10 (Professional e Enterprise Editions)

### Tarefas do administrador
* [Configure o registro de dispositivos](active-directory-azureadjoin-setup.md)

### Tarefas do usuário
* [Configurar um novo dispositivo Windows 10 com o Azure AD durante a instalação](active-directory-azureadjoin-user-frx.md)
* [Configurar um dispositivo Windows 10 com o Azure AD no menu de Configurações](active-directory-azureadjoin-user-upgrade.md)
* [Unir um dispositivo Windows 10 pessoal à sua organização](active-directory-azureadjoin-personal-device.md)



## Habilitar o BYOD na sua organização para o Windows 10
Você pode preparar seus funcionários e usuários para usarem seus dispositivos Windows pessoais (BYOD) para acessar aplicativos e recursos da empresa. Os usuários podem adicionar suas contas do Azure AD (contas corporativas ou de estudante) a um dispositivo Windows pessoal para acessar os recursos de maneira segura e compatível.

### Pré-requisitos
**No nível corporativo (administrador)**

*	Assinatura do AD do Azure

**No nível do usuário**

*	Windows 10 (Professional e Enterprise Editions)


### Tarefas do administrador

* [Configure o registro de dispositivos](active-directory-azureadjoin-setup.md)

### Tarefas do usuário
* [Unir um dispositivo Windows 10 pessoal à sua organização](active-directory-azureadjoin-personal-device.md)


## Informações adicionais
* [Windows 10 para a empresa: maneiras de usar dispositivos para o trabalho](active-directory-azureadjoin-windows10-devices-overview.md)
* [Estendendo os recursos de nuvem para dispositivos Windows 10 por meio da Junção do Active Directory do Azure](active-directory-azureadjoin-user-upgrade.md)
* [Autenticando identidades sem senhas com o Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Saiba mais sobre cenários de uso da Junção do Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Conectar dispositivos ingressados no domínio ao AD do Azure para experiências com o Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configurar a Junção do Azure AD](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_0224_2016--->