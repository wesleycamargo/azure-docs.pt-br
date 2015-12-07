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
	ms.date="11/19/2015" 
	ms.author="femila"/>

# Cenários de uso e considerações de implantação para a Junção do Azure AD 

## Cenários de uso da Junção do Azure AD
Cenário 1: empresas baseadas principalmente na nuvem
--------------------------------------------------------
A Junção do Azure AD pode beneficiá-lo se você operar e gerenciar identidades para seus negócios na nuvem ou for migrar para a nuvem em breve. Você pode usar uma conta que já criou no Azure AD para entrar no Windows 10. Por meio de um [processo de FRX (experiência de primeira execução)](active-directory-azureadjoin-user-frx.md) ou unindo o Azure AD por meio da [experiência de Configurações](active-directory-azureadjoin-user-upgrade.md), seus usuários podem unir suas máquinas ao Azure AD. Agora, seus usuários têm acesso de SSO a seus recursos de nuvem, como o Office 365, no navegador ou em aplicativos do Office.

Cenário 2: instituições de ensino
----------------------------------------------------------------------------------
Instituições de ensino normalmente têm dois tipos de usuário: professores e alunos. Os membros do corpo docente são considerados integrantes de longo prazo da organização e é desejável criar contas locais para eles. Mas os alunos são membros de prazo mais curto da organização e, portanto, podem ser gerenciados no Azure AD, de modo que a escala do diretório pode ser enviada para a nuvem em vez do local. Agora, esses alunos podem entrar no Windows com sua conta do Azure AD e obter acesso aos recursos do Office 365 em aplicativos do Office.

Cenário 3: empresas de varejo
---------------------------------------------------------------------------------------
Empresas de varejo têm trabalhadores sazonais e funcionários de longo prazo. Os funcionários de longo prazo, em período integral, têm contas locais e normalmente usariam computadores dentro do domínio. Mas trabalhadores sazonais são membros de prazo mais curto da organização e, portanto, é desejável gerenciá-los onde seja possível mover as licenças de usuário mais facilmente. A criação desses usuários na nuvem com licenças do Office 365 permite que os usuários tenham os benefícios de entrar em aplicativos do Windows e do Office com uma conta do Azure AD, mantendo uma maior mobilidade de suas licenças quando eles saem.

Cenário 4: outros cenários
------------------------------------------------------------------------------------------
Além dos cenários discutidos acima, você pode se beneficiar ao ter seus usuários com dispositivos unidos ao Azure AD devido a uma experiência simplificada de junção, o gerenciamento de dispositivos no Azure AD, o registro automático do MDM e o logon único no Azure AD e recursos locais.


##Considerações de implantação para a Junção do Azure AD

Permitindo que seus usuários unam um dispositivo da empresa diretamente ao Azure AD
-----------------------------------------------------------------------------------------

As empresas podem fornecer contas somente de nuvem para organizações e empresas parceiras. Esses parceiros, em seguida, obtêm fácil acesso e logon único aos aplicativos e recursos da empresa. Esse cenário é aplicável a usuários que usam seus dispositivos principalmente para acessar recursos na nuvem, como o Office 365 ou aplicativos SaaS que dependem do Azure AD para autenticação.

### Pré-requisitos
**No nível corporativo (administrador)**

*	Assinatura do Azure com o Active Directory do Azure  

**No nível do usuário**

*	Windows 10 (SKUs Professional e Enterprise)

### Tarefas do administrador
* [Configure o registro de dispositivos](active-directory-azureadjoin-setup.md)

### Tarefas do usuário
* [Configura um novo dispositivo Windows 10 com o Azure AD durante a instalação](active-directory-azureadjoin-user-frx.md)
* [Configurar um dispositivo Windows 10 com o Azure AD nas Configurações](active-directory-azureadjoin-user-upgrade.md)
* [Unir um dispositivo Windows 10 pessoal à sua organização](active-directory-azureadjoin-personal-device.md)
  


## Permitir o BYOD na sua organização para o Windows 10
Você pode configurar seus funcionários e usuários para usarem seus dispositivos Windows pessoais para acessar aplicativos e recursos da empresa. Os usuários podem adicionar suas contas do Azure AD (contas de trabalho) a um dispositivo Windows pessoal para acessar recursos de trabalho de maneira segura e compatível.

### Pré-requisitos
**No nível corporativo (administrador)**

*	Assinatura do AD do Azure

**No nível do usuário**

*	Windows 10 (SKUs Professional e Enterprise)


### Tarefas do administrador

* [Configure o registro de dispositivos](active-directory-azureadjoin-setup.md)

### Tarefas do usuário
* [Unir um dispositivo Windows 10 pessoal à sua organização](active-directory-azureadjoin-personal-device.md)


## Informações adicionais
* [Windows 10 para a empresa: maneiras de usar dispositivos para o trabalho](active-directory-azureadjoin-windows10-devices-overview.md)
* [Estendendo os recursos de nuvem para dispositivos Windows 10 por meio da Junção do Active Directory do Azure](active-directory-azureadjoin-user-upgrade.md)
* [Autenticando identidades sem senhas com o Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Saiba mais sobre cenários de uso da Junção do Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Conectar dispositivos ingressados no domínio ao AD do Azure para experiências com Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configurar a Junção do Azure AD](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_1125_2015-->