

<properties
	pageTitle="Ingressar em um dispositivo pessoal para sua organização| Microsoft Azure"
	description="Explica como os usuários podem registrar seus dispositivos Windows 10 pessoais em sua rede corporativa e fornece as etapas de implantação para um cenário BYOD."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="swadhwa"
	editor=""
	tags="azure-classic-portal"/>
<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/27/2016"
	ms.author="femila"/>

# Ingressar em um dispositivo pessoal para sua organização

## Para ingressar em um dispositivo Windows 10 para sua organização

1.	No menu **Iniciar**, selecione **Configurações**.
2.	Selecione **Contas** e, em seguida, clique em **Sua conta**.
3.	Clique em **Adicionar conta corporativa ou de estudante** e, em seguida, digite sua conta organizacional.
4.	Na página de credenciais da sua organização, insira seu nome de usuário e senha e clique em **OK**.
5.	Um desafio de Multi-Factor Authentication será apresentado a você. (Esse desafio é configurável por um administrador de TI.)
6.	O Azure Active Directory (Azure AD) verifica se o dispositivo requer o registro de gerenciamento de dispositivo móvel.
7.	O Windows registra o dispositivo no diretório da organização no Azure AD e no gerenciamento de dispositivo móvel, se apropriado.
8.	Se você for um usuário gerenciado, o Windows levará para a área de trabalho por meio da conexão automático.
9.	Se você for um usuário federado, você será levado para a tela de logon do Windows para inserir suas credenciais.

## Informações adicionais
* [Windows 10 para a empresa: maneiras de usar dispositivos para o trabalho](active-directory-azureadjoin-windows10-devices-overview.md)
* [Estendendo os recursos de nuvem para dispositivos Windows 10 por meio da Junção do Active Directory do Azure](active-directory-azureadjoin-user-upgrade.md)
* [Autenticando identidades sem senhas com o Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Saiba mais sobre cenários de uso da Junção do Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Conectar dispositivos ingressados no domínio ao AD do Azure para experiências com o Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configurar a Junção do Azure AD](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_0928_2016-->