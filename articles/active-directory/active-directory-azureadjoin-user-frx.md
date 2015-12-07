<properties 
	pageTitle="Configurar um novo dispositivo com o AD do Azure durante a instalação | Microsoft Azure" 
	description="Um tópico que explica como os usuários podem configurar a Junção do Azure AD durante sua experiência de primeira execução." 
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

# Configurar um novo dispositivo com o AD do Azure durante a instalação

No Windows 10, os usuários finais podem unir seu dispositivo ao AD do Azure na primeira experiência de execução (FRX). Isso permitirá que as organizações distribuam dispositivos para seus funcionários ou alunos, ou eles podem escolher os seus próprios dispositivos (CYOD). Se você instalar o SKU Professional ou Enterprise para o Windows 10, a experiência padrão é a configuração de dispositivos de propriedade da empresa.

Para adicionar um dispositivo ao AD do Azure
-----------------------------------------------------------------------

1. Após o estágio de **Preparação**, o Assistente de instalação será iniciado.
2. Primeiro personalize sua região e idioma, aceite o EULA e entre online.
<center>
![](./media/active-directory-azureadjoin/active-directory-azureadjoin-customize-region.png) </center>
3. Selecione a rede para se conectar à internet.
4. Selecione se é um dispositivo pessoal ou um dispositivo da empresa:
5. Clique em **este dispositivo pertence à minha organização**. Isso inicia a experiência de junção do Azure AD. A seguir está uma tela que você vê no SKU do Windows 10 Professional. 
<center>
![](./media/active-directory-azureadjoin/active-directory-azureadjoin-who-owns-pc.png) </center>

6.	Insira as credenciais fornecidas a você pela sua organização.
<center>
![](./media/active-directory-azureadjoin/active-directory-azureadjoin-sign-in.png) </center> 
7.	Depois de inserir seu nome de usuário, um locatário correspondente está localizado no AD do Azure. Se você estiver em um domínio federado, você será redirecionado para o servidor local do Secure Token Service (STS) (por exemplo, o AD FS). Se você for um usuário em um domínio não-federado, você precisa inserir suas credenciais diretamente na página hospedada pelo AD do Azure. Você também verá o logotipo de sua organização e um texto de apoio se a identidade visual da empresa tiver sido configurada.
8.	Você será solicitado para um desafio de autenticação multifator. Essa opção é configurada pela TI.
9.	O AD do Azure, em seguida, verificará se esse usuário/dispositivo requer o registro do dispositivo móvel MDM (gerenciamento). 
10.	O Windows, em seguida, registra o dispositivo no diretório da organização no AD do Azure e registra-o no MDM.
11.	Quando isso for feito, se você for um usuário gerenciado, o Windows concluirá o processo de instalação e levará o usuário para a área de trabalho por meio do logon automático.
12.	Se você for um usuário federado, você será levado para a tela de logon do Windows e precisará digitar suas credenciais para fazer logon.

> [AZURE.NOTE]Ingressar em um domínio do Active Directory local no Windows do usuário não é suportado. Portanto, se você planeja adicionar um PC a um domínio você deve selecionar o link "Em vez disso, configure o Windows com uma conta local". Você pode, em seguida, unir o domínio a partir das configurações do PC como você fez antes.

## Informações adicionais
* [Windows 10 para a empresa: maneiras de usar dispositivos para o trabalho](active-directory-azureadjoin-windows10-devices-overview.md)
* [Estendendo os recursos de nuvem para dispositivos Windows 10 por meio da Junção do Active Directory do Azure](active-directory-azureadjoin-user-upgrade.md)
* [Autenticando identidades sem senhas com o Microsoft Passport](active-directory-azureadjoin-passport.md)
* [Saiba mais sobre cenários de uso da Junção do Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Conectar dispositivos ingressados no domínio ao AD do Azure para experiências com Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configurar a Junção do Azure AD](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_1125_2015-->