---
title: 'Solução de problemas: você não pode ir daqui até lá | Microsoft Docs'
description: Este tópico o ajuda a identificar as etapas de correção que pode seguir para obter acesso a um aplicativo.
services: active-directory
keywords: acesso condicional baseado em dispositivo, registro de dispositivo, habilitar registro de dispositivo, registro de dispositivo e MDM
documentationcenter: ''
author: markusvi
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/23/2016
ms.author: markvi

---
# Solução de problemas: você não pode ir daqui até lá
Você recebeu uma página de acesso negado ao acessar um aplicativo como o SharePoint Online. Agora, o que fazer?

Este guia o ajudará a identificar as etapas de correção disponíveis que você pode seguir para acessar o aplicativo.

Em qual plataforma de dispositivos o dispositivo está sendo executado? A resposta a essa pergunta determina a seção certa neste tópico para você:

* Dispositivo Windows
* Dispositivo iOS (iPhone ou iPad)
* Dispositivo Android

## Acesso por meio de um dispositivo Windows
Se o dispositivo é executado no Windows 10, Windows 8.1, Windows 8.0, Windows 7, Windows Server 2016, Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2, escolha a causa apropriada, identificando a página que você obteve ao tentar acessar o aplicativo.

### O dispositivo não está registrado
Se o dispositivo não estiver registrado no Azure AD (Azure Active Directory) e o aplicativo estiver protegido com uma política baseada em dispositivo, você poderá ver uma página com o seguinte conteúdo:

![Mensagens "você não pode acessar esse lugar daqui" para dispositivos não registrados](./media/active-directory-conditional-access-device-remediation/01.png "Cenário")

Se o dispositivo for unido ao domínio para o Active Directory em sua organização, você poderá tentar o seguinte:

1. Verifique se você entrou no Windows usando sua conta corporativa (a conta do Active Directory).
2. Conecte-se à rede corporativa por meio de VPN ou Acesso Direto.
3. Depois de conectado, bloqueie a sessão do Windows usando a tecla Windows + a tecla L.
4. Desbloqueie a sessão do Windows inserindo as credenciais de sua conta corporativa.
5. Aguarde um minuto e tente acessar o aplicativo novamente.
6. Se você receber a mesma página, contate o administrador, clique no link **Mais detalhes** e forneça os detalhes.

Se o dispositivo não estiver unido ao domínio e executar o Windows 10, você terá duas opções:

* Execute o Ingresso do Azure AD.
* Adicione sua conta corporativa ou de estudante ao Windows.

Para obter informações sobre as diferenças entre os dois, confira [Usando dispositivos Windows 10 em seu local de trabalho](active-directory-azureadjoin-windows10-devices.md).

Para executar o Ingresso do Azure AD, faça o seguinte (não disponível para o Windows Phone):

**Atualização de Aniversário do Windows 10**

1. Abra o aplicativo **Configurações**.
2. Clique em **Contas** > **Acesso corporativo ou de estudante**.
3. Clique em **Conectar**.
4. Clique em **Unir este dispositivo ao Azure AD** na parte inferior da página.
5. Autentique para sua organização, forneça prova de autenticação multifator, se necessário e siga as etapas até a conclusão.
6. Saia e entre usando sua conta corporativa.
7. Tente acessar o aplicativo novamente.

**Atualização do Windows de 10 de novembro de 2015**

1. Abra o aplicativo **Configurações**.
2. Clique em **Sistema** > **Sobre**.
3. Clique em **Ingressar no Azure AD**.
4. Autentique para sua organização, forneça prova de autenticação multifator, se necessário e siga as etapas até a conclusão.
5. Saia e entre usando sua conta corporativa (a conta do Azure AD).
6. Tente acessar o aplicativo novamente.

Para adicionar sua conta corporativa ou de estudante, faça o seguinte:

**Atualização de Aniversário do Windows 10**

1. Abra o aplicativo **Configurações**.
2. Clique em **Contas** > **Acesso corporativo ou de estudante**.
3. Clique em **Conectar**.
4. Autentique para sua organização, forneça prova de autenticação multifator, se necessário e siga as etapas até a conclusão.
5. Tente acessar o aplicativo novamente.

**Atualização do Windows de 10 de novembro de 2015**

1. Abra o aplicativo **Configurações**.
2. Clique em **Contas** > **Suas contas**.
3. Clique em **Adicionar conta corporativa ou de estudante**.
4. Autentique para sua organização, forneça prova de autenticação multifator, se necessário e siga as etapas até a conclusão.
5. Tente acessar o aplicativo novamente.

Se o dispositivo não estiver unido ao domínio e executar o Windows 8.1, você poderá executar o Workplace Join e registrar-se no Microsoft Intune fazendo o seguinte:

1. Abra **Configurações do PC**.
2. Clique em **Rede** > **Trabalho**.
3. Clique em **Ingressar**.
4. Autentique para sua organização, forneça prova de autenticação multifator, se necessário e siga as etapas até a conclusão.
5. Clique em **Ativar**.
6. Aguarde a conclusão.
7. Tente acessar o aplicativo novamente.

## Navegador sem suporte
Se estiver acessando o aplicativo por meio dos navegadores a seguir, você verá uma página semelhante à página mostrada anteriormente:

* Chrome, Firefox ou outro navegador que não seja o Microsoft Edge ou o Microsoft Internet Explorer no Windows 10 ou no Windows Server 2016.
* Firefox no Windows 8.1, Windows 7, Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2.

![Mensagem "você não pode acessar esse lugar daqui" para navegadores sem suporte](./media/active-directory-conditional-access-device-remediation/02.png "Cenário")

A única correção consiste em usar um navegador ao qual o aplicativo dê suporte para sua plataforma de dispositivo.

## Acesso por meio de um dispositivo iOS
Confira regularmente para obter instruções para iPhones ou iPads.

## Acesso por meio de um dispositivo Android
Confira novamente em breve para obter instruções para telefones ou tablets Android.

## Próximas etapas
[Acesso condicional ao Azure Active Directory](active-directory-conditional-access.md)

<!----HONumber=AcomDC_0831_2016-->