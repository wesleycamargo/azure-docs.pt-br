---
title: Configurando a Junção do Azure AD para seus usuários | Microsoft Docs
description: Explica como os administradores podem configurar a Junção do Azure AD para o diretório local e o registro de dispositivos.
services: active-directory
documentationcenter: ''
author: femila
manager: swadhwa
editor: ''
tags: azure-classic-portal

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/27/2016
ms.author: femila

---
# Configuração da Junção do Azure AD na sua organização
Antes de configurar a Junção do Azure AD (Junção do Azure Active Directory), é necessário sincronizar seu diretório local de usuários com a nuvem ou criar manualmente as contas gerenciadas no Azure AD.

Instruções detalhadas para sincronizar seus usuários locais com o Azure AD são fornecidas em [Integrando suas identidades locais com o Azure Active Directory](active-directory-aadconnect.md).

Para criar e gerenciar usuários manualmente no Azure AD, consulte [Gerenciamento de usuários no Azure AD](https://msdn.microsoft.com/library/azure/hh967609.aspx).

## Configure o registro de dispositivos
1. Faça logon no Portal do Azure como administrador.
2. No painel esquerdo, selecione **Active Directory**.
3. Na guia **Diretório**, selecione o diretório.
4. Selecione a guia **Configurar**.
5. Vá para a seção **Dispositivos**.
6. Na guia **dispositivos**, defina o seguinte:
   * **NÚMERO MÁXIMP DE DISPOSITIVOS POR USUÁRIO**: selecione o número máximo de dispositivos que um usuário pode ter no Azure AD. Se um usuário atingir esta cota, ele não poderá adicionar mais dispositivos até que um ou mais dos seus dispositivos existentes sejam removidos.
   * **EXIGIR MULTI-FACTOR AUTH PARA UNIR DISPOSITIVOS**: defina se os usuários devem precisar fornecer um segundo fator de autenticação para unir seu dispositivo ao Azure AD. Para saber mais sobre a Azure Multi-Factor Authentication, confira [Introdução à Azure Multi-Factor Authentication na nuvem](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md).
   * **USUÁRIOS PODEM UNIR DISPOSITIVOS AO AZURE AD**: selecione os usuários e grupos que têm permissão para unir dispositivos ao Azure AD.
   * **ADMINISTRADORES ADICIONAIS EM DISPOSITIVOS UNIDOS AO AZURE**: com o Azure AD Premium ou a EMS (Enterprise Mobility Suite), você pode escolher quais usuários recebem direitos de administrador local no dispositivo. Os administradores globais e os proprietários do dispositivo recebem direitos de administrador local por padrão.

<center>![Configurar o registro de dispositivos](./media/active-directory-azureadjoin/active-directory-aadjoin-configure-devices.png) </center>

Após você configurar a Junção do Azure AD para seus usuários, eles podem se conectar ao Azure AD por meio de seus dispositivos pessoais ou corporativos.

A seguir estão os três cenários que você pode usar para habilitar seus usuários para configurarem a Junção do Azure AD:

* Os usuários unem um dispositivo da empresa diretamente ao Azure AD.
* Os usuários unem um dispositivo de empresa ao domínio no Active Directory local e o estendem para o Azure AD.
* Os usuários adicionam contas corporativas e de estudante ao Windows em um dispositivo pessoal

## Informações adicionais
* [Windows 10 para a empresa: maneiras de usar dispositivos para o trabalho](active-directory-azureadjoin-windows10-devices-overview.md)
* [Estendendo os recursos de nuvem para dispositivos Windows 10 por meio da Junção do Active Directory do Azure](active-directory-azureadjoin-user-upgrade.md)
* [Saiba mais sobre cenários de uso da Junção do Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Conectar dispositivos ingressados no domínio ao AD do Azure para experiências com o Windows 10](active-directory-azureadjoin-devices-group-policy.md)
* [Configurar a Junção do Azure AD](active-directory-azureadjoin-setup.md)

<!---HONumber=AcomDC_0928_2016-->