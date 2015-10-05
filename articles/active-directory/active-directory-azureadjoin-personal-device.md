<properties
	pageTitle="Ingressar em um dispositivo pessoal para sua organização| Microsoft Azure"
	description="Um tópico que explica como os usuários podem registrar seus computadores Windows 10 pessoais com a rede corporativa."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/21/2015"
	ms.author="femila"/>

# Ingressar em um dispositivo pessoal para sua organização

Para ingressar em um dispositivo Windows 10 para sua organização
--------------------------------------------------------------------------------------------
1.	No menu **Iniciar**, selecione **Configurações**.
2.	Selecione **Contas** e, em seguida, clique em **Sua conta**.
3.	Clique em **Adicionar conta corporativa ou de estudante** e, em seguida, digite sua conta organizacional.
4.	Você, em seguida, acessará a página de entrada para sua organização. Digite seu nome de usuário e senha e clique em **OK**.
5.	Você será solicitado para um desafio de multi-factor authentication. Essa opção é configurada pela TI.
6.	O AD do Azure, em seguida, verificará se esse usuário/dispositivo requer o registro do dispositivo móvel MDM (gerenciamento).
7.	O Windows, em seguida, registra o dispositivo no diretório da organização no AD do Azure e registra-o em MDM.
8.	Quando isso for feito, se você for um usuário gerenciado, o Windows irá concluir o processo de instalação e levar o usuário para a área de trabalho na tela de logon automático.
9.	Se você for um usuário federado, você será levado à tela de logon do Windows e precisará digitar suas credenciais.

## Informações adicionais
* [Estendendo os recursos de nuvem para dispositivos Windows 10 por meio da Junção do Active Directory do Azure](active-directory-azureadjoin-overview.md)
* [Saiba mais sobre os cenários de uso e considerações de implantação para a Junção do Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Configurar a Junção do Azure AD](active-directory-azureadjoin-setup.md)

<!---HONumber=Sept15_HO4-->