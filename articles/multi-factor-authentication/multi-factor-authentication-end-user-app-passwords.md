<properties 
	pageTitle="O que são senhas de aplicativo no Azure MFA?" 
	description="Esta página ajudará os usuários a entender o que são senhas de aplicativo e para que elas são usadas em relação ao Azure MFA." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtland"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/12/2016" 
	ms.author="billmath"/>



# O que são as senhas de aplicativo no Azure Multi-Factor Authentication?

Determinados aplicativos que não usam navegador, como o cliente de email nativo da Apple, que usa o Exchange Active Sync, atualmente, não oferecem suporte à autenticação multifator. O Multi-Factor Authentication é habilitado por usuário. Isso significa que se um usuário tiver sido habilitado para autenticação multifator e tentar usar aplicativos sem navegador, ele não conseguirá usá-lo. Uma senha de aplicativo permite que isso ocorra.

>[AZURE.NOTE] Autenticação moderna para os clientes do Office 2013
>
> Os clientes do Office 2013 (incluindo o Outlook) agora têm suporte a novos protocolos de autenticação e podem ser habilitados para oferecer suporte ao Multi-Factor Authentication. Isso significa que, uma vez habilitadas, as senhas de aplicativo não são necessárias para usar com os clientes do Office 2013. Para obter mais informações, consulte [Anúncio do preview público da autenticação moderna do Office 2013](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).
 
## Como usar senhas de aplicativo

Veja a seguir do que se lembrar sobre como usar senhas de aplicativo.

- A senha real é gerada automaticamente e não é fornecida pelo usuário. Isso ocorre porque a senha gerada automaticamente é mais difícil para um invasor adivinhar e é mais segura.
- Atualmente, há um limite de 40 senhas por usuário. Se você tentar criar uma depois de ter atingido o limite, será solicitado que você exclua uma de suas senhas de aplicativo existentes para criar uma nova.
- É recomendável que as senhas de aplicativo sejam criadas por dispositivo, e não por aplicativo. Por exemplo, você pode criar uma senha de aplicativo para seu laptop e usá-la para todos os aplicativos nesse laptop.
- Você recebe uma senha de aplicativo na primeira vez que se conecta. Se precisar de mais, é possível criá-las.
 
![Configuração](./media/multi-factor-authentication-end-user-app-passwords/app.png)

Quando tiver uma senha de aplicativo, você poderá usá-la no lugar da senha original com esses aplicativos que não usam navegador. Desse modo, se você estiver usando a autenticação multifator, por exemplo, e o cliente de email nativo da Apple no seu telefone, use a senha de aplicativo para que a autenticação multifator seja ignorada e ele continue funcionando.

## Criação e exclusão de senhas de aplicativo
Durante a conexão inicial, você recebe uma senha de aplicativo que pode usar. Além disso, você também pode criar e excluir senhas de aplicativo posteriormente. Como fazer isso depende de como você usa a autenticação multifator. Escolha a forma que se aplica a você.

