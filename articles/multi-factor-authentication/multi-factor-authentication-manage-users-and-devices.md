<properties 
	pageTitle="Relatórios do Azure Multi-Factor Authentication" 
	description="Isso descreve como alterar as configurações de usuário, como forçar os usuários a fazer o processo de verificação novamente." 
	documentationCenter="" 
	services="multi-factor-authentication" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/24/2016" 
	ms.author="billmath"/>

# Gerenciando configurações de usuário com o Azure Multi-Factor Authentication na nuvem

Como administrador, você pode gerenciar as seguintes configurações de usuário e dispositivo.

- [Exigir que os usuários selecionados forneçam métodos de contato novamente](#require-selected-users-to-provide-contact-methods-again)
- [Excluir senhas de aplicativo de usuários existentes](#delete-users-existing-app-passwords)
- [Restaurar a MFA em todos os dispositivos suspensos para um usuário (visualização pública)](#restore-mfa-on-all-suspended-devices-for-a-user)






Isso é útil se um computador ou dispositivo for perdido ou roubado ou se você precisar remover o acesso de um usuário.


## Exigir que os usuários selecionados forneçam métodos de contato novamente

Essa configuração forçará o usuário a seguir o processo de registro novamente quando fizer logon. Lembre-se de que aplicativos sem navegador continuarão funcionando se o usuário tiver senhas de aplicativos para eles. Você pode excluir as senhas de aplicativos de usuários selecionando também ** Excluir todas as senhas de aplicativo existentes geradas pelos usuários selecionados**.

### Como exigir que os usuários selecionados forneçam métodos de contato novamente




1. Entre no Portal clássico do Azure.
2. À esquerda, clique no Active Directory.
3. Em Diretório, clique no diretório para o usuário de quem deseja exigir o fornecimento do método de contato novamente.
4. Na parte superior, clique em Usuários.
5. Na parte inferior da página, clique em Gerenciar Multi-Factor Auth. Isso abrirá a página da autenticação multifator.
6. Encontre o usuário que você deseja gerenciar e marque a caixa localizada ao lado do nome. Talvez seja necessário alterar o modo de exibição na parte superior.
7. Isso abrirá o link **Gerenciar configurações de usuário** à direita. Clique.
8. Marque **Exigir que os usuários selecionados forneçam métodos de contato novamente**.![Fornecer métodos de contato](./media/multi-factor-authentication-manage-users-and-devices/reproofup.png)
10. Clique em Salvar.
11. Clique em Fechar

## Excluir senhas de aplicativo de usuários existentes

Isso exclui todas as senhas de aplicativo que um usuário criou. Aplicativos sem navegador que estavam associados a essas senhas de aplicativo deixarão de funcionar até que uma nova senha de aplicativo seja criada.

### Como excluir senhas de aplicativo de usuários existentes

1. Entre no Portal clássico do Azure.
2. À esquerda, clique no Active Directory.
3. Em Diretório, clique no diretório do usuário de quem deseja excluir senhas.
4. Na parte superior, clique em Usuários.
5. Na parte inferior da página, clique em Gerenciar Multi-Factor Auth. Isso abrirá a página da autenticação multifator.
6. Encontre o usuário que você deseja gerenciar e marque a caixa localizada ao lado do nome. Talvez seja necessário alterar o modo de exibição na parte superior.
7. Isso abrirá o link **Gerenciar configurações de usuário** à direita. Clique. 
8. Marque **Excluir todas as senhas de aplicativo existentes geradas pelos usuários selecionados**.![Excluir senhas de aplicativo](./media/multi-factor-authentication-manage-users-and-devices/deleteapppasswords.png)
10. Clique em Salvar.
10. Clique em Fechar.

## Restaurar o MFA em todos os dispositivos lembrados de um usuário

Os administradores têm a capacidade de restaurar o Multi-Factor Authentication nos dispositivos e navegadores dos usuários. Ao fazer isso, isso removerá a configuração de lembrar o MFA de todos os dispositivos e navegadores do usuário, e o usuário deverá utilizar o MFA ao entrar na próxima vez.

### Como Restaurar a MFA em todos os dispositivos suspensos para um usuário

1. Entre no Portal clássico do Azure.
2. À esquerda, clique no Active Directory.
3. Em Diretório, clique no diretório do usuário em que deseja restaurar a MFA.
4. Na parte superior, clique em Usuários.
5. Na parte inferior da página, clique em Gerenciar Multi-Factor Auth. Isso abrirá a página da autenticação multifator.
6. Encontre o usuário que você deseja gerenciar e marque a caixa localizada ao lado do nome. Talvez seja necessário alterar o modo de exibição na parte superior.
7. Isso abrirá o link **Gerenciar configurações de usuário** à direita. Clique.
8. Marque **Restaurar a autenticação multifator em todos os dispositivos lembrados** ![Excluir senhas de aplicativo](./media/multi-factor-authentication-manage-users-and-devices/rememberdevices.png)
9. Clique em Salvar.
10. Clique em Fechar.

<!---HONumber=AcomDC_0302_2016-->