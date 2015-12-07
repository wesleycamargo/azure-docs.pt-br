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
	ms.date="11/19/2015" 
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

<ol>
<li>Entre no Portal de Gerenciamento do Azure.</li>
<li>À esquerda, clique no Active Directory.</li>
<li>Em Diretório, clique no diretório para o usuário de quem deseja exigir o fornecimento do método de contato novamente.</li>
<li>Na parte superior, clique em Usuários.</li>
<li>Na parte inferior da página, clique em Gerenciar Multi-Factor Auth. Isso abrirá a página da autenticação multifator. <li>Encontre o usuário que você deseja gerenciar e marque a caixa localizada ao lado do nome. Talvez seja necessário alterar o modo de exibição na parte superior.</li> <li>Isso abrirá o link **Gerenciar configurações de usuário** à direita. Clique.</li> <li>Marque **Exigir que os usuários selecionados forneçam métodos de contato novamente**.</li>

![Fornecer métodos de contato](./media/multi-factor-authentication-manage-users-and-devices/reproofup.png)

<li>Clique em Salvar.</li>
<li>Clique em Fechar</li>

## Excluir senhas de aplicativo de usuários existentes

Isso exclui todas as senhas de aplicativo que um usuário criou. Aplicativos sem navegador que estavam associados a essas senhas de aplicativo deixarão de funcionar até que uma nova senha de aplicativo seja criada.

### Como excluir senhas de aplicativo de usuários existentes

<ol>
<li>Entre no Portal de Gerenciamento do Azure.</li>
<li>À esquerda, clique no Active Directory.</li>
<li>Em Diretório, clique no diretório do usuário de quem deseja excluir senhas.</li>
<li>Na parte superior, clique em Usuários.</li>
<li>Na parte inferior da página, clique em Gerenciar Multi-Factor Auth. Isso abrirá a página da autenticação multifator. <li>Encontre o usuário que você deseja gerenciar e marque a caixa localizada ao lado do nome. Talvez seja necessário alterar o modo de exibição na parte superior.</li> <li>Isso abrirá o link **Gerenciar configurações de usuário** à direita. Clique.</li> <li>Marque **Excluir todas as senhas de aplicativo existentes geradas pelos usuários selecionados**.</li> ![Excluir senhas de aplicativo](./media/multi-factor-authentication-manage-users-and-devices/deleteapppasswords.png) <li>Clique em Salvar.</li> <li>Clique em Fechar.</li>





## Restaurar a MFA em todos os dispositivos suspensos para um usuário

Os administradores têm a capacidade de redefinir a autenticação multifator em seus dispositivos e navegadores. Isso é feito por meio da restauração da autenticação multifator para navegadores e dispositivos do usuário. Quando feito, isso removerá a suspensão de todos os navegadores e dispositivos do usuário.

### Como Restaurar a MFA em todos os dispositivos suspensos para um usuário

<ol>
<li>Entre no Portal de Gerenciamento do Azure.</li>
<li>À esquerda, clique no Active Directory.</li>
<li>Em Diretório, clique no diretório do usuário em que deseja restaurar a MFA.</li>
<li>Na parte superior, clique em Usuários.</li>
<li>Na parte inferior da página, clique em Gerenciar Multi-Factor Auth. Isso abrirá a página da autenticação multifator. <li>Encontre o usuário que você deseja gerenciar e marque a caixa localizada ao lado do nome. Talvez seja necessário alterar o modo de exibição na parte superior.</li> <li>Isso abrirá o link **Gerenciar configurações de usuário** à direita. Clique.</li> <li>Marque Restaurar o Multi-Factor Authentication em todos os dispositivos suspensos.</li> ![Excluir senhas de aplicativo](./media/multi-factor-authentication-manage-users-and-devices/rememberdevices.png) <li>Clique em Salvar.</li> <li>Clique em Fechar.</li>

<!---HONumber=AcomDC_1125_2015-->