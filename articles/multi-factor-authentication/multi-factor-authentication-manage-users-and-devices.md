---
title: "Administradores gerenciam usuários e dispositivos - Azure MFA | Microsoft Docs"
description: "Isso descreve como alterar as configurações de usuário, como forçar os usuários a fazer o processo de verificação novamente."
documentationcenter: 
services: multi-factor-authentication
author: kgremban
manager: femila
editor: yossib
ms.assetid: aac3b922-7cc1-428c-9044-273579aa7b5a
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 847a8bdcf880b56f587f6759058825fd1965d29e
ms.openlocfilehash: 43ab735b91bf3f3f1e9631067827f2c456dd7b72
ms.lasthandoff: 03/01/2017


---
# <a name="managing-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>Gerenciando configurações de usuário com o Azure Multi-Factor Authentication na nuvem
Como administrador, você pode gerenciar as seguintes configurações de usuário e dispositivo:

* Exigir que os usuários selecionados forneçam métodos de contato novamente
* Excluir senhas de aplicativo de usuários existentes
* Restaurar a MFA em todos os dispositivos suspensos para um usuário

## <a name="require-selected-users-to-provide-contact-methods-again"></a>Exigir que os usuários selecionados forneçam métodos de contato novamente
Essa configuração forçará o usuário a seguir o processo de registro novamente quando fizer logon. Lembre-se de que aplicativos sem navegador continuarão funcionando se o usuário tiver senhas de aplicativos para eles.  Você pode excluir as senhas de aplicativos de usuários selecionando também **Excluir todas as senhas de aplicativo existentes geradas pelos usuários selecionados**.

### <a name="how-to-require-users-to-provide-contact-methods-again"></a>Como exigir que os usuários selecionados forneçam métodos de contato novamente
1. Entre no Portal clássico do Azure.
2. À esquerda, clique no Active Directory.
3. Em Diretório, clique no diretório para o usuário de quem deseja exigir o fornecimento do método de contato novamente.
4. Na parte superior, clique em Usuários.
5. Na parte inferior da página, clique em Gerenciar Multi-Factor Auth. Isso abrirá a página da autenticação multifator.
6. Encontre o usuário que você deseja gerenciar e marque a caixa localizada ao lado do nome. Talvez seja necessário alterar o modo de exibição na parte superior.
7. Isso abrirá o link **Gerenciar configurações de usuário** à direita. Clique.
8. Marque **Exigir que os usuários selecionados forneçam métodos de contato novamente**.
   ![Fornecer métodos de contato](./media/multi-factor-authentication-manage-users-and-devices/reproofup.png)
9. Clique em Salvar.
10. Clique em Fechar

## <a name="delete-users-existing-app-passwords"></a>Excluir senhas de aplicativo de usuários existentes
Isso exclui todas as senhas de aplicativo que um usuário criou. Aplicativos sem navegador que estavam associados a essas senhas de aplicativo deixarão de funcionar até que uma nova senha de aplicativo seja criada.

### <a name="how-to-delete-users-existing-app-passwords"></a>Como excluir senhas de aplicativo de usuários existentes
1. Entre no Portal clássico do Azure.
2. À esquerda, clique no Active Directory.
3. Em Diretório, clique no diretório do usuário de quem deseja excluir senhas.
4. Na parte superior, clique em Usuários.
5. Na parte inferior da página, clique em Gerenciar Multi-Factor Auth. Isso abrirá a página da autenticação multifator.
6. Encontre o usuário que você deseja gerenciar e marque a caixa localizada ao lado do nome. Talvez seja necessário alterar o modo de exibição na parte superior.
7. Isso abrirá o link **Gerenciar configurações de usuário** à direita. Clique.
8. Marque **Excluir todas as senhas de aplicativo existentes geradas pelos usuários selecionados**.
   ![Excluir senhas de aplicativo](./media/multi-factor-authentication-manage-users-and-devices/deleteapppasswords.png)
9. Clique em Salvar.
10. Clique em Fechar.

## <a name="restore-mfa-on-all-remembered-devices-for-a-user"></a>Restaurar o MFA em todos os dispositivos lembrados de um usuário
Um dos recursos configuráveis da Autenticação Multifator do Azure está dando aos usuários a opção de marcar dispositivos como confiáveis. Para saber mais, confira [Definir as configurações da Autenticação Multifator do Azure](multi-factor-authentication-whats-next.md#remember-multi-factor-authentication-for-devices-that-users-trust)

Os usuários podem escolher ignorar a verificação em duas etapas durante um período configurável em seus dispositivos regulares. Se uma conta for comprometida ou um dispositivo confiável for perdido, você precisa conseguir remover o status de confiável e exigir novamente a verificação em duas etapas.

A configuração **Restaurar a autenticação multifator em todos os dispositivos lembrados** significa que o usuário será desafiado a executar uma verificação de duas etapas na próxima vez que entrar, independentemente de ter optado por marcar seu dispositivo como confiável. 

### <a name="how-to-restore-mfa-on-all-suspended-devices-for-a-user"></a>Como Restaurar a MFA em todos os dispositivos suspensos para um usuário
1. Entre no Portal clássico do Azure.
2. À esquerda, clique no Active Directory.
3. Em Diretório, clique no diretório do usuário em que deseja restaurar a MFA.
4. Na parte superior, clique em Usuários.
5. Na parte inferior da página, clique em Gerenciar Multi-Factor Auth. Isso abrirá a página da autenticação multifator.
6. Encontre o usuário que você deseja gerenciar e marque a caixa localizada ao lado do nome. Talvez seja necessário alterar o modo de exibição na parte superior.
7. Isso abrirá o link **Gerenciar configurações de usuário** à direita. Clique.
8. Marque **Restaurar a autenticação multifator em todos os dispositivos lembrados**
   ![Excluir senhas do aplicativo](./media/multi-factor-authentication-manage-users-and-devices/rememberdevices.png)
9. Clique em Salvar.
10. Clique em Fechar.