Como você usa a autenticação multifator|Descrição
:------------- | :------------- | 
<a href="#Office">Eu a uso com o Office 365</a> | Isso significa que você desejará criar senhas de aplicativo por meio do portal do Office 365.
<a href="#Microsoft">Não sei</a>|Isso significa que você desejará criar senhas de aplicativo por meio de [https://myapps.microsoft.com](https://myapps.microsoft.com)
<a href="#vai">Eu a uso com o Microsoft Azure</a> | Isso significa que você desejará criar senhas de aplicativo por meio do portal do Azure.

<h2 id="Office">Eu a uso com o Office 365</h2> 

Se você usar uma multi-factor authentication com o Office 365, talvez seja conveniente criar e excluir senhas de aplicativo por meio do portal do Office 365.

### Para criar senhas de aplicativo no portal do Office 365
--------------------------------------------------------------------------------

1. Entre no [Portal do Office 365](https://login.microsoftonline.com/).
2. No canto superior direito, selecione o widget e escolha Configurações do Office 365.
3. Clique em Verificação de Segurança Adicional.
4. À direita, clique no link **Atualizar meus números de telefone usados para segurança da conta.** ![Configuração](./media/multi-factor-authentication-end-user-manage/o365a.png)
5. Isso levará você à página que permitirá que altere suas configurações. ![Nuvem](./media/multi-factor-authentication-end-user-manage/o365b.png)
6. Na parte superior, ao lado de verificação de segurança adicional, clique em **senhas de aplicativo.**
7. Clique em **Criar**.![Nuvem](./media/multi-factor-authentication-end-user-app-passwords-create-o365/apppass.png)
8. Insira um nome para a senha de aplicativo e clique em **Avançar**.![Criar uma senha de aplicativo](./media/multi-factor-authentication-end-user-app-passwords/create1.png)
9. Copie a senha de aplicativo na área de transferência e cole-a no seu aplicativo.![Criar uma senha de aplicativo](./media/multi-factor-authentication-end-user-app-passwords/create2.png)


### Para excluir senhas de aplicativo usando o portal do Office 365
--------------------------------------------------------------------------------


1. Entre no [Portal do Office 365](https://login.microsoftonline.com/).
2. No canto superior direito, selecione o widget e escolha Configurações do Office 365.
3. Clique em Verificação de Segurança Adicional.
4. À direita, clique no link **Atualizar meus números de telefone usados para segurança da conta.** ![Configuração](./media/multi-factor-authentication-end-user-manage/o365a.png)
5. Isso levará você à página que permitirá que altere suas configurações. ![Nuvem](./media/multi-factor-authentication-end-user-manage/o365b.png)
6. Na parte superior, ao lado de verificação de segurança adicional, clique em **senhas de aplicativo.**
7. Do lado da senha do aplicativo que deseja remover, selecione **Excluir**.![Excluir uma senha de aplicativo](./media/multi-factor-authentication-end-user-app-passwords/delete1.png)
8. Confirme a exclusão clicando em **sim**. ![Confirmar a exclusão](./media/multi-factor-authentication-end-user-app-passwords/delete2.png)
9. Quando a senha do aplicativo for excluída, clique em **fechar**. ![Fechar](./media/multi-factor-authentication-end-user-app-passwords/delete3.png)


<h2 id="Microsoft">Eu a uso com o Microsoft Azure</h2>
Se você não tiver certeza de como usar a multi-factor authentication, você sempre pode criar e excluir senhas de aplicativo por meio do portal do Myapps.

### Para criar uma senha de aplicativo usando o portal do Myapps

1. Entre em [https://myapps.microsoft.com](https://myapps.microsoft.com)	
2. Na parte superior, selecione perfil.
3. Selecione verificação de Segurança Adicional.![Nuvem](./media/multi-factor-authentication-end-user-manage/myapps1.png)
4. Isso levará você à página que permitirá que altere suas configurações. ![Configuração](./media/multi-factor-authentication-end-user-manage-myapps/proofup.png)
5. Na parte superior, ao lado de verificação de segurança adicional, clique em **senhas de aplicativo.**
6. Clique em **Criar**.![Criar uma senha de aplicativo](./media/multi-factor-authentication-end-user-app-passwords/create3.png)
7. Insira um nome para a senha de aplicativo e clique em **Avançar**.![Criar uma senha de aplicativo](./media/multi-factor-authentication-end-user-app-passwords/create1.png)
8. Copie a senha de aplicativo na área de transferência e cole-a no seu aplicativo.![Criar uma senha de aplicativo](./media/multi-factor-authentication-end-user-app-passwords/create2.png)

### Para excluir uma senha de aplicativo usando o portal do Myapps

1. Entre em [https://myapps.microsoft.com](https://myapps.microsoft.com)	
2. Na parte superior, selecione perfil.
3. Selecione verificação de Segurança Adicional.![Nuvem](./media/multi-factor-authentication-end-user-manage/myapps1.png)
4. Isso levará você à página que permitirá que altere suas configurações. ![Configuração](./media/multi-factor-authentication-end-user-manage-myapps/proofup.png)
5. Na parte superior, ao lado de verificação de segurança adicional, clique em **senhas de aplicativo.**
6. Do lado da senha do aplicativo que deseja remover, selecione **Excluir**.![Excluir uma senha de aplicativo](./media/multi-factor-authentication-end-user-app-passwords/delete1.png)
7. Confirme a exclusão clicando em **sim**. ![Confirmar a exclusão](./media/multi-factor-authentication-end-user-app-passwords/delete2.png)
8. Quando a senha do aplicativo for excluída, clique em **fechar**. ![Fechar](./media/multi-factor-authentication-end-user-app-passwords/delete3.png)


<h2 id="vai">Crie senhas de aplicativo no portal do Azure</h2>

Se você usar uma autenticação multifator com o Azure, talvez seja conveniente criar senhas de aplicativo por meio do portal do Azure.

### Para criar senhas de aplicativo no portal do Azure

1. Entre no Portal de Gerenciamento do Azure.
2. Na parte superior, clique com o botão direito do mouse no nome do usuário e selecione Verificação de Segurança Adicional.
3. Na parte superior da página de prova, selecione as senhas de aplicativo
4. Clique em **Criar**.
5. Insira um nome para a senha de aplicativo e clique em **Avançar**
6. Copie a senha de aplicativo na área de transferência e cole-a no seu aplicativo.![Nuvem](./media/multi-factor-authentication-end-user-app-passwords-create-azure/app2.png)

### Para excluir senhas de aplicativo no portal do Azure

1. Entre no Portal de Gerenciamento do Azure.
2. Na parte superior, clique com o botão direito do mouse no nome do usuário e selecione Verificação de Segurança Adicional.
3. Na parte superior, ao lado de verificação de segurança adicional, clique em **senhas de aplicativo.**
4. Do lado da senha do aplicativo que deseja remover, selecione **Excluir**.
5. Confirme a exclusão clicando em **sim**.
6. Quando a senha do aplicativo for excluída, clique em **fechar**. ![Fechar](./media/multi-factor-authentication-end-user-app-passwords/delete3.png)

<!---HONumber=AcomDC_0518_2016-->