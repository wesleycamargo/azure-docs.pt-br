---
title: Ajuda e suporte para o aplicativo Microsoft Authenticator | Microsoft Docs
description: Fornece uma lista de perguntas frequentes e respostas relacionadas ao aplicativo Microsoft Authentication e ao Azure Multi-Factor Authentication.
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: pblachar, librown
ms.assetid: f04d5bce-e99e-4f75-82d1-ef6369be3402
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 28846f2effd26c5b7ca7a79fba52d8ba1cb4ea91
ms.openlocfilehash: 9abf35aebceb4a109becba6e97e0a506fc4375d7


---
# <a name="microsoft-authenticator-app-faq"></a>Perguntas frequentes sobre o aplicativo Microsoft Authenticator

Este artigo responde às perguntas comuns que recebemos sobre o aplicativo Microsoft Authenticator. Caso não encontre uma resposta para sua pergunta, vá para o [Fórum do aplicativo Microsoft Authenticator](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp). Também temos outras perguntas frequentes sobre um recurso específico do aplicativo, [Sign in with your phone FAQ](microsoft-authenticator-app-phone-signin-faq.md) (Entrar com seu telefone - perguntas frequentes).

O aplicativo Microsoft Authenticator substituiu o aplicativo Azure Authenticator, e é o aplicativo recomendado ao usar o Azure Multi-Factor Authentication. O aplicativo está disponível para Windows Phone, Android e iOS.

## <a name="frequently-asked-questions"></a>Perguntas frequentes
### <a name="what-happened-to-the-azure-authenticator-multi-factor-auth-and-microsoft-account-apps"></a>O que aconteceu com o Azure Authenticator, Multi-Factor Auth, e aplicativos de conta da Microsoft?
O aplicativo Microsoft Authenticator substitui esses outros aplicativos. Azure Authenticator atualizado para o Microsoft Authenticator. Se você usar o Multi-Factor Auth ou os aplicativos de conta da Microsoft, instale o Microsoft Authenticator e adicione suas contas novamente. Certifique-se de concluir a adição de suas contas para o novo aplicativo antes de excluir os antigos.

O aplicativo Microsoft Authenticator está disponível para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).

### <a name="im-already-using-the-microsoft-authenticator-application-for-verification-codes-how-do-i-switch-to-one-click-push-notifications"></a>Já estou usando o aplicativo Microsoft Authenticator para códigos de segurança. Como alterno para notificações por push de um clique?
Aprovar uma entrada por meio da notificação por push só está disponível para contas pessoais da Microsoft ou para contas corporativas e de estudante da Microsoft, não para contas de terceiros como Google ou Facebook. Se você tiver uma conta corporativa ou de estudante da Microsoft, sua organização poderá optar por desabilitar essa opção.

Se você usa uma conta da Microsoft como sua conta pessoal e quer alternar para notificações por push, você precisa adicionar sua conta novamente. Registre novamente o dispositivo com sua conta e configure as notificações por push.  

Se você usar o Microsoft Authenticator para sua conta corporativa ou de estudante, sua organização decide se deseja permitir as notificações de um clique.

### <a name="do-one-click-push-notifications-work-for-non-microsoft-accounts"></a>Notificações por push de um clique funcionam para contas que não são da Microsoft?
Não, as notificações por push só funcionam com contas da Microsoft e contas do Azure Active Directory. Se sua empresa ou escola usar as contas do Azure AD, elas poderão desabilitar esse recurso.  

### <a name="i-restored-my-device-from-a-backup-and-my-account-codes-are-missing-or-not-working-what-happened"></a>Eu restaurei meu dispositivo de um backup, e os códigos de minha conta estão ausentes ou não estão funcionando. O que aconteceu?
Para fins de segurança, nós não restauramos contas de backups de aplicativo.  Depois de restaurar o aplicativo, exclua as contas e adicione-as novamente.

### <a name="i-got-a-new-device-how-do-i-remove-the-microsoft-authenticator-app-from-my-old-device-and-move-to-the-new-one"></a>Eu tenho um novo dispositivo. Como remover o Microsoft Authenticator de meu dispositivo antigo e movê-lo para um novo?
Adicionar o aplicativo Microsoft Authenticator a um novo dispositivo, não remove-o automaticamente de quaisquer outros dispositivos. Para gerenciar quais dispositivos que estão configurados para a sua conta, visite o site mesmo que você usa para gerenciar a verificação em duas etapas e optar por remover aplicativos antigos.

