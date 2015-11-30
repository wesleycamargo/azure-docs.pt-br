<properties 
	pageTitle="Configurando a Junção do Azure AD para seus usuários | Microsoft Azure" 
	description="Explica como os administradores podem configurar a Junção do AD do Azure para o registro de dispositivos e diretório local."
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
	ms.date="11/17/2015" 
	ms.author="femila"/>

# Configuração da Junção do Azure AD na sua organização

Antes de configurar a Junção do Azure AD, você precisa sincronizar o seu diretório de usuários locais com a nuvem ou criar manualmente as contas gerenciadas no Azure AD.

Instruções detalhadas para sincronizar seus usuários locais ao Azure AD são fornecidas em [Integrando suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md).


Para criar e gerenciar usuários manualmente no Azure AD, consulte [Gerenciamento de usuários no Azure AD](https://msdn.microsoft.com/library/azure/hh967609.aspx).

## Configure o registro de dispositivos 
1. Faça logon no Portal do Azure como um Administrador.
2. No painel esquerdo, selecione Active Directory.
3. Na guia **Diretório**, selecione o diretório.
4. Selecione a guia **Configurar**.
5. Role até a seção **Dispositivos**.
6. Na guia **dispositivos**, defina o seguinte:  
   * **Número máximo de dispositivos por usuário**: selecione o número máximo de dispositivos que um usuário pode ter no Azure AD. Se um usuário atingir esta cota, ele não poderá adicionar mais dispositivos até que um ou mais dos seus dispositivos existentes sejam removidos.
   * **Exigir autenticação multifator para unir dispositivos**: habilitar quando os usuários devem fornecer um segundo fator de autenticação para unir seu dispositivo ao AD do Azure. Para obter mais informações sobre a autenticação multifator, consulte [Introdução à Autenticação Multifator do Azure na nuvem](multi-factor-authentication-get-started-cloud/)
   * **Usuários podem unir dispositivos ao Azure AD**: selecione os usuários e grupos que têm permissão para unir dispositivos ao Azure AD.
   * **Administradores adicionais em dispositivos unidos do Azure AD**: com o Azure AD Premium ou a EMS (Enterprise Mobility Suite), você pode escolher quais usuários recebem direitos de administrador local no dispositivo. Os administradores globais e o proprietário do dispositivo recebem direitos de administrador local por padrão.

<center>![](./media/active-directory-azureadjoin/active-directory-aadjoin-configure-devices.png) </center>
Após você configurar a Junção do Azure AD para seus usuários, eles podem se conectar ao Azure AD por meio de seus dispositivos pessoais ou corporativos.

A seguir estão os três cenários de como você pode habilitar seus usuários para configurarem a Junção do Azure AD:

- Os usuários unem um dispositivo da empresa diretamente ao Azure AD
- Os usuários ingressam em um domínio no dispositivo de empresa ao Active Directory local e o estendem ao Azure AD
- Os usuários adicionam contas de trabalho ao Windows em um dispositivo pessoal 

## Informações adicionais

* [Windows 10 para a empresa: maneiras de usar dispositivos para o trabalho](active-directory-azureadjoin-windows10-devices-overview.md)
* [Estendendo os recursos de nuvem para dispositivos Windows 10 por meio da Junção do Active Directory do Azure](active-directory-azureadjoin-user-upgrade.md)
* [Saiba mais sobre cenários de uso da Junção do Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Conectar dispositivos ingressados no domínio ao AD do Azure para experiências com Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configurar a Junção do Azure AD](active-directory-azureadjoin-setup.md)

<!---HONumber=Nov15_HO4-->