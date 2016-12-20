---
title: "Gerenciar as configurações de verificação em duas etapas | Microsoft Docs"
description: "Gerencie como usar Autenticação Multifator do Azure incluindo alterar suas informações de contato ou configurar seus dispositivos."
services: multi-factor-authentication
keywords: "cliente do multifactor authentication, problema de autenticação, ID de correlação"
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: d3372d9a-9ad1-4609-bdcf-2c4ca9679a3b
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 830eb6627cae71f358b9790791b1d86f7c82c566
ms.openlocfilehash: 8156a7e9d9cbe1631c2e777454d04519b413d551


---
# <a name="manage-your-settings-for-two-step-verification"></a>Gerenciar as configurações de verificação em duas etapas
Este artigo responde a perguntas sobre como atualizar as configurações de autenticação multifator ou de verificação em duas etapas. Se você estiver tendo problemas ao se conectar à sua conta, confira [Tendo problemas com a verificacão em duas etapas](multi-factor-authentication-end-user-troubleshoot.md) para a resolução de problemas.

## <a name="where-to-find-the-settings-page"></a>Onde encontrar a página de configurações
Dependendo de como a sua empresa usa a Autenticação Multifator do Azure, há alguns lugares onde você pode alterar as configurações, como o número de seu telefone.

Se o seu administrador de TI envia uma URL específica ou etapas para gerenciar a verificação em duas etapas, siga as instruções. Caso contrário, as instruções a seguir devem funcionar para todos os outros. Se você seguir estas etapas, mas não vê as mesmas opções, isso significa que sua empresa ou escola personalizaram o próprio portal. Peça ao administrador o link para o portal de Autenticação Multifator do Azure.

1. Entre em [https://myapps.microsoft.com](https://myapps.microsoft.com)  
2. Na parte superior, selecione **perfil**.  
3. Escolha **Verificação de segurança adicional**.  

    ![Myapps](./media/multi-factor-authentication-end-user-manage/myapps1.png)
4. A página de Verificação de segurança adicional carrega com suas configurações.

    ![Prova](./media/multi-factor-authentication-end-user-manage/proofup.png)

## <a name="i-want-to-change-my-phone-number-or-add-a-secondary-number"></a>Desejo alterar meu número de telefone ou adicionar um número secundário
É importante configurar um número de telefone de autenticação secundário.  Como seu número de telefone principal e seu aplicativo móvel provavelmente estão no mesmo telefone, o número de telefone secundário é a única maneira de poder retornar à sua conta caso seu telefone seja roubado ou você o perca.

> [!NOTE]
> Se você não tem acesso ao seu número de telefone principal e precisa de ajuda para entrar em sua conta, confira os tópicos de Ajuda em [Tendo problemas com a verificação em duas etapas](multi-factor-authentication-end-user-troubleshoot.md).
>
>

**Para alterar o número de telefone principal:**  

1. Na página de Verificação de segurança adicional, marque a caixa de texto com seu número de telefone atual e edite com o seu novo número de telefone.  
2. Selecione **Salvar**.  
3. Se este é o número que você usa para a opção de verificação preferencial, você precisa verificar o novo número antes de salvá-lo.  

**Para adicionar um número de telefone secundário:**  

1. Na página de Verificação de segurança adicional, marque a caixa ao lado de **Telefone de autenticação alternativo.**  
2. Insira o número de telefone secundário na caixa de texto.  
3. Selecione **Salvar** e suas alterações são concluídas.  

## <a name="how-do-i-clean-up-microsoft-authenticator-from-my-old-device-and-move-to-a-new-one"></a>Como limpar o Microsoft Authenticator de meu dispositivo antigo e movê-lo para um novo?
Quando você desinstala o aplicativo do dispositivo ou reinicia o dispositivo, ele não remove a ativação no back-end. Para saber mais, veja [Microsoft Authenticator](multi-factor-authentication-microsoft-authenticator.md).

## <a name="next-steps"></a>Próximas etapas
* Obter dicas de solução de problemas e ajuda em [Tendo problemas com a verificação em duas etapas](multi-factor-authentication-end-user-troubleshoot.md)
* Configure [senhas de aplicativo](multi-factor-authentication-end-user-app-passwords.md) para quaisquer aplicativos que não dão suporte à verificação em duas etapas.



<!--HONumber=Nov16_HO3-->


