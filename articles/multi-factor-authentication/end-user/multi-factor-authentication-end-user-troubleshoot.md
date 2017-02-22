---
title: "Solucionar problemas de verificação em duas etapas | Microsoft Docs"
description: "Este documento fornecerá informações aos usuários sobre o que fazer em caso de um problema com a Multi-Factor Authentication do Azure."
services: multi-factor-authentication
keywords: "cliente do multifactor authentication, problema de autenticação, ID de correlação"
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 4ff028f88e984f28bc0f4a228aabed1fabc90560
ms.openlocfilehash: 2eab76ba42955616ae4d6909c1568f03da0c1819


---
# <a name="having-trouble-with-two-step-verification"></a>Problemas com a verificação em duas etapas
Este artigo aborda alguns problemas que podem ocorrer com a verificação em duas etapas. Se o problema que você está tendo não está incluso aqui, forneça comentários detalhados na seção de comentários para que possamos melhorar.

## <a name="i-lost-my-phone-or-it-was-stolen"></a>Perdi meu telefone ou ele foi roubado
Há duas maneiras de recuperar sua conta. A primeira é fazer logon usando seu número de telefone de autenticação alternativo, se você tiver definido um. A segunda é pedir para o administrador limpar as configurações.

Se você tiver perdido seu telefone ou se ele foi roubado, recomendamos que você solicite ao administrador que redefina suas senhas de aplicativo e apague todos os dispositivos memorizados. Se o administrador não estiver certo sobre como fazer isso, envie este artigo para ele: [Gerenciar usuários e dispositivos](../multi-factor-authentication-manage-users-and-devices.md).

### <a name="use-an-alternate-phone-number"></a>Use um número de telefone alternativo
Se você tiver configurado várias opções de verificação, incluindo um número de telefone secundário ou um aplicativo autenticador em um dispositivo diferente, você pode usar uma destas opções para entrar.

Para fazer entrar usando o número de telefone alternativo, siga estas etapas:

1. Entre como faria normalmente.
2. Quando solicitado a verificar sua conta, escolha **Use uma opção de verificação diferente**.
   
    ![Verificação diferente](./media/multi-factor-authentication-end-user-manage/differentverification.png)
3. Selecione o número de telefone que você tem acesso.
   
    ![Telefone alternativo](./media/multi-factor-authentication-end-user-manage/altphone2.png)
4. Quando estiver em sua conta, [gerencie suas configurações](multi-factor-authentication-end-user-manage-settings.md) para alterar o número de telefone de autenticação.

> [!IMPORTANT]
> É importante configurar um número de telefone de autenticação secundário. Se o número de telefone principal e seu aplicativo móvel estiverem no mesmo telefone, você precisa de uma terceira opção se seu telefone for perdido ou roubado.   

### <a name="clear-your-settings"></a>Limpe suas configurações
Se você não tiver configurado um número de telefone de autenticação secundário, será necessário entrar em contato com seu administrador para obter ajuda. Peça para limpar as configurações, para que na próxima vez que você entrar, você será solicitado a [configurar sua conta](multi-factor-authentication-end-user-first-time.md) novamente.

## <a name="i-am-not-receiving-a-text-or-call-on-my-phone"></a>Não estou recebendo uma mensagem de texto ou chamada telefônica em meu telefone
Há várias razões por que você pode tentar entrar, mas não recebe a chamada telefônica ou a mensagem de texto. Se você já recebeu com êxito mensagens ou chamadas telefônicas em seu telefone antes, então, este é provavelmente um problema com o provedor de telefone, não sua conta. Certifique-se de que você tem um bom sinal de celular, e se você está tentando receber uma mensagem de texto, certifique-se de que seu plano de telefone e serviços fornece suporte à mensagens de texto.

Se você aguardou vários minutos por uma chamada ou mensagem de texto, a maneira mais rápida de acessar sua conta é tentar uma opção diferente.

1. Selecione **Usar uma opção de verificação diferente** na página que está aguardando a sua verificação.
   
    ![Verificação diferente](./media/multi-factor-authentication-end-user-troubleshoot/diff_option.png)
2. Selecione o número de telefone ou método de entrega que deseja usar.
   
    Se você tiver recebido vários códigos de verificação, somente o mais recente deles funcionará.

Se você não tiver outro método configurado, contate seu administrador e peça para ele limpar as configurações. Na próxima vez que entrar, você será solicitado a [Configurar autenticação multifator](multi-factor-authentication-end-user-first-time.md) novamente.

Se você geralmente tem atrasos na recepção devido a um sinal de celular ruim, recomendamos que você use o [aplicativo Microsoft Authenticator](microsoft-authenticator-app-how-to.md) em seu smartphone. O aplicativo pode gerar códigos de segurança aleatórios que você usa para entrar, e esses códigos não exigem qualquer conexão de internet ou um sinal de celular.

## <a name="app-passwords-are-not-working"></a>As senhas de aplicativo não estão funcionando.
Primeiro, verifique se você inseriu a senha de aplicativo corretamente.  Se isso ainda não estiver funcionando, tente se conectar e [criar uma nova senha de aplicativo](multi-factor-authentication-end-user-app-passwords.md).  Se isso não funcionar, entre em contato com o administrador e peça que ele [exclua suas senhas de aplicativo existentes](../multi-factor-authentication-manage-users-and-devices.md) e crie uma nova para usá-la.

## <a name="i-didnt-find-an-answer-to-my-problem"></a>Não encontrei uma resposta para o meu problema.
Se você tentou essas etapas de solução de problemas, mas ainda está com problemas, contate o administrador ou a pessoa que configurou a autenticação multifator para você. Eles devem ser capazes de ajudá-lo.

Além disso, você pode postar uma pergunta nos [fóruns do Azure AD](https://social.msdn.microsoft.com/forums/azure/home?forum=WindowsAzureAD) ou [entre em contato com o suporte](https://support.microsoft.com/contactus) e responderemos a seu problema assim que possível.

Ao entrar em contato com o suporte, inclua as seguintes informações:

* **ID de usuário** – qual é o endereço de email que você tentou?
* **Descrição geral do erro** - exatamente qual mensagem de erro você viu?  Se não havia nenhuma mensagem de erro, descreva o comportamento inesperado observado em detalhes.
* **Página**: em qual página você estava quando viu o erro (Inclua a URL.)?
* **ErrorCode** : o código de erro específico que você está recebendo.
* **SessionId** : a ID de sessão específica que você está recebendo.
* **ID de Correlação** : o código de ID de correlação gerado quando o usuário viu o erro.
* **Carimbo de Data/Hora**: em que data e a hora exatas você viu o erro (inclua o fuso horário)?

Muitas dessas informações podem ser encontradas na página de entrada. Quando você não verifica sua hora de entrada, selecione **Exibir detalhes**.

![Detalhes do Erro de Entrada](./media/multi-factor-authentication-end-user-troubleshoot/view_details.png)

A inclusão dessas informações nos ajudará a resolver o problema o mais rápido possível.

## <a name="related-topics"></a>Tópicos relacionados
* [Gerenciar as configurações de verificação em duas etapas](multi-factor-authentication-end-user-manage-settings.md)  
* [Perguntas frequentes sobre o aplicativo Microsoft Authenticator](microsoft-authenticator-app-faq.md)




<!--HONumber=Dec16_HO4-->


