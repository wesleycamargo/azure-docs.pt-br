---
title: "O que são senhas de aplicativo no Azure MFA?"
description: "Esta página ajudará os usuários a entender o que são senhas de aplicativo e para que elas são usadas em relação ao Azure MFA."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: pblachar
ms.assetid: 345b757b-5a2b-48eb-953f-d363313be9e5
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: ce228548f4e48fb89fef3a13ddab5db3a27587e4
ms.openlocfilehash: 956481612556789cabfe8a049a906651f3fe3c1e


---
# <a name="what-are-app-passwords-in-azure-multi-factor-authentication"></a>O que são as senhas de aplicativo no Azure Multi-Factor Authentication?
Determinados aplicativos que não usam navegador, como o cliente de email nativo da Apple, que usa o Exchange Active Sync, atualmente, não oferecem suporte à autenticação multifator. O Multi-Factor Authentication é habilitado por usuário. Isso significa que se um usuário tiver sido habilitado para autenticação multifator e tentar usar aplicativos sem navegador, ele não conseguirá usá-lo. Uma senha de aplicativo permite que isso ocorra.

> [!NOTE]
> Autenticação moderna para os clientes do Office 2013
>
> Os clientes do Office 2013 (incluindo o Outlook) agora têm suporte a novos protocolos de autenticação e podem ser habilitados para oferecer suporte ao Multi-Factor Authentication.  Isso significa que, uma vez habilitadas, as senhas de aplicativo não são necessárias para usar com os clientes do Office 2013.  Para obter mais informações, consulte [Anúncio do preview público da autenticação moderna do Office 2013](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).
>
>

## <a name="how-to-use-app-passwords"></a>Como usar senhas de aplicativo
Veja a seguir do que se lembrar sobre como usar senhas de aplicativo.

* A senha real é gerada automaticamente e não é fornecida pelo usuário. Isso ocorre porque a senha gerada automaticamente é mais difícil para um invasor adivinhar e é mais segura.
* Atualmente, há um limite de 40 senhas por usuário. Se você tentar criar uma depois de ter atingido o limite, será solicitado que você exclua uma de suas senhas de aplicativo existentes para criar uma nova.
* É recomendável que as senhas de aplicativo sejam criadas por dispositivo, e não por aplicativo. Por exemplo, você pode criar uma senha de aplicativo para seu laptop e usá-la para todos os aplicativos nesse laptop.
* Você recebe uma senha de aplicativo na primeira vez que se conecta.  Se precisar de mais, é possível criá-las.

![Configuração](./media/multi-factor-authentication-end-user-app-passwords/app.png)

Quando tiver uma senha de aplicativo, você poderá usá-la no lugar da senha original com esses aplicativos que não usam navegador.  Desse modo, se você estiver usando a autenticação multifator, por exemplo, e o cliente de email nativo da Apple no seu telefone,  use a senha de aplicativo para que a autenticação multifator seja ignorada e ele continue funcionando.

## <a name="creating-and-deleting-app-passwords"></a>Criação e exclusão de senhas de aplicativo
Durante a conexão inicial, você recebe uma senha de aplicativo que pode usar.  Além disso, você também pode criar e excluir senhas de aplicativo posteriormente.  Como fazer isso depende de como você usa a autenticação multifator.  Escolha a forma que se aplica a você.

