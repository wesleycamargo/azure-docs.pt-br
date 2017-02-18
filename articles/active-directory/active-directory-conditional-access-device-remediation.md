---
title: "Solução de problemas para problemas de acesso do Azure Active Directory | Microsoft Docs"
description: "Saiba quais são as etapas que você pode executar para resolver problemas de acesso com recursos online da sua organização."
services: active-directory
keywords: acesso condicional baseado em dispositivo, registro de dispositivo, habilitar registro de dispositivo, registro de dispositivo e MDM
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 8ad0156c-0812-4855-8563-6fbff6194174
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/24/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: fbabf6f2e1e588ba509c4da84ab1700b1b5d4f87
ms.openlocfilehash: ad9f9a8c5b370ffa916b9089ef3ce523fe0266c7


---
# <a name="troubleshooting-for-azure-active-directory-access-issues"></a>Solução de problemas para problemas de acesso do Azure Active Directory
Tente acessar a intranet da organização do SharePoint Online e receba uma mensagem de erro "acesso negado". O que você faz?


Este artigo aborda as etapas de solução que podem ajudá-lo a resolver problemas de acesso com recursos online da sua organização.

Para obter ajuda para resolver os problemas de acesso do Azure Active Directory (Azure AD), vá para a seção do artigo que aborda a sua plataforma de dispositivo:

* Dispositivo Windows
* Dispositivo iOS (volte em breve para obter assistência com iPhones e iPads).
* Dispositivo Android (volte em breve para obter assistência com telefones e tablets Android).

## <a name="access-from-a-windows-device"></a>Acesso por meio de um dispositivo Windows
Se seu dispositivo executar uma das seguintes plataformas, examine as seções a seguir para obter a mensagem de erro mostrada quando você tentar acessar um aplicativo ou serviço:

* Windows 10
* Windows 8.1
* Windows 8
* Windows 7
* Windows Server 2016
* Windows Server 2012 R2
* Windows Server 2012
* Windows Server 2008 R2

### <a name="device-is-not-registered"></a>O dispositivo não está registrado
Se o dispositivo não estiver registrado no Azure AD e o aplicativo estiver protegido com uma política baseada em dispositivo, você poderá ver uma página que mostre as seguintes mensagens de erro:

![Mensagens "Você não pode acessar esse lugar daqui" para dispositivos não registrados](./media/active-directory-conditional-access-device-remediation/01.png "Cenário")

Se o dispositivo for unido ao domínio para o Active Directory em sua organização, tente o seguinte:

1. Verifique se você entrou no Windows usando sua conta corporativa (a conta do Active Directory).
2. Conecte-se à rede corporativa por meio de uma rede virtual privada (VPN) ou DirectAccess.
3. Depois de conectado, pressione a tecla de logotipo do Windows + a tecla L para bloquear a sessão do Windows.
4. Insira as credenciais da sua conta de trabalho para desbloquear sua sessão do Windows.
5. Aguarde um minuto e tente acessar o aplicativo ou o serviço novamente.
6. Se você vir a mesma página, clique em **Mais detalhes** e contate seu administrador com os detalhes.

Se o dispositivo não estiver unido ao domínio e executar o Windows 10, você terá duas opções:

* Executar o Ingresso do Azure AD
* Adicionar sua conta corporativa ou de estudante ao Windows

Para saber mais sobre como essas opções são diferentes, veja [Usando dispositivos Windows 10 em seu local de trabalho](active-directory-azureadjoin-windows10-devices.md).

Para executar a Junção do Azure AD, siga as etapas a seguir para a plataforma em que seu dispositivo é executado. (A junção do Azure AD não está disponível no Windows Phone).

**Atualização de Aniversário do Windows 10**

1. Abra o aplicativo **Configurações** .
2. Clique em **Contas** > **Acesso corporativo ou de estudante**.
3. Clique em **Conectar**.
4. Clique em **Unir este dispositivo ao Azure AD**.
5. Autentique para sua organização, forneça autenticação multifator, se solicitado, e siga as etapas mostradas.
6. Saia e entre com sua conta corporativa.
7. Tente acessar o aplicativo novamente.

**Atualização do Windows de 10 de novembro de 2015**

1. Abra o aplicativo **Configurações** .
2. Clique em **Sistema** > **Sobre**.
3. Clique em **Ingressar no Azure AD**.
4. Autentique para sua organização, forneça autenticação multifator, se solicitado, e siga as etapas mostradas.
5. Saia e entre com sua conta corporativa (sua conta do Azure AD).
6. Tente acessar o aplicativo novamente.

Para adicionar sua conta corporativa ou de estudante, execute as seguintes etapas:

**Atualização de Aniversário do Windows 10**

1. Abra o aplicativo **Configurações** .
2. Clique em **Contas** > **Acesso corporativo ou de estudante**.
3. Clique em **Conectar**.
4. Autentique para sua organização, forneça autenticação multifator, se solicitado, e siga as etapas mostradas.
5. Tente acessar o aplicativo novamente.

**Atualização do Windows de 10 de novembro de 2015**

1. Abra o aplicativo **Configurações** .
2. Clique em **Contas** > **Suas contas**.
3. Clique em **Adicionar conta corporativa ou de estudante**.
4. Autentique para sua organização, forneça autenticação multifator, se solicitado, e siga as etapas mostradas.
5. Tente acessar o aplicativo novamente.

Se o dispositivo não estiver unido ao domínio e executar o Windows 8.1, você poderá executar o Workplace Join e registrar-se no Microsoft Intune, execute estas etapas:

1. Abra **Configurações do PC**.
2. Clique em **Rede** > **Local de Trabalho**.
3. Clique em **Ingressar**.
4. Autentique para sua organização, forneça autenticação multifator, se solicitado, e siga as etapas mostradas.
5. Clique em **Ativar**.
6. Tente acessar o aplicativo novamente.

### <a name="browser-is-not-supported"></a>Não há suporte para navegador
Você pode ter seu acesso negado se estiver tentando acessar um aplicativo ou um serviço usando um dos seguintes navegadores:

* Chrome, Firefox ou outro navegador diferente do Microsoft Edge ou do Microsoft Internet Explorer no Windows 10 ou no Windows Server 2016
* Firefox no Windows 8.1, Windows 7, Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2

Você verá uma página de erro parecida com esta:

![Mensagem "Você não pode acessar esse lugar daqui" para navegadores sem suporte](./media/active-directory-conditional-access-device-remediation/02.png "Cenário")

A única correção consiste em usar um navegador ao qual o aplicativo dê suporte para sua plataforma de dispositivo.

## <a name="next-steps"></a>Próximas etapas
[Acesso condicional ao Azure Active Directory](active-directory-conditional-access.md)




<!--HONumber=Jan17_HO4-->