Para contas pessoais da Microsoft, esse site é sua página de [segurança da conta](https://account.microsoft.com/security). Para contas da Microsoft corporativa ou de estudante, esse site pode ser [MyApps](https://myapps.microsoft.com) ou um portal personalizado que sua organização configurou.

### <a name="how-do-i-remove-an-account-from-the-app"></a>Como remover uma conta do aplicativo?
* iOS: no menu principal, passe o dedo para esquerda em um bloco de conta. Selecione **Excluir**.
* Windows Phone: No menu principal, selecione o botão de menu, em seguida, **Editar contas**. Tocar o **X** ao lado do nome de conta.
* Android: No menu principal, selecione o botão de menu, em seguida, **Editar contas**. Tocar o **X** ao lado do nome de conta.

Caso tenha um dispositivo registrado em sua organização, você precisará concluir uma etapa extra para remover a conta. Nesses dispositivos, o aplicativo Microsoft Authenticator é registrado automaticamente como um administrador do dispositivo. Se você quiser desinstalar completamente o aplicativo, você precisa primeiro cancelar o registro do aplicativo nas configurações do aplicativo.

### <a name="why-does-the-app-request-so-many-permissions"></a>Por que o aplicativo solicita tantas permissões?
Veja a seguir uma lista completa de permissões que solicitamos e como elas são usadas no aplicativo. As permissões específicas que você vê dependem do tipo de telefone que você tem.

* **Câmera**: podemos usar a câmera para digitalizar códigos de barras&2;D quando você adiciona uma conta corporativa, de estudante ou conta não-Microsoft.
* **Contatos e telefone**: quando você entra com sua conta pessoal da Microsoft, tentamos simplificar o processo de localizar contas existentes que você usa em seu telefone.
* **SMS**: quando você entra com sua conta pessoal da Microsoft pela primeira vez, é preciso certificar-se de que o número de telefone corresponde ao que temos no registro. Enviamos uma mensagem de texto para o telefone de onde você baixou o aplicativo. A mensagem contém um código de verificação de 6 a 8 dígitos. Em vez de solicitar que você encontre esse código e insira-o no aplicativo, nós achamos na mensagem de texto para você.
* **Desenhar sobre outros aplicativos**: quando você recebe uma notificação para verificar sua identidade, podemos exibir notificação sobre qualquer outro aplicativo que pode estar em execução.
* **Receber dados da internet**: essa permissão é necessária para enviar notificações.
* **Impedir que o telefone entre em suspensão**: se você registrar seu dispositivo com sua organização, eles podem alterar essa política em seu telefone.
* **Controle de vibração**: você tem a opção de escolher se deseja uma vibração sempre que você receber uma notificação para verificar sua identidade.
* **Usar o hardware de impressão digital**: algumas contas corporativas e de estudantes exigem um PIN adicional sempre que você verifica sua identidade. PARA facilitar o processo, permitimos que você use sua impressão digital em vez da inserção do PIN.
* **Exibir conexões de rede**: quando você adiciona uma conta da Microsoft, o aplicativo requer conexão de rede/internet.
* **Ler o conteúdo de seu armazenamento**: essa permissão é usada apenas quando você relata um problema técnico através das configurações do aplicativo. Algumas informações de seu armazenamento são coletadas para diagnosticar o problema.
* **Acesso de rede completo**: essa permissão é necessária para enviar notificações para verificar sua identidade.
* **Executar na inicialização**: se você reiniciar seu telefone, essa permissão garante que você continue a receber notificações para verificar sua identidade.

### <a name="why-does-the-microsoft-authenticator-app-allow-you-to-approve-a-request-without-unlocking-the-device"></a>Por que o aplicativo Microsoft Authenticator permite que você aprove uma solicitação sem desbloquear o dispositivo?

Esse comportamento é intencional. A verificação em duas etapas exige a comprovação de duas coisas — algo que você sabe e algo que você tem. Algo que você sabe é a senha. Algo que você tem é seu telefone (configurado com o Microsoft Authenticator e registrado como uma prova de MFA).  Portanto, ter o telefone e aprovar a solicitação atende aos critérios para o segundo fator de autenticação. 

## <a name="next-steps"></a>Próximas etapas

### <a name="contact-us"></a>Fale conosco
Se sua pergunta não tiver sido respondida aqui, queremos ouvir sua opinião. Acesse o [Fórum do aplicativo Microsoft Authenticator](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp) para postar a pergunta e obter ajuda da comunidade ou deixe um comentário nesta página.


### <a name="related-topics"></a>Tópicos relacionados
* [Sobre a verificação em duas etapas](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification) da conta da Microsoft
* [Está com dificuldades com a verificação de duas etapas](multi-factor-authentication-end-user-troubleshoot.md) para sua conta corporativa ou de estudante?
* [Usar o Microsoft Authenticator para entrar usando seu telefone](microsoft-authenticator-app-phone-signin-faq.md)




<!--HONumber=Feb17_HO2-->


