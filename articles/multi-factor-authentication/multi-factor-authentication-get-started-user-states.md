<properties 
	pageTitle="Estados de usuário do Microsoft Azure Multi-Factor Authentication" 
	description="Saiba mais sobre estados de usuário no Azure MFA." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/04/2016" 
	ms.author="billmath"/>

# Estados do usuário no Azure Multi-Factor Authentication

As contas de usuário no Azure Multi-Factor Authentication apresentam os três estados distintos a seguir:

Estado | Descrição |Aplicativos que não usam navegador afetados| Observações 
:-------------: | :-------------: |:-------------: |:-------------: |
Desabilitado | O estado padrão para um novo usuário não inscrito no Multi-Factor Authentication.|Não|O usuário não está usando a autenticação multifator.
Habilitado |O usuário foi inscrito no Multi-Factor Authentication.|Não. Eles continuarão a trabalhar até o processo ser concluído.|O usuário está habilitado, mas não concluiu o processo de registro. Eles serão solicitados a concluir o processo na próxima vez que se conectarem.
Imposto|O usuário foi inscrito e concluiu o processo de registro para usar o Multi-Factor Authentication.|Sim. Os aplicativos exigem senhas de aplicativo. | O usuário pode ou não ter concluído o registro. Se tiver concluído o processo de registro, significa que ele está usando a autenticação multifator. Caso contrário, o usuário será solicitado a concluir o processo na próxima vez que se conectar

## Alteração do estado do usuário
Um estado de usuários será alterado dependendo se ele tiver sido configurado para MFA e se o usuário tiver concluído o processo. Quando você ativa o MFA para um usuário, o estado dos usuários será alterado de desabilitado para habilitado. Depois que o usuário, cujo estado tenha sido alterado para habilitado, entre e conclua o processo, seu estado será alterado para imposto.

### Para exibir o estado do usuário
--------------------------------------------------------------------------------
1.  Entre no **portal clássico do Azure** como Administrador.
2.  À esquerda, clique em **Active Directory**.
3.  Em **Diretório**, clique no diretório do usuário que deseja habilitar. ![Clicar no Diretório](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4.  Na parte superior, clique em **Usuários**.
5.  Na parte inferior da página, clique em **Gerenciar Multi-Factor Auth**. ![Clicar no Diretório](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6.  Isso abrirá uma nova guia do navegador. Você poderá exibir o estado dos usuários. ![Clicar no Diretório](./media/multi-factor-authentication-get-started-user-states/userstate1.png)

###Para alterar o estado de desabilitado para habilitado
1.  Entre no **portal clássico do Azure** como Administrador.
2.  À esquerda, clique em **Active Directory**.
3.  Em **Diretório**, clique no diretório do usuário que deseja habilitar. ![Clicar no Diretório](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4.  Na parte superior, clique em **Usuários**.
5.  Na parte inferior da página, clique em **Gerenciar Multi-Factor Auth**. ![Clicar no Diretório](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6.  Isso abrirá uma nova guia do navegador. Localize o usuário que deseja habilitar para o Multi-Factor Authentication. Talvez seja necessário alterar o modo de exibição na parte superior. Verifique se o status é **desabilitado.** ![Habilitar o usuário](./media/multi-factor-authentication-get-started-cloud/enable1.png)
7.  Coloque uma **marca** na caixa ao lado do nome.
7.  À direita, clique em **Habilitar**. ![Habilitar o usuário](./media/multi-factor-authentication-get-started-cloud/user1.png)
8.  Clique em **Habilitar autenticação multifator**. ![Habilitar o usuário](./media/multi-factor-authentication-get-started-cloud/enable2.png)
9.  Você deve observar se o estado do usuário foi alterado de **desabilitado** para **habilitado**. ![Habilitar os Usuários](./media/multi-factor-authentication-get-started-cloud/user.png)
10.  Após habilitar seus usuários, recomendamos a notificação por email. Ele também deve informá-los de como eles podem usar seus aplicativos sem navegador para evitar o bloqueio.

### Para alterar o estado de habilitado/imposto para desabilitado
1.  Entre no **portal clássico do Azure** como Administrador.
2.  À esquerda, clique em **Active Directory**.
3.  Em **Diretório**, clique no diretório do usuário que deseja habilitar. ![Clicar no Diretório](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4.  Na parte superior, clique em **Usuários**.
5.  Na parte inferior da página, clique em **Gerenciar Multi-Factor Auth**. ![Clicar no Diretório](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6.  Isso abrirá uma nova guia do navegador. Encontre o usuário que você deseja desabilitar. Talvez seja necessário alterar o modo de exibição na parte superior. Certifique-se de que o status seja **habilitado** ou **imposto.**
7.  Coloque uma **marca** na caixa ao lado do nome.
7.  À direita, clique em **Desabilitar**. ![Desabilitar usuário](./media/multi-factor-authentication-get-started-user-states/userstate2.png)
8.  Sua confirmação será solicitada. Clique em **Sim**. ![Desabilitar usuário](./media/multi-factor-authentication-get-started-user-states/userstate3.png)
9.  Em seguida, você verá que foi bem-sucedida. Clique em **fechar.** ![Desabilitar usuário](./media/multi-factor-authentication-get-started-user-states/userstate4.png)

<!---HONumber=AcomDC_0810_2016-->