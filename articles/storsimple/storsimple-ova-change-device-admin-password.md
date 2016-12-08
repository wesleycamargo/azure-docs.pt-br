---
title: Alterar a senha do administrador do dispositivo virtual StorSimple | Microsoft Docs
description: "Descreve como usar o portal clássico do Azure ou a interface do usuário da Web do StorSimple Virtual Array para alterar a senha do administrador do dispositivo."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 52acde45-ae29-4edb-9377-46918ab7eef8
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/17/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 85fe4dae6abffebd9d68f3b4110dd45338f35bef


---
# <a name="change-the-storsimple-virtual-array-device-administrator-password"></a>Alterar a senha do administrador do dispositivo da Matriz Virtual StorSimple
## <a name="overview"></a>Visão geral
Quando você usar a interface do Windows PowerShell para acessar o dispositivo virtual StorSimple, será solicitada a inserção de uma senha do administrador do dispositivo. Quando o dispositivo StorSimple é configurado e iniciado pela primeira vez, a senha padrão é *Password1*. Para a segurança dos seus dados, a senha padrão expira na primeira vez que você entra e é necessário alterá-la.

Você também pode usar a interface do usuário da Web local ou o portal clássico do Azure para alterar a senha do administrador do dispositivo a qualquer momento depois que o dispositivo for implantado no seu ambiente de produção. Cada um desses procedimentos é descrito neste artigo.

## <a name="use-the-azure-classic-portal-to-change-the-password"></a>Usar o Portal clássico do Azure para alterar a senha
Execute as etapas a seguir para alterar a senha do administrador do dispositivo por meio do portal clássico do Azure.

#### <a name="to-change-the-device-administrator-password-via-the-azure-classic-portal"></a>Para alterar a senha do administrador do dispositivo por meio do portal clássico do Azure
1. No portal, clique em **Dispositivos** > **Configuração** para seu dispositivo.
2. Role para baixo até a seção **Senha de Administrador do Dispositivo** . Forneça uma senha do administrador que contenha de 8 a 15 caracteres. A senha deve ser uma combinação de caracteres maiúsculos, minúsculos, numéricos e especiais.
3. Confirme a senha.
4. Na parte inferior da página, clique em **Salvar** .

A senha do administrador do dispositivo agora deve estar atualizada. Você pode usar essa senha modificada para acessar o dispositivo localmente.

## <a name="use-the-storsimple-virtual-array-web-ui-to-change-the-password"></a>Usar a interface do usuário da Web do StorSimple Virtual Array para alterar a senha
Execute as etapas a seguir para alterar a senha do administrador do dispositivo por meio da interface do usuário da Web local.

#### <a name="to-change-the-device-administrator-password-via-the-local-web-ui"></a>Para alterar a senha do administrador do dispositivo por meio da interface do usuário da web local
1. Na interface do usuário da Web local, clique em **Manutenção** > **Alteração de senha** para seu dispositivo.
   
    ![alterar password1](./media/storsimple-ova-change-device-admin-password/image40.png)
2. Insira a **Senha atual**.
3. Forneça uma **Nova senha**. A senha deve ter pelo menos oito caracteres. Deve conter três dentre estas quatro opções: caracteres maiúsculos, minúsculos, numéricos e especiais.
   
    Observe que sua senha não pode ser igual às 24 últimas senhas.
4. Insira a senha novamente para confirmá-la.
   
    ![alterar password2](./media/storsimple-ova-change-device-admin-password/image41.png)
5. Na parte inferior da página, clique em **Aplicar**. A nova senha será aplicada em seguida. Se a alteração da senha não for concluída com êxito, você verá o erro a seguir.
   
    ![erro de senha](./media/storsimple-ova-change-device-admin-password/image42.png)
   
    Depois que a senha for atualizada com êxito, você será notificado. Pode-se usar essa senha modificada para acessar o dispositivo localmente.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre a [administração de sua StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).




<!--HONumber=Nov16_HO3-->