| Como você usa a autenticação multifator | Descrição |
|:--- |:--- |
| [Eu a uso com o Office 365](#creating-and-deleting-app-passwords-with-office-365) |Isso significa que você desejará criar senhas de aplicativo por meio do portal do Office 365. |
| [Não sei](#creating-and-deleting-app-passwords-with-myapps-portal) |Isso significa que você desejará criar senhas de aplicativo por meio de [https://myapps.microsoft.com](https://myapps.microsoft.com) |
| [Eu a uso com o Microsoft Azure](#create-app-passwords-in-the-azure-portal) |Isso significa que você desejará criar senhas de aplicativo por meio do portal do Azure. |

## <a name="creating-and-deleting-app-passwords-with-office-365"></a>Criação e exclusão de senhas de aplicativo com o Office 365
Se você usar uma multi-factor authentication com o Office 365, talvez seja conveniente criar e excluir senhas de aplicativo por meio do portal do Office 365.

### <a name="to-create-app-passwords-in-the-office-365-portal"></a>Para criar senhas de aplicativo no portal do Office 365
- - -
1. Entre no [Portal do Office 365](https://login.microsoftonline.com/).
2. No canto superior direito, selecione o widget e escolha Configurações do Office 365.
3. Clique em Verificação de Segurança Adicional.
4. À direita, clique no link **Atualizar meus números de telefone usados para segurança da conta.**
   ![Configurar](./media/multi-factor-authentication-end-user-manage/o365a.png)
5. Isso levará você à página que permitirá alterar as configurações.
   ![Nuvem](./media/multi-factor-authentication-end-user-manage/o365b.png)
6. Na parte superior, ao lado de verificação de segurança adicional, clique em **senhas de aplicativo.**
7. Clique em **Criar**.
   ![Nuvem](./media/multi-factor-authentication-end-user-app-passwords/apppass.png)
8. Insira um nome para a senha de aplicativo e clique em **Avançar**.
   ![Criar uma senha de aplicativo](./media/multi-factor-authentication-end-user-app-passwords/create1.png)
9. Copie a senha de aplicativo na área de transferência e cole-a no seu aplicativo.
   ![Criar uma senha de aplicativo](./media/multi-factor-authentication-end-user-app-passwords/create2.png)

### <a name="to-delete-app-passwords-using-the-office-365-portal"></a>Para excluir senhas de aplicativo usando o portal do Office 365
- - -
1. Entre no [Portal do Office 365](https://login.microsoftonline.com/).
2. No canto superior direito, selecione o widget e escolha Configurações do Office 365.
3. Clique em Verificação de Segurança Adicional.
4. À direita, clique no link **Atualizar meus números de telefone usados para segurança da conta.**
   ![Configurar](./media/multi-factor-authentication-end-user-manage/o365a.png)
5. Isso levará você à página que permitirá alterar as configurações.
   ![Nuvem](./media/multi-factor-authentication-end-user-manage/o365b.png)
6. Na parte superior, ao lado de verificação de segurança adicional, clique em **senhas de aplicativo.**
7. Do lado da senha do aplicativo que deseja remover, selecione **Excluir**.
   ![Excluir uma senha de aplicativo](./media/multi-factor-authentication-end-user-app-passwords/delete1.png)
8. Confirme a exclusão clicando em **sim**.
   ![Confirmar a exclusão](./media/multi-factor-authentication-end-user-app-passwords/delete2.png)
9. Quando a senha do aplicativo for excluída, clique em **fechar**.
   ![Close](./media/multi-factor-authentication-end-user-app-passwords/delete3.png)

## <a name="creating-and-deleting-app-passwords-with-myapps-portal"></a>Criação e exclusão de senhas de aplicativo com portal do Myapps.
Se você não tiver certeza de como usar a multi-factor authentication, você sempre pode criar e excluir senhas de aplicativo por meio do portal do Myapps.

### <a name="to-create-an-app-password-using-the-myapps-portal"></a>Para criar uma senha de aplicativo usando o portal do Myapps
1. Entre em [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Na parte superior, selecione Perfil.
3. Escolha Verificação de Segurança Adicional.
   ![Nuvem](./media/multi-factor-authentication-end-user-manage/myapps1.png)
4. Isso levará você à página que permitirá alterar as configurações.
   ![Configuração](./media/multi-factor-authentication-end-user-manage/proofup.png)
5. Na parte superior, ao lado de verificação de segurança adicional, clique em **senhas de aplicativo.**
6. Clique em **Criar**.
   ![Criar uma senha de aplicativo](./media/multi-factor-authentication-end-user-app-passwords/create3.png)
7. Insira um nome para a senha de aplicativo e clique em **Avançar**.
   ![Criar uma senha de aplicativo](./media/multi-factor-authentication-end-user-app-passwords/create1.png)
8. Copie a senha de aplicativo na área de transferência e cole-a no seu aplicativo.
   ![Criar uma senha de aplicativo](./media/multi-factor-authentication-end-user-app-passwords/create2.png)

### <a name="to-delete-an-app-password-using-the-myapps-portal"></a>Para excluir uma senha de aplicativo usando o portal do Myapps
1. Entre em [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Na parte superior, selecione Perfil.
3. Escolha Verificação de Segurança Adicional.
   ![Nuvem](./media/multi-factor-authentication-end-user-manage/myapps1.png)
4. Isso levará você à página que permitirá alterar as configurações.
   ![Configuração](./media/multi-factor-authentication-end-user-manage/proofup.png)
5. Na parte superior, ao lado de verificação de segurança adicional, clique em **senhas de aplicativo.**
6. Do lado da senha do aplicativo que deseja remover, selecione **Excluir**.
   ![Excluir uma senha de aplicativo](./media/multi-factor-authentication-end-user-app-passwords/delete1.png)
7. Confirme a exclusão clicando em **sim**.
   ![Confirmar a exclusão](./media/multi-factor-authentication-end-user-app-passwords/delete2.png)
8. Quando a senha do aplicativo for excluída, clique em **fechar**.
   ![Close](./media/multi-factor-authentication-end-user-app-passwords/delete3.png)

## <a name="create-app-passwords-in-the-azure-portal"></a>Crie senhas de aplicativo no portal do Azure
Se você usar uma autenticação multifator com o Azure, talvez seja conveniente criar senhas de aplicativo por meio do portal do Azure.

### <a name="to-create-app-passwords-in-the-azure-portal"></a>Para criar senhas de aplicativo no portal do Azure
1. Entre no Portal de Gerenciamento do Azure.
2. Na parte superior, clique com o botão direito do mouse no nome do usuário e selecione Verificação de Segurança Adicional.
3. Na parte superior da página de prova, selecione as senhas de aplicativo
4. Clique em **Criar**.
5. Insira um nome para a senha de aplicativo e clique em **Avançar**
6. Copie a senha de aplicativo na área de transferência e cole-a no seu aplicativo.
   ![Nuvem](./media/multi-factor-authentication-end-user-app-passwords/app2.png)

### <a name="to-delete-app-passwords-in-the-azure-portal"></a>Para excluir senhas de aplicativo no portal do Azure
1. Entre no Portal de Gerenciamento do Azure.
2. Na parte superior, clique com o botão direito do mouse no nome do usuário e selecione Verificação de Segurança Adicional.
3. Na parte superior, ao lado de verificação de segurança adicional, clique em **senhas de aplicativo.**
4. Do lado da senha do aplicativo que deseja remover, selecione **Excluir**.
5. Confirme a exclusão clicando em **sim**.
6. Quando a senha do aplicativo for excluída, clique em **fechar**.
   ![Close](./media/multi-factor-authentication-end-user-app-passwords/delete3.png)



<!--HONumber=Nov16_HO4-->


