---
title: "Estados de usuário do Microsoft Azure Multi-Factor Authentication"
description: "Saiba mais sobre estados de usuário no Azure MFA."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: curtand
ms.assetid: 0b9fde23-2d36-45b3-950d-f88624a68fbd
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 999361daa2faebe3e88cab0b6085a938d6f40e9d
ms.openlocfilehash: 3079969783f589cb90f17eac116c5d57884db49f
ms.lasthandoff: 02/17/2017


---
# <a name="user-states-in-azure-multi-factor-authentication"></a>Estados do usuário no Azure Multi-Factor Authentication
As contas de usuário no Azure Multi-Factor Authentication apresentam os três estados distintos a seguir:

| Estado | Descrição | Aplicativos que não usam navegador afetados | 
|:---:|:---:|:---:|
| Desabilitado |O estado padrão para um novo usuário não inscrito na Autenticação Multifator do Azure (MFA). |Não |
| Habilitado |O usuário foi inscrito no MFA do Azure, mas não foi registrado. Na próxima vez que entrar, ele receberá uma solicitação para que se registre. |Não.  Eles continuarão a trabalhar até o processo ser concluído. |
| Imposto |O usuário foi inscrito e concluiu o processo de registro para usar a MFA do Azure. |Sim.  Os aplicativos exigem senhas de aplicativo. |

## <a name="changing-a-user-state"></a>Alteração do estado do usuário
O estado de um usuário reflete se um administrador o registrou na MFA do Azure e se ele concluiu o processo de registro.

Todos os usuários começam com o status *desabilitado*. Quando você registra os usuários na MFA do Azure, seu estado é alterado para *habilitado*. Quando usuários habilitados entram e concluem o processo de registro, seu estado é alterado para *aplicado*.  

### <a name="view-user-states"></a>Exibir estados do usuário

Use as etapas a seguir para acessar a página em que você pode exibir e gerenciar estados de usuário:

1. Entre no [portal clássico do Azure](https://manage.windowsazure.com) como um administrador.
2. Selecione **Active Directory**à esquerda.
3. Selecione o diretório do usuário que você deseja exibir.
   ![Selecionar o diretório – captura de tela](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4. Selecione **Usuários**.
5. Na parte inferior da página, selecione **Gerenciar Autenticação Multifator**. 
   ![Selecionar Gerenciar Autenticação Multifator – captura de tela](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6. Uma nova guia que exibe os estados do usuário é aberta.
   ![estados do usuário da Autenticação Multifator do Azure – captura de tela](./media/multi-factor-authentication-get-started-user-states/userstate1.png)

### <a name="change-the-state-from-disabled-to-enabled"></a>Alterar o estado de desabilitado para habilitado

1. Use as etapas anteriores para chegar à página de usuários da Autenticação Multifator. 
2. Localize o usuário que você deseja habilitar para a MFA do Azure. Talvez seja necessário alterar o modo de exibição na parte superior. Certifique-se de que o status é **desabilitado**.
   ![Localizar usuário – captura de tela](./media/multi-factor-authentication-get-started-cloud/enable1.png)
3. Marque a caixa, ao lado do nome.
4. À direita, em etapas rápidas, clique em **Habilitar**.
   ![Habilitar o usuário selecionado – captura de tela](./media/multi-factor-authentication-get-started-cloud/user1.png)
5. Selecione **habilitar autenticação multifator**.
   ![Habilitar autenticação multifator – captura de tela](./media/multi-factor-authentication-get-started-cloud/enable2.png)
6. Observe que o estado do usuário foi alterado de **desabilitado** para **habilitado**.
   ![Ver que o usuário agora está habilitado – captura de tela](./media/multi-factor-authentication-get-started-cloud/user.png)

Depois de habilitar os usuários, você deverá notificá-los por email. Inclua a informação de que será solicitado que eles se registrem na próxima vez que entrarem; além disso, informe-os também que alguns aplicativos não navegador podem não funcionar com a verificação em duas etapas. Você também pode incluir um link para nosso [Guia do usuário final da MFA do Azure](./end-user/multi-factor-authentication-end-user.md) para ajudá-los a começar. 

### <a name="to-change-the-state-from-enabledenforced-to-disabled"></a>Para alterar o estado de habilitado/imposto para desabilitado

1. Use as etapas em [Exibir estados do usuário](#view-user-states) para chegar à página de usuários da Autenticação Multifator.
6. Localize o usuário que você deseja desabilitar. Talvez seja necessário alterar o modo de exibição na parte superior. Certifique-se de que o status seja **habilitado** ou **aplicado**.
7. Marque a caixa, ao lado do nome.
8. À direita, em etapas rápidas, clique em **Desabilitar**.
   ![Desabilitar usuário – captura de tela](./media/multi-factor-authentication-get-started-user-states/userstate2.png)
9. Será solicitado que você confirme a ação. Clique em **Sim**.
10. Se o usuário tiver sido desabilitado com êxito, você receberá uma mensagem de êxito. Clique em **fechar**


