

<properties 
	pageTitle="Ingressar em um dispositivo pessoal para sua organização| Microsoft Azure" 
	description="Explica como os usuários podem registrar seus computadores pessoais com Windows 10 em sua rede corporativa fornece as etapas de implantação para um cenário BYOD." 
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

# Ingressar em um dispositivo pessoal para sua organização

Para ingressar em um dispositivo Windows 10 para sua organização
--------------------------------------------------------------------------------------------
1.	No menu **Iniciar**, selecione **Configurações**. 
2.	Selecione **Contas** e, em seguida, clique em **Sua conta**.
3.	Clique em **Adicionar conta corporativa ou de estudante** e, em seguida, digite sua conta organizacional.
4.	Em seguida, você acessará a página de entrada de sua organização. Digite seu nome de usuário e senha e clique em **OK**.
5.	Você será solicitado para um desafio de multi-factor authentication. Essa opção é configurada pela TI.
6.	O AD do Azure, em seguida, verificará se esse usuário/dispositivo requer o registro do dispositivo móvel MDM (gerenciamento). 
7.	O Windows, em seguida, registra o dispositivo no diretório da organização no AD do Azure e registra-o no MDM.
8.	Quando isso for feito, se você for um usuário gerenciado, o Windows concluirá o processo de instalação e levará o usuário para a área de trabalho por meio do logon automático.
9.	Se você for um usuário federado, você será levado para a tela de logon do Windows e precisará digitar suas credenciais.

## Informações adicionais
* [Windows 10 para a empresa: maneiras de usar dispositivos para o trabalho](active-directory-azureadjoin-windows10-devices-overview.md)
* [Estendendo os recursos de nuvem para dispositivos Windows 10 por meio da Junção do Active Directory do Azure](active-directory-azureadjoin-user-upgrade.md)
* [Autenticando identidades sem senhas com o Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Saiba mais sobre cenários de uso da Junção do Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Conectar dispositivos ingressados no domínio ao AD do Azure para experiências com Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configurar a Junção do Azure AD](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_1125_2015-->