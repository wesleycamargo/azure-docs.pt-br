---
title: Perguntas frequentes sobre o aplicativo Microsoft Authenticator
description: Fornece uma lista de perguntas frequentes e respostas relacionadas ao aplicativo Microsoft Authentication e ao Azure Multi-Factor Authentication.
services: multi-factor-authentication
documentationcenter: ''
author: kgremban
manager: femila
editor: pblachar, librown

ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2016
ms.author: kgremban

---
# Perguntas frequentes sobre o aplicativo Microsoft Authenticator
O aplicativo Microsoft Authenticator substituiu o aplicativo Azure Authenticator, e é o aplicativo recomendado ao usar o Azure Multi-Factor Authentication. O aplicativo está disponível para Windows Phone, Android e iOS.

## Perguntas frequentes
* **Já estou usando o aplicativo Microsoft Authenticator para códigos de segurança. Como alterno para notificações por push de um clique?**
  
    Se você usa uma conta da Microsoft como sua conta pessoal e quer alternar para notificações por push, você precisa adicionar sua conta novamente. Isso ocorre porque o aplicativo usa uma senha única. Registre novamente o dispositivo com sua conta e configure as notificações por push.
  
    Se sua conta não tiver a verificação em duas etapas habilitada, consulte [Sobre a verificação em duas etapas](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification) para decidir se ela é adequada a você.
* **Quando poderei usar as notificações por push de um clique no iPhone ou iPad?**
  
    Esse recurso ficará na versão beta até o final de agosto, quando se tornará amplamente disponível para contas da Microsoft. Se você quiser ingressar em nosso programa beta, envie um email para msauthenticator@microsoft.com. Inclua seu nome, sobrenome e ID Apple na mensagem.
* **Notificações por push de um clique funcionam para contas que não são da Microsoft?**
  
    Não, as notificações por push só funcionam com contas da Microsoft e contas do Azure Active Directory. Se sua empresa ou escola usar as contas do Azure AD, elas poderão desabilitar esse recurso.
* **Eu restaurei meu dispositivo de um backup, e os códigos de minha conta estão ausentes ou não estão funcionando. O que aconteceu?**
  
    Para fins de segurança, nós não restauramos contas de backups de aplicativo no momento. Se você restaurar o aplicativo iOS de um backup, suas contas ainda serão exibidas, mas elas não funcionarão para receber as verificações de entrada ou gerar códigos de segurança. Depois de restaurar o aplicativo, exclua as contas e adicione-as novamente.

## Tópicos relacionados
* [Perguntas frequentes sobre o Azure Multi-Factor Authentication](multi-factor-authentication-faq.md)
* [Sobre a verificação em duas etapas](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification) da conta da Microsoft
* [Perguntas frequentes sobre aplicativos de verificação de identidade](https://support.microsoft.com/help/12414/microsoft-account-identity-verification-apps-faq)

<!---HONumber=AcomDC_0921_2016-->