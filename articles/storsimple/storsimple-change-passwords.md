---
title: Alterar senhas por meio do StorSimple Device Manager | Microsoft Docs
description: "Descreve como usar o serviço StorSimple Manager para alterar suas senhas de administrador do dispositivo e do StorSimple Snapshot Manager."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: f178509c-f4e1-48a8-90b2-d4ad050eeb30
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/02/2017
ms.author: alkohli
ms.openlocfilehash: 843d0a4bcc1807a6b1995d067e0f9e572307775d
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2017
---
# <a name="use-the-storsimple-manager-service-to-change-your-storsimple-passwords"></a>Usar o serviço StorSimple Manager para alterar suas senhas StorSimple
> [!NOTE]
> O portal clássico para StorSimple foi preterido. Os Gerenciadores de Dispositivos do StorSimple migrarão automaticamente para o novo Portal do Azure, seguindo o agendamento definido para preteri-los. Você receberá um email e uma notificação de portal para essa mudança. Este documento também será desativado em breve. Para exibir a versão deste artigo para o novo Portal do Azure, acesse [Usar o serviço StorSimple Manager para alterar as senhas do StorSimple](storsimple-8000-change-passwords.md). Para dúvidas sobre a migração, consulte [Perguntas Frequentes: migração para o Portal do Azure](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Visão geral
A página **Configurar** do Portal clássico do Azure contém todos os parâmetros de dispositivo que você pode reconfigurar em um dispositivo StorSimple gerenciado por um serviço StorSimple Manager. Este tutorial explica como você pode usar a página **Configurar** para alterar sua senha de administrador do dispositivo ou do StorSimple Snapshot Manager.

## <a name="change-the-device-administrator-password"></a>Alterar a senha de administrador do dispositivo
Quando você usar a interface do Windows PowerShell para acessar o dispositivo StorSimple, será solicitada a inserção de uma senha de administrador do dispositivo. Quando o primeiro dispositivo StorSimple é registrado em um serviço, a senha padrão para essa interface é *Password1*. Para a segurança de seus dados, você deve alterar essa senha no fim do processo de registro. Não é possível sair do processo de registro sem alterar essa senha. Para obter mais informações, consulte a [Etapa 3: configurar e registrar o dispositivo por meio do Windows PowerShell para StorSimple](storsimple-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

A senha que foi definida pela primeira vez por meio da interface do Windows PowerShell durante o registro pode ser alterada no Portal clássico do Azure. Execute as etapas a seguir para alterar a senha de administrador do dispositivo.

#### <a name="to-change-the-device-administrator-password"></a>Para alterar a senha de administrador do dispositivo
1. No portal clássico, clique em **Dispositivos** > **Configurar** para o seu dispositivo.
2. Role para baixo até a seção **Senha de Administrador do Dispositivo** . Forneça uma senha do administrador que contenha de 8 a 15 caracteres. A senha deve ser uma combinação de três ou mais caracteres maiúsculos, minúsculos, numéricos e especiais.
3. Confirme a senha.
4. Na parte inferior da página, clique em **Salvar** .

A senha do administrador do dispositivo agora deve estar atualizada. Você pode usar essa senha modificada para acessar a interface do Windows PowerShell.

## <a name="change-the-storsimple-snapshot-manager-password"></a>Alterar a senha do StorSimple Snapshot Manager
O software Gerenciador de Instantâneos StorSimple software reside no host Windows e permite que os administradores gerenciem backups do seu dispositivo StorSimple na forma de instantâneos locais e de nuvem.

Ao configurar um dispositivo no StorSimple Snapshot Manager, será solicitado que você forneça o endereço IP e a senha do dispositivo para autenticar o dispositivo de armazenamento. Essa senha será configurada primeiro por meio da interface do Windows PowerShell. Para obter mais informações, consulte a [Etapa 3: configurar e registrar o dispositivo por meio do Windows PowerShell para StorSimple](storsimple-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

A senha que foi definida pela primeira vez por meio da interface do Windows PowerShell durante o registro pode ser alterada no Portal clássico. Realize as etapas a seguir para alterar a senha do StorSimple Snapshot Manager.

#### <a name="to-change-the-storsimple-snapshot-manager-password"></a>Para alterar a senha do StorSimple Snapshot Manager
1. No portal clássico, clique em **Dispositivos** > **Configurar** para o seu dispositivo.
2. Role até a seção **StorSimple Snapshot Manager** . Insira uma senha que tenha 14 ou 15 caracteres. Verifique se a senha contém uma combinação de três ou mais caracteres maiúsculos, minúsculos, numéricos e especiais.
3. Confirme a senha.
4. Na parte inferior da página, clique em **Salvar** .

A senha do StorSimple Snapshot Manager agora deve ser atualizada.

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre a [segurança do StorSimple](storsimple-security.md).
* Saiba mais sobre [como modificar a configuração do dispositivo](storsimple-modify-device-config.md).
* Saiba mais sobre o [uso do serviço StorSimple Manager para administrar seu dispositivo StorSimple](storsimple-manager-service-administration.md).

